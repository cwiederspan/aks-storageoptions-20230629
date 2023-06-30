# AKS Storage Options

A repo for testing some AKS-related storage options.

```bash

# Setup some variables to use
LOCATION=westus3

# Repeat for both SOURCE_NAME and TARGET_NAME
BASE_NAME=cdw-akstesting-20230629

# Create a variable for the storage account name
STORAGE_NAME=cdwakstesting20230629

# Create the resource group
az group create -l $LOCATION -n $BASE_NAME

# Create the cluster
az aks create \
-g $BASE_NAME \
-n $BASE_NAME \
--generate-ssh-keys \
--node-count 1 \
--network-plugin azure \
--enable-blob-driver

# Get the name of the node resource group
NODE_RG=$(az aks show -g $BASE_NAME -n $BASE_NAME --query nodeResourceGroup -o tsv)

# Create the storage account
az storage account create \
-g $NODE_RG \
-n $STORAGE_NAME \
-l $LOCATION \
--sku Standard_LRS

# Get the credentials for accessing the kubernetes cluster
az aks get-credentials -g $BASE_NAME -n $BASE_NAME

# Create the persistent volume
k apply -f pv-blob-nfs.yaml 

# Create the persistent volume claim
k apply -f pvc-blob-nfs.yaml


```
