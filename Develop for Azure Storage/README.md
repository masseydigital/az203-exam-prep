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

### Implement Partitioning Schemes

### Set the appropriate consistency level for operations

## Develop Solutions that use a Relational Database

### Provision and Configure Relational Databases

### Configure Elastic Pools for Azure SQL Database

### Create, Read, Update, and Delete data tables by using code

## Develop Solutions that use Blob Storage

### Move items in blob storage between storage accounts or containers

### set and retrieve properties and metadata

### Implement blob leasing

### Implement data archiving and retention
