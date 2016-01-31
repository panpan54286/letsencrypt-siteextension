# Let's Encrypt Site Extension
[![Build status](https://ci.appveyor.com/api/projects/status/mdg0e31rp0qdu16m/branch/master?svg=true
)](https://ci.appveyor.com/project/sjkp/letsencrypt-siteextension)

This Azure Web App Site Extension enables easy installation and configuration of [Let's Encrypt](https://letsencrypt.org/) issued SSL certifcates for you custom domain names. 

The site extension requires that you have configured a DNS entry for you custom domain to point to Azure Web App. 
*Note with the fully automated mode, you don't even have to configure the custom domain in Azure the extension will do that for you.*

## Step by Step Guide by Nik Molnar
https://gooroo.io/GoorooTHINK/Article/16420/Lets-Encrypt-Azure-Web-Apps-the-Free-and-Easy-Way/ 

## Semi-Automated Installation
With the semi-automated installation you manually add the site extension to your web app. Open the extension and manually click through the 3 step dialog. 

Once this process is complete your custom domain for the site is setup with a Let's Encrypt issued SSL certificate. 

## Fully-Automated Installation
If you setup your site with an Azure Resource Manager template, and want to configure SSL as part of this process you are currently out of luck as ARM templates doesn't support custom hostnames. 

The extension removes this limitations by automatially setting up the custom hostname and requesting and configuring a Let's Encrypt certificate.

To use the Fully Automated Installtion the following Web App settings must be added. 

| Key |	Value
|-----| ----
| letsencrypt:Tenant |	The tenant name e.g. myazuretenant.onmicrosoft.com
| letsencrypt:SubscriptionId |	The subscription id
| letsencrypt:ClientId	| The value of the clientid of the service principal
| letsencrypt:ClientSecret	| The secret for the service principal
| letsencrypt:ResourceGroupName |	The name of the resource group this web app belongs to
| letsencrypt:ServicePlanResourceGroupName |	The name of the resource group with the app service plan that hosts the web app, if the app service plan is in the same plan as the web app, then this property is optional. 
| letsencrypt:AcmeBaseUri |	The url to Let's Encrypt servers e.g. https://acme-v01.api.letsencrypt.org/ or https://acme-staging.api.letsencrypt.org/ (defaults to this)
| letsencrypt:Email	| The Email used for registering with Let's Encrypt
| letsencrypt:Hostnames |	Comma separated list of custom hostnames (externally hosted setup with CNames), that should automatically be configured for the site.

As it can be seen from the list of App Settings a service principal is needed. The service principal must be assigned permissions to the web app, that is required as the extension use it for installing and updating the certificate. (If two resource groups are used, the app service principal must have access to both). 

Besides the App Settings, the two Azure Web Job required connection strings ```AzureWebJobsStorage``` and ```AzureWebJobsDashboard``` must also exists, as the extension relies on an internal Web Job to renew the certificates once they expire. 

To see an example of an ARM template installation look at [azuredeploy.json](LetsEncrypt.ResourceGroup/Templates/azuredeploy.json)
