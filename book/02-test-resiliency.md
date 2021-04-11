# Test Resiliency

1. Activate the watch on the pod in a separate windows to track what's going on your pods while you kill your worker nodes:
    ```sh
    kubectl get po -o wide -w
    ```

1. First, identify the name of the node you wish to drain. You can list all of the nodes and their respective IPs in your cluster with
    ```sh
    kubectl get nodes
    ```

1. Next, tell Kubernetes to drain the node:
    ```sh
    kubectl drain <node name> --ignore-daemonsets --delete-local-data
    ```

    {% hint style='info' %} The node name is the private IP address in IKS. {% endhint %}

    Output:
    ```
    node/10.135.52.88 already cordoned
    WARNING: Deleting pods with local storage: kubernetes-dashboard-7d5d4f48c4-pmgp9; Ignoring DaemonSet-managed pods: logdna-agent-lm8v4, calico-node-78rms, ibm-keepalived-watcher-l6vp8, ibm-kube-fluentd-5ghgk, ibm-master-proxy-48ttq
    pod/ibm-storage-watcher-74c46db958-hfz5v evicted
    pod/kubernetes-dashboard-7d5d4f48c4-pmgp9 evicted
    pod/ibm-cloud-provider-ip-161-156-93-78-5c7d96bb68-pvg9j evicted
    pod/ibm-file-plugin-b759f7cb7-6jmdx evicted
    pod/kube-dns-autoscaler-6cf64dc49f-gh6xk evicted
    pod/calico-kube-controllers-6744d867-7fgxl evicted
    pod/public-crac8b7d523dea4c85bea1139ef7328163-alb1-657b4fbbb4-xvq2q evicted
    pod/client-certificate-demo-676dbdd676-8b7k7 evicted
    pod/kube-dns-amd64-5dc4d6b67-clvnh evicted
    ```

1. Check the status of your node. You should see the status `SchedulingDisabled`
    ```
    NAME           STATUS                     ROLES     AGE       VERSION
    10.123.12.75   Ready                      <none>    48d       v1.11.3+IKS
    10.135.52.88   Ready,SchedulingDisabled   <none>    48d       v1.11.3+IKS
    ```

1. Tell Kubernetes that it can resume scheduling new pods onto the node
    ```sh
    kubectl uncordon <node name>
    ```