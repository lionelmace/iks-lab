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