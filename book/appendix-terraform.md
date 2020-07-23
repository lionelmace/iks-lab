# Deploy the solution using Terraform

[Terraform](https://www.terraform.io/) enables you to safely and predictably create, change, and improve infrastructure. It is an open source tool that codifies APIs into declarative configuration files that can be shared amongst team members, treated as code, edited, reviewed, and versioned.

In this tutorial, you will use a sample configuration to provision a **Kubernetes cluster**, and, a **Postgres** database. Finish by deleting all of the resources created by the configuration.

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

1. Check Terraform installation by running **terraform** in your terminal or command prompt window. You should see a list of **Common commands**.


## Install IBM Cloud Provider

1. Download the IBM Cloud Provider plugin. [https://github.com/IBM-Cloud/terraform-provider-ibm/releases](https://github.com/IBM-Cloud/terraform-provider-ibm/releases)

1. Extract the archive

1. Create a hidden folder for your plug-in.

    ```
    mkdir $HOME/.terraform.d/plugins
    ```

1. Move the IBM Cloud Provider plug-in you extracted into your hidden folder.

    ```
    mv $HOME/Downloads/terraform-provider-ibm* $HOME/.terraform.d/plugins/
    ```

1. Create a .terraformrc file in your home directory that points to the Terraform binary.

    ```
    vi ~/.terraformrc
    ```
    with the following content:
    ```
    providers {
      ibm = "$HOME/.terraform.d/plugins/terraform-provider-ibm_v1.9.0"
    }
    ```
    > Note: Update the provider version accordingly.

1. Export API credential tokens as environment variables
    ```
    export IC_API_KEY="Your IBM Cloud API Key"
    ```


## Initialize Terraform

1. Go to the terraform folder
    ```
    cd .cloud/terraform
    ```

1. Terraform must initialize the provider before it can be used.
    ```
    terraform init
    ```

1. Verify the terraform provider is installed
    ```
    terraform providers
    ```

    Output:
    ```
    .
    └── provider.ibm
    ```

1. Copy terraform/credentials.tfvars.tmpl to terraform/credentials.tfvars by running the below command
    ```
    cp terraform/credentials.tfvars.tmpl terraform/credentials.tfvars
    ```

1. Edit terraform/credentials.tfvars and set the value for ibmcloud_api_key to the Platform API key you obtained.

1. To provision the cluster, the terraform needs your Infrastructure API. To do so, go to [https://cloud.ibm.com/iam#/users](https://cloud.ibm.com/iam#/users)

1. Select the User

1. Scroll down to API Keys

1. Select the **Classic infrastructure API key** - To see your classic infrastructure API key details, select Details in the Options menu.


## Provision a Kubernetes cluster

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