# Amplify Central Custom API Subscription Flow CRM Contact Exists Use Case

This API Builder project implements the Amplify Central Custom API Subscription Flow CRM Contact Exists Use Case described [here](https://blog.axway.com/developer/amplify-central-connected-gateway-custom-api-subscription-flow-with-crm-use-case).

* There are two flows in the project:
  * Approval Flow - Main flow that runs on a timer and retrieves a list of Subscription that are in the REQUEST state
  * Subscriptions Checker - Flow that is invoked from the main flow in a loop over the list of subscriptions. It checks to see if the subscriber is in a whitelist or is a contact in Salesforce.
    * If the subscriber is part of the white list, then the subscription is approved
    * If the subscriber is in Salesforce, then the subscription is approved and a note is added to the contact
    * If neither, then a notification is sent to MS Teams to alert the approval team as shown below. If the approval team adds the subscriber to Salesforce. Then on the next iteration of the main flow, the subscription will be approved and the agents will complete the approval process.

    ![](https://i.imgur.com/6p0OOc5.png)


* Here are the major aspects of the flows:
  * The API Builder swagger flow node and the [Amplify Unified Catalog API](http://apidocs.axway.com/swagger-ui-NEW/index.html?productname=UnifiedCatalog&productversion=1.0.0&filename=swagger.json) swagger are used to create a connector to Amplify Central/Unified Catalog for checking for pending subscriptions
  * Since the current Amplify Platform API swagger is not supported in API Builder, I am using the API Builder REST flow node to make a Platform API call to retrieve the subscriber user details (name and email). I am using the access_token retrieved for the Unified Catalog API for my Platform API call
  * I am using Salesforce REST APIs and the API Builder REST flow node to integrate with Salesforce


* The following Environment variables are used in the project:
  * API_CENTRAL_URL - Base address for Amplify Central (e.g. https://apicentral.axway.com)
  * CLIENT_ID - Amplify Service account client id
  * CLIENT_SECRET - Amplify Service account client secret
  * SFDC_DOMAIN_NAME - Your Salesforce Sales Cloud domain name (for REST API calls). For example, d37000000i55oeac-dev-ed for my Salesforce instance at https://d37000000i55oeac-dev-ed.my.salesforce.com/
  * SFDC_CLIENT_ID - Your Salesforce connected app consumer key
  * SFDC_CLIENT_SECRET - Your Salesforce connected app consumer secret
  * SFDC_USERNAME - Your Salesforce username
  * SFDC_PASSWORD - Your Salesforce password
  * SFDC_SECRET - Your Salesforce secret
  * WHITELIST - an array of email domains that you want to allow to be auto subscribed (e.g. [axway.com, appcelerator.com])
  * MS_TEAMS_WEBHOOK_URL - URL of the MS Teams incoming webhook connector for the channel that the notifications should be sent to
  * INTERVAL - timer interval in mS for how often you want to check Amplify for pending subscription requests


* You can read about creating an Amplify Service Account and retrieving your client id and secret [here](https://blog.axway.com/apis/axway-amplify-platform-api-calls).


* You can read about Salesforce REST APIs and how to create a connected app [here](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm). In addition, you need to modify two OAuth Policies from their default state: Permitted Users and IP Relaxation.
  * In the Administer section click on Manage Apps -> Connected Apps and Edit the Connected App you created and make the following modifications in the OAuth Policies section:
    * Change Permitted Users to All users may self-authorize
    * Change IP Relaxation to Relax IP restrictions


* To use this project simply setup your Salesforce connected account and Amplify service account and collect all the environment variables and run the API Builder project. It will check the subscriptions based on the timer interval. Set the timer properly to avoid spamming MS Teams with the same subscription notifications. For example, add env variables to the `/conf/.env` file and run the project using `npm start`
