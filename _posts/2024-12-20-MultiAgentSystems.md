---
title: A look into Multi Agent Systems Architecture
date: 2024-12-20 10:30:30 +/-TTTT
categories: [AI, Architecture, AgenticAI, RAG, LLM, Multi-Agent]
tags: [agenticai, ai, semantic kernel, llm, rag]     # TAG names should always be lowercase
description: In this post we will see what a multi agent systems is, its patterns and how it can be used to solve complex problems.
media_subpath: "/assets/images/posts/2024-12-13"
---

 > This post is a part of the series on Agentic AI. In this post we will see what a multi agent systems is, its patterns and how it can be used to solve complex problems. My earlier post on Agentic AI can be found [here](https://pravinchandankhede.github.io/posts/AgenticAI/).
{: .prompt-info }

## What are Agents?

AI agents are autonomous systems capable of sensing, learning and acting upon their environments(both digital & physical) to achieve specific objectives.

## What are AI Agents?

AI Agent is an software that leverages the Generative AI capabilities to perform complex task and exhibit an human like behaviour. These agents are self driven and can adjust thier behavior based on environmental and data parameters. These agents do not need constant human intervention and can work continusly round the clock just like any other software. These agents are aslo capable of integrating with variety of tools and perform actions. They keep on learning through a memory component that help them remember and improve upon the responses.

In essense, AI Agents can -

- Understand complex goals
- Plan and execute multi-step tasks
- Interactg with tools, APIs, and other agents
- Learning from feedback and adapting behavior

They go beyond traditional chatbots by incorporating planning, memory, tool use, and collaboration

## Some Usecases for AI Agents

**Proactive AI Agents**: Agents can now initiate actions without being prompted, anticipating user needs and system requirements.

**Hyper-Personalization**: AI agents tailor interactions based on user behavior, preferences, and context, especially in customer service and healthcare.

**Multimodal Capabilities**:: Agents can process and generate text, images, audio, and video, enabling richer interactions.

**Emotional Intelligence**: Some agents are being trained to detect and respond to human emotions, improving user experience in therapy, education, and support.

**Tool-Using Agents**: Agents now integrate with external tools (e.g., databases, APIs, spreadsheets) to complete tasks autonomously.

**Multi-Agent Collaboration**: Systems of agents are being deployed to work together on complex problems—like logistics, simulations, and autonomous robotics.

**Integration with IoT and Edge Devices**: AI agents are increasingly embedded in smart devices, enabling real-time decision-making at the edge.

**Ethical and Transparent Agents**: There's a growing focus on explainability, fairness, and accountability in agent decision-making.

## Types of AI Agents

## Brain of an Agent

## Memory of an Agent

## Communication Patterns for Agent

Modern AI Agent Communication Patterns

| **Pattern**                     | **Description**                                                                 | **Use Cases**                                           | **Example**                                                                 |
|-------------------------------|---------------------------------------------------------------------------------|---------------------------------------------------------|------------------------------------------------------------------------------|
| **Group Chat**                | Multiple agents interact in a shared conversational space.                      | Brainstorming, collaborative planning                   | Agents in AutoGen solving a coding task together.                           |
| **Hand-off**                  | One agent delegates a task to another based on expertise.                       | Customer service, multi-step workflows                  | General agent hands off legal query to a legal expert agent.               |
| **Tool-Using Agent**          | Agents use external tools (APIs, databases, etc.) to complete tasks.            | Research, automation, data analysis                     | Agent queries a weather API and generates a report.                         |
| **Negotiation**               | Agents engage in dialogue to reach agreements or resolve conflicts.             | Resource allocation, contract negotiation               | Agents representing departments negotiate resource usage.                   |
| **Principal-Agent-Counterparty** | Agent acts on behalf of a user to interact with external systems.              | Advocacy, customer service, legal assistance            | AI agent negotiates a refund with a company on behalf of a user.           |
| **ReAct Loop**                | Agents reason, act, observe, and iterate.                                       | Complex task execution, autonomous workflows            | LangChain agent plans, executes, and refines a multi-step task.            |
| **Memory-Augmented**          | Agents use long-term memory to inform future interactions.                      | Personalized assistants, long-running workflows         | Agent remembers user preferences and adapts responses accordingly.         |

Agents have evolved from simple software agents to more complex AI Agents. AI Agents are more intelligent and can take decisions based on the environment they are in. They can also learn from the environment and improve their decision making capabilities.

## Conclusion

We saw the evolution of AI Agents and how they have evolved from simple bots to more complex AI Agents. We also saw the characteristics of AI Agents and the core components of AI Agents. We also saw the categories and types of AI Agents. We also saw the patterns of AI Agents that can be used to solve common problems. AI Agents are the future of AI and will play a key role in solving complex problems.

Please feel free to leave your feedback.
