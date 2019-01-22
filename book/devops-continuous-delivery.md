## Deploy using a Continuous Delivery Pipeline

The **Deploy to IBM Cloud** button is an efficient way to create a cloned copy of the code in a new Git repository (repo) to deploy it to IBM Cloud by using a toolchain.

A toolchain is created that includes a new private clone of your Git repo, a pipeline for building and deploying code changes, the Eclipse Orion Web IDE for editing code on the Cloud, and an issue tracker.

![](https://cloud.ibm.com/devops/setup/deploy/button_x2.png)
[Create toolchain](https://cloud.ibm.com/devops/setup/deploy?repository=https://github.com/lionelmace/mytodo&branch=master)

Once the toolchain has completed, the application will be available at `https://todo.<your-cluster-ingress-domain>`.

The toolchain includes a stage named **UNINSTALL (manual)**. This stage can only be triggered manually and will remove all resources created by the toolchain (app and services).
