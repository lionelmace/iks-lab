# Deploy the app with Helm Charts

Helm helps you manage Kubernetes applications — Helm Charts helps you define, install, and upgrade even the most complex Kubernetes application.

During this lab, we are going to install a helm client and configure it. Then we are going to deploy the same app with Helm.

Charts are easy to create, version, share, and publish — so start using Helm and stop the copy-and-paste madness.

Helm is a client/server application :
* Helm client
* Tiller server.

LMA do we need to configure RBAC?
https://github.com/phthom/ContainerOrchestration/blob/master/4-HelmLab.md


## Initialize Helm & Tiller

1. Initialize Helm by navigating and running the below command in your cluster

    `helm init`

    Results:
    ```
    $ helm init
    $HELM_HOME has been configured at /Users/mace/.helm.

    Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

    Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
    For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation
    Happy Helming!
    ```

1. Verify that the tiller-deploy pod has a Status of Running in your cluster.

    `kubectl get pods -n kube-system -l app=helm`

    Results:
    ```
    $ kubectl get pods -n kube-system -l app=helm
    NAME                           READY     STATUS    RESTARTS   AGE
    tiller-deploy-75f5797b-sxmrq   1/1       Running   0          4m
    ```

## Deploy the app with Helm

1. Go to the folder kubernetes/helm/chart
    ```
    cd mytodos/kubernetes/helm/chart
    ```

1. LMA To install a Helm chart, run the below command
    ```
    helm install . --name mytodos
    ```

    Results:
    ```
    NAME:   mytodos
    LAST DEPLOYED: Fri Nov 16 18:51:12 2018
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Service
    NAME     TYPE       CLUSTER-IP     EXTERNAL-IP  PORT(S)   AGE
    mytodos  ClusterIP  172.21.52.118  <none>       8080/TCP  0s

    ==> v1beta2/Deployment
    NAME     DESIRED  CURRENT  UP-TO-DATE  AVAILABLE  AGE
    mytodos  3        3        3           0          0s

    ==> v1beta1/Ingress
    NAME     HOSTS                                            ADDRESS           PORTS    AGE
    mytodos  todo.hacluster.eu-de.containers.appdomain.cloud  149.81.67.38,...  80, 443  0s

    ==> v1/Pod(related)
    NAME                      READY  STATUS             RESTARTS  AGE
    mytodos-86884779d9-4c62z  0/1    ContainerCreating  0         0s
    mytodos-86884779d9-jzzpt  0/1    ContainerCreating  0         0s
    mytodos-86884779d9-qxnt2  0/1    ContainerCreating  0         0s


    NOTES:
    1. Get the application URL by running these commands:
    https://todo.hacluster.eu-de.containers.appdomain.cloud/
    ```

    > To run with local values in debug mode: ```
    helm install . --name mytodos --values=local-values.yaml --debug```

1. To get the status of the helm deployement
    ```
    helm status mytodos
    ```

## Delete your deployment

1. To delete your deployment
    ```
    helm delete --purge mytodos
    ```

    > Generally, an helm chart is managing many pods, deployments, secrets, volumes and services. So deleting the chart is a quick way to clean up your work.


## Deploy using a Continuous Delivery Pipeline

The **Deploy to IBM Cloud** button is an efficient way to create a cloned copy of the code in a new Git repository (repo) to deploy it to IBM Cloud by using a toolchain.

A toolchain is created that includes a new private clone of your Git repo, a pipeline for building and deploying code changes, the Eclipse Orion Web IDE for editing code on the Cloud, and an issue tracker.

[![Create toolchain](https://cloud.ibm.com/devops/setup/deploy/button_x2.png)](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/lionelmace/mytodo&branch=master)

Once the toolchain has completed, the application will be available at `https://todo.<your-cluster-ingress-domain>`.

The toolchain includes a stage named **UNINSTALL (manual)**. This stage can only be triggered manually and will remove all resources created by the toolchain (app and services).