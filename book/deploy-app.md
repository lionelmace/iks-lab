# Deploy the app in your cluster

Different ways exist to make your app accessible from the internet. To choose the best networking option for your application, you can follow the decision tree available [here](https://cloud.ibm.com/docs/containers?topic=containers-cs_network_planning).

In this lab, we will test the **Ingress**, which a NGINX based reverse proxy.

## Deploy with Ingress

1. Navigate to the folder **kubernetes**.
    ```sh
    cd cloud/kubernetes
    ```

1. Retrieve the values of both the ingress subdomain and the ingress secret of your cluster
    ```
    ibmcloud ks cluster get -c <cluster-name> | grep Ingress
    ```
    Output example:
    ```
    Ingress Subdomain:              iks-466821-483cccd2f0d38128dd40d2b711142ba9-0000.eu-de.containers.appdomain.cloud
    Ingress Secret:                 iks-466821-483cccd2f0d38128dd40d2b711142ba9-0000
    Ingress Status:                 healthy
    Ingress Message:                All Ingress components are healthy
    ```

1. Edit the file `ingress-tls-deploy.yaml`.

1. Replace all the values wrapped in <...> with the appropriate values:
  
    ```yaml
    ---
     # Application to deploy
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: mytodo
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
      annotations:
        kubernetes.io/ingress.class: "public-iks-k8s-nginx"
    spec:
      tls:
      - hosts:
        - mytodo.<ingress-subdomain>
        secretName: <ingress-secret>
      rules:
      - host: mytodo.<ingress-subdomain>
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
    Output example:
    ```
    namespace/mytodo configured
    deployment.apps/mytodo created
    ingress.extensions/mytodo-ingress created
    service/mytodo created
    ```

1. Open a browser and check out the app with the following URL:
    ```
    https://mytodo.<ingress-subdomain>
    ```

{% hint style='info' %}Unless a namespase is specified, any kubernetes deployment is done in the namespace **default**. A best practise is to create a new namespace for this deployment. To learn how to use a custom namespace, you can follow those [instructions](./deploy-app-custom-ns). {% endhint %}

## Resources

* [Copying an existing image pull secret from the default namespace to new namespace](https://cloud.ibm.com/docs/containers?topic=containers-registry#copy_imagePullSecret)
* [Storing the image pull secret in the Kubernetes service account for the selected namespace](https://cloud.ibm.com/docs/containers?topic=containers-registry#store_imagePullSecret)