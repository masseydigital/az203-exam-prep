# Develop Azure Platform as a Service Computer Solution (20-25%)

## Create Azure App Service Web Apps

### Create an Azure App Service Web App
The first step in hosting a web application is to create a web app (an App Service app) inside your Azure subscription.  You can do so through the Azure Portal, Azure CLI, a script, or an IDE.

_Azure App Service_ is a fully managed web application hosting platform.  This Platform as a Service (PaaS) offered by Azure allows you to focus on designing and building your app while Azure takes care of the infrastructure to run and scale your applications.

_Deployment Slots_ allow you to host various instances of your application that can be swapped out, i.e. a staging and production version.

The Azure Portal provides out-of-the-box continuous integration and deployment with Azure DevOps, GitHub, Bitbucket, FTP, or a local Git repo on your development machine.

Azure DevOps also has tight support for Visual Studio that can be published via Web Deploy technology.  App Service also supports FTP-publishing for more traditional workflows.

You can also dynamically scale your web app through the Azure Portal as well.

An _App Service plan_ is a set of virtual server resources that run App Service apps.  The sku or pricing tier determines the resources available to the web apps.  A single App Service plan can host an unlimited number of App Service web apps.  The number of deployed web apps does not affect your bill.

### Create an Azure App Service Background Task by using WebJobs
WebJobs are a feature of Azure App Service that lets you run arbitrary programs or scripts using the same App Service Plan resources on a web app.  To run a WebJob, you'll need an existing Azure App Service web API.  WebJobs can be written as scripts of several different kinds including Windows batch files, Powershell scripts, or Bash shell scripts.  You can upload such scripts and executables directly to the web app in the Azure portal.  Alternatively, you can write WebJobs using a framework such as Python or Node.js.  WebJobs can be written as part of a Visual Studio project.

There are two types of WebJobs, which execute at different times.
1) Continuous: A continuous WebJob starts when you deploy it and will continue to run in an endless loop. If you want to use a continuous WebJob, you must write your own code to implement the loop.
2) Triggered: A triggered WebJob only starts when scheduled or manually triggered.

The container for WebJobs functionality is a Console Application Project.

The _JobHost_ object communicates with Azure once the WebJob is deployed.  You should place connection strings in the App.config file, and code the actions that you want your WebJob to execute in the Functions.cs file.  To run on a CRON schedule, you can code the expression in the settings.json file.  Once you are finished, you can click Publish as Azure WebJob.

The _WebJobs SDK_ is a .Net library that can help simplify many programming tasks common to WebJobs.  It has many built-in features for working with Azure Storage and Service Bus, scheduling tasks, and handling errors.  

A WebJob that interacts with Azure needs two connection strings that can be added to the app.config file:
1) A Storage Account Connection String
2) A WebJobs dashboard connection string

```XML
<connectionStrings>
    <add name="AzureWebJobsDashboard" connectionString="<Connection to the dashboard storage account>" />
    <add name="AzureWebJobsStorage" connectionString="<Connection to the monitored storage account>" />
</connectionStrings>
```

You can also add bindings to avoid hard-coding input and output details in functions by adding them to the _function.json_ file.

### Enable Diagnostics Logging
_Applicatin Logs_ are the output of runtime trace statements in application code.  I.e. you might want to check some logic in your code by adding a trace to show when a particular function in being processed.  Application logging has scale limitation, primarily due to the fact that _files_ are being used to save the logged output.  The type of logging avaialable through the Azure App Service depends on the code framework of the app, and whether runnning Windows or Linux.

ASP.Net apps only run on Windows app services.  You use the System.Diagnostics.Trace class to log information to the application diagnostics log.  ASP.Net Core apps can run on either Windows or Linux.  To log information to Azure application logs, you need to use the _logger factory_ class, and then use one of six-log levels.  Node.js apps run on Windows or Linux, and application logging is enabled through the console() method.  Azure Web apps use the Web server (IIS process) to route messages to log files.  Loggng for linux-basaed scripted apps, such as Node, is determined by the Docker image used for the app's container.  Basic logging, using redirections from STDERR or STDOUT, uses the Docker logs.

In addition to application logging, you can also provide additional application monitoring through _Azure Application Insights_.  To use application insights, you have to include specific code within your app, using the Apps Insights SDK (this costs money).  You can also set-up _Metrics_ for your app which show how your app is performing (CPU, memory, network, and file system usage).

Live log streaming is an easy and efficient way to view live logs for troubleshooting purposes.  Live log stream connects to only one app instance.

You can open log streams using the Azure CLI:

```powershell
az webapp log tail --name \<_app name_\> --resource-group \<_resource group name_\>
```

To use Curl, you need deployment credentials.. there are two types:
1) App Level: Azure automatically creates a username/password pair when you deploy a Web app, and each of your apps has their own separate set of credentials.
2) User Level: You can create your own credentials for use with any Web app; you can manage these credentials in the Azure Portal.

You can use the following azcli command to open a log stream:

```powershell
curl -u {username} https://{sitename}.scm.azurewebsites.net/api/logstream
```

For Windows apps, file system log files are stored in a virtual drive that is associated with your Web app.  This drive is addressable as D:\Home, and includes LogFiles folder; within this folder are one or more the following folders:
1) Application: contains application-generated messages, if File System application logging has been enabled.
2) DetailedErrors: Contains detailed Web server error logs, 
3) http: Contains IIS-level logs
4) W3SVC[number]: contains details of all failed http requests, if Failed request tracing has been enabled.

All Azure Web apps have an associated Source Control Management (SCM) service site.  This site runs the Kudu service, and other site extensions.  Kudu manages deployment and troubleshooting for Azure Web Apps, including options for downloading log files.

## Create Azure App Service Mobile Apps

### Add Push Notifications for Mobile Apps

### Enable Offline Sync for Mobile App

### Implement a Remote Instrumentation Strategy for Mobile Devices

## Create Azure App Service API Apps

### Create an Azure App Service API App

### Create Documentation for the API by Using Open Source and Other Tools

## Implement Azure Functions

### Implement Input and Output Bindings for a Function
_Binding_ provides a declarative way to connect to data from within your code.  They make it easier to integrate with data streams consistently in a function.

There are two types of bindings:
1) Input Binding: An input binding i sa connection to a data source.  Functions can read from these inputs.
2) Output Binding: An output binding is a connection to a data destination.  Functions can write data to these destinations.

There are also _triggers_.  Triggers are special types of input bindings that cause a function to execute.  For example, an Azure Event Grid notification can be configured as a trigger.

The _type_ of binding defines where we are reading or sending data.  Some common bindings include:
1) Blob Storage
2) Azure Service Bus Queues
3) Azure Cosmos DB
4) Azure Event Hubs
5) External Files
6) External Tables
7) HTTP Endpoints

Bindings have the following properties:
1) Name: Defines the function parameter through which you access the data.
2) Type: Identifies the type of binding i.e. the type of data or service we want to interact with
3) Direction: Indicates the direction data is flowing, i.e. is it an input or output binding
4) Connection: Provides the name of an app setting key that contains the connection string.
5) (OPTIONAL) Path: the location of resources

Bindings are defined in JSON and is configured in your function's configuration file: function.json

```JSON
    ...
    {
      "name": "headshotBlob",
      "type": "blob",
      "path": "thumbnail-images/{filename}",
      "connection": "HeadshotStorageConnection",
      "direction": "in"
    },
    ...
```

As of the module, the following types support input bindings:
1) Blob Storage - Read from a blob
2) Azure Cosmos DB - SQL API to retrieve one or more Azure Cosmos DB documents and passes them to the input parameter of the function.
3) Microsoft Graph - Allow you to read files from OneDrive, Excel, and get auth tokens so that you can interact with the Microsoft Graph API
4) Mobile Apps - Loads a record from a mobile table endpoint and passes it into your function
5) Table Storage - You can read data and work with Azure Table Storage

A _Binding Expression_ is specialized text in function.json, function parameters, or code, that is evaluated when the function is invoked to yield a value.

Types of Binding Expressions:
1) App Settings
2) Trigger File Name
3) Trigger Metadata
4) JSON payloads
5) New GUID
6) Current Data & Time

Output Binding Types
1) Blob Storage - write blobs
2) Azure Cosmos DB - write a new document to an Azure Cosmos DB database using the SQL Api
3) Event Hubs - Write events to an event stream
4) HTTP - responsd to the HTTP request sender, can also connect to web hooks
5) Microsoft Graph - write files in OneDrive, modify Excel data, and send email through Outlook
6) Mobile Apps - writes a new record to a Mobile Apps Table
7) Notification Hubs - Send push notifications
8) Queue Storage - Write messages to a queues
9) Send Grid - Send emails
10) Service Bus - Send queue or topic messages
11) Table Storage - Write a table to an Azure Storage account
12) Twilio - Send text messages

**You can also use the output binding template to make it easier to write data to data sources**

### Implement Function Triggers by Using Data Operations, Timers, and Webhooks

### Implement Durable Functions

### Create Azure Function Apps by Using Visual Studio