---
title: Backend for Frontend - API Gateway Pattern
date: 2024-07-27 10:30:30 +/-TTTT
categories: [Architecture, API, Backend]
tags: [api, bff, angular, limit, .net9, middleware]     # TAG names should always be lowercase
description: We will discuss the importance of BFF API Gateway and how to use it effectively.
---

# 🧭 API Gateway Pattern
Today we are in era of application development where we are developing applications that are highly scalable, easy to user, responsive and performant. As applications are growing so is the choice for API driven systems. In this API driven development, most of the system are desinged around the collections of API which work together to provide a set of functionality or feature. This is frontended using a UI layer which helps user 'talk' to system.
There are many choices to develop this UI layer, however the most prominent one is to have single page application or SPA which are becoming defacto standards across industry.

These SPA apps are generally lazily loaded on the client browser and they often call API(s) to fetch the data and perform some actions.


# 🧭 The API Gateway Pattern: A Comprehensive Guide with C# Examples

## 📌 Introduction

In modern software architecture, especially with the rise of **microservices**, the **API Gateway** pattern has become a cornerstone. It acts as a single entry point for clients, handling requests by routing them to the appropriate microservices, aggregating results, and performing cross-cutting concerns like authentication, logging, and rate limiting.

In this  article I will explore the API Gateway pattern in depth, with practical **C# code samples**, and discuss its implementation across different architectural paradigms.

---

## 🧱 What is an API Gateway?

An **API Gateway** is a server that sits between clients and backend services. It abstracts the internal system architecture and provides a unified API to the clients.

### 🔍 Responsibilities of an API Gateway:
- Request routing
- Load balancing
- Authentication and authorization
- Rate limiting and throttling
- Response transformation
- Caching
- Logging and monitoring

---

## 🧩 Why Use an API Gateway?

Without an API Gateway, clients must interact with each microservice individually. This leads to:
- Increased complexity on the client side
- Tight coupling between client and services
- Duplication of cross-cutting concerns

### ✅ Benefits of Using an API Gateway:
- **Simplified Client Communication**: Clients interact with a single endpoint.
- **Centralized Security**: Authentication and authorization are managed in one place.
- **Rate Limiting and Throttling**: Prevent abuse and ensure fair usage.
- **Load Balancing**: Distribute requests evenly across services.
- **Response Aggregation**: Combine responses from multiple services.
- **Protocol Translation**: Convert between different protocols (e.g., HTTP to gRPC).

### Challenges of Using an API Gateway:
- **Single Point of Failure**: If the gateway goes down, all services become inaccessible.
- **Performance Overhead**: Additional processing can introduce latency.
- **Complex Configuration**: Managing routing, security, and policies can be complex.
- **Scalability**: The gateway itself must be scalable to handle traffic.

## Ways to implement API Gateway

1. **Monolithic API Gateway**  
   A single, centralized API Gateway that handles all incoming requests and routes them to the appropriate backend services. This approach is simple to implement but can become a bottleneck as the system scales.

2. **Microservices API Gateway**  
   Each microservice has its own dedicated API Gateway. This approach provides better scalability and isolation but increases the complexity of managing multiple gateways.

3. **Serverless API Gateway**  
   Using serverless platforms like AWS API Gateway or Azure API Management to handle API requests. This approach reduces infrastructure management and scales automatically based on demand.

4. **Custom Middleware**  
   Implementing a custom middleware layer in your backend application to act as an API Gateway. This provides flexibility but requires more development effort and maintenance.

5. **BFF (Backend for Frontend)**  
   Creating a dedicated API Gateway for each frontend application (e.g., web, mobile). This ensures that each frontend gets tailored APIs optimized for its needs.

6. **GraphQL Gateway**  
   Using GraphQL as an API Gateway to aggregate and fetch data from multiple backend services. This approach provides flexibility in querying data but requires a GraphQL server setup.

7. **Service Mesh Integration**  
   Leveraging service mesh tools like Istio or Linkerd to implement API Gateway functionality as part of the service mesh. This approach is suitable for Kubernetes-based environments.

## 🛠️ Implementing an API Gateway in C#

Let’s start with a **basic custom API Gateway** using **ASP.NET Core**.

### 🧪 Sample Setup

Assume we have two microservices:
- `ProductService` at `http://localhost:5001`
- `OrderService` at `http://localhost:5002`

### 📦 Step 1: Create the API Gateway Project

```bash
dotnet new webapi -n ApiGateway
cd ApiGateway
```

### Startup.cs or Program.cs (ASP.NET Core 6+)

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.MapGet("/products", async (HttpClient http) =>
{
    var response = await http.GetAsync("http://localhost:5001/products");
    return Results.Content(await response.Content.ReadAsStringAsync(), "application/json");
});

app.MapGet("/orders", async (HttpClient http) =>
{
    var response = await http.GetAsync("http://localhost:5002/orders");
    return Results.Content(await response.Content.ReadAsStringAsync(), "application/json");
});

app.Run();

```

Add HttpClient to Program.cs:

```csharp
builder.Services.AddHttpClient();
```

This is a basic reverse proxy. Let’s now explore more advanced patterns.

## Cloud-Native API Gateways
### Azure API Management (APIM)

Azure APIM provides:

 - Developer portal
 - Policy-based request/response transformation
 - OAuth2/JWT validation
 - Rate limiting

 Example Policy (XML):

 ```xml
 <inbound>
    <base />
    <validate-jwt header-name="Authorization" failed-validation-httpcode="401">
        <openid-config url="https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration" />
        <required-claims>
            <claim name="aud">
                <value>api://your-api-id</value>
            </claim>
        </required-claims>
    </validate-jwt>
</inbound>
```

### AWS API Gateway
AWS offers:

 - REST and HTTP APIs
 - Integration with Lambda, EC2, or any HTTP backend
 - Built-in throttling, caching, and authorization


## Custom API Gateway with Ocelot (C#)
Ocelot is a popular .NET API Gateway library.

Install Ocelot

```bash
dotnet add package Ocelot
```

ocelot.json

```json
{
  "Routes": [
    {
      "DownstreamPathTemplate": "/products",
      "DownstreamScheme": "http",
      "DownstreamHostAndPorts": [
        { "Host": "localhost", "Port": 5001 }
      ],
      "UpstreamPathTemplate": "/products",
      "UpstreamHttpMethod": [ "GET" ]
    }
  ],
  "GlobalConfiguration": {
    "BaseUrl": "http://localhost:5000"
  }
}
```

Program.cs
```csharp
builder.Services.AddOcelot();
var app = builder.Build();
await app.UseOcelot();
app.Run();
```

## API Gateway in Microservices Architecture

In a microservices setup, the API Gateway:

 - Acts as a facade for internal services
 - Helps with service discovery
 - Enables centralized security

 ### Aggregation Example

 ```csharp
 app.MapGet("/dashboard", async (HttpClient http) =>
{
    var products = await http.GetStringAsync("http://localhost:5001/products");
    var orders = await http.GetStringAsync("http://localhost:5002/orders");

    return Results.Json(new { products = JsonSerializer.Deserialize<object>(products), orders = JsonSerializer.Deserialize<object>(orders) });
});
```

## BFF (Backend for Frontend)
A BFF is a specialized API Gateway tailored to a specific frontend (e.g., mobile, web).

🔄 Why BFF?
 - Different frontends have different needs
 - Avoid over-fetching or under-fetching
 - Encapsulate frontend-specific logic

🧪 Example

```csharp
app.MapGet("/mobile/dashboard", async (HttpClient http) =>
{
    var summary = await http.GetStringAsync("http://localhost:5001/mobile-summary");
    return Results.Json(new { summary });
});
```

You can have separate BFFs for:

 - Web
 - Mobile
 - IoT

## API Gateway vs Service MFeature	API Gateway	Service Mesh

---------------------------------------------------------
| Feature | API Gateway | Service Mesh |
----------------------------------------------------------
| Scope	| North-South (client to service)	| East-West (service to service)
| Focus	| Entry point, auth, rate limiting	| Traffic control, observability
| Examples | 	Ocelot, Azure APIM	| Istio, Linkerd
| Language	| App-level (C#)	| Sidecar proxies (Envoy)esh

Can They Coexist?
Yes! Use API Gateway for external traffic and Service Mesh for internal communication.

## Best Practices
Secure your gateway with OAuth2/JWT
Rate limit to prevent abuse
Use caching for performance
Log and monitor all requests
Keep the gateway stateless
Avoid putting business logic in the gateway


## Testing and Monitoring
Use tools like:

 - Postman for API testing
 - Serilog or Application Insights for logging
 - Prometheus + Grafana for metrics

## 📚 Conclusion
The API Gateway pattern is essential in modern distributed systems. Whether you're building a custom gateway in C#, using cloud-native solutions like Azure APIM, or implementing BFFs for frontend optimization, the pattern provides a powerful abstraction layer that simplifies client interactions and centralizes cross-cutting concerns.

