# Start with Terraform and Azure

Below, a step by step guide to start with Azure and terraform.  
initilaizing the providerusing an azure storage as a safe backend for terraform state storage.  
using the terrform offocial image to run terraform command.  

## prerequisites

To follow this step-by-step, you'll need:
- an azure account (you can start for free)
- a bash command line (WSL is more than ok)
- some tools installed: Azure cli (command line tools for azure), jq (json grepping made easy)
  
## connect with you user to your azure subscription

```
AZ_SUBSCRIPTION=frenchtoast-test-001

az login
az account set --subscription $AZ_SUBSCRIPTION

AZ_SUBSCRIPTION_ID=$(az account show | jq -r .id)
AZ_TENANT_ID=$(az account show | jq -r .homeTenantId)
AZ_REGION=northeurope

TF_SP_NAME=tf_sp_name

az ad sp create-for-rbac --name $TF_SP_NAME --role Contributor --scopes /subscriptions/$AZ_SUBSCRIPTION_ID > sp.output

export ARM_SUBSCRIPTION_ID=$AZ_SUBSCRIPTION_ID
export ARM_TENANT_ID=$AZ_TENANT_ID
export ARM_CLIENT_ID=$(cat sp.output | jq -r .appId) 
export ARM_CLIENT_SECRET=$(cat sp.output | jq -r .password)
```

## create the backend

```
export TF_RESOURCE_GROUP_NAME=tfstate
export TF_STORAGE_ACCOUNT_NAME=tfstate$RANDOM
export TF_CONTAINER_NAME=tfstate
```

## Create resource group storage account and add a lock for no accidental deletion

```
az group create --name $TF_RESOURCE_GROUP_NAME --location $AZ_REGION
 
az storage account create --resource-group $TF_RESOURCE_GROUP_NAME --name $TF_STORAGE_ACCOUNT_NAME --sku Standard_LRS --encryption-services blob
az lock create --name ${TF_RESOURCE_GROUP_NAME}DoNotDelete --resource-group $TF_RESOURCE_GROUP_NAME --lock-type CanNotDelete
```

## Create blob container
`az storage container create --name $TF_CONTAINER_NAME --account-name $TF_STORAGE_ACCOUNT_NAME`


## store the STORAGE KEY
```
ACCOUNT_KEY=$(az storage account keys list --resource-group $TF_RESOURCE_GROUP_NAME --account-name $TF_STORAGE_ACCOUNT_NAME --query '[0].value' -o tsv)
export ARM_ACCESS_KEY=$ACCOUNT_KEY
```
## check all vars before starting
```
printenv | grep ARM_
```

## use terraform without install in your directory
```
alias terraform='docker run --
rm -it -w /tfwork -v $PWD:/tfwork \
 -e ARM_SUBSCRIPTION_ID=$ARM_SUBSCRIPTION_ID \
 -e ARM_TENANT_ID=$ARM_TENANT_ID \
 -e ARM_CLIENT_ID=$ARM_CLIENT_ID \
 -e ARM_CLIENT_SECRET=$ARM_CLIENT_SECRET \
 -e ARM_ACCESS_KEY=$ARM_ACCESS_KEY hashicorp/terraform:1.7'
 
terraform init -upgrade
terraform plan -out step.tfplan
terraform apply step.tfplan
```

# references:
- [quistart for terraform with Azure provider](https://learn.microsoft.com/en-us/azure/developer/terraform/create-resource-group?tabs=azure-cli)
- [create the backend](https://learn.microsoft.com/fr-fr/azure/developer/terraform/store-state-in-azure-storage?tabs=azure-cli)
