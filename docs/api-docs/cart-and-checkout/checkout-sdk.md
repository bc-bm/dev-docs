#  Checkout SDK

The Checkout JS SDK is a wrapper for the BigCommerce Storefront Checkout API.

It allows a developer to create a custom checkout experience to move a customer through the checkout process.
The SDK handles all the ‘heavy-lifting’ such as customer login, getting shipping quotes, and submitting payment for an order.
With the Checkout JS SDK, you can build a [custom checkout presentation](https://github.com/bigcommerce/checkout-js) layer in popular front-end frameworks such as React.

## What can I do with the SDK?

-   You can initialize payment and shipping providers that require client-side setup through a common interface. Below are some examples of these providers:

    -   [Amazon](#required-payment-method-initialization-options)
    -   Braintree
    -   [PayPal](#required-payment-method-initialization-options)
    -   Square
    -   Stripe

 

-   You can use its JavaScript interface for interacting with the web API.

    -   Fetch and submit resources:
        -   Cart
        -   Customer 
        -   Order

    -   Fetch available options:
        -   Billing addresses
        -   Billing countries
        -   Billing states
        -   Payment methods 
        -   Shipping addresses
        -   Shipping countries
        -   Shipping methods 
        -   Shipping states
      

 

The Checkout JS SDK allows you to present the checkout process to the shopper in any way you like and, through front-end technologies and external web services, augment the checkout with extra information.

However, the Checkout JS SDK does not allow you to change the checkout's underpinnings. You must still conform to the SDK's model of the Checkout API to complete a checkout and create an order in BigCommerce.

The SDK does not allow you to implement custom payment, shipping, or tax calculation providers into the checkout. Instead, you can use the configured providers for these services on a given store using our Checkout API.

### Required payment method initialization options 
Some payment methods, like PayPal, Amazon, etc., require you to provide additional initialization options. Amazon requires a container ID to initialize its payment widget. The PayPal method requires specific options to initialize the PayPal Smart Payment button on the checkout page that substitutes a standard submit button. For more details on adjusting your code to make it work with the PayPal method, see the [BigCommerce SDK Repo](https://github.com/bigcommerce/checkout-sdk-js/blob/master/docs/interfaces/PaypalCommercePaymentInitializeOptions.md).

![Checkout Button](https://raw.githubusercontent.com/bigcommerce/dev-docs/master/assets/images/checkout-sdk-01.png "Checkout Button")

## Where can I get the SDK?
The Checkout JS SDK and associated documentation is available from the [BigCommerce SDK Repo.](https://github.com/bigcommerce/checkout-sdk-js)

## How can I update the SDK?
The following steps describe how to update the Checkout SDK package in your existing custom checkout implementation.

1. In your terminal, navigate to the project root of your customer checkout implementation. You can update the SDK in one of the two following ways:

* Run `npm install @bigcommerce/checkout-sdk@<latest-version-number>` in the terminal; or 

* Update the version number in your checkout implementation's package.json file, then run `npm install` in the terminal.
 
<!-- theme: info -->
> #### Note 
> See the checkout-sdk-js repo's [changelog.md](https://github.com/bigcommerce/checkout-sdk-js/blob/master/CHANGELOG.md) for checkout-sdk version information.

2. Test the updated SDK.

## Support and customization
- Enterprise clients can reach out to their account manager to review services and resources available.
- For more on our design policy, please visit [Design Support](https://support.bigcommerce.com/s/article/BigCommerce-Design-Policy#support).
