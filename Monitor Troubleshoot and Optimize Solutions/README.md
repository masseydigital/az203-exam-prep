# Monitor, Troubleshoot, and Optimize Solutions (10-15%)

## Develop code to support scalability of apps and services

### Implement autoscaling rules and patterns (schedule, operational/system metrics, singleton applications)

### Implement code that handles transient faults

## Integrate Caching and Content Delivery within solutions

### Store and Retrieve data in Azure Redis Cache
_Caching_ is the act of storing frequently-accessed data in memory that is very close to the application that consumes the data.  Caching is used to increase performance and reduce the load on your servers.  We use Redis to create an in-memory cache that can provide excellent latency and potentially improve performance.

_Redis (REmote DIctionary Server) cache_ is an open-source, in-memory key value pair store.  It's popular because it's fast an dcan store and manipulate common data types such as strings, hashes, and sets.  

Azure Cache for Redis is based on the popular open-source Redis Cache.  It gives you access to a secure, dedicated Redis cache, managed by Microsoft.  A cache created using Azure Cache for Redis is accessible from any application within Azure.  Azure Cache for Redis is typically used to improve the performance of systems that rely heavily on back-end data stores.

Redis supports a variety of data types all oriented around _binary safe strings_.  This means that you can use any binary sequence for a value.  Each data value is associated to a key which can be used to lookup the value from the cache.  Redis works best with smaller values (100k or less), so consider chopping up bigger data into multiple keys.  

Data is stored in Redis in _nodes_ and _clusters_.  Nodes are space in Redis where your data is stored, Clusters are sets of (3) or more nodes that your dataset is split across.

Redis distributes data in 3 ways:
1) Single Node
2) Multiple Node
3) clustered

Redis caching architectures are split across Azure in tiers:
1) Basic Cache: provides single node Redis caching.  The complete dataset will be stored in a single node.  Used for dev/test.
2) standard Cache: create multiple node architectures in a two-node primary/secondary configuration.  Support master/slave replication.
3) Premium: standard tier but data has the ability to persist, take snapshots, and back-up data.  Includes geo-replication and gives you complete access with Azure Virtual Network.

You can create a Redis Cache with the Azure Portal, Azure CLI, or Azure PowerShell.

There are several commands that are supported in Redis:
1) ping
2) set [key] [value]
3) get [key]
4) exists [key]
5) type [key]
6) incr [key]
7) incrby [key] [amount]
8) del [key]
9) flushdb

Redis also provides its own command-line tools to experiment with commands, (redis-cli)

We also need a way to expire values that are stale in our Redis Cache.  This is done by applying _time to live (TTL)_ to a key.  To connect to a Redis Cache, we need the host address, port number, and an access key.

A popular Api for interacting with a Redis Cache is the _StackExchange.Redis_ package from NuGet.

A connection string will look like the following (you can store the value in Azure Key Vault):

```powershell
[cache-name].redis.cache.windows.net:6380,password=[password-here],ssl=True,abortConnect=False
```

The main connection object in the StackExchange.Redis Cache is the StackExchange.Redis.ConnectionMultiplexer object.

You can connect to a RedisCache with the following code:

```csharp
using StackExchange.Redis;
...
var connectionString = "[cache-name].redis.cache.windows.net:6380,password=[password-here],ssl=True,abortConnect=False";
var redisConnection = ConnectionMultiplexer.Connect(connectionString);
```

Once you have the connection multiplexer, there are (3) primary things that you will want to do:
1) Access a Redis Database
2) Make use of the publisher/subscript features of Redis
3) Access an individual server for maintenance or monitoring purposes

You can access a Redis database with the following code: 

```csharp
IDatabase db = redisConnection.GetDatabase();
```

Once you have a connection to the database, you can run commands against it such as the following:

```csharp
bool wasSet = db.StringSet("favorite:flavor", "i-love-rocky-road");

string value = db.StringGet("favorite:flavor");
Console.WriteLine(value); // displays: ""i-love-rocky-road""
```

The IDatabase interface also includes several other methods to interact with the Redis Cache:
1) CreateBatch
2) CreateTransaction
3) KeyDelete
4) KeyExists
5) KeyExpire
6) KeyRename
7) KeyTimeToLive
8) KeyType : can be string, list, zset, and hash

You can also pass contextual commands to the Redis server with the Execute or ExecuteAsync methods:

```csharp
var result = db.Execute("ping");
Console.WriteLine(result.ToString()); // displays: "PONG"
```

Once you are done with your connection, you can dispose of the object with the .Dispose() method, or wrap the connection in a using block.

```csharp
string connectionString = config["CacheConnection"];

using (var cache = ConnectionMultiplexer.Connect(connectionString))
{

}
```

### Develop Code to implement CDNs in solutions
Azure Content Delivery Networks (CDNs) can help reduce latency and improve performance for high bandwidth content.  A CDN is a network of web servers that cache website content in different geographical locations.  CDNs help to minimize latency by caching website content at point-of-presence (POP) locations that are close to large clusters of users.  The use of a CDN is transparent to users of your site.  CDN's are typically best-suited for applications serving much static content. 

A CDN works by:

1) A user requests a file or asset
2) The request is routed to the closest POP
3) If data is present in the POP, retrieve it, else request it from the origin server, return the data to the user and store a local copy.

There are four types of CDNs available in Azure:
1) Standard Microsoft
2) Standard Akamai
3) Standard Verizon
4) Premium Verizon

Azure Content Delivery Network (CDN) is Azure's Contend Delivery Network.  It's a distributed network of servers that is used to cache and store content.  The server locations are referred to as Point-of-Presence (POP) locations.  CDNs store cached data on edge servers, or servers close to your users, in these POP locations.

Azure CDN requires specifying a new CDN in Azure.  Then you configure an endpoint that points to the origin server or blob storage account.  Azure requires unique names for the CDN path and origin server URL.

There are two main parts of a CDN:
1) CDN Profiles: A container for one or more CDN endpoints.  Each CDN endpoint specifies a pricing tier and provides a link to the cached content on the POP.
2) CDN Endpoints: When you create an endpoint, you must ensure that the endpoint type matches the origin source correctly.  I.e. static website in Azure require an endpoint to "custom origin".  

Azure CDNs have a unique url that follows the format endpointname.azureedge.net.  Custom domain settings enable you to specify a Canonical Name (CName) record in Domain Name System (DNS) that points ot the CDN URL.  Suppose the user types in the custom domain name, for example, www.contoso.com.  The DNS maps that domain name to the POP endpoint URL and connects the user to the URL.

There are two types of caching behvaiour that Azure CDN can perform
1) Caching rules: Can either be global or custom.  Custom rules apply to specific paths and file extensions.
2) Query string caching: Query string caching enales you to configure how Azure CDN responds to a query string.  Query string caching has no effect on files that can't be cached.

_Geo-filtering_ enables you to allow or block content in specific countries, based on the country code.  In the Azure CDN Standard for Microsoft tier, you can only allow or block the entire site.

### Invalidate cache content (CDN or Redis)

## Instrument Solutions to Support Monitoring and Logging

### Configure instrumentation in an app or service by using Application Insights

### Analyze and troubleshoot solutions by using Azure Monitor

### Implement Applications Insights Web Test and Alerts
