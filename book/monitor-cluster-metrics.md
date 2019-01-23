# Installing agent on IBM Kubernetes Service (IKS)

Sysdig Platform documentation encompasses everything related to handling the Sysdig agent installation, administration and configuration.

NOTE: These instructions assume that you have provisioned a Sysdig service instance on IBM Cloud, have obtained an ACCESS-KEY and have your KUBECONFIG environment variable pointed to the appropriate cluster.

## Install script:
The following script can be run to configure the Sysdig configuration based on the arguments and deploy it to a kubernetes cluster:
```
curl -sL https://raw.githubusercontent.com/draios/sysdig-cloud-scripts/master/agent_deploy/IBMCloud-Kubernetes-Service/install-agent-k8s.sh | bash -s -- -a <ACCESS-KEY> -c <SYSDIG-ENDPOINT> -t <TAGS>
```

## Manual Installation:

1. Download the required yaml files from the following links to a directory:
    * sysdig-agent-clusterrole.yaml
    * sysdig-agent-configmap.yaml
    * sysdig-agent-daemonset-v2.yaml

1. Create a service account called sysdig-agent that will be used to monitor the kubernetes cluster:
    ```
    kubectl create serviceaccount sysdig-agent
    ```

1. User your ACCESS-KEY to create a secret for the cluster:
    ```
    kubectl create secret generic sysdig-agent --from-literal=access-key=<ACCESS-KEY>
    ```

1. Use the **sysdig-agent-clusterrole.yaml** to create a clusterrole and clusterrolebinding:
    ```
    kubectl apply -f /tmp/sysdig-agent-clusterrole.yaml
    kubectl create clusterrolebinding sysdig-agent --clusterrole=sysdig-agent --serviceaccount=default:sysdig-agent
    ```

1. Use an editor to open the **sysdig-agent-configmap.yaml** file. Add the following REQUIRED params:
    * **collector** - This should be URL that the agent should be sending it metrics to e.g. us-south.monitoring.test.cloud.ibm.com
    * **ssl** - This needs to be set true
    * **ssl_verfiy_certificate** - This needs to be set to false
    * **new_k8s** - This needs to be set to true to capture kube state metrics
You may also add an OPTIONAL entry for tags. An example yaml would look like this:
    ```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: sysdig-agent
    data:
        dragent.yaml: |
            ### Agent tags
            tags: linux:ubuntu,dept:dev,local:nyc
            collector: us-south.monitoring.test.cloud.ibm.com
            ssl: true
            ssl_verify_certificate: false
            new_k8s: true
    ```

1. Apply the config map to the cluster:
    ```
    kubectl apply -f sysdig-agent-configmap.yaml
    ```

1. Finally, apply the daemonset to deploy the Sysdig agent to cluster:
    ```
    kubectl apply -f sysdig-agent-daemonset-v2.yaml
    ```


## Navigating Sysdig UI

Sysdig Monitor is part of Sysdig’s container intelligence platform. Sysdig uses a unified platform to deliver security, monitoring, and forensics in a container and microservices-friendly architecture.

Once the agent has started sending metrics to Sysdig for your environment, you can use the Sysdig Monitor UI to view and analyze that data.