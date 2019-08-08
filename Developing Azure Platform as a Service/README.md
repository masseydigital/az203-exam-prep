# Develop Azure Platform as a Service Computer Solution (20-25%)

## Create Azure App Service Web Apps

### Create an Azure App Service Web App

### Create an Azure App Service Background Task by using WebJobs

### Enable Diagnostics Logging

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
8) Queue Storage - Write messages to a queue
9) Send Grid - Send emails
10) Service Bus - Send queue or topic messages
11) Table Storage - Write a table to an Azure Storage account
12) Twilio - Send text messages

**You can also use the output binding template to make it easier to write data to data sources**

### Implement Function Triggers by Using Data Operations, Timers, and Webhooks

### Implement Durable Functions

### Create Azure Function Apps by Using Visual Studio