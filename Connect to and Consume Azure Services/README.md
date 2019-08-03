# Connect to and consume Azure and Third-Party Services (20-25%)

## Develop an App Service Logic App

### Create a Logic App

A _business process_ is a sequence of tasks that produce a specific outcome.  The result might be a decision, some data, or a notification.

_Azure Logic Apps_ is a cloud service that automates the execution of your business processes.  You use a tool called the _Logic Apps Designer_ to arrange pre-made components into the sequence that you need.  You can connect to hundreds of different pre-built components when you use Azure.

### Create a Custom Connector for Logic Apps

A _connector_ is a component that provides an interface to an external service.  For example, the Twitter connector allows you to send and retrieve tweets.  Connectors work by using the external service's REST or SOAP API to do work.

YOu can write custom connectors to access services that don't have pre-built connectors.  Those services must have a REST or SOAP API. 

To create the custom connector, you first generate an OpenAPI or Postman description of the API.  You then use that API description to create a Custom Connector resource in Azure portal.

Once you create your new connector, you can share it with other people in your organization, or submit it to Microsoft for certification.  If Microsoft certifies your connector, it will be included in the set of connectors available to all users.

_Trigger_: an event that occurs when a specific set of conditions are satisfied.  Triggers activate automatically when conditions are met.  

_Action_: an operation that executes a task in your business process.  

_Connector_: container for related triggers and actions.

_Control Actions:_ allow you to perform different functions based on an input.  _Condition_ statements are a boolean true/false:yes/no expression, _For each_ and _Until_ keywords are used for looping, and Unconditional _Branch_ instructions.

When not to use Logic Apps:
1) No external connections to other apis
2) You require consistent, high-speed performance
3) You have complex business logic
4) You have a complex custom api taht you willl have to create

_Logic App Designer_ gives you a design canvas to create workflows for your logic app.

There are three broad classes of triggers: 

1) Data
   a) Polling: Runs when new data is available, but only after a poll.  You set-up a frequency and an interval to control how often it will run.

   b) Push: runs immediately when new data is available.  These are implemented by using Webhooks.  The benefit of these is that they do not incur an additional polling cost for checking when new data is available.  Not every connector offers push, but it is generally recommended over polling.

2) Time: Runs on a time schedule

3) Manual: Custom code to invoke logic

Triggers can pass in _Parameters_ and pass back _return values_.

### Create a Custom Template for Logic Apps

Azure Logic Apps offer over 200 built-in connectors that fall into the following categories:
1) Built-In Connectors: Have actions and triggers that integrate with Azure Apps and Functions
2) Managed Connectors: Have triggers and actions that call other services and systems.
3) Managed API Connectors: Have actions and triggers that integrate with Azure Blob Storage, Office 365, Dynamics, Power BI, and more
4) On-Premises Connectors: Have actions and triggers that integrate with on-premise installations of SQL Server, Sharepoint Server, Oracle, and File Shares
5) Integration Account Connectors: Transform and validate XML, encode and decode flat files, and process Business-to-Business (B2B) messages with AS2, EDIFACT, and X12 protocols
6) Enterprise Connectors: Provide access to enterprise systems such as SAP, IBM, Message Queue, etc.

Custom connectors must implement the OpenAPI definition.  In the WebAPI you can implement the description with either OpenAPI or a Postman collection. You can define the api using the _Swashbuckle Nuget Package_.

```csharp
using Swashbuckle.AspNetCore.Swagger;
```

## Integrate Azure Search within Solutions

[Azure Search](https://docs.microsoft.com/en-us/learn/modules/intro-to-azure-search/2-what-is-azure-search)

### Create an Azure Search index
Azure Search Indexes can be created in the following ways:

1) Using the portal, Azure Search includes a built-in index designer

-Add an index thorugh the designer, giving it a name
-Add fields to the index, specifying the key field and field attributes

2) Programmatically, Azure Search indexes can be created through code using the C# .Net Sdk

-Create a _SearchServiceClient_ object class for connecting to the search service
-Create an _Index_ object
-Call the _Indexes.Create_ method to send the index object to the search servic

**To create an index for Azure Search in C#, you must obtain the URL endpoint and API key of the search service**

3) Azure Search REST API, can be used to manage indexes and documents

You can use PowerShell, or Bash, to call the Azure Search Service REST Api to manage your search service.  The index definition and searchable content are provided in the request body as well-formatted JSON content.

**To get started using the Azure CLI, you must have created the Search Service; in the portal, make note of the Search service endpoint URL and primary admin key**

-Create an object to store the header for the REST Api call

```powershell
$headers=
@{
'api-key' = '<your-admin-api-key>'
'Content-Type' = 'application/json'
'Accept' = 'application/json' 
}
```

-Test the details are correct by trying to connect to the API with Invoke-RestMethod

```powershell
Invoke-RestMethod -Uri <your-search-url> -Headers $headers | ConvertTo-Json
```

-Create an object to store the index schema definition of your index
```powershell
$body = @"
{
    "name": "video-catalog",  
    "fields": [
        {"name": "id", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "title", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.microsoft"},
        {"name": "difficulty", "type": "Edm.Int32"},
        {"name": "length", "type": "Edm.DateTimeOffset", "facetable": false},
        {"name": "publication", "type": "Edm.DateTimeOffset", "facetable": false},
        {"name": "size", "type": "Edm.Int32"}
    ],
    "suggesters": [  
    {  
       "name": "northwindsuggester",  
       "searchMode": "analyzingInfixMatching",  
       "sourceFields": ["title"]  
    }  
    ]
}
"
```

-Create a URL for the REST API endpoint, add the index name to the end of the Search Service URL

```powershell
$url = "https://<your search service name>.search.windows.net/indexes/video-catalog?api-version=2019-05-01"
```

-Create the index using the Invoke-RestMethod

```powershell
Invoke-RestMethod -Uri $url -Headers $headers -Method Put -Body $body | ConvertTo-Json
```

### Import Searchable Data
There are (2) approaches for loading data into an index

1) _Pushing Data_ JSON data is pushed into an Azure Search index via either REST Api or the .Net SDK.  Pushing data has the most flexibility as it has no restrictions on the data source type, or frequency of execution.

2) _Pulling Data_ Serach service indexers can be created using either Azure Data Import Wizard, or programmatically.  Data sources are crawled to load JSON documents into the index.  An indexer maps source fields to their matching fields in the index.  If necessary, an indexer will transform the imported data into a JSON format.

Supported Data Sources for Azure Portal Import:

1) Existing data source from another indexer
2) Azure SQL Database
3) SQL Server on an Azure VM
4) Cosmos DB
5) Azure Blob Storage
6) Azure Table Storage

REST API or .NET SDK

Data can be pushed or pulled into Azure Search via the Azure Search REST API.  The .NET SDK can be thought of as a wrapper around the REST API, with the exposed classes using the REST API endpoints under the covers.

.NET SDK Package: <mark style="background-color: lightgray">Microsoft.Azure.Search</mark>

Write code that:

1) Ceates a <mark style="background-color: lightgray">_SearchIndexClient_</mark> object class for connecting to the search index.

2) Creates an <mark style="background-color: lightgray">_IndexBatch_</mark> object to contain JSON search documents.

3) Calls the <mark style="background-color: lightgray">_Documents.Index_</mark> method to upload the batch of documents.

**Example of Loading Data**

1) Use the cloud shell to create a storage account and container

```Powershell
export AZURE_STORAGE_ACCOUNT="videoblobstorage"$RANDOM
export CONTAINER="videocontainer"$RANDOM
az storage account create --name $AZURE_STORAGE_ACCOUNT -g Learn-0e9722da-51ee-450d-ad0d-bf2dedeecd02 --kind StorageV2 --sku Standard_LRS
export CREDENTIALS=$(az storage account show-connection-string --name $AZURE_STORAGE_ACCOUNT -o tsv)
az storage container create --connection-string $CREDENTIALS --name $CONTAINER
```

2) Download your companies video catalog into the cloud storage.

```powershell
curl https://raw.githubusercontent.com/MicrosoftDocs/mslearn-introduction-to-azure-search/master/video-catalog.json -o video-catalog.json
```

3) Upload the video catalog to the blob storage account

```powershell
az storage blob upload --connection-string $CREDENTIALS --container-name $CONTAINER --file video-catalog.json --name VideoCatalog
```

### Query the Azure Search Index
_Azure Search Queries_ work in a similar way to HTTP or a REST API request.  They're constructed of two key parts, the request an d the response.  Azure Search is built on, and extends, the Apache Lucene project.

To query the REST API you need:
1) The service endpoint and index collection
2) The API version
3) An API Key
4) A query type
5) The query

**Azure Search supports Lucene query syntax**

Examples

yoga

yoga + -hatha

*$%top=3&orderby=Diffculty desc

*&$filter=Size lt 500

## Establish API Gateways

### Create an APIM Instance

### Configure authentication for APIs

### Define Policies for APIs

## Develop Event-Based Solutions

### Implement Solutions that Use Azure Event Grid
[Azure Event Grid](https://docs.microsoft.com/en-us/learn/modules/choose-a-messaging-model-in-azure-to-connect-your-services/4-choose-event-grid)

_Azure Event Grid_ is a fully-managed event routing service running on top of Azure Service Fabric.  It distributes events from different sources, such as Azure blob storage accounts.

There are several concepts in Azure Event Grid that connect a source to a subscriber:

__Events__: What happened, they are the data messages passing through Event Grid that describe what has taken place.
```JSON
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```


__Event Sources__: Where the event took place, i.e. Azure Storage, IoT Hub.  Verbiage can be swapped interchangeably to _publisher_.


__Topics__: the endpoint where publishers send events. Can be divided into __System Topics__ and __Custom Topics__.  System topics connect to Azure services, while custom topics connect to Third-Party services.


__Event Subscriptions__: The endpoint or built-in mechanism to route events, sometimes to multiple handlers


__Event Handers__: The app or service reacting to the event

Events can be generated by the following Azure resource types:
1) Azure Subscriptions or Resource Groups
2) Container Registry
3) Event hub
4) Service Bus
5) Storage Accounts
6) Media Services
7) Azure IoT Hub
8) Custom Events

The following types of objects in Azure can receive and handle events from Event Grid:
1) Azure Functions
2) Webhooks
3) Azure Logic Apps
4) Microsoft Flow

### Implement Solutions that Use Azure Notification Hubs

### Implement Solutions that use Azure Event Hub

_Event Hubs_ is an intermediary for the publish-subscribe communication pattern.  Unlike Event Grid, however, it is optimized for extremely high throughput.  It is similar to Event Grids, but provides a few more services.

As Event Hub receives communications, it divides them into partitions.  Partitions are buffers into which the communications are saved.

Event Hubs can send all your events immediately to Azure Data Lake or Azure Blob storage for inexpensive, permanent persistence.  

All publishers are authenticated and issued a token.  This means Event Hubs can accept events from external devices and mobile apps.

## Develop Message-Based Solutions

### Implement Solutions that use Azure Service Bus
Azure Service Bus is the goto solution to handle messages while Azure Event Grid is the goto solution for handling events.

Azure Service Bus can exchange messages in three different ways:

1) Queues: FIFO message relay which decouples the source and destination components.

2) Topics: FIFO message relay that can send a message to multiple destinations

3) Relays: Two-way communication protocol between clients

There are two types of queues that can be used to send messages
1) Service Bus Queues
2) Storage Queues

To use the storage bus in Visual Studio, you can use the <mark style="background-color: lightgray"> Microsoft.Azure.ServiceBus</mark> NuGet package.  The most important class in this library is the <mark style="background-color: lightgray">QueueClient</mark> class.

To connect to a queue in a Service Bus you need to pass the endpoint and the access key.

To send data to the queues you can write the following code:

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.ServiceBus;

queueClient = new QueueClient(TextAppConnectionString, "PrivateMessageQueue");

string message = "Sure would like a large pepperoni!";
var encodedMessage = new Message(Encoding.UTF8.GetBytes(message));
await queueClient.SendAsync(encodedMessage);
```

To receive data from the queue:


```csharp
queueClient.RegisterMessageHandler(MessageHandler, messageHandlerOptions);

await queueClient.CompleteAsync(message.SystemProperties.LockToken);
```

For Service Bus Topics, you will use the <mark style="background-color: lightgray">TopicClient</mark> instead of the <mark style="background-color: lightgray">QueueClient</mark> class to send messages and the <mark style="background-color: lightgray">SubscriptionClient</mark> class to receive messages.

_Filters_ can be used to limit the messages that are sent out to subscribers.  

There are three types of Filters:

1) Boolean Filters: TrueFilter sends messages to the current subscription, FalseFilter ensures messages are not sent to the current subscription.

2) SQL Filters: A SQL filter specifies a condition by using the same syntax as a WHERE clause in an SQL query.

3) Correlation Filters: Matches a set of conditions against properties of each message.

**SQL Filters are more computationally expensive than correlation filters**

Sending Messages with Topics:

```csharp
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Azure.ServiceBus;

topicClient = new TopicClient(TextAppConnectionString, "GroupMessageTopic");

string message = "Cancel! I can't believe you use canned mushrooms!");
var encodedMessage = new Message(Encoding.UTF8.GetBytes(message));
await TopicClient.SendAsync(encodedMessage);
```

Receiving Messages with Topics:
```csharp
subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, "GroupMessageTopic", "NorthAmerica");

subscriptionClient.RegisterMessageHandler(MessageHandler, messageHandlerOptions);

await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);
```

### Implement Solutions that use Azure Queue Storage queues

_Azure Queue Storage_ is an Azure service that implements cloud-based queues.  Each queue maintains a list of messages.  Application components access a queue using a REST API or an Azure-supplied client library.  

A message in a queue is a byte array of up to 64KB.  You can use JSON or XML to format your mesage into an object.

You can create a storage accoun  with the following command:
```powershell
az storage account create --name [unique-name] -g [resource-group] --kind [StorageV2] --sku [Standard_LRS]
```

To access a queueu, you need three pieces of information:
1) Storage account name
2) Queue Name
3) Authorization Token

Every request to a queue must be authorized, some of the options include:
1) Azure Active Directory - RBAC Authentication and identity specific clients based on AAD Credentials
2) Shared Key - Account key (encrypted key signature)
3) Shared Access Signature - Generated URI that grants limited access to objects in storage account to clients

To access a queue using a REST API, you use a URL that combines the name of the storage account with the domain.  An authorization header must also be sent with every request.
<mark style="background-color: lightgray">http://[storageaccount].queue.core.windows.net/[queue name]

The connection string combines a storage account name and account key.
```csharp
string connectionString = "DefaultEndpointsProtocol=https;AccountName=<your storage account name>;AccountKey=<your key>;EndpointSuffix=core.windows.net"
```

Queue storage implements _at-least-once delivery_.  After the receiver gets a message, that message remains in the queue but is invisible for 30 seconds.  After 30 seconds the message reappears in the queue and another receiver can process it to completion.

The Azure Storage Client Library for .Net, <mark style="background-color: lightgray">WindowsAzure.Storage</mark>, provides types to represent each of the objects you need to interact with:

1) <mark style="background-color: lightgray">CloudStorageAccount</mark> represents your Azure storage account
2) <mark style="background-color: lightgray">CloudQueueClient</mark> represent Azure Queue Storage.
3) <mark style="background-color: lightgray">CloudQueue</mark> represents one of your queue instances.
4) <mark style="background-color: lightgray">CloudQueueMessage</mark> represents a message.

Connecting to a queue:
```csharp
CloudStorageAccount account = CloudStorageAccount.Parse(connectionString);

CloudQueueClient client = account.CreateCloudQueueClient();

CloudQueue queue = client.GetQueueReference("myqueue");
```

In general, the sender application should always create the queue.

To send a message:
```csharp
var message = new CloudQueueMessage("your message here");

CloudQueue queue;
//...

await queue.AddMessageAsync(message);
```

To receive and delete a message:
```csharp
CloudQueue queue;
//...

CloudQueueMessage message = await queue.GetMessageAsync();

if (message != null)
{
    // Process the message
    //...

    await queue.DeleteMessageAsync(message);
}
```