# Deploy the app in your cluster

Different ways exist to make your app accessible from the internet. To choose the best networking option for your application, you can follow the decision tree available [here](https://cloud.ibm.com/docs/containers/cs_network_planning.html#planning).

In this lab, we will test the **Ingress**, which a NGINX based reverse proxy.

## Create a Kubernetes namespace

By default any deployment is done in the namespace **default**. A good practise is to create a dedicated namespace for this deployement. Let's do it.

1. Create a Kubernetes namespace called `mytodo`
    ```
    kubectl create namespace mytodo
    ```

1. Set up the Container Registry image pull secret in the new namespace to be able to pull image during the deployement later. Let's copy the all-icr-io image pull secret from the default namespace to the new namespace `mytodo`
    ```
    kubectl get secret all-icr-io -n default -o yaml | sed 's/default/mytodo/g' | kubectl create -n mytodo -f -
    ```

1. Verify that the secrets are created successfully.
    ```
    kubectl get secrets -n mytodo | grep icr-io
    ```

1. To deploy containers, add the image pull secret to the service account of the namespace so that any deployment in the namespace can pull images from the registry.
    ```
    kubectl patch  -n mytodo serviceaccount/default -p '{"imagePullSecrets":[{"name": "all-icr-io"}]}'
    ```

1. Verify that your image pull secret was added to your default service account.
    ```
    kubectl describe serviceaccount default -n mytodo
    ```
    Example output:
    ```
    Name:                default
    Namespace:           mytodo
    Labels:              <none>
    Annotations:         <none>
    Image pull secrets:  all-icr-io
    Mountable secrets:   default-token-xs4kr
    Tokens:              default-token-xs4kr
    Events:              <none>
    ```

## Deploy with Ingress

1. Navigate to the folder **kubernetes**.
    ```sh
    cd cloud/kubernetes
    ```

1. Edit the file `ingress-tls-deploy.yaml`.

1. Replace all the values wrapped in <...> with the appropriate values:

    * registry-region    **de** for Frankfurt
    * registry-namespace **lab-registry**
    * cloud-region       **eu-de** for Frankfurt
    * cluster-name       **lab-11**
  
    Your YAML file should look as follows:
    ```yaml
    ---
     # Create a Kubernetes namespace
    apiVersion: v1
    kind: Namespace
    metadata:
      name: mytodo

    ---
     # Application to deploy
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mytodo
      namespace: mytodo
    spec:
      replicas: 2 # tells deployment to run 2 pods matching the template
      selector:
        matchLabels:
          app: mytodo
      template:   # create pods using pod definition in this template
        metadata:
          labels:
            app: mytodo
            tier: frontend
        spec:
          containers:
          - name: mytodo
            image: <registry-region>.icr.io/<registry-namespace>/mytodo-<lastname>:1.0
            imagePullPolicy: Always
            resources:
              requests:
                cpu: 250m     # 250 millicores = 1/4 core
                memory: 128Mi # 128 MB
              limits:
                cpu: 500m
                memory: 384Mi
            # envFrom:
            # - secretRef:
            #     name: database-credentials
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: mytodo-ingress
      namespace: mytodo
      annotations:
        # Force the use of https if the request is http
        ingress.bluemix.net/redirect-to-https: "True"
    spec:
      tls:
      - hosts:
        - mytodo.<cluster-name>.<cloud-region>.containers.appdomain.cloud
        secretName: <cluster-name>
      rules:
      - host: mytodo.<cluster-name>.<cloud-region>.containers.appdomain.cloud
        http:
          paths:
          - path: /
            backend:
              serviceName: mytodo
              servicePort: 8080
    ---
     # Service to expose frontend
    apiVersion: v1
    kind: Service
    metadata:
      name: mytodo
      namespace: mytodo
      labels:
        app: mytodo
        tier: frontend
    spec:
      ports:
      - protocol: TCP
        port: 8080
      selector:
        app: mytodo
        tier: frontend
    ```

1. Deploy the app into your Kubernetes cluster.
    ```sh
    kubectl apply -f ingress-tls-deploy.yaml
    ```
    Result:
    ```
    deployment.apps/mytodo created
    ingress.extensions/mytodo-ingress created
    service/mytodo created
    ```

1. Open a browser and check out the app with the following URL:
    ```
    https://mytodo.<cluster-name>.eu-de.containers.appdomain.cloud
    ```
    In this example, the url would be ```https://mytodo.lab-cluster-1.eu-de.containers.appdomain.cloud```


## Resources

* [Copying an existing image pull secret from the default namespace to new namespace](https://cloud.ibm.com/docs/containers?topic=containers-registry#copy_imagePullSecret)
* [Storing the image pull secret in the Kubernetes service account for the selected namespace](https://cloud.ibm.com/docs/containers?topic=containers-registry#store_imagePullSecret)