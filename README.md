# AZ-203 Exam Prep Course Notes
This is my course notes repo for AZ-203 Developing Solutions for Microsoft Azure Exam Prep.

## Virtual Machines
A _Virtual Machine (VM)_ is essentially an instance of a computer that includes its own memory, ram, OS, etc.  Infrastructure as a Service (IAAS).  Can use SSH or RDP to control remotely.  Each Azure VM has its own pricing plan which bases with per second pricing, and also has yearly pricing plans.  Some high performance VM's are blocked by default to protect the user from purchasing something that costs a lot of money.

Every resource that you create must live in a _resource group_.  Resource groups allow you to bundle resources into similar functions, and helps manage cost planning.

By default, VM's block public communication on their ports.  Users are able to allow selected ports to be open based on their funtionality.

You can create _ARM templates_ (JSON files) that can be stored in Azure or downloaded to machine to expedite the creation process for VM's and other Azure resources.

You can configure a DNS name to map to your public ip address.

To connect as a web server through public ip address, go to Connect in the toolbar.  For windows, you will connect through RDP and for linux you will connect through SSH.  RDP is port 3389.  You can download an RDP file to download and connect through RDP to your VM.

The files in an Azure storage account are by default encrypted _secure storage encryption_.  The VHD itself is not encrypted though, but you can encrypt with Bitlocker.  Keys can be stored in a _Key Vault_ azure resource.  Key lockers can store Keys, Secrets, and Certificates.  Encryption can only be done with standard resources and above.

The _Azure Cloud Shell_ allows you to run commands in bash or powershell within the browser.  To run Azure Cloud Shell, you need to have a storage account.

## Azure Batch
A batch job is any job that needs to be run multiple times.  i.e. simulations, weather predictions, rendering a movie, etc.  An Azure batch job will manage creating resources such as VM's to complete the job.  To use Azure Batch, you need an Azure Batch account which is separate from your Azure Subscription.  

[Azure Batch Samples](https://github.com/Azure-Samples/azure-batch-samples)

The settings json file in the project tells Azure what resources to use for the batch, and the account settings json file tells Azure what account settings to use.

_Batch Explorer_ allows you to visualize batch jobs running.

## Azure Containerized Solutions / Azure Kubernetes Services (AKS)
_Containers_ are a mid-way point between IAAS and PAAS.  It is a standard unit of software that packages up code and all of its dependencies so that the application can run quickly between different computing environments.  

[Kubernetes](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/) is a portable, extensible, open-source platform for managing containerized workloads and services that was created by Google in 2015.  Kubernetes is consistent across all cloud platforms inlcluding Azure, AWS, and Google Cloud.  Creating a Kubernetes cluster in Azure is similar to creating a VM in Azure.

_Azure Kubernetes Service (AKS)_ is Azure's Kubernetes service that you can build.  Because it uses clustering, you will have an orchestration node, and multiple worker nodes.  

You can use cloud shell to connect to your created Kubernetes cluster.  To start, you must set your credentials for your cluster using the _get-credentials_ command.  You can use the _kubectl_ command to interact with your kubernetes cluster.  You can deploy code to your cluster by using a _yaml_ file.  The _kubectl get nodes_ command can be used to see the status of your nodes. The _kubectl get service_ command can be used to see what services are being created for your cluster.

_Kubernetes dashboard_ can be installed locally to visualize your Kubernetes cluster.  It requires [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) to be downloaded and installed.  Once you install the CLI, you should have access to the _az_ keyword.  You can download the aks cli using the command _az aks install-cli_.  The Kubernetes dashboard runs on a local web server on your machine.

To create _Docker_ containers you need to install Docker onto your local machine.  [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/) will allow us to do just that.  Docker containers run in a virtual box. You can use the _docker images_ command to view the images that are in your docker container.

The _Container Registry_ resource in Azure is a place that you can store containers which users can pull containers from (similar to how Github holds code).  They have different SKUs that have different capabilities for respective prices.  To tag containers for your container registry, you can use the _docker tag [app-name] [container registry name]/[app name]:[v#>_ command].  To push the docker image, you can use the _docker push [container registry name]/[app name]:[v#]_ command.

[Code Samples](https://github.com/Azure-Samples/aks-dotnet-manage-kubernetes-cluster)

## Azure App Service
Publishing web apps to Azure is very similar to publishing containers to Azure.  _Azure App Service_ is a service for hosting web applications on Azure.  Azure app service can utilize apps written in .Net, .Net Core, Java, Ruby, Node.js, PHP, or Python.  There is a difference between Windows and Linux apps.  The _Web App_ resource can be used to create web apps on Azure.  Names for web apps must be unique across Azure.  You can use domain names you've registered if you do not like the azure naming conventions.  

The _app service plan_ is similar to hosting plans that you would use on a website such as Wordpress.  There are 3 tiers; Dev / Test, Production, and Isolated with multliple selections under each. 

 The Dev/Test plans include:  _F1_ for 1 GB memory and 60 minutes/day compute.  This plan doesn't include any additional features.  The next plan up is _D1_ and has 1GB of memory and 240 minutes/day compute.  With this tier, you can also select a custom domain.  The next tier up is _B1_ and includes 1.75 GB memory, A-series compute and 100 total ACU.  Additional features include custom domain names with SNI SSL bindings and manual scale.

The Production Plans include: S1, P1V2, P2V2, P3V2, S2, S3, P1, P2, and P3.  The S in the plan name stands for standard and the P is for premium.  These plans include more features such as auto scale, staging slots, daily backups, traffic manager, etc.  Premium plans generally include more of the features.

The Isolated Plan includes: I1, I2, and I3.  Isolated plans run on your own hardware and your own network.  The additional features that you get are centered around security and control over the hardware of the app.  There is an additional charge for the hardware leasing.

_Azure Compute Unit (ACU)_: is a number that expresses the performance of a app service plan.  The numbers are proportional, i.e. a plan with 100 ACU is half as powerful as a plan with 200 ACU.

When you create a web app, three resources will be added to your resource group:  the app, the service plan, and application insights.  If you upload a new web app with no code, Microsoft has a default web page that will load when you go to the link url.  You can use FTP to deploy your code, and Microsoft Visual Studio has plugins to help deploy the code to Azure.

_Web Jobs_ are back end jobs that can be run in Azure.   In order for a web job to run, someone needs to go to your website.  Web jobs run even though users are not currently connected to your website.  Web jobs are essentially background processes.  They can be run based on _CRON_ expressions.

The _Monitoring_ settings of your web apps allow you collect data and troubleshoot issues when running your web app.  _Diagnostic Logs_ allow you to generate logs for your web application.  Some log files will require you to have a storage account.  When you create logs, you can select how long you hold onto the logs in Azure.  You can also stream logs by connecting to the log streaming service.

[Code Samples](https://github.com/Azure-Samples/app-service-web-dotnet-get-started)