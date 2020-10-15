# Appendixes

## Appendix - Assigning Access to Namespaces
    ```
    ibmcloud iam user-policy-create xxx.xxx@fr.ibm.com \
    --service-name containers-kubernetes \
    --service-instance 03cf54bd4548416581e63783dbe645c2 \
    --region us-south \
    --resource-type namespace \
    --resource kube-system \
    --roles Viewer
    ```


## Appendix - Issues when pushing to the container registry

Below is a list of solutions to resolve issues you may face when pushing your images into the Container Registry.

1. Error: Request access to the resource is denied

    ```
    denied: requested access to the resource is denied
    ```
    Note that adding a namespace only affects one region - if you want the same namespace in multiple regions you need to target each region and add it in each one. Make sure the namespace you are trying to push the image to exists.

1. Error: You have exceeded your storage quota

    In this tutorial, the image is less than 100MB. Once you start pushing images you will rapidly reach the default limit of 500MB and get the following error:
    ```
    unauthorized: You have exceeded your storage quota. Delete one or more images, or review your storage quota and pricing plan
    ```
    Check the current quota:
    ```
    ibmcloud cr quota
    Getting quotas and usage for the current month, for account 'IBM'...

    QUOTA          LIMIT    USED     ADDITIONAL INFORMATION
    Pull traffic   5.0 GB   0 B
    Storage        512 MB   546 MB   Your account has exceeded its storage quota. Delete one or more images, or review your storage quota and pricing plan.
    ```
    Check the current plan:
    ```
    ibmcloud cr plan
    Getting pricing plan for account 'Lionel Mace's Account'...

    You are on the 'Free' pricing plan.
    ```
    You increase your quota you will need to upgrade plan:
    ```
    ibmcloud cr plan-upgrade standard
    ```
    Finally, you will increase the quota or just set to Unlimited
    ```
    ibmcloud cr quota-set --storage unlimited
    ```

## Appendix - Using Kubernetes namespaces

In order to isolate the applications you deploy in the cluster, you may want to leverage Kubernetes namespace. Using namespace has an impact on the following commands:

1. Create a new namespace in your cluster. Let's call it *mytodo*
    ```
    kubectl create namespace mytodo
    ```

1. Verify your new namespace was created
    ```
    kubectl get namespaces
    ```

1. The service cloudant should be bound in this namespace
    ```
    ibmcloud ks cluster-service-bind <cluster_id> mytodo <service_instance_name>
    ```
    Example:
    ```
    ibmcloud ks cluster-service-bind ad35aacc139b4e11a6f3182fb13d24af mytodo todo-cloudant
    ```

1. The new namespace does not contain the secret to access the private container registry. The default namespace has by default this secret to access the registry. If you try to deploy without this step, you will get this error:
    > Failed to pull image "de.icr.io/mace/mytodo:1": rpc error: code = Unknown desc = Error response from daemon: Get https://de.icr.io/v2/mace/mytodo/manifests/v1: unauthorized: authentication required
    
    In order to add this registry secret, run the following command:
    ```
    kubectl --namespace <CLUSTER_NAMESPACE> create secret docker-registry <IMAGE_REGISTRY_SECRET_NAME> --docker-server=<REGISTRY_URL> --docker-password=<IBMCLOUD_API_KEY> --docker-username=iamapikey --docker-email=a@b.com
    ```
    For example:
    ```
    kubectl --namespace mytodo create secret docker-registry private-registry-secret --docker-server=de.icr.io --docker-password=<IBMCLOUD_API_KEY> --docker-username=iamapikey --docker-email=a@b.com
    ```
    > You can generate the api key using the following command:
    ```ibmcloud iam api-key-create IBMCLOUD_API_KEY```

1. Modify the yml file to set the image registry secret.
    ```yaml
    ---
    apiVersion: extensions/v1beta1
    kind: Deployment
    metadata:
    name: mytodo
    spec:
    replicas: 2 # tells deployment to run 2 pods matching the template
    template: # create pods using pod definition in this template
        metadata:
        labels:
            app: mytodo
            tier: frontend
        spec:
        imagePullSecrets:
        - name: private-registry-secret
        containers:
        - name: mytodo
            image: <region>.icr.io/<namespace>/mytodo:1
            imagePullPolicy: Always
        ...
    ```

1. Deploy the container in the new namespace
    ```
    kubectl create -f deploy2kubernetes.yml --namespace mytodo
    ```