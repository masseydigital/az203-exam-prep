# Monitor, Troubleshoot, and Optimize Solutions (10-15%)

## Develop code to support scalability of apps and services

### Implement autoscaling rules and patterns (schedule, operational/system metrics, singleton applications)

### Implement code that handles transient faults

## Integrate Caching and Content Delivery within solutions

### Store and Retrieve data in Azure Redis Cache

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
