# Connect to Mongo DBaaS

The initial version of the application uses an in-memory database to store the todo. Yet, the web application can be configured to store the todo in either a Cloudant or a Mongo DBaaS.

We will use a Mongo DB to demonstrate how to connnect the web app to the Mongo service using the Kubernetes Secret.

## Create an instance of Mongo and get its credentials

1. Let's create the instance of Mongo using the CLI

    ```sh
    ibmcloud resource service-instance-create mongo-db databases-for-mongodb standard eu-de -g dev --service-endpoints private -p \ '{
    "members_disk_allocation_mb": 61440,
    "members_memory_allocation_mb": 3072,
    "version": "4.2"
    }'
    ```

    > Note: Expect the provisioning of Mongo instance to take around 12-14 minutes.

1. We need some credentials to access the service. Service credential is a subset of something called a service key.

    ```sh
    ibmcloud resource service-key-create mongo-key --instance-name mongo-db -g dev
    ```

    ```sh
    ibmcloud resource service-key mongo-key --output JSON | jq '.[].credentials' > ./mongo_credentials.txt
    ```

1. Duplicate the file `credentials.template.env` in the root folder to a new file `credentials.env`

1. Edit the file `credentials.env` and fill out the value for both CLOUDANT_USERNAME and CLOUDANT_APIKEY from the generated credentials above. Make sure you uncomment those 2 lines

## Create the Kubernetes Secret

1. Unless you have created a new namespace, we will use the namespace **default** in this lab. To find the list of Kubernetes namespaces:

    ```sh
    kubectl get namespaces
    ```

    Output:

    ```sh
    NAME          STATUS    AGE
    default       Active    7d
    ibm-system    Active    7d
    kube-system   Active    7d
    ```

1. Create the secret used by the application to obtain service credentials:

    ```sh
    kubectl create secret generic database-credentials --from-env-file=credentials.env
    ```

    Result:
    > secret/database-credentials created

1. Control that your secret was successfully created

    ```sh
    kubectl get secrets
    ```

1. You can view the secret in the Kubernetes Dashboard

    ![Kubernetes Secrets](./images/k8s-secret.png)

## Redeploy the app

1. Modify the YAML to uncomment the last 3 lines of the Deployment section as of **envFrom**.

    Your YAML file should look as follows:

    ```yaml
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
            envFrom:
            - secretRef:
                name: database-credentials
    ```

1. Update the application with this new configuration

    ```sh
    kubectl apply -f ingress-tls-deploy.yaml
    ```

1. Create a new todo in the web app.

    ![](./images/webapp-db.png)

1. Check the Cloudant Dashboard to validate this new todo has been persisted in the DB.
