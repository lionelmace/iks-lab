# Install the Kubernetes Service and Container Registry plugins

`ibmcloud` is the command line tool to interact with IBM Cloud. It comes with plugins to work with IBM Cloud services. For the lab, we need two plugins **Kubernetes Service** and **Container Registry**.

1. Open a command line utility.

1. Before installing any plugin, you need to add the repository hosting IBM Cloud CLI plug-ins.
    ```sh
    ibmcloud plugin repos
    ```
    Output:
    ```
    Listing added plug-in repositories...

    Repo Name   URL                                    Description
    IBM Cloud   https://plugins.cloud.ibm.com          Official repository (Formerly named 'Bluemix')
    ```

1. If you don't see a repository, run the following command:
    ```sh
    ibmcloud plugin repo-add "IBM Cloud" https://plugins.cloud.ibm.com
    ```

## Install the Kubernetes Service (KS) plugin
To create Kubernetes clusters, and manage worker nodes, install the Container Service plug-in.

1. To install the Container Service plugin, run the following command:
    ```sh
    ibmcloud plugin install container-service -r "IBM Cloud"
    ```

1. Log in to the IBM Cloud Kubernetes Service. The prefix for running commands by using the IBM Cloud Kubernetes Service plug-in is **ibmcloud ks**.
    ```sh
    ibmcloud ks init
    ```


## Install the Container Registry (CR) plugin
1. To manage a private image registry, install the Container Registry plug-in.

    This plug-in connects to a private image registry, where you can store docker images that can be used to build containers. The prefix for running registry commands is **ibmcloud cr**.
    ```sh
    ibmcloud plugin install container-registry -r "IBM Cloud"
    ```

## Verify the plugins installation
1. To verify that the plug-in has been installed properly, run the following command:
    ```sh
    ibmcloud plugin list
    ```
    and both plug-ins are displayed in the results:
    ```
    Listing installed plug-ins...

    Plugin Name          Version
    container-registry                     0.1.347   
    container-service/kubernetes-service   0.2.19 
    ```
1. To update the container registry plugin
    ```sh
    ibmcloud plugin update container-registry
    ```