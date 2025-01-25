---
title: Semantic Kernel Vector Memory using Azure Search
date: 2025-01-16 10:30:30 +/-TTTT
categories: [Architecture, AgenticAI, Vector, Embeddings]
tags: [semantic kernel, ai, plugins, planner, llm, vector store, azure search]     # TAG names should always be lowercase
description: This post highlights the benefits of Semantic Kernel vector memory and its use in developing AI Agents. It also shows how to utilize Azure Search to store and retrieve the vectors and use them in the AI Agents.
---

## What is Semantic Kernel?
[Semantic Kernel](https://learn.microsoft.com/en-us/semantic-kernel/overview/) is an SDK by Microsoft which helps us to write AI Agents. These AI Agents are highly collaborative and can use different LLMs to infer and act like humans.
It helps us develop agents that are intelligent, autonomous and adaptable. Semantic Kernel has many features that helps us to do the mundane coding taks systematically and at a good level of abstraction. One such feature is Vector Memory which is main topic of this blog post.

## Semantic Kernel Vector Memory
The vector memory feature in Semantic Kernel allows us to store and manage embeddings in a vector store. This store can be either in-memory or backed by an external database, depending on the use case. The vector memory is what gives an agent its power to remember and infer the things to better arrives at a decision. This is used as a basis for RAG based automation. 
Semantic Kernel supports variety of vector stores that can be found [here](https://learn.microsoft.com/en-us/semantic-kernel/concepts/vector-store-connectors/out-of-the-box-connectors/).

### Key Characteristics
 - **In-Memory Storage**: The vector store can operate entirely in memory, which allows for high-speed operations and quick access to data. This is ideal for scenarios where performance is critical, and the data does not need to be persisted long-term.
 - **Support for Various Data Types**: The vector store supports a wide range of data types, including strings, numbers, and custom objects. This allows it to be suitable for variety of use cases.
 - **Multiple Vectors per Record**: The vector store can handle multiple vectors within a single record, allowing for complex data representations and more detailed embeddings.
 - **Advanced Querying Capabilities**: The vector store supports advanced querying capabilities, including filtering and full-text search, which enhances its utility in data retrieval applications.

## Benefits of Semantic Kernel Vector Memory Integration
Semantic Kernel is a powerful tool that enables the generation of embeddings for text data. These embeddings are numerical representations of text that capture the semantic meaning of the content. The benefits of using Semantic Kernel include:

 - **Improved Search Accuracy**: By converting text into embeddings, search algorithms can better understand the context and relevance of the data, leading to more accurate search results.
 - **Enhanced Data Analysis**: Embeddings allow for advanced data analysis techniques, such as clustering and classification, which can uncover hidden patterns and insights.
 - **Scalability** : Semantic Kernel can handle large volumes of data, making it suitable for enterprise-level applications.

## What is Azure Search?
Azure Search is a cloud-based search service that provides powerful indexing and querying capabilities. It is essential for:

 - **Efficient Data Retrieval**: Azure Search enables fast and efficient retrieval of data from large datasets.
 - **Advanced Querying**: With support for complex queries, Azure Search allows users to perform sophisticated searches and filter results based on various criteria.
 - **Integration with Other Azure Services**: Azure Search seamlessly integrates with other Azure services, such as Azure Cognitive Services and Azure Machine Learning, to provide a comprehensive data processing solution.
 - **Skill Set**: Azure Search is a powerful tool that allows you customized the retrival, extraction, transformation of content by using skill pipelines.

## Demo Application
In this blog post, I will demonstrate how to use Semantic Kernel and Azure Search to generate and store embeddings for text data. I will create an EmbeddingEngine class that interacts with the vector store and the text embedding generation service to generate embeddings for text paragraphs and upload them to a specified collection in Azure Search.


### Understanding the EmbeddingEngine Class
The EmbeddingEngine class holds the code to generate and store the embeddings in a vector store. The class works at an abstraction that does the work using interfaces and methods as defined by Semantic Kernel. This enable the plugin & plug model, in which the core implementation remains the same and actual embedding algorithm or physical vector store can be changed without modifying this class.

The class is declared as follows:
```csharp
internal class EmbeddingEngine(IVectorStore vectorStore, ITextEmbeddingGenerationService textEmbeddingGenerationService)
{
    // Methods to generate and upload embeddings
}
```
[IVectorStore](https://learn.microsoft.com/en-us/dotnet/api/microsoft.semantickernel.data.ivectorstore?view=semantic-kernel-dotnet) and [ITextEmbeddingGenerationService](https://learn.microsoft.com/en-us/dotnet/api/microsoft.semantickernel.embeddings.itextembeddinggenerationservice?view=semantic-kernel-dotnet) are interfaces that define the methods for interacting with the vector store and generating text embeddings, respectively.

The GenerateEmbeddingsAndStore method in the EmbeddEngine class is responsible for generating embeddings for each document record and uploading them to the specified collection. Here is the code snippet for this method:

```csharp
public async Task GenerateEmbeddingsAndStore(String collectionName, IEnumerable<DocumentRecord> documentRecords)
{
    var collection = vectorStore.GetCollection<String, DocumentRecord>(collectionName);
    await collection.CreateCollectionIfNotExistsAsync();

    foreach (var record in documentRecords)
    {
        // Generate the text embedding.
        Console.WriteLine($"Generating embedding for record: {record.RecordId}");
        record.TextEmbedding = await textEmbeddingGenerationService.GenerateEmbeddingAsync(record.Text);

        // Upload the text Record.
        Console.WriteLine($"Upserting record: {record.RecordId}");
        await collection.UpsertAsync(record);

        Console.WriteLine();
    }
}
```

> Carefully notice how the method utilizes the textEmbeddingGenerationService to generate embeddings calls the UpsertAsync to store the embeddings in the specified vector collection.
{:.info prompt}

Storing Embeddings in Vectors
The embeddings generated by the EmbeddEngine are stored in vectors within the vector store. This abstract way of storing embeddings ensures that the data is organized and easily retrievable.

Creating Record Definitions
Record definitions are used to create structured representations of the data that will be indexed in Azure Search. The DocumentRecord class is an example of a record definition used in this process.

Using Record Definitions to Create an Index
The GetDocumentRecord method in the EmbeddEngine class creates instances of DocumentRecord based on the input text. These records are then used to create an index in Azure Search. Here is the code snippet for this method:

```csharp
public DocumentRecord GetDocumentRecord(KeyValuePair<String, String> entry)
{
    return new DocumentRecord
    {
        Key = Guid.NewGuid().ToString(),
        DocumentUri = "https://www.microsoft.com",
        ParagraphId = Guid.NewGuid().ToString(),
        Text = entry.Value,
        Title = entry.Key,
    };
}
```

Instantiating and Configuring the Kernel
To leverage the capabilities of Semantic Kernel and Azure Search, we need to instantiate and configure the kernel. This involves setting up the Azure OpenAI endpoint and the Azure AI Search-based vector store.

Configuring Azure OpenAI Endpoint
The Azure OpenAI endpoint is configured using the AddAzureOpenAITextEmbeddingGeneration method. This method requires the deployment name, endpoint, and API key. Here is the code snippet for configuring the Azure OpenAI endpoint:

```csharp
var kernelBuilder = Kernel.CreateBuilder();
var jsonSerializerOptions = new JsonSerializerOptions { PropertyNamingPolicy = JsonNamingPolicy.SnakeCaseLower };

kernelBuilder.AddAzureOpenAITextEmbeddingGeneration(
        deploymentName: AppSetting.EmbeddingModelDeploymentName!,
        endpoint: AppSetting.Endpoint!,
        apiKey: AppSetting.Key!);
``` 

Configuring Azure AI Search-based Vector Store
The Azure AI Search-based vector store is configured using the AddAzureAISearchVectorStore method. This method requires the Azure Search URL and the Azure Search key. Here is the code snippet for configuring the Azure AI Search-based vector store:

```csharp
kernelBuilder.AddAzureAISearchVectorStore(
    new Uri(AppSetting.AzureSearchURL),
    new AzureKeyCredential(AppSetting.AzureSearchKey));
```

Integrating the EmbeddEngine with the Kernel
Once the kernel is configured, we can integrate the EmbeddEngine with the kernel and use it to generate and upload embeddings.

Adding the EmbeddEngine as a Plugin
The EmbeddEngine is registered as a singleton service in the kernel's service collection. Here is the code snippet for adding the EmbeddEngine as a plugin:

```csharp
kernelBuilder.Services.AddSingleton<EmbeddEngine>();
Kernel kernel = kernelBuilder.Build();
```

Generating and Uploading Embeddings
With the EmbeddEngine integrated into the kernel, we can generate and upload embeddings for the text data. Here is the code snippet for generating and uploading embeddings:

```csharp
var embeddEngine = kernel.Services.GetRequiredService<EmbeddEngine>();
var vectorStore = kernel.Services.GetRequiredService<IVectorStore>();

var paragraphs = await GetParagraphs("Data\\Banking.json");

foreach(KeyValuePair<String, String> paragraph in paragraphs)
{            
    var record = embeddEngine.GetDocumentRecord(paragraph);
    await embeddEngine.GenerateEmbeddingsAndUpload("banking-documentation", new[] { record });
}
```

Conclusion
In this blog post, we explored the benefits of using Semantic Kernel and the importance of Azure Search in data embedding and retrieval. We provided a detailed explanation of the EmbeddEngine class, including how embeddings are generated and stored in vectors. We also covered the creation of record definitions and how they are used to create an index in Azure Search. Finally, we demonstrated how to instantiate and configure the kernel, integrate the EmbeddEngine as a plugin, and generate and upload embeddings.

By leveraging the power of Semantic Kernel and Azure Search, organizations can achieve efficient and accurate data processing and retrieval, enabling them to unlock valuable insights from their data.
