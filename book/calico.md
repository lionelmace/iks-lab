# Controlling traffic with network policies

Every Kubernetes cluster is set up with a network plug-in called [Calico](https://www.projectcalico.org/). Default network policies are set up to secure the public network interface of every worker node in IBM® Cloud Kubernetes Service.

If you have unique security requirements or you have a multizone cluster with VLAN spanning enabled, you can use Calico and Kubernetes to create network policies for a cluster. With Kubernetes network policies, you can specify the network traffic that you want to allow or block to and from a pod within a cluster. 

## Install and Configure the Calico CLI

1.  Include the --admin and --network options with the ibmcloud ks cluster-config command. --admin downloads the keys to access your infrastructure portfolio and run Calico commands on your worker nodes. --network downloads the Calico configuration file to run all Calico commands.

    ```
    ibmcloud ks cluster-config --cluster <cluster_name_or_ID> --admin --network
    ```

1. Create the /etc/calico directory.
    ```
     sudo mkdir /etc/calico
    ```

1. Move the Calico configuration file that you previously downloaded to the directory.
    ```
    sudo mv /Users/<user>/.bluemix/plugins/container-service/clusters/<cluster_name>-admin/calicoctl.cfg /etc/calico
    ```

1. On Mac OS, install Calico with Homebrew
    ```
    brew install calico
    ```

1. Otherwise, [download the Calico CLI.](https://github.com/projectcalico/calicoctl/releases)

1. Verify that the Calico configuration is working correctly.
    ```
    calicoctl get nodes
    ```

## To view network policies in clusters

1. View all of the Calico and Kubernetes network policies that were created for the cluster. This list includes policies that might not be applied to any pods or hosts yet. For a network policy to be enforced, a Kubernetes resource must be found that matches the selector that was defined in the Calico network policy.

    Network policies are scoped to specific namespaces:
    ```
    calicoctl get NetworkPolicy --all-namespaces -o wide
    ```

    Global network policies are not scoped to specific namespaces:
    ```
    calicoctl get GlobalNetworkPolicy -o wide
    ```

1. View details for a network policy.
    ```
    calicoctl get NetworkPolicy -o yaml <policy_name> --namespace <policy_namespace>
    ````

1. View the details of all global network policies for the cluster.
    ````
    calicoctl get GlobalNetworkPolicy -o yaml
    ```

## Set a Global Network Policy

1. Apply the following Global Network Policy to deny all ingress traffic
    ```yml
    apiVersion: projectcalico.org/v3
    kind: GlobalNetworkPolicy
    metadata:
    name: cfee-ingress-deny-all
    spec:
    applyOnForward: true
    ingress:
    - action: Deny
        destination: {}
        source: {}
    order: 3000
    selector: ibm.role=='worker_public'
    types:
    - Ingress
    ```

## Resources

For additional resources pay close attention to the following:

- [Controlling traffic with network policies](https://cloud.ibm.com/docs/containers?topic=containers-network_policies#cli_install)
- [Tutorial: Using Calico network policies to block traffic](https://cloud.ibm.com/docs/containers?topic=containers-policy_tutorial#lesson3)