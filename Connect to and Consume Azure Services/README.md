# Connect to and consume Azure and Third-Party Services (20-25%)

## Develop an App Service Logic App

### Create a Logic App
1) Create a logic app
2) Choose a connector and a trigger, i.e. Recurrence, every 24 hours


### Create a Custom Connector for Logic Apps

### Create a Custom Template for Logic Apps

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

### Implement Solutions that Use Azure Notification Hubs

### Implement Solutions that use Azure Event Hub

## Develop Message-Based Solutions

### Implement Solutions that use Azure Service Bus

### Implement Solutions that use Azure Queue Storage queues



