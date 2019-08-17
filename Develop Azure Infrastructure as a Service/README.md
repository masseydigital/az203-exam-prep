# Develop Azure Infrastructure as a Service Computer Solution (10-15%)

## Implement Solutions that use Virtual Machines

### Provision VMs
Azure VMs are an on-demand, scalable, cloud computing resource.  They're similar to virtual machines that are hosted in Windows Hyper-V.  They include processor, memory, storage, and networking resources.  VMs can be defined and deployed on Azure in several ways: Azure portal, a script (Azure CLI or Azure Powershell), or thorugh an Azure Resource Manager template.  Azure provides different plans or different VM levels of varying performance.

When creating a Windows VM in Azure, you also create resources to host the VM.  These resources work together to virtualize a computer and run the Windows operating system. 
1) A virtual machine that provides CPU and memory resources
2) An Azure Storage account to hold the virtual hard disks
3) Virtual disks to hold the OS, applications, and data
4) Virtual network (VNet) to connect the VM to other Azure Services or your own on-premises hardware.
5) A network interface to communicate with the VNet
6) A public IP address so you can access the VM (optional)

Virtual Networks (VNets) are used in Azure to provide private connectivity between Azure Virtual Machines and other Azure services.  VMs and other services that are part of the same virtual network can access one another.  It is important to select a range of IP addresses for your network that will not overlap with others in your organization.  There is no security boundary between subnets in a VNet, however, you can set-up Network Security Groups (NSGs) to act as software firewalls at the Network Interface and subnet level.

Some good questions to answer before deploying a VM are:
1) What does the server communicate with?
2) Which ports are open?
3) Which OS is used?
4) HOw much disk space is in use?
5) What kind of data does this use?  Are there restrictions with not having it on-prem
6) What sort of CPU, memory, and disk I/O load does the server have?  Is there burst traffic to account for?

A good naming convention for an Azure resource may include: 
1) Enviornment : dev, prod, QA
2) Location : uw (US West)
3) Instance : 01, 02
4) Product or Service : service
5) Role : sql, web, messaging

i.e. devusc-webvm01 : might represent the first development web server hosted in the US Souther Central location

### Create ARM Templates
Azure Resource Manager (ARM) Templates allow you to define resources to deploy for a job.  They are defined as JSON files which list the resources.

Azure Powershell can be used to automate repetitive tasks such as generating infrastructure from ARM templates.  Azure Powershell is an optional add-on package that adds Azure-specific commands (referred to as cmdlets).  i.e. the New-AzVM cmdlet will create a new virtual machine.

```powershell
New-AzVm `
    -ResourceGroupName "TestResourceGroup" `
    -Name "test-wp1-eus-vm" `
    -Location "East US" `
    -VirtualNetworkName "test-wp1-eus-network" `
    -SubnetName "default" `
    -SecurityGroupName "test-wp1-eus-nsg" `
    -PublicIpAddressName "test-wp1-eus-pubip" `
    -OpenPorts 80,3389
```

Another option for scripting and command-line Azure interaction is the Azure CLI.  It is Microsoft's cross-platform command-line tool for managing Azure resources such as virtual machines and disks from the command line.  You can use it on macOS, Linux, and Windows, or in the browser using Cloud Shell.

You an create an Azure VM with the _az vm create_ command.

```powershell
az vm create \
    --resource-group TestResourceGroup \
    --name test-wp1-eus-vm \
    --image win2016datacenter \
    --admin-username jonc \
    --admin-password aReallyGoodPasswordHere
```

The Azure REST Api provides developers with operations categorized by resource as well as the ability to create and manage VMs.

The Azure Client SDK encapsulates the Azure REST Api to allow for developers to manage Azure resources with a variety of programming languages.

An ARM template can contain the following sections:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "",
    "parameters": {  },
    "variables": {  },
    "functions": [  ],
    "resources": [  ],
    "outputs": {  }
}
```

1) Parameters: Where you specify which values are configurable when the template runs.  For example, you might allow users of your template to specify a username, password, or domain name.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "Username for the Virtual Machine."
    }
  },
  "adminPassword": {
    "type": "securestring",
    "metadata": {
      "description": "Password for the Virtual Machine."
    }
  }
}
```

2) Variables : This is where you define values that are used throughout the template.  This makes it easier to configure changes in values used throughout your template.

```json
"variables": {
  "nicName": "myVMNic",
  "addressPrefix": "10.0.0.0/16",
  "subnetName": "Subnet",
  "subnetPrefix": "10.0.0.0/24",
  "publicIPAddressName": "myPublicIP",
  "virtualNetworkName": "MyVNET"
}
```
3) Functions : Define procedures that you don't want to have to re-write throughout your template.

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

4) Resources : This is where you define the Azure resources that make up your deployment.  Note in the example below that apiVersion is ensuring that you are using the correct api that you are expecting in your template.

```json
{
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[variables('publicIPAddressName')]",
  "location": "[parameters('location')]",
  "apiVersion": "2018-08-01",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsLabelPrefix')]"
    }
  }
}
```

5) Output : This is where you define any information you'd like to receive when the template runs.  For example, you might want to receive your VM's IP address or FQDN.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
  }
}
```

**VSCode has an Azure Resource Manager Tools extension to help write ARM templates.**

Azure Quickstart templates are Resource Manager templates that are provided by the Azure community - many available on Github.

You can use the Azure Resource Manager Visualizer to see how your ARM template is going to arrange resources, before deploying to Azure.

You can also run a test deployment within Azure CLI or Azure Powershell to test out what would happen during your deployment.  

To create a resource group in Azure CLI, you can use the _az group create_ command with a --name and --location parameters.

You can validate a deployment in the Azure CLI with the following command:
```powershell
az group deployment validate \
  --resource-group [sandbox resource group name] \
  --template-file azuredeploy.json \
  --parameters adminUsername=$USERNAME \
  --parameters adminPassword=$PASSWORD \
  --parameters dnsLabelPrefix=$DNS_LABEL_PREFIX
```

### Configure Azure Disk Encryption for VMs

## Implement Batch Jobs by using Azure Batch Services

### Manage Batch Jobs by using Batch Service API
Azure Batch is intended to make flexible and scalable compute workflows.  The workflows can perform complex calculations and parallelize them to complete them faster.  Azure Batch itself is free, you only need to pay for the services that are spun up by it.

All Azure Batch actions must occur inside an Azure Batch account.  You can provision a storage account to manage file inputs and applications during execution.  Within your Azure Batch account, you can create pools of virtual compute nodes which are managed and scheduled by Azure Batch service.

The Azure Batch service manages the orchestration of a job that is to be completed by Azure Batch.  It will bring online the required nodes and schedule the tasks that need to run on them.  You will use the Azure CLI to create a pool, create worker nodes, create a job, and create tasks.

### Run a Batch Job by Using Azure CLI, Azure Portal, and other tools
Azure Batch command take the following form:
az batch verb --properties

You can cretae an Azure Batch account with the following command:

```powershell
az batch account create \
 --name $BATCH_ACCOUNT \
 --resource-group $RESOURCE_GROUP \
 --location <choose a location from the list above>
```

You can sign-in to your Azure Batch account with the following command: 

```powershell
az batch account login \
 --name $BATCH_ACCOUNT \
 --resource-group $RESOURCE_GROUP \
 --shared-key-auth
```

You can create a pool of VMs with the following command:

```powershell
az batch pool create \
 --id mypool --vm-size Standard_A1_v2 \
 --target-dedicated-nodes 3 \
 --image canonical:ubuntuserver:16.04-LTS \
 --node-agent-sku-id "batch.node.ubuntu 16.04"
```

You can create a batch job with the following command : 

```powershell
az batch job create \
 --id myjob \
 --pool-id mypool
```

You can create a batch task with the following command:

```powershell
for i in {1..10}
do
   az batch task create \
    --task-id mytask$i \
    --job-id myjob \
    --command-line "/bin/bash -c 'echo \$(printenv | grep \AZ_BATCH_TASK_ID) processed by; echo \$(printenv | grep \AZ_BATCH_NODE_ID)'"
done
```

To delete a batch job : 

```powershell
az batch job delete --job-id myjob -y
```

To view the status of a job : 
```powershell
az batch task show \
 --job-id myjob2 \
 --task-id mytask1
```

You can visualize how jobs are running by downloading and installing Batch Explorer.

### Write Code to Run an Azure Batch Services Batch Job

An Azure Batch account contains the following resources: 
1) Application Package: Used to add applications that can be used by tasks in a Batch.  An Azure Batch account can contain up to 20 application packages.
2) Pool: Contains compute nodes, which are the engines that run your Batch job.  
3) Node: Each node can be assigned a number of tasks to run, the tasks are allocated and managed by Azure Batch.
4) Job: Jobs manage collections of tasks.  A job is associated with a specific pool.  An Azure Batch account can have many jobs.
5) Task: Tasks run applications.  These can be contained in an application package, o rin an Azure Storage container.  Tasks process input files, an don completion can write to output containers.

Before you manage the Azure Batch services from a .Net application, you have to create the Azure Batch account and Storage account.  You'll need to create a .Net Core console app in the Cloud Shell that uses Azure Batch and Azure Storage client libraries.

There are two NuGet packages you'll need to import into your app.  The first is the Azure Batch client library, Microsoft.Azure.Batch.  You'll use this library to create and delete Azure Batch Pools, create and delete workload jobs, and create and delete tasks, and monitor running tasks.  The next one that you'll need is the Azure Storage client library, Microsoft.Azure.Storage.Blob, which allows you to connect to, and manage, files in an Azure Storage account.  Finally, you will need The Azure Batch Management library, Microsoft.Azure.Management.Batch to manually have your app communicate with your manually created Batch and Storage accounts.

The typical usage pattern for Azure Batch is:
1) Create a Batch Service account (Batch Management Api)
2) Create a Storage Account (Storage API)
3) Create a Blob client to manage file processing (Storage API)
4) Upload Files to Process (Storage Api)
5) Create a pool of compute nodes (Batch Api)
6) Create a job to run on those nodes (Batch Api)
7) Add a task to the job to run (Batch Api)
8) Monitor the tasks progress (Batch Api)
9) Download processed files when finished (Storage Api)
10) Delete the input storage container, delete the pool, delete the job (Batch Api & Storage Api)

## Create Containerized Solutions

### Create an Azure Managed Kubernetes Service (AKS) Cluster

### Create Container Images for Solutions

### Publish an image to the Azure Container Registry

### Run containers by using Azure Container Instance or AKS