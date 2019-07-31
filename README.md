# AZ-203 Exam Prep Course Notes
This is my course notes repo for AZ-203 Developing Solutions for Microsoft Azure Exam Prep.  The structure of this repository is all of the course notes located inside of this repo for the Udemy course and then each section of the exam will have examples where I implemented a solution to meet the objective.  The solutions will either be custom solutions related to projects that I'm interested in, or solutions that Microsoft, or another third-party has suggested.

The sections of the Exam are:
1) Develop Azure Infrastructure as a Service (10-15%)
2) Develop Azure Platform as a Service Compute Solutions (20-25%)
3) Develop for Azure Storage (15-20%)
4) Implement Azure Security (10-15%)
5) Monitor, Troubleshoot, and Optimize Solutions (10-15%)
6) Connect to and Consume Azure adn Third-Party Services (20-25%)

# Udemy Course Notes

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

[Code Samples](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts)

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

## Azure Mobile Apps
Mobile apps in the context of Azure is the backend services that the front end applications on the phone need to use.  This also includes _notification hubs_.  Notification hubs you _notification workspaces_.  

There are 3 tiers of pricing tiers for mobile notifications.  F1 is free, up to 1M pushes and can be used to 500 devices.  B1 allows 10M pushes and 200k Active Devices.  S1 has 10M pushes and 10M active devices.  The S1 tier also has a lot of extra included features including telemetry, bulk operations, scheduled pushes, and multi-tenancy.  

The _Notification Hub Dash_ allows you to visualize the mobile notifications you are managing.  You can modify settings for different manufacturers including Apple, Google, Windows, Windows Phone, Amazon, and Baidu.

There is a Mobile App service that is provided on Azure.  Mobile apps are very similar to web apps, but also include a mobile app section.  This section includes things such as databases, apis, and tables.  

[Code Samples](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications)

## API Apps
Api apps are created the same way as web apps.  API are web apps that don't have a user interface.  There are two features that distinguish api apps: Cross-Origin-Resource- Sharing (CORS) and API definitions.  CORS limits access to who can use the API.  SwashBuckle and Swagger allow you to build API documentation for your api. You can post the url/swagger to the API definition to tie the API defintion to the App Service.

[Code Samples](https://github.com/Azure-Samples/dotnet-core-api)

## Function Apps
Functions apps have the choice between app service plans and consumption plans.  The app service plan is similar to the previous app service plan.  The consumption plan means that you are only charged when the function is run.  App service plans must run on the same OS, i.e. a Windows plan will not work with a Linux plan.  For the windows plan you can run .Net, Python, and Java.  Functions need a storage account to be used.

When you create functions, you can use Visual studio, VS Code, any other edtor, or in-portal.  The most common hooks for function apps is Webhook + API, Timer, and other templates.  Every function must have 1 trigger and can only have 1 trigger.  You can also select the inputs and outputs for the function.  An example of an output is a blob containing a string.

_Durable Functions_ are functions which are able to perform more complex functionality.  With durable functions you can create chains of functions that are more complicated than normal chains of functions.  There are different application patterns that can be used to design durable functions.  Some of these include Chaining, Fan-Out/Fan-In, Async Http APIs, Monitoring, and Human Interaction.

Azure functions are also an option in Visual Studio.  The benefit of using visual studio instead of the in-app editor is that you can use source control and other code management to share and keep your code up.  Once you complete your azure function in visual studio you can publish it.

[Code Samples](https://github.com/Azure-Samples/durablefunctions-apiscraping-dotnet)

## Azure Storage Accounts
_Azure storage accounts_ store data inside of Azure.  Many resources available in Azure will hvae options for creating storage accounts in their options.  The name of your storage account must be unique across all of Azure.  It is best practice to store files near your targeted users when selecting a location for your storage account.  StorageV2 only support _Azure Resource Manager_ data model.  Azure also supports the older storage model. 

 By default you get 3 copies of your storage in a _locally-redundant storage_.  _Zone-redundant storage_ will store 3 copies in your zone.  Both of these tiers are free by default.  _Geo-redundant storage_ stores your data across geolocations for an additional price.  _Read-access geo-redundant storage_ functions similarly to geo-redundant storage, but is read-access.

 The access tier is either _cool_ or _hot_.  Hot storage means that you are implementing standard read/write and are able to access it instantly.  With cool storage, you are guaranteeing that you will use the storage for 30 days, but you don't need instant access to the data.  It has higher cost for access and lower cost for storage.  There is a also a third option called _archive_ that can be used in applications to store data which will not be accessed for 180 days.

 You can add additional security to a storage account by putting it on a virtual network that can only be accessed by other resources on the network.

 _Tagging_ allows you to assign values to resources which group resources into logical groupings.

 There are 4 services available in a standard storage account: Blobs, Files, Tables, and Queues.
 Blobs: REST-based object storage for unstructured data
 Files: Files shares that use the standard SMB 3.0 protocol
 Tables: Tabular data storage
 Queues: FIFO data type that scales app according to traffic

 Storage tables contain tabular data that can be accessed by url.  To start working with tables, use the _Nuget Package Manager_ to download the WindowsAzure.Storage package.  You can use functions inside of the package to create and commit tables to Azure.  Once you've committed data you can build queries to get data from your table.

 [Code Samples](https://github.com/Azure-Samples/storage-table-dotnet-getting-started)

 ## CosmosDB
_CosmosDB_ is a fully managed, globally distributed, multi=model database service.  Azure's CosmosDB provides an enterprise grade Table experience.  It scales globally for services, and is recommended over a storage account for Enterprise level apps.  To use CosmosDB you must create a Database account which is unique across Azure.  CosmosDB has it's own SLA which in the tutorial is a 99.999 SLA.  

The API that you choose for your account determines how your data is stored.  Some data formats are SQL, MongoDB, Cassandra, Azure Table, and Gremlin (graph).  You can select options for geo-redundancy and multi-region writes.  You can set your CosmosDB to be on a Virtual Network to determine which resources have access to your CosmosDB database.

Pricing for CosmsoDB includes how much storage you are using and how often you are accessing the data.

The quick start tab for CosmosDB will help you easily start creating a new resource.  The Collections section allows you to add new collections to your resource.  When you add a new collection, you can limit throughput by checking the provision database throughput, and setting a level that you are comfortable with.  Throughput can also be set at the collection level.

To get started with developing for CosmosDB in Visual Studio, use the Nuget Package Manager to install Microsoft.Azure.[databaseType] where databaseType is the type of database that was selected when the database was created in Azure (i.e. for SQL, select Microsoft.Azure.DocumentDB).  Browsing ComsoDB in NPM will show you other Third Party libraries to help you manage your CosmosDB more efficiently.

_Data Explorer_ inside CosmosDB allows you to visualize your table data.  When you uploade Table Data to CosmosDB, it will attach several additional fields to your data entry: id, _rid, _self, _etag, _attachments, and _ts.

[Code Samples](https://github.com/Azure-Samples/dotnet-cosmosdb-quickstart)

## Consistency
_Consistency_ in data for Azure describes the order and the timing that data is available to users.  There are different levels that you can choose for your specific application.  Consistency is available for CosmosDB, but not regular storage accounts.

_Strong:_ means that data replication happens near instantaneously on all regions servers.  A downside of this form of consistency is that you have to wait for data to be replicated before being able to read it.

_Boundless Staleness:_ introduces a delay from the host region to other secondary regions.  It is recommended for applications featuring group collaboration and sharing, stock ticker, publish-subscribe queueing, etc.

_Session:_ depending on the session of the user, they will be guaranteed consistency.  This means that certain regions may be introduced a delay.

_Consistent Prefix:_ data will be displayed in the right order, but not necesarily with the same lag.

_Eventual:_ This is the weakest consistency.  There is no guarantee on order.  An example of this is retweets, likes, or non-threaded comments.

## Azure SQL Database
There are a number of options for creating SQL Databases within Azure.  _Azure SQL Database_ is the recommended SQL database for many common workflows in Azure.  SQL Server 2017 with Windows Server 2016 running on a VM is another option which gives you much more control over the hardware.  _SQL Data Warehouse_ is an option if you have _Data Lake_ sizes of data that you need to manage.  There are also several Software as a Service (SAAS) database solutions available in Azure including MySQL, PostgresSQL, and MariaDB.

An Azure SQL database has a database server and the database itself.  You have the option of pre-loading data into a new database such as the AdventureWorksLT sample database.  You have the option of backing up your database.  Your database must live in a server.  You have the option of creating a new server if you do not have one already.  The server name has the format _[Server Name].database.windows.net_.  You must create an admin username and password for your server, and select a region that the database will live in.

An _elastic pool_ is an option if you have multiple databases with similar functionality.  I.e. 20 databases that share a central resource server.  

_Pricing_ for an SQL database is based on performance and size, or based on vCore performance.  The three options are Basic (5 DTU, 2 GB max), Standard (10-3000 DTU, 250 GB max), and Premium (125-4000 DTU, 1 TB max) and is matched to a _Database Transaction Unit (DTU)_.  The premium plan also includes additional options.  DTU's are linear (100 DTU's is 100x better performance).

_VCore Performance_ pricing is based on number of cores and performance as well as Provisioned or Serverless technology.  There are three options for VCore pricing: General Purpose, HyperScale, and Business Critical.  There are options for Gen4 or Gen5 cores, number of vCores, and Max Data Size.  Gen4 allows 1-24 vCores, and 1GB-1TB of storage.  Gen5 allows for 2-80 vCores, and 1GB-4TB of data.  _Serverless_ resources are auto-scaled to your usage, and only work with Gen5.  Hyperscale purchasing also includes the number of replicas that you would like created.

The _Query Editor_ allows you to visualize data that is in your SQL database.  It contains an inline interface that you can use to develop queries for your database.

**For backup servers, you need to have another server account in that region.  Azure will handle the replication between servers in different regions.**

You can select your servers firewall by selecting _Show firewall settings_ on the overview tab of your server.  Doing so will bring your the firewalls configuration screen.  The default firewall allows services from Azure to access, and requires you to white list any other additional ips.  You can add your local ip by clicking Add Client IP in the top toolbar.  This is required for you to be able to access the server/databases locally.

The _SQL Server Management Studio (SSMS)_ is a management software that helps you access/modify databases running in the cloud.  You can create new Tables and Queries using this software.  Logging into your server through Azure should reflect SSMS updates after you make and commit them.

The namespace in Visual Studio for SQL Database is _System.Data.SqlClient_.  The connection string for connecting to the SQL server is located under the settings section, Connection String option.  There are different options for the connection type including ADO.Net, JDBC, ODBC, and PHP.  There are fields in the connection string that you will have to enter including {username} and {password}.  You can run SQL commands against your database by using the SQLCommand method, passing in your command string and connection.

[Code Samples](https://github.com/Azure-Samples/dotnet-sqldb-tutorial)

## Blob Containers
_Blobs_ are REST-based object storage for unstructured data.  They are basically a container that you can throw files into.

There are three access levels that you can give blob containers: Private, Blob, and Container.  

Hitting the _Upload_ button will give you the option to upload a file to the container.  You can set the authentication type when you upload.  You can also chnage the blob type, and upload images to a folder (this folder is a virtual folder).  

To manipulate the blob storage files, you can download _AzCopy_.  AzCopy provides an api for server side copying in the command line.  _AzCopy /?_ gives you examples of all of the commands that you can use. The first parameter of AzCopy is /Source -- you can get the source url from the resource.  The _/Dest_ command is the location that you want to place the file. The _/Pattern_ command is the pattern that you want to copy files from.  You can use keys or Secure Access Signatures to access blob containers.  The _/SourceKey_ is the source authentication key used for your container.  The _/DestKey_ is the destination key for the destination location.   _/SyncCopy_ copies an image locally first before uploading it to the destination.  

_Acquire Lease_ is a lock on the blob container.  You must provide the _Lease ID_ to access files in a leased blob container.  Leased files cannot be deleted until Break Lease is clicked or the api calls the breaking command.

_Storage Access Tiers_ change how a file can be downloaded.  A Hot access type means the file is immediately available.  A Cool access type means that the file is not immediately available.  i.e. for backups.  Changing your access tier changes your cost.  Making containers cool (30 days) makes the storage cheaper and the access more expensive.  Archive access tier is even longer term storage (180 days).  Hot storage is about twice as expensive as cool, but half as much more for write operations than cool or archive.  Read operations are about half as much for hot items and a large amount more for archived access. Data retrieval is free for hot items, $0.01/GB for cool and $0.02 for archived items.  

[Code Samples](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started)

## Azure Authentication
The central service that deals with authentication and identity is _Azure Active Directory_.  _Active Directory_ is a directory service that Microsoft developed for Windows domain networks.   

_Azure AD Connect_ can be used to sync an on-premise active directory with an Azure Active Directory.  You can use a mixture of synced and non-synced resources to meet your needs.  

To create a new Active Directory account, go to Create a Resource, and search Active Directory.  Once you find Azure Active Directory, you will be taken to a Create directory screen.  The options that you have to fill out are Organization Name, Initial Domain Name, and the Country or Region.  The default domain name is [doman name].onmicrosoft.com.

You can change your active directory by going to the account icon in the upper right and selecting your active directory account.  When you select it, it will look like a new Azure account in the portal.

[AzureADQuickStarts Github](https://github.com/AzureADQuickStarts/)

**To use active directories in a web app, you must change it from a single tenant application to a multi-tenant application.**
**The above comment is not longer true for the new Azure stack.  Instead to follow the tutorial, go to authentication and change the supported account type to Acconts in any organizational directory

Azure also supports multi-factor authentication by default. When you click the sign-in button on the test app after enabling multi-factor authentication, you will get a page asking for an additional authentication method to ensure that the user is who they say they are.

[Code Samples](https://github.com/Azure-Samples/active-directory-dotnet-native-desktop)

## Azure Access Control
Rule Based Access Control (RBAC): Controls access to resources through a set of rules.

Best practice is to give members the minimum access they need to perform their job function.

In Azure, access control is managed through the Acess Control (IAM) resource.

You can add roles through the Add role assignment option.  There are dozens of different options for roles that users can be assigned to.  Owner is the highest level of access control.  A lot of resources in Azure have their own specific permissions.  I.e. SQL DB Contributor.  You can also create your own custom roles.

_Shared Access Signatures_ are URIs that grant access to resources without needing to create an account on Azure Active Directory.  Azure provides two keys to ensure that your applications can run at any time.  There are several options avaialble for SASs including the storage type, the level of access, and the different abilities the user has i.e. read, write, delete, etc.  Once all options are entered, you can generate a SAS and a connection string.  This will generate a SAS token.

## Secure Data
Azure storage is encrypted by default at REST through Secure Service Encryption.  You cannot turn off Encryption, but you can use your own keys.  If you transport your file to another application and out of Azure you will be transferring the file unencrypted.  You can encrypt on transport by going to the Configuration tab in Settings.

Servers are encrypted wtih _Transparent Data Encryption_ setting.  Like with storage, you can choose to use your own encryption key.  At the database level, they are also encrypted with Transparent Data Encryption.  Databases have the option to not be encrypted.

_Azure Key Vault_ allows you to create and store keys for the security of your data.  There are two plans that can be selected for Azure Key Vaults: Standard and Premium.  The premium version uses a hardware solution to generate keys.  You can restrict key vaults to virtual networks.  Key vaults can store keys, certificates, and certificates.  You can version your keys to maintain an archive of keys, secreate, and certificates.  You can call these credentials through your .Net code.

[Code Samples](https://github.com/Azure-Samples/key-vault-dotnet-manage-key-vaults)

## Scaling Apps & Services
_Scaling_ refers to an application changing the amount of resources available based on user or statistical demand.  This can happen both manually and automatically.  To scale automatically, you cannot use the Basic plan.

_Scale Up/Down_ means moving up or down to a more expensive or less expensive plan.  

_Scaling Out_ referes to adding more instances of a resources to solve demand.

_Autoscaling_ allows you to scale based on a set of rules set in Azure.  The most common type of auto-scaling is based on a metric.  There are several different metric sources that can be selected as source to scale on.  You also have several metric types that you can select to scale a resource off of.  You can set a minimum and maximum scale to ensure that you are utilizing the correct amount of resources.  In addition to setting rules that ratchet up, you should also set rules to ratchet down to ensure you are able to recover from increases/decreases in traffic.

_Virtual Machine Scale Sets_ is a scaling solution for virtual machines (infrastructure as a service).  Autoscaling with VM's can change the number of VM's based on CPU thresholds.

_Transient Faults_ are faults that occur through the usage of cloud computing.  Some things that you can do to combat transient faults: 1) Retry/back-off logic: if an action fails, submit it again, 2) Don't tightly couple applications; use queues, databases, and other messaging systems, 3) expect that api calls will not always work, and handle errors gracefully.

[Code Samples](https://github.com/Azure-Samples/app-service-dotnet-scale-web-apps)

## Caching and Content Delivery Networks
_Redis_ is an open source application that is used for caching content in-memory (nothing is written in memory).  Redis is considered to be a back-end caching method.  A new Redis Cache will have a DNS; [DNS Name].redis.cache.windows.net .  There are different pricing tiers based on the size as well as many different additional options.  Redis is used for short term storage that your users can use to quickly access data.  

You can use Redis in .Net with the NPM package Microsoft.Azure.Management.Redis (official) or StackExchange.Redis(Third Party, industry standard).  Azure provides a StackExchange Redis key by default.  In addition to strings, you can store geospatial data, hash keys, lists, etc.

Microsoft supports _Content Delivery Networks (CDNs) in Azure.  A CDN sits in front of a website, takes anything that doesn't change in the web-page, and store it closer to the user to speed up delivery, i.e. if a user requests 1000 resources per minutes, can get down to 100 per minute by using a CDN.  There are two components two a CDN: Profile and the Endpoint.  A CDN runs on a global scale.  There are 3 companies that provide CDN resources in Azure.  Microsoft, Verizon, and Akamai.  Pricing is standard across the board for the three companies with Verizon having a premium tier.  

An _endpoint_ represents the location that is used to access files.  Endpoints for CDNs in Azure have the url [endpoint name].azureedge.net . The files can live in storage, cloud service, web app, or custom origin.  You will also select the origin hostname.  You can purge the CDN if you create a new application to use your CDN.  Another common practice is to put the version number in the file name.  The users browser contributes to the savings in using a CDN.  Using a CDN saves on pricing and performance.

## Monitoring and Logging
_Azure Monitor_ provides a central location for collecting and monitoring logs for your Azure resources.  You can create new diagnostic settings to choose what information is stored and how long to store it.

_Guest Level Monitoring_ is an enabled service that pulls the host level metrics from your virtual machines, i.e. CPU, storage, etc.

You can store your logs elsewhere using the _Sinks_ tab.

Function app logs get stored in application insights.

There is a concept in Azure called _Analytics Workspace_.  Workspaces have their own subscription to bin your analytics data.

## Consuming Azure Services
_Logic Apps_ are connectors between different services.  It is a workflow application.  When you create a logic app, you get taken to the logic app designer.  There are a ton of templates that you can use to quickly set-up functions.  If this, then that (IFTT).  An example of a logic app is sending an email each time Bill Gates posts a tweet.  Just like function apps, Logic Apps require a trigger.  

_Azure Search_ provides a search service that can be used within your web application.  Azure search services have a url with the form [search name].search.windows.net.  There are different options for pricing for Azure search: Free, Basic, and Standard.  

_API Management_ provides a front end to your api to protect it from malicious use.  It is an API gateway and developer portal that can provide api documentation, throttle/quota api calls, monitor api health, etc.  There are various pricing plans that you can select: developer, basic, standard, premium, and consumption.  You can point your created api to the different types of apps that you can create in Azure.

There are a couple of ways to send messages between apps: _Queues_ and _Events_.  Azure can create events through _Event Hubs_ and _Event Grids_.  