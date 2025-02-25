# Orders Overview

This article introduces BigCommerce's [Orders V2](/api-reference/store-management/orders) and [Orders V3](/api-reference/store-management/order-transactions) REST API resources. [Orders V2](/api-reference/store-management/orders) exposes endpoints for [creating](/api-reference/store-management/orders/orders/createanorder), [reading](/api-reference/store-management/orders/orders/getallorders), [updating](/api-reference/store-management/orders/orders/updateanorder), and [deleting](/api-reference/store-management/orders/orders/deleteallorders) orders; it also includes endpoints for managing [order shipments](/api-reference/store-management/orders/order-shipments) and [order shipping addresses](/api-reference/store-management/orders/order-shipping-addresses). [Orders V3](/api-reference/store-management/order-transactions) surfaces [order transactions](/api-reference/store-management/order-transactions/transactions/gettransactions) and [order refunds](/api-reference/store-management/order-transactions/order-refunds/) endpoints. For information on processing order payments by API, see [Payments API Overview](/api-docs/payments/payments-api-overview).

### Prerequisites:

* [A BigCommerce store](https://support.bigcommerce.com/s/article/Starting-a-Bigcommerce-Trial)
* Access token for [API authentication](/api-docs/getting-started/authentication/rest-api-authentication) with the following [scopes](/api-docs/getting-started/authentication/rest-api-authentication#oauth-scopes):
  * Orders - **modify**
  * Products - **read-only**
* [Product](/api-reference/catalog/catalog-api/products/createproduct) with [variants](/api-reference/store-management/catalog/product-variants/createvariant).

## Creating an order

To [create an order](/api-reference/store-management/orders/orders/createanorder), send a `POST` request to `/stores/{{STORE_HASH}}/v2/orders`.

```http title="Create an order" lineNumbers
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "billing_address": {
    "first_name": "Jane",
    "last_name": "Doe",
    "street_1": "123 Main Street",
    "city": "Austin",
    "state": "Texas",
    "zip": "78751",
    "country": "United States",
    "country_iso2": "US",
    "email": "janedoe@email.com"
  },
  "products": [
    {
      "name": "BigCommerce Coffee Mug",
      "quantity": 1,
      "price_inc_tax": 50,
      "price_ex_tax": 45
    }
  ]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/orders/createanorder#requestrunner) -->

<!-- theme: info -->
> #### Note
> * The example above contains the minimum required fields for a [create order](/api-reference/store-management/orders/orders/createanorder) request.
> * The product ordered is a *custom* product; custom products do not exist in the catalog.

## Changing order status

Specify [order status](/api-reference/store-management/orders/order-status/getorderstatus) by including the `status_id` property in the [create order](/api-reference/store-management/orders/orders/createanorder) request. To [update an order](/api-reference/store-management/orders/orders/updateanorder) and change its status, send a `PUT` request to `/v2/orders/{{order_id}}`.

```http title="Change order status" lineNumbers
PUT https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders/{{order_id}}
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "status_id": 2
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/orders/createanorder#requestrunner) -->

To [get a list of order statuses](/api-reference/store-management/orders/order-status/getorderstatus), send a `GET` request to `/stores/{{STORE_HASH}}/v2/order_statuses`.

```http title="Example request: Get order statuses"
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/order_statuses
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/order-status/getorderstatus#requestrunner) -->

&nbsp;
```json title="Example response: Get order statuses" lineNumbers
[
  {
    "id": 0,
    "name": "Incomplete",
    "system_label": "Incomplete",
    "custom_label": "Incomplete - Testing",
    "system_description": "An incomplete order happens when a shopper reached the payment page, but did not complete the transaction.",
    "order": 0
  },
  ...
]
```

<!-- theme: info -->
> #### Note
> * If not specified, `status_id` defaults to `1`.
> * The refunded status is neither paid nor unpaid.
> * For information on changing `custom_label` in the control panel, see [Order Statuses](https://support.bigcommerce.com/s/article/Order-Statuses#rename).
> * When an order is created, set to `Awaiting Fulfillment`, and then manually edited, inventory levels won't reflect a change in stock. To learn more about inventory stock settings, see [Stock Adjustment Settings](https://support.bigcommerce.com/s/article/Inventory-Tracking?language=en_US#stock-adjustment).

## Specifying order customer

Specify the [customer](/api-reference/store-management/customers-v3/customers/customersget#responses) by including a `customer_id` in the [create order](/api-reference/store-management/orders/orders/createanorder) request.

```http title="Example request: Specify order customer" lineNumbers
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json


{
  "customer_id": 1,
  "billing_address": {...},
  "products": [...]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/orders/createanorder#requestrunner) -->

To [get a list of customers](/api-reference/store-management/customers-v3/customers/customersget), send a `GET` request to `/stores/{{STORE_HASH}}/v3/customers`.

```http title="Example request: Get a list of customers" lineNumbers
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v3/customers
X-Auth-Token: {{ACCESS_TOKEN}}
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/customers-v3/customers/customersget#requestrunner) -->

<!-- theme: info -->
> #### Note
> Set `customer_id` to `0` to create a guest order.

## Including shipping addresses

Add [shipping addresses](/api-reference/store-management/orders/order-shipping-addresses/updateashippingaddress#request-body) by including a [shipping_address array](/api-reference/store-management/orders/order-shipping-addresses/updateashippingaddress#request-body) in the [create order](/api-reference/store-management/orders/orders/createanorder) request.

```http title="Example request: add shipping addresses" lineNumbers
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "billing_address": {...},
  "shipping_addresses": [
    {
      "first_name": "Rusty",
      "last_name": "Gates",
      "company": "Example LLC",
      "street_1": "123 Example ST",
      "street_2": "",
      "city": "Austin",
      "state": "Texas",
      "zip": "12345",
      "country": "United States",
      "country_iso2": "US",
      "phone": "5128675309",
      "email": "rusty.gates@example.com"
    }
  ],
  "products": [...]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/orders/createanorder#requestrunner) -->

<!-- theme: info -->
> #### Note
> Add multiple shipping addresses to [ship to multiple locations](#shipping-to-multiple-locations).

## Adding products

Specify [products from the catalog](/api-reference/store-management/orders/order-shipping-addresses/updateashippingaddress#request-body) by including a [products array](/api-reference/store-management/orders/order-shipping-addresses/updateashippingaddress#request-body) in a `POST` request to the [create order](/api-reference/store-management/orders/orders/createanorder) endpoint.

```http title="Example request: Add products" lineNumbers
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "billing_address": {...},
  "products": [
    {
      "name": "BigCommerce Coffee Mug", # custom product
      "quantity": 1,
      "price_inc_tax": 50,
      "price_ex_tax": 45
    },
    {
      "product_id": 184,               # product from catalog
      "quantity": 1,
      "product_options": [
        {
          "id": 200,
          "value": "180"
        },
        {
          "id": 230,
          "value": "192"
        }
      ]
    }
  ]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/orders/createanorder#requestrunner) -->

To get the `product_options.id` and `product_options.value` of a product for the order `products` array, send the following `GET` request to [Get variants by product id](/api-reference/catalog/catalog-api/product-variants/getvariantsbyproductid). See the example response that follows, or consult the [response schema](/api-reference/catalog/catalog-api/product-variants/getvariantbyid#responses).

```http title="Example request: Get product variants" lineNumbers
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v3/catalog/products/{{product_id}}/variants
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/catalog/catalog-api/product-variants/getvariantsbyproductid#requestrunner) -->

&nbsp;
```json title="Example response: Get product variants" lineNumbers
{
  "data": [
    {
      "id": 421,
      "product_id": 184,
      ...
      "option_values": [
        {
          "id": 180,         // product_options.value
          "label": "Red",
          "option_id": 200,  // product_options.id
          "option_display_name": "Color"
        },
        {
          "id": 192,
          "label": "Small",
          "option_id": 230,
          "option_display_name": "T-Shirt Size"
        }
      ]
    }
    ...
  ]
}
```

<!-- theme: info -->
> #### Note
> * Custom products do not get added to the catalog.
> * If the product's price is not specified in the [create order](/api-reference/store-management/orders/orders/createanorder) request, BigCommerce's pricing service calculates the price by applying applicable currency conversions and [pricing operations](/api-docs/store-management/pricing-order-operation) (such as [price lists](https://support.bigcommerce.com/s/article/Price-Lists) and [customer group discounts](https://support.bigcommerce.com/s/article/Customer-Groups#pricing)) to the product's catalog price; use `price_inc_tax` and `price_ex_tax` to override the calculated price.
> * Marketing promotions currently do not apply to orders created with the Orders API.
> * If you override `price_ex_tax` or `price_inc_tax`, override both; otherwise, order totals will not calculate correctly.
> * Overriding `price_inc_tax` or `price_ex_tax` does not change variant pricing.



## Creating order shipments

Once an order has products, a billing address, and a shipping address, you can create an order shipment.

To [create an order shipment](/api-reference/store-management/orders/order-shipments/createordershipments), send a `POST` request to `/stores/{{STORE_HASH}}/v2/orders/{{order_id}}/shipments`.

```http title="Example request: Create an order shipment" lineNumbers
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}}/v2/orders/{{order_id}}/shipments
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

  "tracking_number": "EJ958083578UK",
  "comments": "Janes Order",
  "order_address_id": "128",
  "shipping_provider": "",
  "items": [
    {
      "order_product_id": 194,
      "quantity": 1
    },
    {
      "order_product_id": 195,
      "quantity": 1
    }
  ]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/order-shipments/createordershipments#requestrunner) -->

|Property|Description|
|-|-|
|`tracking_number`|Shipping provider tracking number; used to generate tracking link|
|`comments`|Optional comments|
|`order_address_id`| Obtain with [Get Order Shipping Address](/api-reference/store-management/orders/order-shipping-addresses/getallshippingaddresses)|
|`shipping_provider`| Optional; used to create tracking link; see [Create Order Shipment](/api-reference/store-management/orders/order-shipments/createordershipments) for accepted values|
|`items.order_product_id`|Obtain with [Get Order Products](/api-reference/store-management/orders/order-products/getallorderproducts). For non-variant products, use the `id`.|

<!-- theme: info -->
> #### Note
> * Create multiple shipments by specifying a subset of products and quantities in each `POST` request.
> * Create an order shipment with product variants by using the `id` returned in each `GET` request.
> * Creating order shipments triggers email notifications; adjust [Order Notification](https://support.bigcommerce.com/s/article/Customer-Order-Notifications#enable) settings in the [control panel](https://login.bigcommerce.com/deep-links/manage) to change this behavior.
> * Deleting a shipment does **not** move the order out of `shipped` status.

## Shipping to multiple locations

You can create multiple shipments for orders, and each shipment can have a different `order_address_id`.

```http title="Example 1: order_address_id"
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}}/v2/orders/{{order_id}}/shipments
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "order_address_id": "123",
  "shipping_provider": "usps",
  "items": [
    {
      "order_product_id": 2,
      "quantity": 1
    }
  ]
}
```
&nbsp;
```http title="Example 2: different order_address_id"
POST https://api.bigcommerce.com/stores/{{STORE_HASH}}}/v2/orders/{{order_id}}/shipments
X-Auth-Token: {{ACCESS_TOKEN}}
Content-Type: application/json
Accept: application/json

{
  "order_address_id": "456",
  "shipping_provider": "",
  "items": [
    {
      "order_product_id": 5,
      "quantity": 1
    }
  ]
}
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/order-shipments/createordershipments#requestrunner) -->

|Property|Description|
|-|-|
|`order_address_id`|Obtain with [Get Order Shipping Address](/api-reference/store-management/orders/order-shipping-addresses/getallshippingaddresses)|
|`shipping_provider`| Optional; used to create tracking link; see [Create Order Shipment](/api-reference/store-management/orders/order-shipments/createordershipments) for accepted values|
|`items.order_product_id`|Obtain with [Get Order Products](/api-reference/store-management/orders/order-products/getallorderproducts). For non-variant products, use the `id`.|

## Getting shipping quotes

To [get shipping quotes](/api-reference/store-management/orders/order-shipping-addresses-quotes/getshippingquotes), send the following `GET` request. See the example response that follows, or consult the [response schema](/api-reference/store-management/orders/order-shipping-addresses-quotes/getshippingquotes#responses). 

```http title="Example request: Get shipping quotes" lineNumbers
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders/{{order_id}}/shipping_addresses/{{shipping_address_id}}/shipping_quotes
X-Auth-Token: {{ACCESS_TOKEN}}
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/order-shipping-addresses-quotes/getshippingquotes#requestrunner) -->

&nbsp;
```json title="Example response: Get shipping quotes" lineNumbers
{
  "id": "16",
  "uuid": "18aaa5eb-3c7a-4bf8-bfaa-d14d155606f1",
  "timestamp": "Mon, 30 Jul 2018 15:32:35 +0000",
  "shipping_provider_id": "bcproductbased",
  "shipping_provider_quote": [],
  "provider_code": "productfixedshipping",
  "carrier_code": "",
  "rate_code": "",
  "rate_id": ""
}
```

Generating a quote through a shipping carrier is not supported. You can specify a shipping carrier when creating an order shipment. You can generate the quote elsewhere, then update the `shipping_cost_ex_tax` and `shipping_cost_inc_tax` for the order total to be correct.

## Getting order taxes

To [get order taxes](/api-reference/store-management/orders/order-taxes/getordertaxes), send the following `GET` request. See the example response that follows, or consult the [response schema](/api-reference/store-management/orders/order-taxes/getordertaxes#responses). 

```http title="Example request: Get order taxes" lineNumbers
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v2/orders/{{order_id}}/taxes
X-Auth-Token: {{ACCESS_TOKEN}}
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/orders/order-taxes/getordertaxes#requestrunner) -->

&nbsp;
```json title="Example response: Get order taxes" lineNumbers
[
  {
    "id": 13,
    "order_id": 138,
    "order_address_id": 39,
    "tax_rate_id": 1,
    "tax_class_id": 0,
    "name": "Tax",
    "class": "Default Tax Class",
    "rate": "8.0000",
    "priority": 0,
    "priority_amount": "17.6400",
    "line_amount": "17.6400"
  }
]
```

The response's [order tax object](/api-reference/store-management/orders/order-taxes/getordertaxes) `name` property gets set to `API Tax Override` when generated by third-party tax services like [Avalara Premium](https://www.bigcommerce.com/apps/avalara-avatax/?search=avalara).

```json title="Example response detail: Tax object from get order taxes" lineNumbers
[
  {
    "id": 13,
    "order_id": 138,
    "order_address_id": 39,
    "tax_rate_id": 1,
    "tax_class_id": 0,
    "name": "API Tax Override",
    ...
  }
]
```

BigCommerce submits tax documents to Avalara when an order moves from an **unpaid** status to a **paid** status and voids tax documents when an order moves from a **paid status** to an unpaid status.

| Existing Status | Status Passed | Resultant Status | Avalara Tax Document Submission |
| - | - | - | - |
| Any | None | `Pending` | None |
| Paid or `Refunded` | Paid | Paid | None |
| Unpaid or `Refunded` | Unpaid | Unpaid | None |
| Paid or `Refunded` | Unpaid | Unpaid | Tax document voided |
| Unpaid or `Refunded` | Paid | Paid | Tax document submitted |

<!-- theme: info -->
> #### Note
> * Abbreviated state names (ex: `CA` instead of `California`) in an order address will cause tax document submission to fail.
> * You can calculate taxes using rules specified in the store unless [automatic taxes](https://support.bigcommerce.com/s/article/Automatic-Tax-Setup) are enabled.
> * You can optionally override tax values by specifying `price_inc_tax` and `price_ex_tax` in an [update order request](/api-reference/store-management/orders/orders/updateanorder).
> * If a store has [automatic tax](https://support.bigcommerce.com/s/article/Automatic-Tax-Setup) enabled, BigCommerce does not compute sales tax on orders created with the API.

## Getting order transactions

To [get order transactions](/api-reference/store-management/order-transactions/transactions/gettransactions), send the following `GET` request. See the example response that follows, or consult the [response schema](/api-reference/store-management/order-transactions/transactions/gettransactions#responses).

```http title="Example request: Get order transactions" lineNumbers
GET https://api.bigcommerce.com/stores/{{STORE_HASH}}/v3/orders/{{order_id}}/transactions
X-Auth-Token: {{ACCESS_TOKEN}}
Accept: application/json
```

<!-- [![Open in Request Runner](https://storage.googleapis.com/bigcommerce-production-dev-center/images/Open-Request-Runner.svg)](/api-reference/store-management/order-transactions/transactions/gettransactions#requestrunner) -->

&nbsp;
```json title="Example response: Get order transactions" lineNumbers
{
  "data": [
    {
      "id": 85926313,
      "order_id": "121",
      "event": "purchase",
      "method": "nonce",
      "amount": 1,
      "currency": "USD",
      "gateway": "squarev2",
      "gateway_transaction_id": "pN5Kd7R9ilEI2ygBawCy7tMF|qwnAFAxRZ7tYRtIpZULg1yMF",
      "status": "ok",
      "test": false,
      "fraud_review": false,
      "reference_transaction_id": {},
      "date_created": "2018-05-08T15:06:12+00:00",
      "avs_result": {...},
      "cvv_result": {...},
      "credit_card": {},
      "gift_certificate": {},
      "store_credit": {},
      "offline": {},
      "custom": {},
      "payment_instrument_token": {},
      "payment_method_id": "squarev2.card"
    }
  ],
  "meta": {...}
}
```

<!-- theme: info -->
> #### Note
> * Not all payment gateways return the full card or fraud detail. Depending on the payment method, different information will be available.



## Handling refunds

[Orders V3](/api-reference/store-management/order-transactions) exposes endpoints for managing [order refunds](/api-reference/store-management/order-transactions/order-refunds). For an overview on using these endpoints, see [Order Refunds in API Docs](/api-docs/orders/payment-actions).

## Calculating totals

Order `subtotal` and `total` calculate automatically; edits to the following properties trigger a recalculation.

|Property|Type|Description|
|-|-|-|
|`products`|`array[obj]`|Used to calculate shipping, taxes, and subtotal|
|`shipping_cost_ex_tax`|`float`|Shipping cost, excluding tax|
|`shipping_cost_inc_tax`|`float`|Shipping cost, including tax|
|`handling_cost_ex_tax`|`float`|Value of handling cost, excluding tax|
|`handling_cost_inc_tax`|`float`|Value of handling cost, including tax|
|`wrapping_cost_ex_tax`|`float`|Value of wrapping cost, excluding tax|
|`wrapping_cost_inc_tax`|`float`|Value of wrapping cost, including tax|
|`billing_address`|`obj`|Used to calculate shipping and taxes|
|`shipping_addresses`|`array[obj]`|Used to calculate shipping and taxes|

You can override calculated values such as product prices, subtotals, and totals by sending a fixed value in the request. If you do not supply values for these properties, you will automatically calculate them based on the preset store values and tax rules.

<!-- theme: info -->
> #### Note
> * If you override `subtotal` or `total`, override both; the system will not re-calculate the other.
> * To add a manual discount, overwrite the product price or `discount_amount`.

## FAQ

**Is adding coupons available?**

Coupon redemption is unavailable. You can not write to the `coupon_discount` field. You can add a discount to the order by using the `discount_amount`.

**How do I create an order for a guest?**

To specify a guest checkout, set `customer_id` to 0.

**How do I set the order source?**

You cannot specify the `order_source`; its value is external. You can optionally specify a value for `external_source` to define which external source the order is coming from - e.g., POS system X, accounting system Y, etc.

**Can I create an order with only custom products?**

Yes, the store's catalog does not include products.

**What is the difference between country_ISO2 and country?**

There is no requirement to specify country when you specify `country_ISO2` in the shipping and billing addresses and vice versa.

**How can I take payment for an order?**

You can either process payment through a third party or using the control panel.

**Can I generate a shipping quote from a carrier using the API?**

Not at this time. If you create an order either in the control panel or by API, it will return a 204 when trying to get a shipping quote.

## Related resources

### Articles

* [Payments API Overview](/api-docs/payments/payments-api-overview)
* [Order Refunds](/api-docs/orders/payment-actions)
* [Order Statuses](https://support.bigcommerce.com/s/article/Order-Statuses)
* [Order Notifications](https://support.bigcommerce.com/s/article/Customer-Order-Notifications)

### Endpoints

* [Storefront Orders](/api-reference/cart-checkout/storefront-orders)
* [Orders v2](/api-reference/store-management/orders)
* [Orders v3](/api-reference/store-management/order-transactions)

### Webhooks

* [Orders](/api-docs/store-management/webhooks/events#orders)
