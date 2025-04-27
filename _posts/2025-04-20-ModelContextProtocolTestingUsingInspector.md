---
title: Inspecting tools for an Model Context Protocol server using @modelcontextprotocol/inspector tool
date: 2025-04-20 10:30:30 +/-TTTT
categories: [Architecture, Agentic AI, Model Context Protocol, Testing]
tags: [@modelcontextprotocol/inspector, semantic kernel, ai, ai agents, plugins, planner, llm, vector store, mcp, .NET]     # TAG names should always be lowercase
description: In this post I will show how to test a mcp server using inspector tool. We will utilise a mcp server created in my earlier blog post.
media_subpath: "/assets/images/posts/2025-04-20"
---

In this post I will show how to test a MCP server by using a basic UI based testing tool. 

> **Info:** This blog uses the MCP server created in my earlier blog post [here](https://pravinchandankhede.github.io/posts/ModelContextProtocolSimple/)
{: .prompt-info }


# Model Context Protocol
Model Context Protocol (MCP) is a new way of defining the integration between the AI apps and AI models. You can find more information here [model context protocol](https://modelcontextprotocol.io/introduction) with C#. 

MCP helps you connect with variety of sources and expose them in a way which is similar to other MCP servers. This way the client code is simplified, and it can focus on implementing the core lgoic rather than trying to integrate the Agent with all varied data sources.

# Standalone Testing of the MCP server 
In this section I will describe about a open source npm package that can we used to test an mcp server without needing a mcp client. This allows you to quickly go through the capabilities exposed by the server and also invoke them from the same interface.

## Installing the inspector
Open a command prompt and run the below command. This command will install and run tool on your machine. Make sure you have Node runtime on your machine.

```bash
>npx @modelcontextprotocol/inspector
```

The tool will output somehting like below
```bash
Starting MCP inspector...
⚙️ Proxy server listening on port 6277
🔍 MCP Inspector is up and running at http://127.0.0.1:6274
```

## Configuration of inspector
Inspector provides various options to configure the server. One such option is Transport. MCP server can connect over multiple transport channel with the clients. In this article we would be  using an [Server Side Events](https://modelcontextprotocol.io/docs/concepts/transports#server-sent-events-sse)

 - Now open this URL in browser and you should see a screen like this -
![default screen](/assets/images/posts/2025-04-20/image.png)

 - Select SSE for Transport type, provide the sse URL and then click connect. The connection would be done and you can see a "List Tools" button enabled
![transport selection](/assets/images/posts/2025-04-20/image-1.png)

 - Once you click "List Tools@, it would show you all the tools available with this server
![listing the tools](/assets/images/posts/2025-04-20/image-2.png)

 - Select one of the tool and a testing pane will open. You can run it and test
![tool call result](/assets/images/posts/2025-04-20/image-3.png)

## Source Code
You can find the source code for this project at below location

[Code](https://github.com/pravinchandankhede/agenticai/tree/main/src/model-context-protocol-demo)

## Conclusion
My objective was to demonstrate the MCP server testing at a very basic level without considering the complexity of LLMs and other tools. In later post, I will demonstrate how to integrate with LLM and AI Agents.