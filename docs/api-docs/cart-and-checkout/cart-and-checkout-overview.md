# Cart and Checkout
## Storefront Cart and Checkout

The Storefront APIs are for managing the contents of a shopper's cart and checkout using JavaScript in the context of a storefront session.

### When to use the Storefront APIs
* Analytics and Tracking
* Retrieving cart data client-side
* Quick Order Form
* Upsell applications

### Using the Fetch API
The [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) is an alternative to [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) for making http requests in JavaScript. You can use the Fetch API to interact with the Storefront APIs and return a shopper's cart, checkout, or order.

Most modern browsers, except Internet Explorer, support Fetch API. We recommend using a [Polyfill](https://github.com/github/fetch) for Internet Explorer 10+ and XMLHttpRequest for earlier versions.


To learn more about using the Fetch API with the Storefront see our [Working with the Storefront Cart and Checkout APIs](/api-docs/cart-and-checkout/working-sf-apis) tutorial.


You can run fetch requests from the browser console to test, or you can use the [Scripts API](/api-docs/scripts/scripts-overview) to inject JavaScript into your theme's footer.

```js title="Log cart details to the console" lineNumbers
console.log('Log Cart');
fetch('/api/storefront/cart', {
  credentials: 'include'
}).then(function(response) {
  return response.json();
}).then(function(myJson) {
  console.log(myJson);
}).catch(function (error) {
  console.log(error);
});
```
&nbsp;
```js title="Log checkout details to the console" lineNumbers
console.log('Log Checkout');
fetch('/api/storefront/cart?includes=consignments.availableShippingOptions', {
  credentials: 'include'
}).then(function (response) {
  return response.json();
}).then(function (cartJson) {
  console.log(cartJson);
  return cartJson[0].id;
}).catch(function (error) {
  console.log(error);
}).then(function (cartId) {
  return fetch('/api/storefront/checkouts/' + cartId, {
    credentials: 'include'
  })
}).then(function (response) {
  return response.json();
}).then(function (checkoutJson) {
  console.log(checkoutJson);
}).catch(function (error) {
  console.log(error);
});
```
<!-- theme: info -->
> #### Log order details
> To log order details to the console, use the [Scripts API](/api-docs/scripts/scripts-overview) to inject the script into your theme's footer or use the [Script Manager](https://support.bigcommerce.com/s/article/Using-Script-Manager?language=en_US) to add the script directly to `order-confirmation.html`.

```js title="Log order details to the console" lineNumbers
console.log('Log Order');
fetch('/api/storefront/order/' + checkout.order.id, {
  credentials: 'include'
}).then(function (response) {
  return response.json();
}).then(function (myJson) {
  console.log(myJson);
}).catch(function (error) {
  console.log(error);
});
```

To access `checkout.order.id` from `order-confirmation.html`, use the Stencil theme's page context to assign the value of `checkout.order.id` to a variable. The following example assigns the value returned by `{{checkout.order.id}}` to the variable `jsContextOrderId`, then fetches order details and logs them to the console.

```html title="Log order details to the console" lineNumbers
<script>
// assign order ID to a variable
let jsContextOrderId = {{checkout.order.id}};

// use jsContextOrderId to fetch and log order details
fetch(`/api/storefront/order/${jsContextOrderId}`, {
  credentials: 'include'
}).then(function (response) {
  return response.json();
}).then(function (myJson) {
  console.log(myJson);
}).catch(function (error) {
  console.log(error);
});
</script>
```


## Server-to-Server Cart and Checkout


The Server-to-Server APIs are for managing the contents of a shopping cart and checkout remotely from a server. Using the Cart, Checkout, and Payment APIs allows for a fully headless ecommerce solution.


### CaaS

[Commerce-as-a-Service](https://www.bigcommerce.com/commerce-as-a-service/) is made possible with the Server-to-Server APIs. It lets the underlying Cart, Checkout, Order, and Payment APIs be called from a separate app, allowing you to create a completely custom checkout without touching the BigCommerce storefront.


## When to use
* Modifying cart contents, such as price matching
* Taking a shopper through cart and checkout using an app
* Quote Builder - Building a cart then restoring it on the storefront
* Native mobile apps
* Pre-filling customer information

## Persistent Cart


[Persistent Cart](https://support.bigcommerce.com/s/article/Persistent-Cart) lets a logged-in shopper access the same cart across devices. Persistent Cart is available on [Plus, Pro, and Enterprise plans](https://www.bigcommerce.com/essentials/pricing/).

Persistent Cart works with our Storefront Cart and Server-to-Server Cart.

To learn more on setup, see [Persistent Cart](https://support.bigcommerce.com/s/article/Persistent-Cart).

## Troubleshooting cart errors
We will go over common Storefront Cart and Checkout as well as Server-to-Server Cart and Checkout errors. Each scenario will include the error message and how to fix the error. The most common errors are listed below.


### Please create some text for the API option [422]
*Server to Server Cart API*

***Issue:*** When a cart contains a product that has an incorrect or missing text modifier.

***Resolution:***
Options and modifiers refer to a list of choices on a product. Options used to build out variants and modifiers are not tied to variants at all. To learn more about options and modifiers, see [Products Overview](/api-docs/catalog/products-overview#products-overview_modifier-options).

To add a product to the cart with a single modifier (text field), POST to the [Cart API](/api-reference/cart-checkout/server-server-cart-api/cart/createacart) without the `variant_id`.


```json title="Single modifier" lineNumbers
{
  "line_items": [
    {
      "quantity": 1,
      "product_id": 1001,
      "option_selections": [
        {
          "option_id": 123,
          "option_value": "Hello!"
        }
      ]
    }
  ]
}
```

To add a product to the cart with one option (radio button) associated with it, use just the `variant_id` in the request.

```json title="Single option" lineNumbers
{
  "line_items": [
    {
      "quantity": 1,
      "product_id": 1001,
      "variant_id": 2331
    }
  ]
}
```

To add a product with both an option and a modifier associated with it, use the `option_id` and `option_value`. This example uses a radio button (option) and a text field (modifier).

```json title="Modifier and option" lineNumbers
{
  "line_items": [
    {
      "quantity": 1,
      "product_id": 101,
      "option_selections": [
        {
          "option_id": 231,
          "option_value": 456
        },
        {
          "option_id": 123,
          "option_value": "Hello!"
        }
      ]
    }
  ]
}
```

### Missing line_items in request body [422]

*Server to Server Cart API*

***Issue:***
When a required product modifier is missing. A product can have a modifier that is not required. In those cases, you can add the product to the cart without the modifier.

***Resolution:***
Use the [Get Products](/api-reference/catalog/catalog-api/products/getproducts) or [Get Modifier](/api-reference/catalog/catalog-api/product-modifiers/getmodifiers) endpoints to return the modifier ID. The `modifier_id` = `option_id`.

To create a cart with a product modifier:

```json title="Create cart with modifier" lineNumbers
{
  "line_items": [
    {
      "quantity": 1,
      "product_id": 1001,
      "option_selections": [
        {
          "option_id": 123,
          "option_value": "Hello!"
        }
      ]
    }
  ]
}
```

### A shipping address for this order is incomplete [422]
*Server to Server Cart API*

***Issue:***
This error can return when the customer ID of a cart has changed.

***Resolution:***
You can link the customer ID to discounts and pricing available to that customer. However, if coupons, discounts, taxes, or shipping changes, anything that affects the cart price is invalidated. 

A better option is to create a cart with the `customer_id` as part of the request body and use the [Get Customers](/api-reference/customer-subscribers/customers-api/customers/getallcustomers) endpoint to get the `customer_id`.

To create a cart with a `customer_id`:

```json title="Create cart with customer ID" lineNumbers
{
  "customer_id": 12,
  "line_items": [
    {
      "quantity": 5,
      "product_id": 191
    }
  ]
}
```

### This product has options; variant ID is required [422]
*Server-to-Server Cart API*


***Issue:***  When a product has options and variant ID is not supplied in either the create or update cart request.

***Resolution:***
To get the variant ID use the [Get Products](/api-reference/catalog/catalog-api/products/getproducts) endpoint or the [Get Variants](/api-reference/catalog/catalog-api/product-variants/getvariantsbyproductid) endpoint.
To create a cart with a variant ID:

```json title="Create cart with variant ID" lineNumbers
{
  "line_items": [
    {
      "quantity": 1,
      "product_id": 1001,
      "variant_id": 2331
    }
  ]
}
```

***Issue:***
The `option_id` is incorrect.

***Resolution:***
To get the correct `option_id`, make a request to [Get Products](/api-reference/catalog/catalog-api/products) or [Get Options](/api-reference/catalog/catalog-api/product-options/getoptions).

```json title="Create cart option ID" lineNumbers
{
  "line_item": {
    "quantity": 1,
    "product_id": 76,
    "list_price": 170.00,
    "option_selections": [
      {
        "option_id": 21506,
        "option_value": 10090
      }
    ]
  }
}
```

*Storefront Cart API*

***Issue:***

* The required modifier on the storefront is missing. This missing modifier can include a text modifier with no selection. Make sure the `optionValue` is not blank.

* The variantID is incorrect.
* The modifierID is incorrect.

**Resolution:**
To add a product to the cart with a single modifier (text field), POST to the [Cart API](/api-reference/cart-checkout/storefront-cart-api/cart/createacart) without the `variant_id`. Use the `optionId` and `optionValue` instead.

```json title="Create cart optionId and optionValue" lineNumbers
{
  "lineItems": [
    {
      "quantity": 1,
      "product_id": 1001,
      "optionSelections": [
        {
          "optionId": 123,
          "optionValue": "Hello!"
        }
      ]
    }
  ]
}
```

To add a product to the cart with one option (radio button) associated with it, use just the `variant_id` in the request.

```json title="Create Storefront Cart variantID" lineNumbers
{
  "lineItems": [
    {
      "quantity": 1,
      "productId": 1001,
      "variantId": 2331
    }
  ]
}
```

To add a product that has both an option and a modifier associated with it, then use the `option_id` and `option_value`. This example uses a radio button (option) and a text field (modifier).

```json title="Add item with option and modifier" lineNumbers
{
  "lineItems": [
    {
      "quantity": 1,
      "product_id": 101,
      "optionSelections": [
        {
          "optionId": 231,
          "optionValue": 456
        },
        {
          "optionId": 123,
          "optionValue": "Hello!"
        }
      ]
    }
  ]
}
```

### You can only purchase a maximum of :qty of the :product per order [409]
*Server to Server Cart API*

***Issue:***
When you add less than a product’s minimum required purchase or more than the maximum purchase to a cart.


***Resolution:***
Check the product for `order_quantity_minimum` and `order_quantity_maximum` for the correct amount to add to the cart. Use the [Get Product](/api-reference/catalog/catalog-api/products/getproducts) endpoint.


### Internal Server Error [500]

*Server to Server Cart API*

***Issue:***
Trying to edit a cart that does not exist.

***Resolution:***
Carts are only valid 30 days past the `date_last_modified`. Check the [Get Carts](/api-reference/cart-checkout/storefront-cart-api/cart/getacart) endpoint for the current available session cart.


*Storefront Cart API*

***Issue:***
When you add less than a product’s minimum required purchase or more than the maximum allowed purchase to a cart. Use the [Get Product](/api-reference/catalog/catalog-api/products/getproducts) endpoint to check for min/max purchase restrictions.

***Resolution:***
Check the product for order_quantity_minimum and order_quantity_maximum for the correct amount to add the cart. Use the [Get Product](/api-reference/catalog/catalog-api/products/getproducts) endpoint.

## Related resources

### Articles
* [Working with the Storefront Cart and Checkout](/api-docs/cart-and-checkout/working-sf-apis)

### Endpoints
* [Storefront Cart](/api-reference/cart-checkout/storefront-cart-api)
* [Storefront Checkout](/api-reference/cart-checkout/storefront-checkout-api)
* [Server-to-Server Cart](/api-reference/cart-checkout/server-server-cart-api)
* [Server-to-Server Checkout](/api-reference/cart-checkout/server-server-checkout-api)

### Webhooks
* [Orders](/api-docs/getting-started/webhooks/webhook-events#webhook-events_orders)
* [SKU](/api-docs/getting-started/webhooks/webhook-events#webhook-events_sku)
* [Cart](/api-docs/getting-started/webhooks/webhook-events#webhook-events_cart)
