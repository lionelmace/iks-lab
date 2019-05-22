# Build the docker image with your application code

Before you can deploy the application on the cluster, you first need to build the Docker image.

1. **Build** a Docker image. Make sure to replace the value region and namespace
    ```sh
    docker build . -t <region>.icr.io/<namespace>/todo-<lastname>:1.0
    ```
    > Region is **de** for the Frankfurt datacenter.

1. Verify that the image was successfully added to your local docker registry
    ```sh
    docker images
    ```
    Output:
    ```
    REPOSITORY                         TAG                 IMAGE ID            CREATED             SIZE
    de.icr.io/lab-registry/todo-mace   1.0                 30bd6bcc47bc        42 minutes ago      124MB
    node                               11.13.0-alpine      f34bcc2815ed        6 weeks ago         75.4MB
    ``` 