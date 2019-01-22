# Provisioning with Terraform

[Terraform](https://www.terraform.io/) enables you to safely and predictably create, change, and improve infrastructure. It is an open source tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

In this tutorial, you will use a sample configuration to provision a **Kubernetes cluster**, and, a **Postgres** database. Finish by deleting all of the resources created by the configuration.

## Install Terraform and the IBM Cloud provider for Terraform

https://cloud.ibm.com/docs/tutorials/plan-create-update-deployments.html#install-terraform-and-the-ibm-cloud-provider-for-terraform

https://github.com/IBM-Cloud/terraform-provider-ibm/releases/tag/v0.14.0

1. Download and install Terraform for your system.

1. Download the Terraform binary for the IBM Cloud provider. To setup Terraform with IBM Cloud provider, refer to this link

1. Create a .terraformrc file in your home directory that points to the Terraform binary. In the following example, /opt/provider/terraform-provider-ibm is the route to the directory.

    ```
    # ~/.terraformrc
    providers {
    ibm = "/opt/provider/terraform-provider-ibm"
    }
    ```

1. Verify the terraform provider is installed

    ```terraform providers````

    Output:
    ```
    .
    └── provider.ibm
    ```

## Set Platform API key

1. Copy terraform/credentials.tfvars.tmpl to terraform/credentials.tfvars by running the below command
    ```
    cp terraform/credentials.tfvars.tmpl terraform/credentials.tfvars
    ```

1. Edit terraform/credentials.tfvars and set the value for ibmcloud_api_key to the Platform API key you obtained.

1. Initialize terraform
    ```
    terraform init
    ```

1. To provision the cluster, the terraform needs your Infrastructure API. To do so, go to [https://cloud.ibm.com/iam#/users](https://cloud.ibm.com/iam#/users)

1. Select the User

1. Scroll down to API Keys

1. Select the **Classic infrastructure API key** - To see your classic infrastructure API key details, select Details in the Options menu.


## Provision a 

1. Start provisioning
    ```
    terraform apply
    ```

1. Clean up
    ```
    terraform destroy
    ```