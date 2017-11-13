JSV4 Postman
------------
<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
***Contents***

- [Overview](#overview)
- [Requirements](#requirements)
- [Installation and Configuration](#installation-and-configuration)
  - [Download or clone this repository to your local machine](#download-or-clone-this-repository-to-your-local-machine)
    - [Download as Zip](#download-as-zip)
    - [Clone the Project](#clone-the-project)
  - [Add Your Client ID to your HTML/Javascript Client Page](#add-your-client-id-to-your-htmljavascript-client-page)
  - [Setup and Configure Postman Collection](#setup-and-configure-postman-collection)
    - [Import the Postman Collection](#import-the-postman-collection)
    - [Create an Environment](#create-an-environment)
    - [Add Client ID and Secrent](#add-client-id-and-secrent)
- [Perform the Checkout Flow as a Customer](#perform-the-checkout-flow-as-a-customer)
- [Opereate the REST Calls Using Postman](#opereate-the-rest-calls-using-postman)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->
# Overview #
This project provides an example checkout.js implementation (JSv4) for authorization with a postman collection used as execution and capture.

* An .html file provides the client web page for customer authorization and presentation of the incontext checkout flow.
* A Postman collection provides the structure for using Postman as a REST client for performing the remaining operations of execute, authorization, capture, etc.

# Requirements #

* A currently JSv4 supported browser  to view your JSv4 enabled webpage.
* [Postman](https://www.getpostman.com) API client for making REST calls.
* PayPal Sandbox "REST API App" setup and configured.
* A text editor such as a Notepad or [Notpad++](https://notepad-plus-plus.org) for windows, [SublimeText](https://www.sublimetext.com), [BBEdit](https://www.barebones.com/products/bbedit/) for Mac -- to make some minor edits to your JSv4 enabled webpage.

# Installation and Configuration #

We will need to get a copy of this code to your local machine in order to make some slight changes, and run the Postman REST calls.  Once we have the files local, we will add our credentials to both the .html file and Postman in order to authenticate with the PayPal Sandbox environment.

## Download or clone this repository to your local machine ##
You can either download the entire contents of this project to your local machine or clone the repository using Git.

### Download as Zip ###
On the main project page of this github repository, you can use the "Download Zip" feature provided by GitHub.  From the main page, you can see the download link by hovering over the "Clone or Download" button.  The following image is an example of the "Download ZIP" link for this project:

![Download ZIP](images/git-download-zip.png?raw=true "Download ZIP")

Once downloaded, unzip the contents into the directory of your choice.

### Clone the Project ###
Using GIT to access and copy the project is the preferred method of downloading files to your local machine.  [MORE ON THIS TO COMNE]

## Add Your Client ID to your HTML/Javascript Client Page

The PayPal Sandbox "Client ID" is used to identify the merchant and perform key security operations required to operate the JSv4 .html client.  Edit the file [chekout-js.html line 72](checkout-js.html#L72) and add your Client ID into the appropriate attribute of the code.  Specifically, put your Client ID into to single quotes in place of ```<insert sandbox client id>``` :

 ```javascript
        env: 'sandbox', // sandbox | production
        client: {
            sandbox: '<insert sandbox client id>',
            production: '<insert production client id>'
        },

        locale: 'en_US',
 ```

This page provides more details regarding the Client ID location in the [developer.paypal.com](https://developer.paypal.com) console.

## Setup and Configure Postman Collection ##

### Import the Postman Collection ###
Launch Postman.  Once in the application, you can "import" a new collection of API calls to use for the purposes of our example.  Specifically:

1. Click the import button in the upper left of the Postman application.
1. Once the import modal comes up, select the "Import File >> Choose Files" option and select the file in this project [postman-paypal-rest-basic-collection.json](postman-paypal-rest-basic-collection.json)

![Import Collection](images/postman-import.png?raw=true "Import Collection")

You should have an imported collection that looks like the following:

![Imported Collection](images/postman-imported.png?raw=true "Imported Collection")

### Create an Environment ###
You will need to create an "environment" within Postman to hold oAuth tokens and other items behind the scenes.  Select the 'gear' looking icon in the upper right and choose the option "Manage Environments".  A modal appears where you can "Add" a new environment.  Enter an appropriate name, and close the modal:

![Postman Environment](images/postman-environment.png?raw=true "Postman Environment")


### Add Client ID and Secrent ###
We need to add our credentials to the imported Postman collection above in order to authenticate against the PayPal oAuth system.  Within the new Postman collection, double-click/open the item "OAuth Token Request - Sandbox".  Here, select the "Authorization" subtab where you will be presented with a screen to enter auth credentials.  In our case (these should already be selected) we will be using:

* Type: 'Basic Auth'
* User:  [Your REST API App 'Client ID']
* Password:  [Your REST API App 'Secret']

Enter the the selections accordingly, and click "Send".  You should see a response as in the following:

![Postman Auth Setup](images/postman-authentication.png?raw=true "Postman Auth Setup")

NOW, you should be ready to run the demo!!!

# Perform the Checkout Flow as a Customer

Open the checkout-js.html file in a supported broswer.  This page has been configured to complete the REST equivalent of SetExpressCheckout" in classic nomenclature.  The page should appear with some JSON and the PayPal Checkout button appearing at the bottom:

![Checkout](images/checkout-js.png?raw=true "Checkout")

Complete the checkout flow (assuring our Client ID has been inserted into the appropriate spot in [checkout-js.html](checkout-js.html)) resulting in a page similar to the following:

![Checkout Complete](images/checkout-completed.png?raw=true "Checkout Complete")

Note the **ID** and **payer_id** fields in particular.

# Opereate the REST Calls Using Postman

Now, we are ready to complete the "execute" and "capture" calls of the flow.






