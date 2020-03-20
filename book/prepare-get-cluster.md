# Get a cluster

In this section, you will either use your own cluster or get access to a IBM Cloud Lab account which contains pre-provisioned clusters. Each lab attendee should have access to one cluster.


## Create a Kubernetes cluster

1. Create a standard cluster in the dashboard [https://cloud.ibm.com/kubernetes/catalog/create](https://cloud.ibm.com/kubernetes/catalog/create)

    * Number of worker: 1
    * Worker flavor: u3c.2x4
    * Kube version: 1.17.4

1. The cluster can be created by CLI
    ```
    ibmcloud ks cluster create classic --name mycluster --zone fra04 --flavor u3c.2x4 --workers 1 --version 1.17.4
    ```
    If you already have a public and private vlans, you would need this command
    ```
    ibmcloud ks cluster create classic --name mycluster --zone <zone> --flavor <flavor> --workers 1 --version <version> --private-vlan <vlan-id> --public-vlan <vlan-id>
    ```
    For example:
    ```
    ibmcloud ks cluster create classic --name mycluster --zone fra04 --flavor u3c.2x4 --workers 1 --version 1.17.4 --private-vlan 2700262 --public-vlan 2700260
    ```

## Get a Kubernetes cluster

1. Go to the web page given by the instructor and enter your IBM ID (the email you used to sign up) and the lab key given by the trainer. Make sure to select the region where the cluster has been provisioned.

    ![](./images/request-cluster.png)

1. **Submit**. You will be added to the Lab account and granted access to a cluster.

    ![](./images/get-cluster.png)

## Verify your access to the cluster

1. Refresh your [IBM Cloud Dashboard](http://cloud.ibm.com)

1. Select the lab account when you have been invited by clicking on the account selection drop down in the top nav bar.

1. Click on **View all** in the Resource Summary tile.

1. Under **Kubernetes Clusters**, click on the cluster that has been assigned to you.

1. Launch the **Kubernetes Clusters** and have a look around! You can come back to this dashboard throughout your lab.

    ![](./images/resource-list.png)

You will use this cluster for this lab. Note that this is a Standard/Paid cluster (as opposed to a FREE cluster.)