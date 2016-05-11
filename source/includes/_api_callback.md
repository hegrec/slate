# App Installation

## Introduction

A user at a store's Control Panel kicks off the installation or update sequence by clicking to install your app, or by clicking an installed app to update its scopes. BigCommerce redirects the user to the **Auth Callback URI** provided during [app registration](/api/registration). The **Auth Callback URI** must be publicly available, fully qualified, and served over TLS.

PRO TIP: The request comes from the client browser, rather than directly from Bigcommerce. This allows you to use a non-publicly-available **Auth Callback URI** while testing your app.

The following diagram illustrates the entire sequence.

![bc-oauth-install+update-seq.svg](https://www.filepicker.io/api/file/jrCCco74ShqIiILykrG5)

The remainder of this section discusses each action your app needs to take during the sequence.

1.  [Receiving the `GET` request](#get-req)
2.  [Responding to the `GET` request](#get-response)
3.  [Making the `POST` request](#post-req)
4.  [Receiving the `POST` response](#post-receipt)

## Receiving the GET request

### Overview

The `GET` request to your **Auth Callback URI** contains a temporary code that you can exchange for a permanent OAuth token. It also includes a unique value that identifies the store installing or updating your app, as well as other values.

### Parameters

The following table details the full list of parameters and values included in the `GET` request from Bigcommerce to your **Auth Callback URI**. Bigcommerce passes these within the URI itself as query parameters.

| Parameter | Description |
| --- | --- |
| code | Temporary code to exchange for a permanent OAuth token. See [Making the POST request](#post-req) below for more information about this exchange. |
| scope | List of scopes authorized by the user. As a best practice, your app should validate this list to ensure that it matches the app's needs, and fail if it does not. However, at this time, the user does not have any opportunity to pick and choose between scopes. The dialog presented to the user requires the user to approve all scopes or none. |
| context | The store hash: a unique value that identifies the store on which a logged-in user has clicked to install or your app. Bigcommerce passes this along with a context path, as follows: `stores/{store_hash}`. Save the store hash value, because you will need to pass it in all your requests to the Stores API. |

### Example – Initial Installation

This example initiates the token exchange, with a requested scope of `store_v2_orders`:

```
GET /auth?code=qr6h3thvbvag2ffq&scope=store_v2_orders&context=stores/g5cd38 HTTP/1.1  
Host: app.example.com
```

### Example – Updating Scopes

Scope updates must always be additive. The following example requests a scope of `store_v2_products`, in addition to the initially requested scope of `store_v2_orders`:

```
GET /auth?code=qr6h3thvbvag2ffq&scope=store_v2_orders+store_v2_products&context=stores/g5cd38 HTTP/1.1  
Host: app.example.com
```

(Note that when your app receives a new token, any previously issued token is invalidated.)


## Responding to the GET request

Upon receiving the `GET` request at your **Auth Callback URI**, your app should return some HTML to the merchant browser. BigCommerce renders this in an iframe inside of the Control Panel. It could be a form that collects further information from the user, or you could redirect the user to your app's main page. If you do not pass back some HTML, the user will be left looking at a blank screen. Such an app would not be accepted into the App Store.

## Making the POST request

### Overview

The `POST` request's primary purpose is to exchange the temporary access code for a permanent OAuth token. However, your app must pass a number of additional values to accomplish the exchange. Pass the parameters and their values inside the request body, using query parameters and URL-encoding. To achieve this, you must include the following HTTP header: `Content-Type: application/x-www-form-urlencoded`

Make the `POST` request to the following address: `https://login.bigcommerce.com/oauth2/token`.


#### Initial Installation

During initial installation, upon receiving the `POST`, BigCommerce marks the status of your app as "Installed," removes the progress-indicator overlay, and places your app icon in the left-hand navigation of the Control Panel. With the progress-indicator overlay removed, the user can interact with the HTML that you returned in your <a href="#get-response">`GET` response</a>.

#### Updates

During app updates, upon receiving the `POST`, BigCommerce removes the update prompt from the Control Panel.


### Parameters

Include values for each of the following parameters.

| Parameter | Description |
| --- | --- |
| client_id | The Client ID for your app, obtained during [registration](/api/registration). |
| client_secret | The Client Secret for your app, obtained during [registration](/api/registration). |
| code | Temporary access code received in the [GET request](#get-req) discussed above. |
| scope | List of OAuth scopes received in the [GET request](#get-req) discussed above. |
| grant_type | Always use the following: authorization_code. |
| redirect_uri | Must be identical to your registered Auth Callback URI. |
| context | The store hash received in the [GET request](#get-req), in the format: `stores/{_store_hash_}` |

### Examples – Initial Installation

*   [HTTP](#token-http)

```
POST /oauth2/token HTTP/1.1
Host: login.bigcommerce.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 186

client_id=236754&client_secret=m1ng83993rsq3yxg&code=qr6h3thvbvag2ffq&scope=store_v2_orders&grant_type=authorization_code&redirect_uri=https://app.example.com/oauth&context=stores/g5cd38
```

*   [PHP](#token-php)

```
use Bigcommerce\Api\Connection;
$tokenUrl = "https://login.bigcommerce.com/oauth2/token";
$connection = new Connection();
$connection->useUrlencoded();
$response = $connection->post($tokenUrl, array(
    "client_id" => "236754",
    "client_secret" => "m1ng83993rsq3yxg",
    "redirect_uri" => "https://app.example.com/oauth",
    "grant_type" => "authorization_code",
    "code" => $request->get("code"),
    "scope" => $request->get("scope"),
    "context" => $request->get("context"),
));
$token = $response->access_token;
```

### Examples – Updating Scopes

Here again, scope updates must be additive. The following example requests a scope of `store_v2_products`, in addition to the initially requested scope of `store_v2_orders`:

*   [HTTP](#token-http)
```
<div class="bui-tabs">
  <ul class="bui-nav bui-nav-tabs">
    <li class="is-active">
      <a href="#token-http" data-toggle="tab">HTTP</a>
    </li>
    <li>
      <a href="#token-php" data-toggle="tab">PHP</a>
    </li>
  </ul>
  <div class="bui-tab-panel is-active" id="token-http">
<pre><code><b>POST /oauth2/token HTTP/1.1
Host: login.bigcommerce.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 186

client_id=236754&client_secret=m1ng83993rsq3yxg&code=qr6h3thvbvag2ffq&scope=store_v2_orders+store_v2_products&grant_type=authorization_code&redirect_uri=https://app.example.com/oauth&context=stores/g5cd38
</b></code></pre>
  </div>
  <div class="bui-tab-panel" id="token-php">
```

*   [PHP](#token-php)
```
use Bigcommerce\Api\Connection;

$tokenUrl = "https://login.bigcommerce.com/oauth2/token";
$connection = new Connection();
$connection->useUrlencoded();
$response = $connection->post($tokenUrl, array(
    "client_id" => "236754",
    "client_secret" => "m1ng83993rsq3yxg",
    "redirect_uri" => "https://app.example.com/oauth",
    "grant_type" => "authorization_code",
    "code" => $request->get("code"),
    "scope" => $request->get("scope"),
    "context" => $request->get("context"),
));

$token = $response->access_token;
```

## Receiving the POST response

### Overview

The `POST` response will include a JSON object containing the permanent OAuth token, user information, and other values. Upon receiving the permanent OAuth token, store it securely. You should also store the user and store hash values, to identify the user and store at load and uninstall. The following sections detail the contents of the JSON body.

### JSON Values

| Name | Data Type | Value Description |
| --- | --- | --- |
| access_token | string | The permanent OAuth token that your app can use to make requests to the Stores API on behalf of the user. Store this value securely. |
| scope | string | List of authorization scopes. |
| id | integer | Unique identifier for the user. Store this value to identify the user at load and uninstall. |
| email | string | The user’s email address. Store this value to identify the user at load and uninstall. |
| context | string | The store hash, as well as a base path: `stores/{_store_hash_}` |

### JSON Example – Initial Installation

```
{
  "access_token": "g3y3ab5cctiu0edpy9n8gzl0p25og9u",
  "scope": "store_v2_orders",
  "user": {
    "id": 24654,
    "email": "merchant@mybigcommerce.com"
  },
  "context": "stores/g5cd38"
}
```

### JSON Example – Updating Scopes

Update requests will refresh the payload's `access_token` and `scope` values. Here again, the following example requests a scope of `store_v2_products`, in addition to the initially requested scope of `store_v2_orders`:

```
<code class="language-js">{
  "access_token": "hyjielngd8iu0edpy9n8gzl0p25xc7q",
  "scope": "store_v2_orders store_v2_products",
  "user": {
    "id": 24654,
    "email": "merchant@mybigcommerce.com"
  },
  "context": "stores/g5cd38"
}
```
