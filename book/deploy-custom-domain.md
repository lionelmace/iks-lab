# Use a custom domain to access your app.

In the step The initial version of the application uses an in-memory database to store the todo. Yet, the web application can be configured to store the todo in either a Cloudant or a Mongo DBaaS.
We will use a Cloudant DB to demonstrate how to connnect the web app to the  Cloudant service using the Kubernetes Secret.

## Create an instance of Cloudant and its credentials

1. Go to the [Cloud Services Catalog](https://cloud.ibm.com/catalog) 

1. Select the service **Cloudant**