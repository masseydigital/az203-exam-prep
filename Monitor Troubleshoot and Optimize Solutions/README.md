# Monitor, Troubleshoot, and Optimize Solutions (10-15%)

## Develop code to support scalability of apps and services

### Implement autoscaling rules and patterns (schedule, operational/system metrics, singleton applications)
Autoscaling enables a system to adjust the resources required to meet the varying demand from users, while controlling the costs associated with these resources.  Autoscaling can be triggered on a schedule, or by assessing whether the system is running short on resources.  Autoscaling is a feature of an App Service Plan.  

Autoscaling is a cloud system or process that adjusts available resources based on the current demand.  It performs in and out scaling as opposed to up and down scaling.  This means that Azure App Service Autoscaling can add servers, vms, etc, but not increase the power of them.

Autoscaling makes its decision based on rules that you define.  A rule specifies the threshold for a metric, and triggers an autoscale event when the threshold is crossed.  It can also deallocate resources when the workload has diminished.  You must be careful to limit the rules so that a DDoS attack does not end up bankrupting you.

Azure provides (2) metrics for autoscaling:
1) Scale based on a metric: i.e. length of disk queue, # of HTTP requests, etc.
2) Scale to a specific instance count according to schedule.  i.e. particular day of the week.

Metrics that you can monitor for you web app include:
1) CPU Percentage: An indication of the CPU usage across all instances.  
2) Memory Percentage: Captures memory occupancy across instances.
3) Disk Queue Length: The number of outstanding I/O operations across all instances.
4) Http Queue Length: How many client requests are awaiting.
5) Data In: # of bytes receives across all instances
6) Data Out: # of bytes sent by all instances.
7) Azure Service Metrics:  You can also scale based on metrics from other Azure App Services. i.e. Service Bus Queue length

Autoscaling works by analyzing trends in metric values over time across all instances.  The steps include:

1) An autoscale rule aggregates the values retrieved for a metric for all instances across a period of time know as _time grain_.  In most cases this is 1 minute.  The last aggregated value is known as the _time aggregation_.  options include Average, Minimum, Total, Last, and Count.

2) An autoscale rule performs a longer-term aggregation which is known as the _Duration_.  The minimum duration is 5 minutes.

Once the autoscale rule detects that a metric has crossed a threshold, it an perform an autoscale action.  It can be to scale in or scale out.  An autoscale operation uses an operator (less than, greater than, equal to) to determine how to react to a threshold.  

An autoscale action has a _cooldown_ period specified in minutes.  During this interval, the scale rule won't be triggered again.  The minimum period is 5 mins.

It is best practice to pair scale in and scale out rules for threshold values.  When scaling out, only 1 condition needs to be met to perform the scale.  When scaling in, all conditions need to be met to perform the scale.  

### Implement code that handles transient faults
Transient errors are an error or loss of service that quickly resolves itself.  Some examples from a cloud based service are: network issues, app is competing for client usage, infrastructure is poor.  

Loss of connectivity is the most obvious error that an app connecting to a cloud app can face.  In many cases these errors are self-healing, and once you have connection again they go away.  You can do this by setting reconnect policies if your app loses connection.  

A web service can have an active connection, but be unable to respond to calls quickly.  All apps need to decide when to give up on a request.  An app should only retry a request if the operation is known to be idempotent.  i.e. if a request increments a value, then if all the requests are processed the value may not be correct.  

Successful cloud-based services need to have a policy of throttling requests when the service is struggling under load to respond in an appropriate amount of time.  If a service notifies an app that it is being throttled, the app should reduce the number of request it is sending.  

A service may also become unavailable for a multitude of reasons.  A best practice is to cache api calls to be able to run api actions if the server is down.

The first step to reacting to transient faults is to detect them.  The following list defines three error types:
1) Transient:  It will be self-correcting and that's why retrying the operation could be successful after a period of time.
2) Permanent: The app can't expect a response from the service, and should try to get the data from another service or local cache.
3) Terminal: There's nothing the app can do without access to the service, and the app should quit.

Once the error is detected, the app can implement a _retry strategy_ using any information the service has given as to when the operation should be retried. A good retry strategy has the following characteristics:
1) A defined maximum # of retries
2) The interval between retries (can be random or exponential)
3) Uses a configuration file so that these can easily be adjusted.

Some common retry strategies include:
1) Instant retry: If packets are corrupted, try again by sending straight away
2) Regular: Instant retry, then try every x number of time grain
3) Incremental: Instant retry, then wait fixed periods of time incremented by a value
4) Exponential: Instant retry, then wait period exponentially over a time period.
5) Random:  Any combination of the above.

Sometimes apis may also provide a _Retry-After_ header in the response that will define the apps retry-strategy.  

Representational State Transfer (REST) is an approach for writing client and server application.  RESTful services are stateless, the client and server don't keep track of each other.  REST is normally sent through HTTP requests.  

Whatever the error is returned by the service, the apps needs to catch a returned error, check what it is, and then take appropriate steps to handle it.  In C#, this is a try/catch block. 

## Integrate Caching and Content Delivery within solutions

### Store and Retrieve data in Azure Redis Cache
_Caching_ is the act of storing frequently-accessed data in memory that is very close to the application that consumes the data.  Caching is used to increase performance and reduce the load on your servers.  We use Redis to create an in-memory cache that can provide excellent latency and potentially improve performance.

_Redis (REmote DIctionary Server) cache_ is an open-source, in-memory key value pair store.  It's popular because it's fast and can store and manipulate common data types such as strings, hashes, and sets.  

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
System administrators want apps to include some form of logging to help manage application performance in production.  Monitoring of these logs can be automated and help your company identify any health issues across its suite of apps.  There have been several attempts to standardize logging from organiations like Apache, who have external libraries such as Log4net and Log4J.

There is a common set of logging levels that languages will implement to some degree:
1) Trace or finest: Lowest level of information - may include personal information
2) Debug: Used in active development.
3) Information: Can be included to show health of the app.
4) Warning: Show abnormal and unexpected flows in an app.  
5) Error or Critical: An app needs to quit, or restart due to a failure.  

Transient errors should be logged at the warning level and should include (1) Time to recover from the error, (2) Number of retry attempts before resolved.

The C# libraries Microsoft.Extensions.Logging and Microsoft.Extensions.DependencyInjection are good places to start for loggging in .Net.

```csharp
private static ILogger main_logger;

// Instantiate Dependency Injection and configure logger
var serviceProvider = new ServiceCollection()
    .AddLogging(cfg => cfg.AddConsole())
    .Configure<LoggerFilterOptions>(cfg => cfg.MinLevel=LogLevel.Debug)
    .BuildServiceProvider();

// Set instances of logger
var logger = serviceProvider.GetService<ILogger<RetryPolicy>>();
main_logger = serviceProvider.GetService<ILogger<Program>>();

retries = new RetryPolicy(logger);
```

### Configure instrumentation in an app or service by using Application Insights

### Analyze and troubleshoot solutions by using Azure Monitor

### Implement Applications Insights Web Test and Alerts
