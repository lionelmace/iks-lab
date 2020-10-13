# Deploy the solution using Terraform

[Terraform](https://www.terraform.io/) enables you to safely and predictably create, change, and improve infrastructure. It is an open source tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

The IBM Cloud Terraform Provider is available on [HashiCorp Terraform Registry](https://registry.terraform.io/providers/IBM-Cloud/ibm).

In this tutorial, you will use a sample configuration to provision a **VPC**, a **Kubernetes cluster** and, a **Postgres** database. Finish by deleting all of the resources created by the configuration.

## Install Terraform

1. Download and install Terraform for your system. [https://learn.hashicorp.com/terraform/getting-started/install.html](https://learn.hashicorp.com/terraform/getting-started/install.html)

1. On MacOS, Terraform can also be installed using Terraform version manager `tfenv`
    ```
    brew install tfenv
    ```
    ```
    tfenv install <version>
    ```
    For example:
    ```
    tfenv install 0.12.24
    ```

1. Check Terraform installation by running `terraform` in your terminal. You should see a list of **Common commands**.


## Provision a VPC to host your cluster

1. Export API credential tokens as environment variables
    ```
    export TF_VAR_ibmcloud_api_key="Your IBM Cloud API Key"
    ```

1. Go to the terraform folder
    ```
    cd cloud/terraform/02-vpc
    ```

1. Terraform must initialize the provider before it can be used.
    ```
    terraform init
    ```
    Output
    ```
    Initializing the backend...

    Initializing provider plugins...
    - Finding ibm-cloud/ibm versions matching "1.12.0"...
    - Installing ibm-cloud/ibm v1.12.0...
    - Installed ibm-cloud/ibm v1.12.0 (self-signed, key ID AAD3B791C49CC253)

    Partner and community providers are signed by their developers.
    If you'd like to know more about provider signing, you can read about it here:
    https://www.terraform.io/docs/plugins/signing.html

    Terraform has been successfully initialized!

    You may now begin working with Terraform. Try running "terraform plan" to see
    any changes that are required for your infrastructure. All Terraform commands
    should now work.

    If you ever set or change modules or backend configuration for Terraform,
    rerun this command to reinitialize your working directory. If you forget, other
    commands will detect it and remind you to do so if necessary.
    ```

1. Start provisioning
    ```
    terraform apply
    ```


## Provision a Kubernetes cluster

1. Go to the terraform folder
    ```
    cd cloud/terraform/03-iks
    ```

1. Terraform must initialize the provider before it can be used.
    ```
    terraform init
    ```
    Output
    ```
    Initializing the backend...

    Initializing provider plugins...
    - Finding ibm-cloud/ibm versions matching "1.12.0"...
    - Installing ibm-cloud/ibm v1.12.0...
    - Installed ibm-cloud/ibm v1.12.0 (self-signed, key ID AAD3B791C49CC253)

    Partner and community providers are signed by their developers.
    If you'd like to know more about provider signing, you can read about it here:
    https://www.terraform.io/docs/plugins/signing.html

    Terraform has been successfully initialized!

    You may now begin working with Terraform. Try running "terraform plan" to see
    any changes that are required for your infrastructure. All Terraform commands
    should now work.

    If you ever set or change modules or backend configuration for Terraform,
    rerun this command to reinitialize your working directory. If you forget, other
    commands will detect it and remind you to do so if necessary.
    ```

1. Perform a dry run to show what infrastructure terraform intends to create
    ```
    terraform plan
    ```
    > You can activate debug log by running: *export TF_LOG=TRACE*

1. Start provisioning
    ```
    terraform apply
    terraform apply -target=ibm_container_cluster.cluster
    ```

1. Once provisioned, reads and outputs a Terraform state or plan file in a human-readable form.
    ```
    terraform show
    ```

1. Clean up
    ```
    terraform destroy
    ```

## Resources

For additional resources pay close attention to the following:

- [Tutorial: Deploy a LAMP stack using Terraform](https://cloud.ibm.com/docs/tutorials?topic=solution-tutorials-infrastructure-as-code-terraform#setup)
- [Tutorial: Plan, create and update deployment environments](https://cloud.ibm.com/docs/tutorials/plan-create-update-deployments.html#plan-create-and-update-deployment-environments)