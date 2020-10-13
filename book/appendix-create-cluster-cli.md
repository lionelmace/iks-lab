# Create a kubernetes cluster using the CLI

To create a cluster, you have two options either a Lite cluster or a Standard one.

+ A **Lite cluster** is a free cluster comes with a single worker node to deploy container pods upon. A worker node is the compute host, typically a virtual machine, that your apps run on. Go to Step *Create a Lite cluster*

+ A **Standard cluster** is a production-ready cluster with as many worker nodes as you want. A Standard cluster requires requires a IBM Cloud Infrastructure account. Go to Step *Create a Standard cluster*.

## Create a Lite cluster

1. Create your Lite cluster.
    ```
    ibmcloud ks cluster-create --name <your-cluster-name>
    ```
    Once the cluster reaches the **deployed** state you can provision pods, but they will be enqueued until the cluster’s pods are finished provisioning. Note that it takes up to 15 minutes for the worker node machine to be ordered and for the cluster to be set up and provisioned.

1. Verify that the creation of the cluster was requested.
    ```
    ibmcloud ks clusters
    ```

1. Check the status of the worker node(s).
    ```
    ibmcloud ks workers <cluster_name_or_id>
    ```


## Create a Standard cluster

1. Select the Kubernetes version
    ```
    ibmcloud ks versions
    ```
    Output
    ```
    Kubernetes Versions
    1.16.15
    1.17.12
    1.18.9 (default)
    1.19.2
    ```

1. Review the data centers that are available.
    ```
    ibmcloud ks zones --provider vpc-gen2
    ```
    Output for Frankfurt:
    ```
    eu-de-1      eu-de-1      Frankfurt         -
    eu-de-2      eu-de-2      Frankfurt         -
    eu-de-3      eu-de-3      Frankfurt         -
    ```

1. Review the machine types available in the data center
    ```
    ic ks flavors --zone <zone>
    ```
    Output for eu-de-1:
    ```
    Name         Cores   Memory   Network Speed   OS             Server Type   Storage   Secondary Storage   Provider
    b2.16x64†    16      64GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    b2.32x128†   32      128GB    1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    b2.4x16†     4       16GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    b2.8x32†     8       32GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    bx2.16x64    16      64GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    bx2.2x8†     2       8GB      4Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    bx2.32x128   32      128GB    16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    bx2.48x192   48      192GB    16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    bx2.4x16     4       16GB     8Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    bx2.8x32     8       32GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    c2.16x32†    16      32GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    c2.2x4†      2       4GB      1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    c2.32x64†    32      64GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    cx2.16x32    16      32GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    cx2.2x4†     2       4GB      4Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    cx2.32x64    32      64GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    cx2.48x96    48      96GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    cx2.4x8†     4       8GB      8Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    cx2.8x16     8       16GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    m2.16x128†   16      128GB    1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    m2.8x64†     8       64GB     1000Mbps        UBUNTU_18_64   virtual       100GB     0B                  vpc-classic
    mx2.16x128   16      128GB    16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    mx2.2x16†    2       16GB     4Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    mx2.32x256   32      256GB    16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    mx2.48x384   48      384GB    16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    mx2.4x32     4       32GB     8Gbps           UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    mx2.8x64     8       64GB     16Gbps          UBUNTU_18_64   virtual       100GB     0B                  vpc-gen2
    ```

1. Get the available VPCs in your account
    ```
    ibmcloud is vpcs
    ```
    Output for fra02:
    ```
    Listing vpcs for generation 2 compute in resource group demo and region eu-de under account ACME as user lionel.mace@fr.ibm.com...
    ID                                          Name            Status      Classic access   Default network ACL   Default security group   Resource group
    r010-68dfdcd1-a8ca-4302-83a1-a14d7408ab7c   vpc-eu-de-iks   available   false            vpc-eu-de-iks-acl     vpc-eu-de-iks-sg         demo
    ```
    >  When you create a Kube cluster with no vlans in create command, those should get created for you.

1. Get the available subnet for the vpc above
    ```
    ic is subnets
    ```

1. Create cluster
    ```
    ibmcloud ks cluster create vpc-gen2 \
      --name <cluster-name> \
      --zone <zone> \
      --workers <number-of-workers> \
      --flavor <machine-type> \
      --version <kubernetes-version> \
      --vpc-id <vpc-id> \
      --subnet-id <subnet-id>
    ```
    For example:
    ```
    ibmcloud ks cluster create vpc-gen2 \
      --name mycluster \
      --zone eu-de-1 \
      --version 1.18.9 \
      --flavor cx2.2x4  \
      --workers 2 \
      --vpc-id r006-809f4e33-36fb-4bdd-a026-bd2a4918eeee \
      --subnet-id 0717-30090a05-434a-42e3-973f-dde7999acb2c

    ```

1. Verify that the creation of the cluster was requested.
    ```
    ibmcloud ks clusters
    ```

1. Check the status of the worker node(s).
    ```
    ibmcloud ks workers <cluster_name_or_id>
    ```
    
    > The cluster creation process is as follow:

    | State             | Status                                       |
    | ----------------- | -------------------------------------------- |
    | provision_pending | Waiting for master to be deployed            |
    | provisioning      | Provisioning in progress                     |
    | deploying         | Starting worker deployment                   |
    | deploying         | Installing Docker Runtime                    |
    | deploying         | Configuring Kubectl CLI                      |
    | deploying         | Preparing Worker for Calico Networking Setup |
    | deploying         | Starting Kube Worker Services                |
    | deploying         | Configuring Calico-Workers                   |
    | deployed          | Deploying                                    |
    | deployed          | Deploy Automation Successful                 |
    | normal            | Ready                                        |


## Connect to the Kubernetes cluster

1. Connect to the Kubernetes Cluster. Use the kubectl command to check that you are connected:

    ```kubectl get nodes```


## Access the cluster via Kubernetes CLI

1. You will need the kubeconfig data and certs to connect to your cluster using kubectl. You can download the config to your local machine via the CLI.
    ```
    ibmcloud ks cluster-config <cluster_name_or_id>
    ```
