# BeanBean

One click deploy for the web and api services needed to host a BitClout node.

# Deployment

## Prerequisits
- An Azure Subscription
    - This is to handle hosting billing. "Basic" tier is fine as Azure support isn't necessary for this.
    - https://signup.azure.com/
- A custom domain
    - Any registrar will do as long as you have access to update the DNS Records.

## Deploy to Azure
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FJaysonDeMarchi%2FBeanBean%2Fmain%2Fazure-templates%2Fazuredeploy.json)

## Post Deploy Steps

Once successfully deployed Azure will have created three services:
- The web app - The frontend. This is the app your end users will access directly via your custom domain.
- The api app - The core/backend.
- The app service plan - The hardware configuration the above two apps are running on. This is the service that will affect billing and you will look to scale depending on traffic. By default this is set to Premium v3.

At the time of writing, a custom domain name is required to deploy with Azure. This may not be the case in the future.

### Configure the custom DNS

1. Open App Services - You can use the search bar at the top of Azure.
    https://share.getcloudapp.com/8LuoBYod
2. Open the '*-api' service
    https://share.getcloudapp.com/NQuokpYX
3. Go to 'Custom Domains'
    https://share.getcloudapp.com/9ZudlyRA
4. Click '+ Add custom domain'
    https://share.getcloudapp.com/4gunQXZ0
5. Enter the api url for your custom domain - This should be 'api.' + your domain. For example our domain is bitcloutpulse.com, so our api domain is api.bitcloutpulse.com
    https://share.getcloudapp.com/Z4uKdG75
6. Click 'Validate'
7. Add the DNS records to your registrar - This will require adding the provided CNAME and TXT records to verify ownership. https://dnschecker.org/#CNAME/api.bitcloutpulse.dev
    https://share.getcloudapp.com/4gunQXeJ
8. Click 'Add custom domain' - You may have to wait for the Hostname availability, and domain ownership checks to pass. Domain propegation takes a few minutes after updating the DNS record. A way to check is to use dnschecker for the CNAME and TXT records. 
    https://share.getcloudapp.com/2NuqYp9G
9. Go to 'TLS/SSL settings' - We will need the SSL binding to access the domain using https.
    https://share.getcloudapp.com/7KuoeWrr
10. Click 'Private Key Certificates (.pfx)'
    https://share.getcloudapp.com/RBul5Jp9
11. Click 'Create App Service Managed Certificate'
    https://share.getcloudapp.com/E0uA5LQL
12. Click 'Create' - This is a slow process so expect this step to take a few minutes.
    https://share.getcloudapp.com/eDuyQEOA
13. Go back to 'Custom Domains'
    https://share.getcloudapp.com/9ZudlyRA
14. Click 'Add binding' on our custom domain
    https://share.getcloudapp.com/P8u9xXrQ
15. Select our custom domain in the dropdown, our newly created SSL Cert, and SNI SSL.
16. Click 'Add binding'
    https://share.getcloudapp.com/bLukzZ46 
17. Repeat steps 2 - 16 for the '*-web' service. This service is the front facing site users will access, so for step 5 the domain will just be your domain (e.g. in our example in step 5 we would just use bitcloutpulse.com).

# Development

## Build Phase
The issue we'll face on any hosting platform is they require a published image to deploy with Docker. Our build phase here takes the base BitClout images for their frontend, core, and nginx config, then templates out their URLs to environment variables.

```bash
# Builds our images based on the content within build/
# This is just running docker compose -f ./build/docker-compose.yaml build under the hood
./bin/build
```

## Deploy Phase
Deploys our wrapper images with the associated environment variables. This relies on the docker images built from the build phasae. Ideally a client just has to modify the env variables if they wanted to spin up their own instance in Azure and shouldn't need to modify the actual compose files.