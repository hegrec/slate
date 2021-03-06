# 2016

## June

### API: Order Shipment resource adds "shipping_provider" field

The Order Shipment resource has a new `shipping_provider` field, which contains the enum of the delivery service used to fulfill the shipment.

We have added this field to the [Order Shipment](/api/v2#shipping-methods) documentation, including the sample responses for Order Shipment endpoints that can return this field.


### Store Information endpoint now passes secure store URL

The Store Information endpoint now provides a `secure_url` field, containing the store's secure (HTTPS) URL.

This new field facilitates the <a href="https://jwt.io/introduction/" target="_blank"> JWT (JSON Web Token)</a> approach to login tokens. Apps and integrations will be able to generate tokens on the client side, containing a partial URL. By consuming the `secure_url` value, the app or integration will be able to complete the URL.

We have this new property to our documentation on the Store Information [object](/api/v2/#store-information-object) and [resource](/api/v2/#get-a-store-s-information).


### New variables separate raw price from currency

Bigcommerce's Blueprint themes framework now provides two new global price variables that separately handle raw price versus national currency:

- `%%GLOBAL_RawProductPrice%%`
- `%%GLOBAL_SelectedCurrencyCode%%`

The `%%GLOBAL_RawProductPrice%%` variable specifies a product's raw numeric price, with no currency metadata. The `%%GLOBAL_SelectedCurrencyCode%%` variable specifies the currency as an [ISO 4217](https://en.wikipedia.org/wiki/ISO_4217#Active_codes)–compliant alphanumeric code.

You can use these variables as alternatives to the `%%GLOBAL_ProductPrice%%` variable, which combines the numeric price with a currency token. If your theme uses microdata, these new variables facilitate two microdata best practices:

- Isolate numeric price from currency metadata.
- Represent currency as a unique alphanumeric code, rather than as an "ambiguous" token/symbol that could be shared among multiple national currencies.

The two new variables are available in all snippets where products are available. (In general, this is anywhere that `%%GLOBAL_ProductPrice%%` appears). You can find them listed on [this updated reference page](/themes/global_variables).


## May

### Order Shipment resource adds "shipping_provider" field

The Order Shipment resource has a new `shipping_provider` field, which contains the enum of the delivery service used to fulfill the shipment.

We have added this field to the [Order Shipment object](/api/v2/orders/#shipment-object-properties)'s documentation, and also to sample responses for 
[Order Shipment endpoints](/api/v2/orders/#shipments-operations) that can return this field.


### Stencil Cornerstone theme 1.2.1 released

Version 1.2.1 of the Stencil Cornerstone theme is now available. This maintenance release corrects a bug that allowed shoppers to add items to their cart without selecting those items' required options. It also corrects an Internet Explorer 10–specific bug that blocked shoppers from editing cart contents and certain other functions.

For details, please see the Cornerstone 1.2.1 [release notes](https://stencil.bigcommerce.com/v1.0/docs/release-notes-cornerstone-121-theme).

Once once you have pulled the latest code from the [Stencil repo](https://github.com/bigcommerce/stencil), you can access this version by checking out its tag, using: `git checkout v1.2.1`


### List Orders endpoint adds email filter

The List Orders endpoint now allows filtering by customer email address. We have added the `email` field to the list of filters on the [List Orders reference page](api/v2/orders/#list-orders).


### Apps can now request new auth scopes after publication

Apps for BigCommerce stores can now request new OAuth scopes after publication. To grant these new scopes, merchants no longer need to uninstall and reinstall the app.

For details (including how to make sure your apps are compatible with this new option), please see [this earlier advisory](#auth-scopes-advisory). 

We have updated our [app registration](/api/v2#registration) instructions, and our [app installation/update](/api/v2#installation) reference and diagram, to cover token exchanges that provide new scopes for installed apps.


### Stencil Cornerstone 1.2 theme released; adds logo positioning

Version 1.2 of the Stencil Cornerstone theme is now available. This release allows merchants to use Theme Editor to globally align their store's logo left, center, or right. There are also several minor bug fixes.

For details, please see the Cornerstone 1.2 [release notes](https://stencil.bigcommerce.com/v1.0/docs/release-notes-cornerstone-12-theme).

Once once you have pulled the latest code from the [Stencil repo](https://github.com/bigcommerce/stencil), you can access this version by checking out its tag, using: `git checkout v1.2.0`


### Store Information endpoint adds "features" array

The Store Information endpoint now provides a `features` array, whose elements flag features that affect app compatibility or functionality. This array's initial `stencil_enabled` (Boolean) element indicates whether a store is using a Stencil theme, as in this example:

    "features": {
        "stencil_enabled": true
    }

BigCommerce will add other features to this array, as warranted. For details, please see our documentation on the Store Information [object](/api/v2/#store-information-object) and [resource](/api/v2/#get-a-store-s-information).


### Stencil custom/private theme uploads now available

Developers can now upload custom/private Stencil themes to storefronts, via the BigCommerce control panel. 

For details, please see [this Knowledge Base article](https://support.bigcommerce.com/articles/Public/Custom-Theme-Upload) and [this troubleshooting page](https://stencil.bigcommerce.com/docs/uploading-a-custom-theme).


## April 

### New API SKU Properties Available

We have updated the product SKU API to make several new properties available directly on the SKU resource. This update means that you no longer need to create a product rule in order to set these properties – you can now update them directly on the SKU:

| Property | Type | Description |
|---|---|---|
| price | decimal | This SKU's base price on the storefront. If this value is null, the product's default price (set in the Product resource's price field) will be used as the base price. |
| adjusted_price | decimal | The SKU's price on the storefront – after the product's base price is inherited, and/or any option set or any product rules are applied. This property is READ-ONLY. |
| weight | decimal | This SKU's base weight on the storefront. If this value is null, the product's default weight (set in the Product resource's weight field) will be used as the base weight. |
| adjusted_weight | decimal | This SKU's weight on the storefront – after the product's base weight is inherited, and/or any option set or any product rules are applied. This property is READ-ONLY.|
| is_purchasing_disabled | boolean | If true, this prohibits purchasing of the SKU.|
| purchasing_disabled_message | string  | The message to display if purchasing is disabled on this SKU. |
| image_file | string | The image that will be displayed when this SKU is selected on the storefront. When updating a SKU image, send the publicly accessible URL. Supported image formats are JPEG, PNG, and GIF. |

You can see the new SKU schema on our updated [Object page][1]. You'll find usage guidelines, and updated sample responses, on our updated [Resources page][2].

This update requires no immediate changes to your applications. Any existing SKU-only rules will still function as before, and will still be accessible via the API.

However, as we make SKU-only properties available directly on SKUs, BigCommerce plans to eventually deprecate the management of SKU-only properties via product rules.

[1]: /api/v2/
[2]: /api/v2/


### Please prepare your apps for new auth-scopes options

Starting May 2, 2016, BigCommerce will enable you to change your application's required scopes, after you have published the app. Merchants will be prompted to update the app, and to grant new scopes, the next time they load the app. You will then receive a new token with the updated scopes.  

This change opens up new options for your apps. However, at a minimum, you might need to update your code, because the Auth Callback URI can now be called at times other than installation. Your app must be ready to properly interpret such calls to `auth_callback_url` for purposes other than initial installation.  

Please read this entire advisory to ensure that your application will continue to function as expected, after we make this change.  

#### Scopes background

When you registered your application in Developer Portal, you were required to specify an `auth_callback_url`, to specify a `load_url`, and to request some scopes. Scopes define permissions associated with a set of store resources. By granting your app access to scopes, a merchant allows you to use their store's API.  

For example, if you want to access the BigCommerce `products` API for your user's store, you must request the `products` scope. Each merchant that installs your app must grant you access to that scope, so that you can consume their products.   

The `auth_callback_url` is used to safely receive a token associated with the granted scopes. The `load_url` is used to redirect merchants to your application when they click on it from their store's control panel.  

#### The problem we solved

Prior to this change, you had no way to change the scopes requested by your published app. If&nbsp;you changed these scopes, existing installations would cease to function. This forced each merchant to uninstall, then reinstall, your app to grant you the new scopes.  

#### What hasn't changed?

* When your app is installed, you'll still receive an HTTP call on the `auth_callback_url` that you have registered in Developer Portal.
* You'll still need to do a token exchange, to receive a new token for granted scopes that you have requested.

#### What has changed?

* If you change the scopes you are requesting in Developer Portal, BigCommerce will mark your application as needing reauthorization. The next time the merchant loads your app, you will receive a call on the `auth_callback_url` that you registered. You should then reinitiate the token exchange process, to receive a new token for that store with the updated scopes.  
(Note: This applies to existing installations, where the scopes you are requesting do not match the token generated when the app was first installed.)

* We added an additional field in the signed payload you receive in your `auth_callback_url`, containing the scopes associated with the token.

Merchants will see changes only under certain conditions:   

#### What hasn't changed?

* When merchants install your application, they will still see a confirmation screen prompting them to grant you the scopes you requested.
* After completing installation, or when they launch your application, merchants will still be redirected to the `load_url` you registered.

#### What has changed?

* If your app requires reauthorization, merchants will now see a reauthorization screen – similar to the initial installation screen – indicating the newly requested scopes.

1. Modify your handler for `auth_callback_url`, to account for the fact that it can now be called after your app has already been installed. If you have special, one-time initialization code that you execute during this callback, you must modify it to run that code only once per store. Note that you will get this call, with a new code, any time a merchant grants new scopes to your application.

2. After you complete the token exchange, save the new access token. (You must save this token because the new token will be associated with the scopes that were granted.)

3. Use the new access token to consume the store's API.

You might have designed your application with the assumption that the `auth_callback_url` would be triggered only at install time. However, under the new flow, `auth_callback_url` will also be triggered when a merchant grants new scopes to an already-installed app.  

If you rely on `auth_callback_url` to trigger one-time initialization workflows – like charging the customer, or sending a welcome email – be aware that requesting new scopes might unintentionally re-trigger these workflows.  

Note that until the merchant reauthorizes your app, the old token will continue to work with the limited set of scopes that were granted when the merchant initially installed your app.  

Below are some foreseeable problems and their solutions.   

#### Problem 1: Your auth_callback_url endpoint accounts only for new installs, not reauthorizations

##### Steps leading to this problem:

* You initially requested a certain scope (e.g., `view products`)
* A merchant installs your app
* You add an extra scope (e.g., `view orders`) to your list of requested scopes
* The next time the merchant loads your app, you receive a call on the `auth_callback_url` you registered in Developer Portal.

If your app incorporates an assumption that calls to the `auth_callback_url` happen only at install time, it might treat this call as a new install, re-triggering initialization workflows that you did not intend.

##### Solution

You should save the `store_hash` associated with the token that your app received during the OAuth token exchange. Any time you receive a call on the `auth_callback_url`, you should first look in your database for the `store_hash`.

If the `store_hash` already exists, you will know that your app was already installed for that store, and that you have just been granted the new scopes.

If the `store_hash` does not exist, you will know that this is a first-time installation for the given store.

From our sample app, [here is an example](https://github.com/bigcommerce/hello-world-app-ruby-sinatra/blob/master/hello.rb#L102) of appropriate handling of this logic.

#### Problem 2: Missing uninstall_callback_url endpoint

##### Steps leading to this problem:

* You do not keep track of which merchants have uninstalled your app
* A merchant uninstalls your app
* This merchant reinstalls your app
* You receive a call on the `auth_callback_url` you registered in Developer Portal
* At this point, you have no way to know whether this is an install or a reauthorization

##### Solution:

We highly recommend that you register an endpoint in Developer Portal as the `uninstall_callback_url`. This way, any time a merchant uninstalls your application, your `uninstall_callback_url` will be hit.

It is important that you keep track of uninstalls, so that the next time you receive a call on your `auth_callback_url`, you'll be able to differentiate between a new installation versus a merchant granting new scopes for an already installed application.

One way to keep track of uninstalls is by managing your database entries: Remove the `store_hash` of the corresponding install. Another way is to use a boolean that is flagged/unflagged as a merchant installs/uninstalls your application.


## March

### SNI (Server Name Indication) required as of June 30, 2016

As of June 30, 2016, all requests to the BigCommerce API will be required to support [Server Name Indication][3] (SNI). After that date, requests will fail if they don't support SNI.

[3]: https://en.m.wikipedia.org/wiki/Server_Name_Indication

### Orders API provies opt-in email field

The Orders API provides a new `is_email_opt_in` field. This Boolean field will be `true` if the shopper has opted in (on the checkout page) to receive a store's email newsletter. It is read-only.

We have updated the following documentation to cover this new field:  

* [Orders][4] object
* [Create an Order][5] endpoint
* [Update an Order][6] endpoint

[4]: /api/v2/#orders
[5]: /api/v2/#create-an-order
[6]: /api/v2/#update-an-order

### Stencil Framework now generally available

Developers can now install the Stencil themes framework without registering for access. Please follow the documentation link that's relevant to your experience with Stencil:

* New to Stencil? We recommend starting with [this overview][7].
* Installing Stencil for the first time? Use these [complete instructions][8].
* Early Access participant? To receive future updates of the framework, we ask that you please [reinstall Stencil][9] from our new open repositories.

[7]: https://stencil.bigcommerce.com/docs
[8]: https://stencil.bigcommerce.com/docs/installing-and-launching-stencil-1
[9]: https://stencil.bigcommerce.com/docs/early-access-please-reinstall

## February 

### Gift Certificate API

BigCommerce has published a new API for managing gift certificates. The API allows your applications to manage gift certificates' amount/balance, purchaser, recipient, dates of purchase and expiration, and current status.

The new endpoint information is available [here][10].

[10]: /api/v2#gift_certificates

### Customer/Address resource have new custom fields

Two Customers endpoints, and two Customer Addresses endpoints, now provide support for read-only custom fields:

        Customers
          List Customers
          Get a Customer

        Customer Addresses
          List Customer Addresses
          Get a Customer Address

You can access custom fields within the new   **`form_fields`**   element. For details and examples, please see our updated [Customers][11] and [Customer Addresses][11] reference pages.

[11]: /api/v2#customers

### New Store Updated & Order Webhooks

BigCommerce has made available two new webhooks. We encourage you to use these webhooks, as appropriate, in your applications:

* `store/order/message/created` reports [order messages][12] created by customers (via a customer account) or by merchants (via the control panel).
* `store/information/updated` reports updates to a store's settings (domain, address, currency, tax inclusion, etc.).

We have updated the [webhooks reference page][13] to include these new webhooks.

[12]: /api/v2/#order-messages
[13]: /#placeholder

### Expanded faceted-search display

[Compatible][14] Blueprint themes can now display up to 500 values in faceted-search results. This expands the previous 30-value limit on facets like brands, categories, product options, and custom fields.

To enable this expansion, you must retrofit your theme by either replacing or manually updating three files. Procedures and code snippets are in [this new documentation section][15].

We have also updated merchant-oriented documentation that covers [enabling faceted search][16] and [option-value limits][17].

[14]: https://support.bigcommerce.com/articles/Public/Product-Filtering-Settings#themes
[15]: /themes/product-filtering-toolkit#ExpandDisplay
[16]: https://support.bigcommerce.com/articles/Public/Product-Filtering-Settings#enabling
[17]: https://support.bigcommerce.com/articles/Public/Platform-Limits/

### Banners API

BigCommerce has published a new API for managing storefront banners. The API allows your applications to manage banners' display location, timing, and content.

Information about this new endpoint is available [here][18].

[18]: https://developer.bigcommerce.com/api/v2#banners

## January

### New global product variables: SKU, Brand Name, Custom Fields

BigCommerce's Blueprint theme framework now provides three new global variables:

* `%%GLOBAL_ProductSku%%`
* `%%GLOBAL_ProductBrandName%%`
* `%%GLOBAL_ProductCustomFields%%`

These variables are available in all snippets where products are available. (In general, this is anywhere that `%%GLOBAL_ProductPrice%%` appears). They are listed on [this reference page][19].

[19]: /themes/global_variables

### New product and SKU webhooks added

BigCommerce has made available several new webhooks. In the list below, the new options are highlighted within the `store/product/`,   
`store/product/inventory/`, `store/sku/`, and `store/sku/inventory*` categories. We encourage you to use these webhooks, as appropriate,  
in your applications:

```
  store/order/
    store/order/created
    store/order/updated
    store/order/archived
    store/order/statusUpdated
  store/product/
    store/product/created
    store/product/updated
    store/product/deleted
    store/product/inventory/updated
    store/product/inventory/order/updated
  store/product/inventory/
    store/product/inventory/updated
    store/product/inventory/order/updated
  store/sku/
    store/sku/created
    store/sku/updated
    store/sku/deleted
    store/sku/inventory/updated
    store/sku/inventory/order/updated
  store/sku/inventory/
    store/sku/inventory/updated
    store/sku/inventory/order/updated**
  store/customer/
    store/customer/created
    store/customer/updated
    store/customer/deleted
  store/app/uninstalled
```

We have updated the [webhooks reference page][20] to include these new webhooks.

[20]: /api#webhooks

### Status fields added to order/statusUpdated webhook

On January 22nd, 2016, BigCommerce will add status fields to the   `store/order/statusUpdated`   webhook event. These new fields will allow your applications to monitor order status more efficiently, with fewer API calls.

You are not required to incorporate these new fields, although we encourage you to take advantage of them. However, please use this advance notification to make sure that these new fields' presence will not disrupt your code.

Currently, to find out an order's status, an application must GET the order each time it receives the   `statusUpdated`   event. But often, the application needs to act on orders only in a single status. Because one order will generate multiple statusUpdated webhooks as the order goes from incomplete to completed, the GET requests add up to unnecessary traffic.

As of January 21ish, 2016, the webhook message will include details about the order's new and previous status. Applications that monitor the added   `new_status_id`   and   `previous_status_id`   fields will no longer need to GET to the order, simply to determine whether the order needs to be processed.

The   `new_status_id`   and   `previous_status_id`   fields will be included in a newly added   `status`   element, formatted like this:

```
    "status": {
             "previous_status_id": "0",
             "new_status_id": "11"
    }
```

The   `previous_status_id`   field will allow your applications to troubleshoot orders by tracing the path these orders took through fulfillment. You could also build logic around this field, allowing your application to process only orders that pass from one certain status to another.

For example, you could prevent the double-processing of orders whose status accidentally changed, without having to store and check a list of already processed orders.

Here is an example of a webhook message in its currently supported format:

```
    {
      "scope": "store/order/statusUpdated",
      "store_id": "353242",
      "data": {
        "type": "order",
        "id": "817"
      },
      "hash": "24c6b87efc683de186387b12746455230e250fab",
      "created_at": 1449701096,
      "producer": "stores/z4zn3wo"
    }
```

Here is the same webhook message in the new format, with the added   `status`   fields set off with blank lines for visibility:

```
    {
      "scope": "store/order/statusUpdated",
      "store_id": "353242",
      "data": {
        "type": "order",
        "id": "817",

        "status": {
                 "previous_status_id": "0",
                 "new_status_id": "11"
        }

      },
      "hash": "24c6b87efc683de186387b12746455230e250fab",
      "created_at": 1449701096,
      "producer": "stores/z4zn3wo"
    }
```

(This second example shows an order moving from an "Incomplete" status [`id: 0`] to an "Awaiting Fulfillment" status [`id: 11`]. This change occurs whenever an order is paid for during storefront checkout.)

### Include or exclude specific fields for Products resource

Your `Get a Product` or `List Products` requests can now include or exclude specific fields, by appending one of these options:

* `?include=`
* `?include=@summary`
* `?exclude=`

Certain fields are retrieved with all requests. For details and examples, please see [this documentation][21].

[21]: /api/v2#get-a-product