# Develop for Azure Storage (15-20%)

## Develop Solutions that Use Storage Tables

### Design and Implement Policies for Tables

### Query Table Storage by Using Code

### Implement Partitioning Schemes
The following are key metrics of Azure Cosmos DB:
1) _Total request / second_ and the proportion of requests rejected because the provisioned throughput is exceeded
2) _Total throughput_: measured in Request Units per second (RU/s) and the distribution of throughput across partitions.
3) _Total Storage_: measured in kilobytes (KB), and the distribution of data across partitions.

A _Request Unit (RUs)_ is the amount of CPU, Disk I/O, and memory required to read 1 KB of data in 1 second.  Operations like reading data, writing data, or querying data take more RUs.  

You can create an Azure Cosmos DB account with the following bash command:
```bash
az cosmosdb create --resource-group Sandbox Resource Group --name $COSMOS_NAME
```

An Azure Cosmos DB account is a container for one or more _databases_.  An Azure Cosmos DB database is a container for one or more _collections_.   A collection contains _documents_.  A document is an unstructured set of key/value pairs, read and written in JSON format.

_Partitioning_ is the distribution and grouping of your data across the underlying resources.  Documents are grouped in a partition based on the value of the partition key.  You specify the partition key when you create the collection.  A partition key can be connected to properties in the JSON, or multiple properties.  An effective partioning strategy distributes data and access evenly across partitions.  Partitions cannot be changed once the collection is created.

An _index_ is a catalog of docuument properties and their values.  It includes links to documents that contain properties equal to each property value.  Indexing makes searching a collection more efficient.  Indexes can be changed after a colletion is created.

There are three indexing modes that you can use with Azure Cosmos DB:
1) Consistent: The index is updated synchronously every time a new document is written to the collection.
2) Lazy: The index is updated at a lower priority.
3) None: No index is created.

You can create a database (in this case called mslearn) with the following bash command:
```bash
az cosmosdb database create --resource-group Sandbox Resource Group \
        --name $COSMOS_NAME --db-name mslearn
```

You can create a partition with the following command:
```bash
az cosmosdb collection create --resource-group Sandbox Resource Group \
        --name $COSMOS_NAME --db-name mslearn --collection Small \
        --partition-key-path /id --throughput 400
```

A _hot partition_ is a partition that is accessed more than a normal partition.

## Develop Solutions that Use CosmosDB

### Create, read, update, and delete data by using appropriate APIs
Azure CosmosDB is a globally distributed and elastically scalable database.  It has a low latency that is backed by a comprehensive set of Service Level Agreements (SLAs).  Consistency can be an issue when working with distributed systems, but Azure Cosmos DB alleviates this situation by offering you five different consistency levels:
strong, bounded staleness, session, consistent prefix, and eventual.

Azure CosmosDB is run with a multi-model approach, meaning it has the ability to use document, key-value, wide-column, or graph-based data.

Core (SQL) i sthe default API for Azure Cosmos DB, which provides you with a view of your data that resembles a traditional NoSQL document store.  You can query the hierarchical JSON documents with SQL-like language.

Azure Cosmos DB's API for MongoDB supports the MongoDB wire protocol.  This API allows existing MongoDB client SDK's, drivers, and tools to interact with the data transparently, as if they are running against an actual MongoDB database.

Azure Cosmos DB's support for the Cassandra API makes it possible to query data by using the Cassandra Query Language (CQL), and your data will appera to be a partitioned row store.  

Azure Cosmos DB's Azure Table API provides support for applications that are written for Azure Table Storage that need premium capabilities like global distribution, high availability, scalable throughput.  The original Table API only allows for indexing on the Partition and Row keys; there are no secondary indexes.  Storing table data in Cosmos DB automatically indexes all the properties, and requires no index management.

Choosing Gremlin as the API provides a graph-based view over the data.  Remember that at the lowest level, all data in any Azure Cosmos DB is stored in an ARS format.  A graph-based view on the database means data is either a vertex, or an edge. Azure Cosmos DB supports Apache Tinkerpop's Gremlin language.  Results will be returned in GraphSON format.

### Implement Partitioning Schemes

### Set the appropriate consistency level for operations

## Develop Solutions that use a Relational Database

### Provision and Configure Relational Databases

### Configure Elastic Pools for Azure SQL Database

### Create, Read, Update, and Delete data tables by using code

## Develop Solutions that use Blob Storage
Blobs give you file storage in the cloud and an API that lets you build apps to access the data.  Blobs are similar to local files, except that they can be accessed from anywhere.  Azure blob strorage _unstructured_, meaning there are no restrictions on the types of data that it can hold.  I.e. a blob can hold a PDF, a JPG, JSON, MP3, etc.  Blobs are generally not appropriate for structured data that needs to be queried frequently.  They do not have indexing features, but can be used in conjunction with databases to store non-queryable data.

### Move items in blob storage between storage accounts or containers
In Blob storage, every blob lives inside a _blob container_.  You can store an unlimited number of blobs in a container and an unlimited number of blobs in a storage account.  Containers are "flat" which means they can only store blobs, not other containers.  The Blob storage API is REST-based and supported by client libraries in many popular languages.

Blobs are flat and do not support container nesting, but you can use slashes in the naming to denote specific groupings for the files.  i.e. finance/budgets/2018/q1.xls.  This is called virtual directories.

There are three types of blobs that you can store data in:
1) Block blobs: composed of blocks of different sizes that can be uploaded independently and in parallel.  Writing to a block blob involves uploading data to blocks and committing them to the blob.
2) Append Blobs: specialized block blobs that support only appending new data, but they are efficient at it.  Append blobs are great for scenarios like storing logs or writing streamed data.
3) Page Blobs: designed for scenarios that involve random-access read and writes.  Page blobs are used to the Virtual Hard Disk (VHD) files used by Azure Virtual Machines, but they're great for any scenario that involves random access.

You can create a storage account with the following Azure Cli command:

```powershell
az storage account create \
  --kind StorageV2 \
  --resource-group [sandbox resource group name] \
  --location eastus \
  --name <your-unique-storage-account-name>
```

containers can be created with the following command:
```powershell
az storage container create -h
```

A typical workflow for apps that use Azure Blob storage is:
1) Retrieve configuration: At startup, load the storage account configuration.  This is typically a storage account connection string.
2) Initialize Client: Use the connection string to initialize the Azure Storage client library.  This creates the objects the app will use to work with the Blob storage API.
3) Use: make API calls with the client library to operate on containers and blobs.

The typical workflow pattern for Blob storage consists of the following steps:
1) Call CloudStorageAccount.Parse(or TryParse) with your connection string to get a CloudStorageAccount.
2) Call CreateCloudBlobClient on the CloudStorageAccount to get a CloudBlobClient
3) Call GetContainerReference on the CloudBlobClient to get a CloudBlobContainer
4) Use methods on the container to get a list of blobs and/or get references to individual blobs to upload and download data.

```csharp
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString); // or TryParse()
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference(containerName);
```

Calling CreateIfNotExistAsync on a CloudBlobContainer is the best way to create a container when your applciation starts or when it first tries to use it.  To use Blob storage, you can add the WindowsAzure.Storage library.

```csharp
public Task Initialize()
{
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConfig.ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageConfig.FileContainerName);
    return container.CreateIfNotExistsAsync();
}
```

Working with an individual blob in the Azure Storage SDK for .Net Core requires a _blob reference_ - an instance of an _ICloudBlob_ object.  You can get a list of the blobs in a container using _CloudBlobContainers_ ListBlobsSegmentedAsync_ method.  The standard pattern for getting every blob in a container is:
```csharp
BlobContinuationToken continuationToken = null;
BlobResultSegment resultSegment = null;

do
{
    resultSegment = await container.ListBlobsSegmentedAsync(continuationToken);

    // Do work here on resultSegment.Results

    continuationToken = resultSegment.ContinuationToken;
} while (continuationToken != null);
```

You can upload blobs with the following command:
```csharp
public Task Save(Stream fileStream, string name)
{
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConfig.ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageConfig.FileContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference(name);
    return blockBlob.UploadFromStreamAsync(fileStream);
}
```

You can download blobs with the following command:
```csharp
public Task<Stream> Load(string name)
{
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConfig.ConnectionString);
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageConfig.FileContainerName);
    return container.GetBlobReference(name).OpenReadAsync();
}
```

### Set and retrieve properties and metadata

### Implement blob leasing

### Implement data archiving and retention
