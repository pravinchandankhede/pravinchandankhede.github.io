---
title: Model Context Protocol - Integrate MCP tools with Semantic Kernel Plugins and Azure OpenAI LLM.
date: 2025-04-15 10:30:30 +/-TTTT
categories: [Architecture, Agentic AI, Model Context Protocol, Azure OpenAI, Semantic Kernel]
tags: [ai, ai agents, plugins, planner, llm, vector store, mcp, .NET]     # TAG names should always be lowercase
description: In this post I will show how to integrate tools exposed by a MCP server and integrate them with Semantic Kernel using the Plugin features. We will also use Azure OpenAI to call these tools.
---

# Model Context Protocol
This is a demo project that demonstrate the concept of using [model context protocol](https://modelcontextprotocol.io/introduction) with C#. It uses the core Nuget packages [ModelContextProtocol](https://packages.nuget.org/packages/ModelContextProtocol/0.1.0-preview.10) and [SemanticKernel](https://www.nuget.org/packages/Microsoft.SemanticKernel).
MCP helps you connect with variety of sources and expose them in a way which is similar to other MCP servers. This way the client code is simplified, and it can focus on implementing the core lgoic rather than trying to integrate the Agent with all varied data sources.

In this post I will show how to use [Semantic Kernel SDK](https://learn.microsoft.com/en-us/semantic-kernel/overview/) with MCP servers and tools. This integration helps build Agents that are able to talk to diverse set of tools using the same protocol streamlining the developments process and code. 

> **Info:** This blog uses the Banking Service and MCP server created in my earlier blog post [here](https://pravinchandankhede.github.io/posts/ModelContextProtocolSimple/)
{: .prompt-info }


## Semantic Kernel based client with MCP Client Tools and Azure OpenAI LLM Integration
This client demonstrate how to integrate the MCP tools with Semantic Kernel based client. The client uses the `McpClient` class to connect to the MCP server and list down all the tools available with it. The client then uses the `SemanticKernel` class to create a kernel instance and add the MCP tools to the Plugins collection. The kernel is then used to call the LLM using the tools. Lets see the steps to implement this.

### Installing Semantic Kernel and Other needed NuGets
Create a .NET Console application and install below Nuget Packages to it. You can choose to use the Visual Studio or .NET CLI to install the packages.
```bash
dotnet add package Microsoft.SemanticKernel
dotnet add package Microsoft.SemanticKernel.Connectors.AI.OpenAI
dotnet add package ModelContextProtocol
dotnet add package Microsoft.Extensions.Logging.Console
```

### Create a Semantic Kernel Client
Use the below code to create an instance of `KernelBuilder` instance. This will be used to create an instance of Kernel later. The code also configures the Azure OpenAI LLM endpoint for `ChatCompletion` and provides logging support for it.
```csharp
var builder = Kernel.CreateBuilder();

builder.AddAzureOpenAIChatCompletion(
	AppSetting.DeploymentName,
	AppSetting.Endpoint,
	AppSetting.Key);

builder.Services.AddLogging(loggingBuilder =>
{
	loggingBuilder.AddConsole();
	loggingBuilder.SetMinimumLevel(LogLevel.Information);
});

return builder;
```

### Create a MCP Client
We will then configure the MCP Client to get the list of tools supported by the MCP client. This code is mostly same as used in my above demo

```csharp
public static async Task<IList<McpClientTool>> GetMcpTools()
{
	// Read this endpoint from a config file.
	var endpoint = "http://localhost:5000/sse";

	// Create a new SseClientTransport with the endpoint.
	var sseClientTransport = new SseClientTransport(new SseClientTransportOptions { Endpoint = new ri(endpoint) });
	// Create a new McpClient using the SseClientTransport.
	var client = await McpClientFactory.CreateAsync(clientTransport: sseClientTransport);

	return await client.ListToolsAsync();
}
```

### Setup the MCP Tools with Semantic Kernel
Next we will configure the tools with the `KernelBuilder` instance. The code first gets the `Kernel` instance and then the list of `McpClientTools`. These are then added to the `Plugins` collection of SemanticKernel. Here we use helper method which performs the job of transformation tools into plugins. 

```csharp
var kernel = GetKernelBuilder().Build();
var tools = GetMcpTools().GetAwaiter().GetResult();

kernel.Plugins.AddFromFunctions("Banking", tools.Select(tool => tool.AsKernelFunction()));
```

### Call the LLM with User Query
Now we will simulate a user query to LLM and see if the `Kernel` is able to call the MCP tools. The code gives a hard coded prompt to the LLM and then calls the `GetBalance` tool. The tool call is internally by `Kernel` itself as we have set the `FunctionCallBehaviour` property to Auto. We have also configured to retain the parameters returned by the LLM. This really helps in automating the call and subsequent calling logic becomes quite easy. The client code do not need to intercept the LLM response anymore and then further decide to call the tool manually. This simplifies the client code.

We then print the response to the console window. As you can see the Semantic Kernel really makes it very simple to integrate with MCP tools and automate most of the complexity of calling tools as we have seen in my earlier basic demos.

```csharp
OpenAIPromptExecutionSettings executionSettings = new()
{
	Temperature = 0,
	FunctionChoiceBehavior = FunctionChoiceBehavior.Auto(options: new() { RetainArgumentTypes = rue })
};

var prompt = "get me the balance for JohnDoe";
var result = await kernel.InvokePromptAsync(prompt, new(executionSettings)).ConfigureAwaitfalse);
Console.WriteLine($"\n\n{prompt}\n{result}");
```

### Run
To run the demo -
- You can first start the BankingService project, this will start the APIs on `http://localhost:7001`
- Then you can start the MCP server project, this will start the MCP server on `http://localhost:5000/sse`
- Finally you can start the MCP Semantic Kernel Client project, this will connect to the LLM and send the user query. In response to user query, it will call the Tool and again call LLM to get final response.

You can see a sample output below -
```bash
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function (null)-InvokePromptAsync_04f2495fcfe04074aeb16a0eb2763883 invoking.
info: Microsoft.SemanticKernel.Connectors.AzureOpenAI.AzureOpenAIChatCompletionService[0]
      Prompt tokens: 140. Completion tokens: 20. Total tokens: 160.
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function Banking-GetBalance invoking.
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function Banking-GetBalance succeeded.
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function Banking-GetBalance completed. Duration: 0.2761426s
info: Microsoft.SemanticKernel.Connectors.AzureOpenAI.AzureOpenAIChatCompletionService[0]
      Prompt tokens: 201. Completion tokens: 14. Total tokens: 215.
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function (null)-InvokePromptAsync_04f2495fcfe04074aeb16a0eb2763883 succeeded.
info: Microsoft.SemanticKernel.KernelFunction[0]
      Function (null)-InvokePromptAsync_04f2495fcfe04074aeb16a0eb2763883 completed. Duration: 3.6370541s


get me the balance for JohnDoe
The balance for JohnDoe is $1500.75.
```

From the trace you can see, it actually called the "Banking-GetBalance" tool and passed the parameters to it. The tool then called the BankingService API and returned the result back to LLM. The LLM then returned the final response back to the client. This is a very simple example of how to integrate with MCP tools using Semantic Kernel and Azure OpenAI LLM.

## Source Code
You can find the source code for this project at below location

[Code](https://github.com/pravinchandankhede/agenticai/tree/main/src/model-context-protocol-demo/Clients/MCPSemanticKernelClient)

## Conclusion
My objective was to demonstrate the model context protocol and its consumption using the Semantic KErnel SDK. As you can see, both these technologies compliments each other and its quite interesting to see how the Semantic Kernel is able to extensible to integrate with modern protocols. This makes a great choice to consider for Agent development which we will see in later posts.