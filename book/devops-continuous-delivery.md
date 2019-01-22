## Deploy using a Continuous Delivery Pipeline

In this tutorial, you create an open toolchain and use it to deploy the web app, package it into a Docker container, and then deploy the app to a Kubernetes cluster using Helm charts.

IBM Cloud Continuous Delivery supports Helm functions in the Delivery Pipeline. 

The toolchain used for this tutorial is configured out of the box to perform sanity checks before build or deploy activities, and manages development privacy by using a private image registry and configurable namespaces (both in the container registry and Kubernetes cluster).

The toolchain includes:

* Git Repos and Issue Tracking for source control and issue tracking.
* Eclipse Orion Web IDE for online editing.
* Vulnerability Advisor to ensure the container we're deploying is secure.
* Deployment to IBM Cloud Kubernetes with Helm.

# Create a Toolchain

1. Open the creation page for the toolchain by clicking [Create toolchain](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/lionelmace/mytodo&branch=master)

1. The diagram of the toolchain shows each tool integration in its lifecycle phase in the toolchain.

    ![](./images/toolchain-landing.png)

1. Review the default information for the toolchain settings. Select the region **Frankfurt** and the Resource Group **lab**.

1. Click Delivery Pipeline.

    * **App name**: Enter the name for your application. The app name must be unique (if you want, you can just use the default value provided).

    * **Container Registry Region**: Select the region you want the docker images to be created into.

    * **Container Registry Namespace**: This is your folder in the global image registry in a region, used to manage your own set of images. It must be a unique name. In this lab, we use **lab_registry**.

    * **IBM Cloud API Key**: Create a new IBM Cloud API Key.

    * **Cluster Region**: Select the region for the target cluster. 

    * **Cluster Name**: The name of the Kubernetes  cluster that you got assigned. Exemple: `lab-cluster-1`

    * **Cluster Namespace**: It is a best practice to use explicit namespaces in clusters to separate deployed resources. For instance, we use ‘prod’ to denote a prod environment within this cluster. Later in the tutorial, we will add a ‘staging’ environment within the same cluster. You can leave this setting at its default 'prod' value.

1. Click Create. After a few moments, your new toolchain's Overview page opens.

    ![](./images/toolchain-created.png)

# Explore Delivery Pipeline in your toolchain



1. Once the toolchain has completed, the application will be available at
    `https://todo.<your-cluster-ingress-domain>`.

The toolchain includes a stage named **UNINSTALL (manual)**. This stage can only be triggered manually and will remove all resources created by the toolchain (app and services).
