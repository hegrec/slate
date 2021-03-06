## <span class="jumptarget"> <a name="webhooks_intro"></a>Webhooks </span>

<a href="http://en.wikipedia.org/wiki/Webhook" target="_blank">Webhooks</a> allow developers to build apps that receive information, in near real-time, about events that happen on Bigcommerce stores. Webhooks require you to provide a callback URI where you want us to send information about the events that your app subscribes to. When the event happens we'll send a `POST` request to your callback URI and then your app can perform some action based on that event.

For example, you might build an app that needs to know when:

*   An order is placed.
*   A product is added.
*   A customer record is updated.


<aside class="notice">
NOTES: Webhooks differ from the rest of the Stores API as follows:
  <ul>
	<li>OAuth is required; basic authentication is not supported.</li>
	<li>Self-signed certificates are not supported.</li>
	<li>JSON is required; XML is not supported.</li>
  </ul>
</aside>


All webhooks requests must include the following in their HTTP headers:

```
     Accept: application/json
     Content-Type: application/json
     X-Auth-Client: _the OAuth client id_
     X-Auth-Token: _the OAuth token_
```

### <span class="jumptarget"> Prerequisites </span>

Before you can send any requests or receive any responses, you will need the following:

*   **A store**: you can get a sandbox store by joining the [Partner Program](https://www.bigcommerce.com/partners/).
*   **OAuth Client ID**: obtained during [App Registration](/api/v2/#registration).
*   **OAuth token**: obtained during [App Installation](/api/v2/#installation).
*   **Valid TLS/SSL setup**: verify your setup at the following site: [https://sslcheck.globalsign.com](https://sslcheck.globalsign.com).

<aside class="notice">
NOTES: Any one of the following will cause a connection failure:
  <ul>
	<li>Hostname/DNS mismatch.</li>
	<li>Self-signed certificate.</li>
	<li>Intermediate certificates not loaded.</li>
  </ul>
</aside>


### <span class="jumptarget"> Creating webhooks </span>

<aside class="warning">
MIGRATION NOTE: Stacked heads; insert something here!
</aside>

#### <span class="jumptarget"> Sending the POST request </span>

To create a webhook, send a `POST` request to the `hooks` resource, including:

*   As the `scope` value, the event for which you would like to receive notification. See next section for the list of possibilities.
<aside class="notice">
NOTE: Wildcards are supported for <code>scope</code>.
</aside>
*   As the `destination` value, the callback's fully qualified URI.

*   A `headers` object containing one or more name-value pairs, both string values (optional). If you choose to include a `headers` object, Bigcommerce will include the name-value pair(s) in the HTTP header of its `POST` requests to your callback URI at runtime. While this feature could be used for any purpose, one is to use it to set a secret authorization key and check it at runtime. This provides an additional level of assurance that the `POST` request came from Bigcommerce instead of some other party, such as a malicious actor.

*   By default, new webhooks will be set to be inactive and will have a blank value. If you want to create a webhook that should be active initially, you can also pass the following name-value pair: `"is_active": true`.

An `HTTP 201` response indicates that the webhook was set successfully.

<aside class="warning">
MIGRATION NOTE: Links above and below here still need to be set, after Slate file refactoring.
</aside>

Please review the [hooks resource](https://developer.bigcommerce.com/api/stores/v2/webhooks#create-hook) and [webhook object](https://developer.bigcommerce.com/api/objects/v2/webhook) pages for more details.

#### <span class="jumptarget"> List of webhook events </span>

*   store/order/*
*   store/order/created
*   store/order/updated
*   store/order/archived
*   store/order/statusUpdated
*   store/product/*
*   store/product/created
*   store/product/updated
*   store/product/deleted
*   store/customer/*
*   store/customer/created
*   store/customer/updated
*   store/customer/deleted
*   store/app/uninstalled

### <span class="jumptarget"> Receiving webhook callbacks </span>

You'll need to build an application and configure your server to receive the callback, which we send when events are triggered.

<aside class="notice">
NOTE: It can take up to one minute for BigCommerce to start sending <code>POST</code> requests to your callback URI following the creation of a webhook.
</aside>

#### <span class="jumptarget"> Lightweight callback payload </span>

In the callback, we send a light payload with only minimum details regarding the event that's been triggered. This gives you maximum flexibility as to how you want to handle the notification in your application. For instance, if you subscribe to the `store/product/update` event, we'll send you the product ID when it's been updated, and you might want to handle it by fetching the product via a request to the [Products resource](/api/v2/products#get-a-product).

An example payload follows.

```
{"store_id":11111,"producer":"stores/abcde","scope":"store/order/statusUpdated","data":{"type":"order","id":173331},"hash":"3f9ea420af83450d7ef9f78b08c8af25b2213637"}
```

#### <span class="jumptarget"> Multiple events are triggered during bulk data imports </span>

Bulk data imports will trigger the relevant events for every record affected. For example, if you have a hook on `store/product/created`, when the merchant imports 2,000 products, then we will send 2,000 individual callback events.

#### <span class="jumptarget"> Payloads are serialized </span>

Payloads are serialized per hook per store.

In the future, we are looking at enabling a replay feature, allowing you to replay select events. What this means is, based on the serialized payload IDs, you can detect if you've missed certain callbacks and then, via a future update, you will be able call a replay method to get the missing events.

### <span class="jumptarget"> Respond to webhook callbacks </span>

To acknowledge that you received the webhook without issue, your server should return a `200 HTTP` status code. Any other information you return in the request headers or request body will be ignored. Any response code outside the `200` range, including `3_xx_` codes, will indicate to us that you did not receive the webhook. When a webhook is not received (for whatever reason), we will attempt to callback as described just below.

### <span class="jumptarget"> Callback retry mechanism </span>

Webhooks will do their best to deliver the events to your callback URI. The dispatcher will attempt several retries until the maximum retry limit is reached, as follows:

*   Whenever a webhook gives a non-`2_xx_` response, or times out, the app will be blocked for 60 seconds.
*   Webhooks created during that 60-second block will be queued up to send on the next scheduled retry attempt after the block expires, so that webhooks are not lost.

The dispatcher will then attempt several retries (at increasing intervals) until the maximum retry limit is reached, as follows:

#### <span class="jumptarget"> Retry intervals </span>

1.  60 seconds after the most recent failure
2.  180 seconds after the most recent failure
3.  300 seconds after the most recent failure
4.  600 seconds after the most recent failure
5.  900 seconds after the most recent failure
6.  1800 seconds after the most recent failure
7.  3600 seconds after the most recent failure
8.  7200 seconds after the most recent failure
9.  21600 seconds after the most recent failure
10.  50400 seconds after the most recent failure
11.  86400 seconds (24 hours) after the most recent failure

After the final retry attempt above (cumulatively, 48 hours after the first delivery attempt), the webhook will automatically be deactivated, and we will send an email to the developer's email address registered on the subscribing app. Should you wish to reactivate the hook, you can set the `is_active` flag back to `true` via a [PUT request](/api/stores/v2/webhooks#update-a-hook) to the `hooks` resource.

### <span class="jumptarget"> Updating a webhook </span>

Using your OAuth access token, send a [PUT request](/api/stores/v2/webhooks#update-hook) to the `hooks` resource.

### <span class="jumptarget"> Deleting a webhook </span>

Using your OAuth access token, send a [DELETE request](/api/stores/v2/webhooks#delete-hook) to the `hooks` resource.

### <span class="jumptarget"> Troubleshooting </span>

<aside class="warning">
MIGRATION NOTE: Stacked heads; insert something here!
</aside>

#### <span class="jumptarget"> Not receiving the POST requests to my callback URI </span>

As noted above, if your app does not return an `HTTP 2_xx_` to Bigcommerce upon receipt of the POST request to the callback URI, Bigcommerce considers it a failure. Bigcommerce will keep trying for a little over 48 hours. At the end of that time, Bigcommerce sends an email to the email address set during app registration and flips the `is_active` flag to `false`.

You can proactively check to make sure that everything is OK by periodically making a GET request and checking the `is_active` flag.

If you receive an email or discover that the `is_active` flag has been flipped to `false`, try the following.

*   Check to see if your app is responding to the POST request with something other than `HTTP 200`.
*   Check to make sure that your server has a valid TLS/SSL setup. One way to do this is by visiting the following website: <a href="https://sslcheck.globalsign.com" target="_blank">https://sslcheck.globalsign.com<a>. Any of the following will cause the TLS/SSL handshake to fail: 

*   Self-signed certificate
*   Host name of the certificate does not match the server's DNS
*   Your server's key or trust store has not been loaded up with the intermediate certificates necessary to establish the chain of trust

Once you have resolved the issue preventing the connection, send a PUT request to flip the `is_active` flag back to `true`. This will cause Bigcommerce to start trying to send the POST requests to your callback URI again.

#### <span class="jumptarget"> Not receiving an HTTP 201 response after sending POST to create webhook </span>

After sending a POST request to create a webhook, you should get an HTTP 201 back. If you do not, check your TLS/SSL setup and the HTTP header in your request. The requirements for the HTTP header are discussed in the [introduction](#webhooks_intro) above.

### <span class="jumptarget"> Tools for Debugging and Testing Webhooks </span>

<aside class="warning">
MIGRATION NOTE: Stacked heads; insert something here!
</aside>

##### <span class="jumptarget"> RequestBin </span>

While planning your integration, [RequestBin](http://requestb.in/) is a very helpful tool to help view webhooks we send without much setup. In seconds you can start seeing the webhooks we are firing and their data.

##### <span class="jumptarget"> ngrok </span>

As you are building your integration, you may desire the abilty to test webhooks on your dev machines.

We suggest using [ngrok](https://ngrok.com/), which can be used to easily set up tunnels between a server running on localhost and a public URL.

This enables our webhooks to be sent to your localhost environments via a public URL. No production push required.

