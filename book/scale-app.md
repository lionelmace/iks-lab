# Scale the application using the replica

The application was deployed with 2 replicas. Let's scale it to 3 replicas.

1. Let's scale up to 3 replicas
    ```sh
    kubectl scale --replicas=3 deployment/mytodos
    ```

1. Then, inspect your Pods again.
    ```sh
    kubectl get pods
    ```
    You should now see 3 pods.

# Clean up the environment

1. Delete the deployment and the services
    ```sh
    kubectl delete -f ingress-tls-deploy.yaml
    ```