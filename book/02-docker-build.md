# Build the docker image with your application code

Before you can deploy the application on the cluster, you first need to build the Docker image.

1. **Build** a Docker image. Make sure to replace the value region and namespace
    ```sh
    docker build . -t <region>.icr.io/lab-registry/todo-<yourlastname>:1.0
    ```
    > Region is **de** for the Frankfurt datacenter.

    If you use the Cloud Shell, note that docker is not available yet. So use this command instead.
    ```
    ibmcloud cr build . -t <region>.icr.io/lab-registry/todo-<yourlastname>:1.0
    ```
    > This command will run **docker build** directly on the cloud. So you don't need to push your image later.

1. Verify that the image was successfully added to your local docker registry
    ```sh
    docker images
    ```
    Output:
    ```
    REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
    de.icr.io/lab-registry/mytodo-mace   1.0                 30bd6bcc47bc        42 minutes ago      124MB
    node                                 11.13.0-alpine      f34bcc2815ed        6 weeks ago         75.4MB
    ``` 