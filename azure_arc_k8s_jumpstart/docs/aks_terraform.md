# Overview

The following README will guide you on how to use the provided [Terraform](https://www.terraform.io/) plan to deploy an [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/en-us/azure/aks/intro-kubernetes) cluster and connected it as an Azure Arc cluster resource.

# Prerequisites

* Clone this repo

* [Install or update Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest). Azure CLI should be running version 2.6.0 or later. Use ```az --version``` to check your current installed version.

* [Install Terraform >=0.12](https://learn.hashicorp.com/terraform/getting-started/install.html)

* Create Azure Service Principal (SP)   

    To connect the AKS cluster installed on the VM to Azure Arc, Azure Service Principal assigned with the "Contributor" role is required. To create it, login to your Azure account run the following command:

    ```az login```

    ```az ad sp create-for-rbac -n "http://AzureArcK8s" --role contributor```

    Output should look like this:
    ```
    {
    "appId": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "displayName": "AzureArcK8s",
    "name": "http://AzureArcK8s",
    "password": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX",
    "tenant": "XXXXXXXXXXXXXXXXXXXXXXXXXXXX"
    }
    ```
    **Note**: It is optional but highly recommended to scope the SP to a specific [Azure subscription and Resource Group](https://docs.microsoft.com/en-us/cli/azure/ad/sp?view=azure-cli-latest) 

# Deployment

The only thing you need to do before executing the Terraform plan is to export the environment variables which will be used by the plan. This is based on the Azure Service Principle you've just created and your subscription.  

* Export the environment variables needed for the Terraform plan.

    ```export TF_VAR_client_id=<Your Azure Service Principal App ID>```   
    ```export TF_VAR_client_secret=<Your Azure Service Principal App Password>```

* Run the ```terraform init``` command which will download the Terraform AzureRM provider.

![](../img/aks_terraform/01.png)

* Run the ```terraform apply --auto-approve``` command and wait for the plan to finish. 

Once the Terraform deployment is completed, a new AKS cluster in a new Azure Resource Group is created. 

![](../img/aks_terraform/02.png)

![](../img/aks_terraform/03.png)

![](../img/aks_terraform/04.png)

* Now that you have a running AKS cluster, edit the environment variables section in the included [az_connect_aks](../aks/terraform/scripts/az_connect_aks.sh) shell script.

![](../img/aks_terraform/05.png)

* In order to keep your local environment clean and untouched, we will use [Azure Cloud Shell](https://docs.microsoft.com/en-us/azure/cloud-shell/overview) (located in the top-right corner in the Azure portal) to run the *az_connect_aks* shell script against the AKS cluster. 

![](../img/aks_terraform/06.png)

* Upload the *az_connect_aks* shell script and run it using the ```. ./az_connect_aks``` command.

**Note**: The extra dot is due to the script has an *export* function and needs to have the vars exported in the same shell session as the rest of the commands. 

![](../img/aks_terraform/07.png)

![](../img/aks_terraform/08.png)

![](../img/aks_terraform/09.png)

![](../img/aks_terraform/10.png)

* Once the script run has finished, the AKS cluster will be projected as a new Azure Arc cluster resource.

![](../img/aks_terraform/11.png)

![](../img/aks_terraform/12.png)

# Delete the deployment

The most straightforward way is to delete the Azure Arc cluster resource via the Azure Portal, just select the cluster and delete it. 

![](../img/aks_terraform/13.png)

If you want to nuke the entire environment, delete both the AKS and the AKS resources Resource Groups .

![](../img/aks_terraform/14.png)

![](../img/aks_terraform/15.png)