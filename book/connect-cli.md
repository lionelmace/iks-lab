# Target your Resource Group

You need to target a Resource Group (RG) to view your cluster or to add a service (e.g. Cloudant Database). Access to the group and the resources within it are managed by using Identity and Access Management (IAM).

1. In this lab, the Resource Group **lab** has been created. Select this resource group.

    ```sh
    ibmcloud target -g lab
    ```

1. Verify you can see your cluster.

    ```sh
    ibmcloud ks clusters
    ```

    Output
    ![](./images/cli-cluster-list.png)

1. Download the Kubernetes configuration files and certificates to connect to your cluster by using kubectl commands
    ```sh
    ibmcloud ks cluster config -c <cluster-name>
    ```

1. The cluster configuration is visible in the folder `.kube` 
    ```sh
    more .kube/config
    ```

1. You can now execute 'kubectl' commands against your cluster. For example, get the worker nodes of your cluster:
    ```sh
    kubectl get nodes
    ```
