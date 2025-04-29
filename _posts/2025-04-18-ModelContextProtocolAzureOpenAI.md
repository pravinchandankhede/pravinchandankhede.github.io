---
title: Model Context Protocol - How to integrate Azure OpenAI ChatCompletion LLM calls with MCP Tools.
date: 2025-04-18 10:30:30 +/-TTTT
categories: [Architecture, Agentic AI, Model Context Protocol, Azure OpenAI]
tags: [ai, ai agents, plugins, planner, llm, vector store, mcp, .NET]     # TAG names should always be lowercase
description: In this post I will show how to integrate tools exposed by a MCP server and consume them through a LLM call made using raw Azure OpenAI client libraries.
---

# Model Context Protocol
This is a demo project that demonstrate the concept of using [model context protocol](https://modelcontextprotocol.io/introduction) with C#. It uses the core Nuget packages [ModelContextProtocol](https://packages.nuget.org/packages/ModelContextProtocol/0.1.0-preview.10)
MCP helps you connect with variety of sources and expose them in a way which is similar to other MCP servers. This way the client code is simplified, and it can focus on implementing the core lgoic rather than trying to integrate the Agent with all varied data sources.

In this post I will show how to use LLM tool calling feature utlizing Azure OpenAI classes and tools exposed thorugh an MCP server. 

> **Info:** This blog uses the Banking Service and MCP server created in my earlier blog post [here](https://pravinchandankhede.github.io/posts/ModelContextProtocolSimple/)
{: .prompt-info }


## MCP Client using Azure OpenAI Nuget & LLM Integration
This client demonstrates how to use the MCP tools with Azure OpenAI `ChatCompletion` classes. In this we create tools and then call them through an LLM using the tool calling technique.

### Create a Azure Client
It first creates a connection using Azure OpenAI client. This is done using the `AzureOpenAIClient` class. The client is used to create a `chatClient` instance that will be used to call an LLM based on user query.
```csharp
AzureOpenAIClient azureClient = new(
	new Uri(AppSetting.Endpoint),
	new ApiKeyCredential(AppSetting.Key));
ChatClient chatClient = azureClient.GetChatClient(AppSetting.DeploymentName);
```
### Create a MCP Client
The code later gets a list of `McpClientTool`s from the MCP server. It connects to the SSE based endpoint and retrives the list fo tools supported by the MCP server.
```csharp
var endpoint = "http://localhost:5000/sse";

// Create a new SseClientTransport with the endpoint.
var sseClientTransport = new SseClientTransport(new SseClientTransportOptions { Endpoint = new Uri(endpoint) });
// Create a new McpClient using the SseClientTransport.
_client = await McpClientFactory.CreateAsync(clientTransport: sseClientTransport);

_mcpClientTools = await _client.ListToolsAsync();
return _mcpClientTools;
```

### Set the MCP Tools for AzureOpenAIClientOptions
We will create a new `AzureOpenAIClientOptions` instance and set the `Tools` property to the list of tools we got from the MCP server. This will allow the Azure OpenAI client to use the MCP tools when calling the LLM. This instance will be later send as an argument while calling LLM with user query and conversation history.
```csharp
ChatCompletionOptions options = new();

foreach (var tool in tools)
{
	options.Tools.Add(ChatTool.CreateFunctionTool(tool.Name, tool.Description));
}
```
### Azure OpenAI LLM Call
The code then calls `CompleteChat` method to call the LLM. The method is using the `ChatClient` instance, takes `ChatCompletionOptions` instance and the user query as input. The method returns a `ChatCompletion` object which contains the response from the LLM.
```csharp
ChatCompletion completion = chatClient.CompleteChat(conversationMessages, options);
```

### Tool Invocation
The chat completion response from LLM can be of 2 types specified as part of `FinishReason` property.
 - **Stop**: This means the LLM has completed the response and no further action is required.
 - **ToolsCall**: This means the LLM has called a tool and we need to invoke the tool to get the result. The `ToolCalls` property contains the list of tools and the parameters required by that tool. As the LLM might have identifed multiple tools we need to call all of them sequentially and add the response to conversation before calling LLM again.

The process is repeated until the LLM returns a `Stop` response.
```csharp
while (completion.FinishReason != ChatFinishReason.Stop)
{
	if (completion.FinishReason == ChatFinishReason.ToolCalls)
	{
		// Add a new assistant message to the conversation history that includes the tool calls
		conversationMessages.Add(new AssistantChatMessage(completion));

		foreach (ChatToolCall toolCall in completion.ToolCalls)
		{
			conversationMessages.Add(new ToolChatMessage(toolCall.Id, 
				await HandleToolExecutionAsync(toolCall.FunctionName, GetParameters(toolCall.FunctionArguments))));
		}				
	}

	completion = chatClient.CompleteChat(conversationMessages, options);
}
```

### Run
To run the demo - 
- You can first start the BankingService project, this will start the APIs on `http://localhost:7001`
- Then you can start the MCP server project, this will start the MCP server on `http://localhost:5000/sse`
- Finally you can start the MCP Azure OpenAI Client project, this will connect to the LLM and send the user query. In response to user query, it will call the Tool and again call LLM to get final response.

You can see a sample output below -
```bash
Tool: GetBalances
Description: Get a list of accounts and balance.

Tool: GetBalance
Description: Get a balance by name.

Tool: Echo
Description: Echoes the message back to the client.

Tool: Length
Description: Echoes the length of message back to the client.

Key: name, Value: JohnDoe
Executing tool: GetBalance with parameters: System.Collections.Generic.Dictionary`2[System.String,System.Object]
Tool result: {"name":"JohnDoe","amount":1500.75}
Assistant: The balance for account "JohnDoe" is $1500.75.
```

## Source Code
You can find the source code for this project at below location

[Code](https://github.com/pravinchandankhede/agenticai/tree/main/src/model-context-protocol-demo/MCPAzureOpenAIClient)

## Conclusion
My objective was to demonstrate the integration of MCP tool with LLM tool calling feature. The implementation I showed is core logic behind many frameworks like Semantic Kernel, LangGrapg etc.