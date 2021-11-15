# Control traffic with Calico network policies

Every Kubernetes cluster is set up with a network plug-in called [Calico](https://www.projectcalico.org/). Default network policies are set up to secure the public network interface of every worker node in IKS.

If you have unique security requirements, you can use Calico and Kubernetes to create network policies for a cluster. With Kubernetes network policies, you can specify the network traffic that you want to allow or block to and from a pod within a cluster. 


## Install the Calico CLI

{% hint style='info' %} The Calico CLI is pre-installed in the [Cloud Shell](https://cloud.ibm.com/shell). If you use it, skip the installation  and go to the next section Configure the Calico CLI.{% endhint %}

1. On Mac OS, install Calico with Homebrew
    ```
    brew install calico
    ```
    
1. Otherwise, [download the Calico CLI](https://github.com/projectcalico/calicoctl/releases).


## Configure the Calico CLI

1.  Include the --admin and --network options with the `ibmcloud ks cluster config` command. **--admin** downloads the keys to access your infrastructure portfolio and run Calico commands on your worker nodes. **--network** downloads the Calico configuration file to run all Calico commands.

    ```
    ibmcloud ks cluster config --cluster <cluster_name_or_ID> --admin --network
    ```

1. Create the /etc/calico directory.
    ```
     sudo mkdir /etc/calico
    ```

1. Move the Calico configuration file that you previously downloaded to the directory.
    ```
    mv /Users/<user>/.bluemix/plugins/container-service/clusters/<cluster_name>-admin/calicoctl.cfg /etc/calico
    ```

1. Verify that the Calico configuration is working correctly.
    ```
    calicoctl get nodes
    ```


## View existing network policies in the cluster

1. View all of the global network policies that were created by default for the cluster.
    ```
    calicoctl get GlobalNetworkPolicy -o wide
    ```
    > Global network policies are not scoped to specific namespaces.

1. View the details for a global network policy.
    ```
    calicoctl get GlobalNetworkPolicy -o yaml <policy_name>
    ```


## Block the incoming traffic to the Automatic Load Balancer

1. Retrieve and copy the Ingress Subdomain of your cluster
    ```
    ibmcloud ks cluster get --cluster <iks-cluster-name> 
    ```

1. Retrieve the IP of Ingress
    ```
    dig <iks-luster-name>.<region>.containers.appdomain.cloud
    ```

1. Edit the calico policy in folder **.cloud/calico/calico-deny-alb-traffic.yml** to replace the ALB IPs.
    ```yml
    --- 
    apiVersion: projectcalico.org/v3
    kind: GlobalNetworkPolicy
    metadata:
      name: deny-alb-traffic
    spec:
      applyOnForward: true
      preDNAT: true
      ingress:
      - action: Deny
        destination:
          nets:
          # (ALB) Automatic Load Balancer (= Ingress Subdomain)
          - <Automatic_Load_Balancer_IP>/32
          - <Automatic-Load-Balancer_IP>/32
          - <Automatic_Load_Balancer_IP>/32
          ports:
          - 80
          - 443
        protocol: TCP
        source: {}
      selector: ibm.role == 'worker_public'
      order: 4000
      types:
      - Ingress
    ```

1. Apply this Global Network Policy to deny all ingress traffic.
    ```
    calicoctl apply -f calico-deny-alb-traffic.yml
    ```

1. You should not be able to access any apps available on your sub domain ***.containers.appdomain.cloud**.

## Whitelist the incoming traffic from your laptop IP.

1. Find the IP of your laptop.

1. Edit the calico policy in folder **.cloud/calico/calico-allow-traffic-from-my-ip.yml** to replace the ALB IPs and your laptop IP.
    ```yml
    ---
    apiVersion: projectcalico.org/v3
    kind: GlobalNetworkPolicy
    metadata:
      name: allow-traffic-from-my-ip
    spec:
      applyOnForward: true
      preDNAT: true
      ingress:
      - action: Allow
        destination:
          nets:
          # (ALB) Automatic Load Balancer (= Ingress Subdomain)
          - <Automatic_Load_Balancer_IP>/32
          - <Automatic-Load-Balancer_IP>/32
          - <Automatic_Load_Balancer_IP>/32
          ports:
          - 80
          - 443
        protocol: TCP
        source:
          nets:
          -  <your-laptop-ip>/32
      selector: ibm.role == 'worker_public'
      order: 2000
      types:
      - Ingress
    ```

1. Apply this Global Network Policy to allow the traffic.
    ```
    calicoctl apply -f calico-allow-traffic-from-my-ip.yml
    ```

     

1. Both rules you applied are now listed in the Global network policies of your cluster.
    ```
    calicoctl get GlobalNetworkPolicy -o wide
    ```

1. You should now be able to access any apps available on your sub domain ***.containers.appdomain.cloud** from your laptop.


## Resources

For additional resources pay close attention to the following:

- [Controlling traffic with network policies](https://cloud.ibm.com/docs/containers?topic=containers-network_policies#cli_install)
- [Tutorial: Using Calico network policies to block traffic](https://cloud.ibm.com/docs/containers?topic=containers-policy_tutorial#lesson3)