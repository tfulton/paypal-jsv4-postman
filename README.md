JSV4 Postman
------------
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
***Contents***

- [Overview](#overview)
- [Requirements](#requirements)
- [Installation and Configuration](#installation-and-configuration)
  - [Download or clone this repository to your local machine](#download-or-clone-this-repository-to-your-local-machine)
  - [Add Your Client ID to the HTML/Javascript Client Page](#add-your-client-id-to-the-htmljavascript-client-page)
  - [Setup and Configure Postman Collection](#setup-and-configure-postman-collection)
- [Checkout Using JSv4 Enabled Web Page](#checkout-using-jsv4-enabled-web-page)
- [Using Postman for Execute, Auth, Capture and other REST Calls](#using-postman-for-execute-auth-capture-and-other-rest-calls)
  - [Authenticate via oAuth](#authenticate-via-oauth)
  - [Execute the Payment](#execute-the-payment)
  - [Authorize the Order](#authorize-the-order)
  - [Capture the Authorization](#capture-the-authorization)
- [Summary](#summary)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# Overview #
This project provides an example checkout.js implementation (JSv4) for an 'AS2' transaction flow.  A JSv4 enabled web page provides the customer checkout experience; Postman is used for execution, auth and capture.

# Requirements #

* PayPal Sandbox "REST API App" setup and configured.
* Any standard JSv4 supported browser to view your JSv4 enabled webpage.
* [Postman](https://www.getpostman.com) API client for making REST calls.
* A text editor such as a Notepad or [Notpad++](https://notepad-plus-plus.org) for windows, [SublimeText](https://www.sublimetext.com), [BBEdit](https://www.barebones.com/products/bbedit/) for Mac -- to make some minor edits to your JSv4 enabled webpage.

# Installation and Configuration #

## Download or clone this repository to your local machine ##
We will need to get a local copy of this code in order to make some small changes.  We will add our credentials to both the .html file **and** Postman in order to authenticate with the PayPal Sandbox environment.  You can either download a .zip or clone the repository using Git.  More detailed instructions can be found [here](./GitSetup.md#clone-or-download-project).

## Add Your Client ID to the HTML/Javascript Client Page

The PayPal Sandbox "Client ID" is used to identify the merchant and perform key security operations required by the JSv4 client.  Edit the file [chekout-js.html line 72](checkout-js.html#L72) and add your Client ID into the 'client.sandbox' attribute of the code.  Specifically, put your Client ID in place of ```<insert sandbox client id>``` :

 ```js
        env: 'sandbox', // sandbox | production
        client: {
            sandbox: '<insert sandbox client id>',
            production: '<insert production client id>'
        },

        locale: 'en_US',
 ```

More details on REST API Apps and the Client ID location in the [developer.paypal.com](https://developer.paypal.com) console can be found [here](./PPRestApp.md).

## Setup and Configure Postman Collection ##

### Import the Postman Collection ###
Launch Postman.  Once in the application, you can "import" a new collection of API calls for our example.  Specifically:

1. Click the import button in the upper left of the Postman application.
1. Once the import modal comes up, select the "Import File >> Choose Files" option and select the file in this project [postman-paypal-rest-basic-collection.json](postman-paypal-rest-basic-collection.json)

![Import Collection](images/postman-import.png?raw=true "Import Collection")

You should have an imported collection that looks like the following:

![Imported Collection](images/postman-imported.png?raw=true "Imported Collection")

### Create an Environment ###
Next, create an 'environment' within Postman to hold oAuth tokens and other items behind the scenes.  Select the 'gear' icon in the upper right and choose the option "Manage Environments".  A modal appears where you can "Add" a new environment.  Enter an appropriate name, and close the modal:

![Postman Environment](images/postman-environment.png?raw=true "Postman Environment")


### Add Client ID and Secret ###
Add our credentials to the imported Postman collection above in order to authenticate against the PayPal oAuth system.  Within the new Postman collection, open the "OAuth Token Request - Sandbox" operation.  Select the 'Authorization' tab where you will be presented with a screen to enter credentials.  These should already be selected, and we will be using:

* Type: 'Basic Auth'
* User:  [REST API App 'Client ID']
* Password:  [REST API App 'Secret']

Enter the the selections accordingly, and click "Send".  You should see a response similar to the following:

![Postman Auth Setup](images/postman-authentication.png?raw=true "Postman Auth Setup")

Next, let's run the demo!!!

# Checkout Using JSv4 Enabled Web Page

Open the checkout-js.html file in a supported browser (assuring our Client ID has been inserted into the appropriate attribute in [checkout-js.html](checkout-js.html)).  This page has been configured to complete the REST equivalent of SetExpressCheckout" in classic nomenclature.  We should see some JSON and the PayPal Checkout button:

![Checkout](images/checkout-js.png?raw=true "Checkout")

Complete the checkout flow resulting in a page similar to the following.  Note the **ID** (PAY-ID) and **payer_id** (PAYER-ID) fields in particular in the response:

![Checkout Complete](images/checkout-completed.png?raw=true "Checkout Complete")


# Using Postman for Execute, Auth, Capture and other REST Calls
Now, we are ready to complete the "execute" and "capture" calls of the flow.  The API calls we make use REST URI and request body JSON as per the docs: [Payments API reference documentation](https://developer.paypal.com/docs/api/payments/).  Use these documents as your base reference moving forward.

NEXT:  open up Postman and navigate to the imported collection.

## Authenticate via oAuth
Authentication for PayPal REST uses the oAuth authentication protocol.  "Send" the "OAuth Token Request - Sandbox" operation as described in [Setup and Configure Postman Collection](setup-and-configure-postman-collection).

You should see the access token in the response body as shown below.  The 'access_token' is saved automatically to your environment as indicated in the documentation [Setup and Configure Postman Collection](setup-and-configure-postman-collection):

```json
{
    "scope": "https://uri.paypal.com/services/subscriptions https://api.paypal.com/v1/payments/.* https://api.paypal.com/v1/vault/credit-card https://uri.paypal.com/services/applications/webhooks openid https://uri.paypal.com/payments/payouts https://api.paypal.com/v1/vault/credit-card/.*",
    "nonce": "2017-11-13T19:37:55Z2av4hmyP3dQSIrlNf1PrCgXZW15M3CXxJVGD4ys-_pU",
    "access_token": "A21AAHWgqKNpKLYkiUxjEIOcvJ2CR7MBlfd7QBYyoaim7vKFLc_DgnLC7gqzM6w9IddEiDyysfPhuOP7LVRYwNs4LAAiBx8TQ",
    "token_type": "Bearer",
    "app_id": "APP-80W284485P519543T",
    "expires_in": 32384
}
```

## Execute the Payment
Use the 'Execute Payment' operation to complete the customer approval of the order.  The PAY-ID and 'payer_id' from the checkout flow are used to execute the payment:  See the corresponding highlighted areas in the image following:

1. Open the 'Execute Payment' operation in the left hand navigation pane.
1. Build the [execute URI](https://developer.paypal.com/docs/api/payments/#payment_execute).  Replace the {payment_id} URI parameter with your PAY-ID. (See depiction below)
1. Build the JSON request body.  In the Postman collection example, you can use the existing JSON and replace the 'payer_id' attribute with your PAYER-ID.  (See depiction below)

![Execute](images/postman-execute.png?raw=true "Execute")

** Response **
If successful, your response will contain a JSON payload similar to below.  Use 'transactions.related_resources.order.id' to authorize your order in the next section.  Additionally, you can use the HATEOS links provided in the response payload ('transactions.related_resources.order.links') to help build your authorization URI later:

```json
{
    "id": "PAY-3W249660WW2717409LIFDEQA",
    "intent": "order",
    "state": "approved",
    "cart": "9S281710AK5473645",
    "payer": {
        "payment_method": "paypal",
        "status": "VERIFIED",
        "payer_info": {
            "email": "tofulton-buyer@paypal.com",
            "first_name": "test",
            "last_name": "buyer",
            "payer_id": "VTW5PBHNTV24A",
            "shipping_address": {
                "recipient_name": "test buyer",
                "line1": "400 Alabama Street",
                "line2": "Suite 200",
                "city": "San Francisco",
                "state": "CA",
                "postal_code": "94112",
                "country_code": "US"
            },
            "country_code": "US"
        }
    },
    "transactions": [
        {
            "amount": {
                "total": "10.01",
                "currency": "USD",
                "details": {}
            },
            "payee": {
                "merchant_id": "U3W86TPYJE8D6"
            },
            "item_list": {
                "shipping_address": {
                    "recipient_name": "test buyer",
                    "line1": "400 Alabama Street",
                    "line2": "Suite 200",
                    "city": "San Francisco",
                    "state": "CA",
                    "postal_code": "94112",
                    "country_code": "US"
                }
            },
            "related_resources": [
                {
                    "order": {
                        "id": "O-81T919910A434173N",
                        "create_time": "2017-11-14T00:14:06Z",
                        "update_time": "2017-11-14T00:14:06Z",
                        "amount": {
                            "total": "10.01",
                            "currency": "USD",
                            "details": {}
                        },
                        "state": "PENDING",
                        "reason_code": "ORDER",
                        "links": [
                            {
                                "href": "https://api.sandbox.paypal.com/v1/payments/orders/O-81T919910A434173N",
                                "rel": "self",
                                "method": "GET"
                            },
                            {
                                "href": "https://api.sandbox.paypal.com/v1/payments/payment/PAY-3W249660WW2717409LIFDEQA",
                                "rel": "parent_payment",
                                "method": "GET"
                            },
                            {
                                "href": "https://api.sandbox.paypal.com/v1/payments/orders/O-81T919910A434173N/do-void",
                                "rel": "void",
                                "method": "POST"
                            },
                            {
                                "href": "https://api.sandbox.paypal.com/v1/payments/orders/O-81T919910A434173N/authorize",
                                "rel": "authorization",
                                "method": "POST"
                            },
                            {
                                "href": "https://api.sandbox.paypal.com/v1/payments/orders/O-81T919910A434173N/capture",
                                "rel": "capture",
                                "method": "POST"
                            }
                        ],
                        "parent_payment": "PAY-3W249660WW2717409LIFDEQA"
                    }
                }
            ]
        }
    ],
...
}
```


## Authorize the Order
We next build our 'authorize order' call.  Specifically:

1. Use 'Auth Order' operation in the left hand navigation pane.
1. Build the [authorize order URI](https://developer.paypal.com/docs/api/payments/#order_authorize).  Replace the {order_id} URI parameter with your order.id from the 'execute' call above. You can also use the HATEOS 'authorization' link provided in the execute response body above.
1. Build the JSON request body.  In the Postman collection example, you can use the existing JSON and replace the 'amount' related values as appropriate.

** Response **
If successful, your authorization response will contain a JSON payload similar to below:

```json
{
    "id": "2AU82319EU0433436",
    "create_time": "2017-11-14T00:53:55Z",
    "update_time": "2017-11-14T00:54:00Z",
    "amount": {
        "total": "3.00",
        "currency": "USD"
    },
    "state": "authorized",
    "protection_eligibility": "INELIGIBLE",
    "parent_payment": "PAY-3W249660WW2717409LIFDEQA",
    "links": [
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/orders/O-81T919910A434173N",
            "rel": "order",
            "method": "GET"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/authorization/2AU82319EU0433436/reauthorize",
            "rel": "reauthorize",
            "method": "POST"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/authorization/2AU82319EU0433436",
            "rel": "self",
            "method": "GET"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/authorization/2AU82319EU0433436/capture",
            "rel": "capture",
            "method": "POST"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/authorization/2AU82319EU0433436/void",
            "rel": "void",
            "method": "POST"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/payment/PAY-3W249660WW2717409LIFDEQA",
            "rel": "parent_payment",
            "method": "GET"
        }
    ]
}
```

## Capture the Authorization
We are now ready to capture the authorized order.  Using the authorization id or HATEOS capture link from the authorization call above, build our capture call.  Specifically:

1. Use the 'Capture Auth' operation in the left hand navigation pane.
1. Build the [capture URI](https://developer.paypal.com/docs/api/payments/#authorization_capture).  Replace {authorization_id} from the authorize order call above.  You can also use the HATEOS 'capture' link provided in the 'authorization' response body above.
1. Build the JSON request body.  In the Postman collection example, you can use the existing JSON and replace the 'amount' and other values as appropriate.

** Response **
If successful, your capture response will contain a JSON payload similar to below:

```json
{
    "id": "13U262351W371525Y",
    "create_time": "2017-11-14T01:14:29Z",
    "update_time": "2017-11-14T01:14:34Z",
    "amount": {
        "total": "3.00",
        "currency": "USD"
    },
    "is_final_capture": false,
    "state": "completed",
    "reason_code": "None",
    "parent_payment": "PAY-3W249660WW2717409LIFDEQA",
    "transaction_fee": {
        "value": "0.39",
        "currency": "USD"
    },
    "links": [
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/capture/13U262351W371525Y",
            "rel": "self",
            "method": "GET"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/capture/13U262351W371525Y/refund",
            "rel": "refund",
            "method": "POST"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/authorization/2AU82319EU0433436",
            "rel": "authorization",
            "method": "GET"
        },
        {
            "href": "https://api.sandbox.paypal.com/v1/payments/payment/PAY-3W249660WW2717409LIFDEQA",
            "rel": "parent_payment",
            "method": "GET"
        }
    ]
}
```


# Summary #
In this project, we should have gone through a simple JSv4 client checkout flow.  Using Postman also operated a number of REST API calls to execute, authorize and capture the original order.  The operations give us an insight into how a (straightforward) AS2 transaction flow moves data from the client and server side components.


