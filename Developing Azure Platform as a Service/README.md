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

Mobile backend services need to be able to support data storage, user authentication, data synchronization, and the ability to send notifications to a variety of mobile device types.

### Add Push Notifications for Mobile Apps
The Mobile Apps feature of Azure App Service is a collection of Azure services commonly used to support mobile client apps.   It includes data storage, user authentication, and push notfiications.  They are accessible via REST Api or using a microsoft provided API that can be added to your mobile client.  There are two parts to every mobile application (1) the client app and (2) the supporting mobile service on Azure.

Mobile Apps provides a backend service that can be defined using either ASP.Net or node.js.  this allows for OData v3 source that can be linked ot either an Azure SQL Database, or an on-premises hosted SQL server.  This data can be accessed via REST or using a Microsoft provided SDK.

When using the Mobile Apps SDK in your client app to access your data, you will also get a robust API to manage data synchronization between the client mobile app and the cloud data store.  

Mobile Apps includes support for several identify providers.  You can use Azure Active Directory or common client providers including Facebook, Google, Twitter, and Microsoft.  Mobile Apps provides OAuth 2.0 authentication for each.

When using the client SDKs, you can integrate with Azure Notifications Hub to manage and send notifications to your app users.

The client applications are automatically created for iOS, Android, Xamarin, and Apache Cordova.  The client projects includes the Mobile Apps SDK and are configured to work with your instance of the Mobile App Service.

Azure Notification Hubs provide an easy-to-use infrastructure that enables you to send mobile push notifications from any backend to any mobile platform.  

Push notifications are able to notify users when an event has occurred.  On Windows Store and Windows Phone applications, the notification results in a _toast_ (a modeless window appearing at the top of the screen) or with tile updates on the Start screen.  Push notifications are delivered through platform-specific infrastructures called _Platform Notification Systems (PNS)_.

There are 4 main challenges when developing push notification systems
1) Platform Dependency: Each platform generally has its own interface to send push notifications
2) Scale: scaling of both token updates and requests to the PNS.
3) Routing: PNSs provide a way to send a message to a device.  In most apps, however, notifications are targeted at users and/or interest groups.  As such, the app backend must maintain a registry that associates interest groups with device tokens.
4) Monitoring & Telemetry: Tracking and aggregating the outcomes of millions of notifications is not trivial and it is usually an important component of any solution that uses push notifications.

Notification Hubs aim to alleviate the 4 above challenges.  The Advantage of using Notification Hubs is:
1) Multi-platform support: Support for all major platforms, i.e. iOS, Android, Windows Phone, etc.
2) Works with any backend: cloud / on-prem, .Net, php, Java, node
3) Scale: Notification Hubs scale to millions of devices without the need of re-architecting or sharding.
4) Rich Delivery Patterns: Broadcast, unicast/multicast, Segmentation
5) Personalization: Each device can have one or more templates.
6) Security: Shared Access Secrets (SAS) or federated authentication
7) Rich Telemetry: Available in the portal and programmatically.

### Enable Offline Sync for Mobile App
Offline sync allows end users to interact with a mobile app -- viewing, adding, or modifying data without a network connection.  Changes are stored in a local database.  Once the device is back online, these changes are synced with the remote backend.

The Azure Mobile App offline features allow you to interact with a local database when ou are in an offline scenario.  To use these features in your app, you initialize a _SyncContext_ to a local store.  Then reference your table through the _IMobileServiceSyncTable_ interface.  

When using sync tables, your client code controls when local changes are synchronized with an Azure Mobile App backend.  Nothing is sent to the backend until there is a call to _push_ local changes.  Similarly, the local store is populated with new data only when there is a call to _pull_ data.

_Push_: Push is an operation on the sync context and sends all CUD changes since the last push.  Note that it is not possible to send only an individual table's changes, because otherwise operations could be sent out of order.

_Pull_: Pull is performed on a per-table basis and can be customized with a query to retrieve only a subset of the server data.  The Azure Mobile client SDK then insert the resulting data into the local store.

_Implicit Pushes_:  If a pull is executed against a table that has pending local updates, the pull first executes a _push()_ on the sync context.  This push helps minimize conflicts between changes that are already queued and new data from the server.

_Incremental Sync_: The first parameter to the pull operation is a _query name_ this is used only on the client.

_Purging_: You can clear the contents of the local store using IMobileServiceSyncTable.PurgeAsync.  Purging may be necessary if you have stale data in the client database or if you want to discard all pending chagnes.

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
_Durable Functions_ are an extension of Azure Functions that enables you to perform long-lasting, stateful operations in Azure.  Azure provides the infrastructure for maintaining state information.  You can use Durable Functions to orchestrate a long-running workflow.

Benefits of using Durable Functions:
1) They enable you to write event driven code.  They can wait asynchronously for one or more external events, and then respond to them.
2) You can chain functions together.  You can implement common patterns such as fan out/in, which uses one function to invoke others in parallel, and then accumulate the results.
3) You can orchestrate and coordinate functions, and specify the order in which functions should execute.
4) The state is managed for you.  You don't have to write your own code to save information for a long-running function.

Durable functions allow you to define stateful workflows using an _Orchestration Function_.  They provide the added benefits of:
1) Defining the workflow in code.  You don't need to write a JSON description or use a workflow design tool.
2) Functions can be called both synchronously and asynchronously.  Output form the called functions is saved locally in variables and used in subsequent function calls.
3) Azure checkpoints the progress of a function automatically when the function awaits.  Azure may choose to dehydrate the function and save its state while the function waits, to preserve resources and reduce costs.  When the function starts running again, Azure will rehydrate it and restore its state.

There are three durable function types:
1) Client: the entry point for creating an instance of a Durable function orchestration.  They can run in response to an event from many sources, such as a new HTTP request arriving, a message being posted to a message queue, an event arriving in an event stream.  
2) Orchestrator: describe how actions are executed, and the order in which they are run.  You write the logic in code (C# or Javascript).
3) Activity: are the basic units of work in a durable function orchestration.  An activity function contains the actual work performed by the tasks being orchestrated.

You can use Durable Functions to implement many common workflow patterns, including:
1) Function Chaining:  The workflow executes a sequence of functions in a specified order.  The output of one function is applied to the input of the next function in the sequence.  The output of the final function formulates the result.
2) Fan out/Fan in: This pattern runs multiple functions in parallel and then waits for all the functions to finish.  The results of the parallel executions can be aggregated or used to compute the final result.
3) Async HTTP Apis: Addresses the problem of coordinating state of long-running operations with external client.  An HTTP call can trigger the long-running action.  Then, it can redirect the client to a status endpoint.  The client can learn when the operation is finished by polling this endpoint.
4) Monitor: A recurring process in a workflow, possibly looking for a change in state.  For example, you could use this pattern to poll until specific conditions are met.
5) Human Interaction: Combines automated processes that also involve some human interaction.  An example of this is an approval process.

The _moment.js_ library contains date/time functions that you can use with durable functions.

### Create Azure Function Apps by Using Visual Studio
You can create Azure Function apps through Visual studio by installing the _Azure Functions and Web Job Tools_ extension.  Azure functions are triggered by an event rather than directly from any applications.

The events available are: 
1) Blob Trigger:  Occurs when you upload or modify Azure blob storage
2) Event Hub Trigger: Occurs when Event Hub receives a message.
3) Azure Cosmos DB Trigger: Occurs when a document is added to, or modified in, an Azure Cosmos DB database.  You can use this trigger to integrate Azure Cosmos DB with other services.
4) Http Trigger: Occurs when an Http request occurs in a Web App.  You can also trigger to respond to Webhooks.  A _webhook_ is a callback that occurs when an item hosted by a website is modified.  
5) Queue Trigger: Occurs when a new item is added to an Azure Storage Queue.
6) Service Bus Queue Trigger: Occurs when a new item is added to an Azure Service Bus Queue
7) Service Bus Topic Trigger: Occurs in response to a new message arriving on a Service Bus Topic.
8) Timer Trigger: Occurs on a defined schedule.

Azure currently provides two versions of the runtime environment required to run Azure Functions.  Version 1 uses the .Net Framework 4.7, whereas version 2 runs using the .Net Core 2.

An Azure Function app stores management information, code, and logs in Azure Storage.  The storage account must support Azure Blob, Queue, Files, and Table Storage.

You can restrict access privileges to users performing an HTTP request with the following levels of access:
1) Anonymous: No authentication is required and any user can trigger the function.
2) Function: The Http request must provide a key that enables the Azure function runtime to authorize the request.
3) Admin: This is similar to function in that the user must supply a key with the request, but for this access level the key must be an _admin key_.

Unless you are using an Http request, you must also supply a connection string and other details necessary for the function app to access the resource.

An Azure Function is implemented as a _static_ class.  This static class provides a static, asynchronous method named **Run**, which acts as the entry point for the function.  The parameters passed to the Run method provide the context for the trigger.  All Azure Functions pass in the ILogger parameter which can be used to write log messages, which the function app will write to storage for later analysis.

Example Http Request:
```csharp
public static class Function1
{
    [FunctionName("Function1")]
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string name = req.Query["name"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        name = name ?? data?.name;

        return name != null
            ? (ActionResult)new OkObjectResult($"Hello, {name}")
            : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
    }
}
```

Example Blob trigger:
```csharp
public static class Function2
{
    [FunctionName("Function2")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Connection = "xxxxxxxxxxxxxxxxxxxxxxx")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
}
```

Behind the scenes, an Azure Function App is a collection of one or more VM's, running a web server.  In Visual Studio, the Publish Wizard will allocate the resources needed to run your Function app.

You can also set-up Continuous deployment with one of several providers or push a .zip deployment with the following Azure Cli command

```bash
az functionapp deployment source config-zip \
-g <resource-group> \
-n <function-app-name> \
--src <zip-file>
```