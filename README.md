# AZ-700 Lab Environment

This readme contains the Azure CLI commands I used to create each lab environment. Many tutorials deploy resources the portal, which is a good way to learn about the options available during deployment.

I wanted to use the Azure CLI, so I can deploy and destroy resources quickly within my subscription. 

## Lab Environment via the portal
This video by Susanth Sutheesh describes how resources are deployed via the portal https://www.youtube.com/watch?v=DFdOi0QVso4  - I would recommend viewing this video, as part of your preparation for AZ-700. As a DevOps engineer, I have followed the excellant instructions in the youtube video and produced Azure CLI commands for each task.

## Virtual Networks

![Virtual Network](az700-vnet.png)


### Create Resource Group

```
$location="NorthEurope"
$resource_group_name="rg-az700-cli"

az group create --location $location --name $resource_group_name
```

### Create VNETs and subnets

```
# Deploy Research VNET
$vnet_name="ResearchVnet"
$location="SouthEastAsia"
$resource_group_name="rg-az700-cli"
$address_prefixes="10.40.0.0/16"
$subnet_name="ResearchSystemSubnet"
$subnet_prefixes="10.40.0.0/24"

az network vnet create --name $vnet_name `
   --resource-group $resource_group_name `
   --address-prefixes $address_prefixes `
   --subnet-name $subnet_name `
   --subnet-prefixes $subnet_prefixes `
   --location $location

# Deploy Core VNET
$vnet_name="CoreServicesVnet"
$location="EastUS"
$resource_group_name="rg-az700-cli"
$address_prefixes="10.20.0.0/16"
$subnet_name="GatewaySubnet"
$subnet_prefixes="10.20.0.0/27"

az network vnet create --name $vnet_name `
   --resource-group $resource_group_name `
   --address-prefixes $address_prefixes `
   --subnet-name $subnet_name `
   --subnet-prefixes $subnet_prefixes `
   --location $location

# Deploy Datbase Subnet
$subnet_name="DatabaseSubnet"
$subnet_prefixes="10.20.20.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name 

# Deploy Shared Services Subnet
$subnet_name="SharedServicesSubnet"
$subnet_prefixes="10.20.10.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name

# Deploy Public Web Services Subnet
$subnet_name="PublicWebServiceSubnet"
$subnet_prefixes="10.20.30.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name

# Deploy Manufacturing VNET
$vnet_name="ManufacturingVnet"
$location="WestEurope"
$resource_group_name="rg-az700-cli"
$address_prefixes="10.30.0.0/16"
$subnet_name="ManufacturingSystemSubnet"
$subnet_prefixes="10.30.10.0/24"

az network vnet create --name $vnet_name `
   --resource-group $resource_group_name `
   --address-prefixes $address_prefixes `
   --subnet-name $subnet_name `
   --subnet-prefixes $subnet_prefixes `
   --location $location

# Deploy SensorSubnet1
$subnet_name="SensorSubnet1"
$subnet_prefixes="10.30.20.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name

# Deploy SensorSubnet2
$subnet_name="SensorSubnet2"
$subnet_prefixes="10.30.21.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name

# Deploy SensorSubnet3
$subnet_name="SensorSubnet3"
$subnet_prefixes="10.30.22.0/24"

az network vnet subnet create --name $subnet_name `
   --address-prefixes $subnet_prefixes `
   --vnet-name $vnet_name `
   --resource-group $resource_group_name

```

### Create Private DNS Zone and Private Links

```
$resource_group_name="rg-az700-cli"
$dns_zone_name="Contoso.com"

# Note private DNS is a global service, no location is specified
az network private-dns zone create --resource-group $resource_group_name `
   --name $dns_zone_name

# Link core service vnet set to auto registration
$private_link_name="CoreServiceVnetLink"
$registration_enable='true'
$resource_group_name="rg-az700-cli"
$dns_zone_name="Contoso.com"
$vnet_name="CoreServicesVnet"

az network private-dns link vnet create --name $private_link_name `
    --registration-enable $registration_enable `
    --resource-group $resource_group_name `
    --virtual-network $vnet_name `
    --zone-name $dns_zone_name



# Link Manufacturing vnet to auto registration
$private_link_name="ManufacturingVnetLink"
$registration_enable='true'
$resource_group_name="rg-az700-cli"
$dns_zone_name="Contoso.com"
$vnet_name="ManufacturingVnet"

az network private-dns link vnet create --name $private_link_name `
    --registration-enable $registration_enable `
    --resource-group $resource_group_name `
    --virtual-network $vnet_name `
    --zone-name $dns_zone_name

# Link Research vnet to auto registration
$private_link_name="ResearchVnetLink"
$registration_enable='true'
$resource_group_name="rg-az700-cli"
$dns_zone_name="Contoso.com"
$vnet_name="ResearchVnet"

az network private-dns link vnet create --name $private_link_name `
    --registration-enable $registration_enable `
    --resource-group $resource_group_name `
    --virtual-network $vnet_name `
    --zone-name $dns_zone_name

```

### Create Virtual Machines to test connectivity

> These scripts use public ips, to be used in a lab environment. These vm creation scripts are not suitable for production environments

These scripts create linux virtual machines

> Note: These scripts rely on a ssh private and public key to be generated into your windows user directory. https://learn.microsoft.com/en-us/azure/virtual-machines/linux/mac-create-ssh-keys


```
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

### Create Virtual Machine in Manufacturing vnet to test global peering


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

### Create Global Peering between CoreServices and Manufacturing VNETs

```
$name="CoreServicesVnet-to-ManufacturingVnet"
$remote_vnet="ManufacturingVnet"
$resource_group_name="rg-az700-cli"
$vnet_name="CoreServicesVnet"

az network vnet peering create `
   --name $name `
   --remote-vnet $remote_vnet `
   --resource-group $resource_group_name `
   --vnet-name $vnet_name `
   --allow-forwarded-traffic `
   --allow-vnet-access

$name="ManufacturingVnet-to-CoreServicesVnet"
$remote_vnet="CoreServicesVnet"
$resource_group_name="rg-az700-cli"
$vnet_name="ManufacturingVnet"

az network vnet peering create `
   --name $name `
   --remote-vnet $remote_vnet `
   --resource-group $resource_group_name `
   --vnet-name $vnet_name `
   --allow-forwarded-traffic `
   --allow-vnet-access

```