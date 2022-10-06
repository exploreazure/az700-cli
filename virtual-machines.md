# Deploy Test Virtual Macines

## 1. Introduction
Labs 1 and 2 require virtual machines to create connectivity. This article provides the Azure CLI commands which deploy the virtual machines to each region.

## 2. Pre-Reqs
Each lab provides instructions on how to deploy virtual networks in each region. This task must be completed prior to deploying the virtual machines in this article.

## 3. Create Virtual Machines in East US Region

> These scripts use public ips, to be used in a lab environment. These virtual machine creation scripts are not suitable for production environments

These scripts create linux virtual machines

> Note: These scripts rely on a ssh private and public key to be generated into your windows user directory. https://learn.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys


```
# Deploy testvm1 in East US

$location="NorthEurope"
$resource_group_name="rg-az700-cli-vms"
az group create --location $location --name $resource_group_name

$vm_name="testvm1"
$resource_group_name="rg-az700-cli-vms"
$vnet_resource_group_name="rg-az700-cli"
$location="EastUS"
$admin_user="azureuser"
$ssh_key="~/.ssh/id_rsa.pub"
$vnet_name="CoreServicesVnet"
$subnet_name="DatabaseSubnet"

az vm create `
   --resource-group $resource_group_name `
   --name $vm_name `
   --image UbuntuLTS `
   --admin-username $admin_user `
   --ssh-key-value $ssh_key `
   --size Standard_B2s `
   --public-ip-sku Standard `
   --location $location `
   --subnet $(az network vnet subnet show -g $vnet_resource_group_name --vnet-name $vnet_name -n $subnet_name -o tsv --query id) `
   --nsg-rule SSH

# Deploy testvm2 in EastUS
$vm_name="testvm2"
$resource_group_name="rg-az700-cli-vms"
$vnet_resource_group_name="rg-az700-cli"
$location="EastUS"
$admin_user="azureuser"
$ssh_key="~/.ssh/id_rsa.pub"
$vnet_name="CoreServicesVnet"
$subnet_name="DatabaseSubnet"

az vm create `
   --resource-group $resource_group_name `
   --name $vm_name `
   --image UbuntuLTS `
   --admin-username $admin_user `
   --ssh-key-value $ssh_key `
   --size Standard_B2s `
   --public-ip-sku Standard `
   --location $location `
   --subnet $(az network vnet subnet show -g $vnet_resource_group_name --vnet-name $vnet_name -n $subnet_name -o tsv --query id) `
   --nsg-rule SSH

# to ssh to both vm use below command
# ssh -i '~/.ssh/id_rsa' azureuser@<publiciptestvm1>
# Open another terminal session
# ssh -i '~/.ssh/id_rsa' azureuser@<publiciptestvm2>
# from testvm1 ssh session ping testvm2.contoso.com
# from testvm2 ssh session ping testvm1.contoso.com
# both ping commands should return a reply
# This completes the task

```

## 4. Create Virtual Machine in West Europe Region

```
$vm_name="testvm3"
$resource_group_name="rg-az700-cli-vms"
$vnet_resource_group_name="rg-az700-cli"
$location="WestEurope"
$admin_user="azureuser"
$ssh_key="~/.ssh/id_rsa.pub"
$vnet_name="ManufacturingVnet"
$subnet_name="ManufacturingSystemSubnet"

az vm create `
   --resource-group $resource_group_name `
   --name $vm_name `
   --image UbuntuLTS `
   --admin-username $admin_user `
   --ssh-key-value $ssh_key `
   --size Standard_B2s `
   --public-ip-sku Standard `
   --location $location `
   --subnet $(az network vnet subnet show -g $vnet_resource_group_name --vnet-name $vnet_name -n $subnet_name -o tsv --query id) `
   --nsg-rule SSH

# to ssh to both vm use below command
# ssh -i '~/.ssh/id_rsa' azureuser@<publiciptestvm3>

```

## Conclusion

You have now deployed virtual machines in both East US and West Europe using Azure CLI. To remotely connect to each virtual machine, use the following command:

```
ssh -i '~/.ssh/id_rsa' azureuser@<publicipvirtualmachine>
```