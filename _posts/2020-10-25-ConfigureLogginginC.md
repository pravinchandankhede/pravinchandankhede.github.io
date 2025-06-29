---
title: Configure Logging in C#.NET
date: 2018-10-25 10:30:30 +/-TTTT
categories: [Design, Logging, middleware, .net, azure ]
tags: [api, background, logger, json, formatters,.net, middleware]     # TAG names should always be lowercase
description: We will see an implementation of logging using the ILogger and ILoggerOptions in C# and .NET.
mermaid: true
---

## The Importance of Logging

## Logging options available in .NET Core

Lets start with understanding these technologies in detail.

### ILogger and ILoggerOptions

#### Key Features

- **Self-descriptive**: Data is wrapped in tags that describe its meaning.
- **Platform-independent**: Works across different systems and technologies.
- **Hierarchical structure**: Data is organized in a tree-like format.
- **Extensible**: You can define your own tags and structure.

### What is XSLT?

**[XSLT (eXtensible Stylesheet Language Transformations)](https://en.wikipedia.org/wiki/XSLT)** is a declarative language designed specifically for transforming XML documents. It excels in:

- **Mapping one XML structure to another**.
- **Filtering, sorting, and restructuring data**.
- **Generating different output formats** (XML, HTML, plain text, JSON).

It is part of the **XSL (Extensible Stylesheet Language)** family and works by applying transformation rules defined in an XSLT stylesheet.

#### How XSLT Works

- XSLT uses **templates** to match elements in the source XML.
- It applies **XPath expressions** to navigate and select nodes.
- The transformation engine processes the XML and outputs a new document based on the rules.

## Demo Preparation

### Demo XML File

We will use the below XML file for all the below demo code. It contains list of authors and books.
Below table describes each attribute of these entities

### Demo XLST File

The belwo file shows a typical transformation logic that we can use to generate a HTML table from given XML file

## C# Support for XML and XSLT

### Overview

C# and the .NET Framework provide robust, high-performance support for working with XML and XSLT. These capabilities are essential for building transformation pipelines, especially in enterprise applications. We will explore some of the most important classes and thier usage. This will give you a solid foundaiton to work on advance XML scenarios.

### Key Namespaces and Classes

Below are some of the important namespaces in .NET. Depending upon your use case, these can be utilized in combination.

| Namespace | Purpose |
|-----------|---------|
| `System.Xml` | Core XML support (DOM, readers, writers) |
| `System.Xml.XPath` | XPath querying support |
| `System.Xml.Xsl` | XSLT transformation engine |
| `System.Xml.Schema` | XML Schema validation |

### Key .NET classes used in XML Processing & Transformation

- **[`XslCompiledTransform`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xsl.xslcompiledtransform)**: Compiles XSLT stylesheets for fast, reusable transformations.
- **[`XslTransform`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xsl.xsltransform)**: XSL transformation.
- **[`XmlReader`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xmlreader) / [`XmlWriter`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xmlwriter)**: Use this classes for forward-only, streaming access to XML documents. Ideal for large files.
- **[`XmlDocument`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xmldocument)**: This provides a DOM based approach to load, modify & save the XML document. This is mostly used to manipulate document pre or post transformaiton.
- **[`XPathNavigator`](https://learn.microsoft.com/en-us/dotnet/api/system.xml.xpath.xpathnavigator)**: This provides a read-only cursor for efficient XPath queries execution.

We will now see a sample code for each of these classes.

## Using XslCompiledTransform class

In this case we will use XslCompiledTransform class to performs the transformaiton. Here -

- We first load the XSLT file in the `XslCompiledTransform` instance.
- We create a output stream using `XmlWriter` class. This will write output path.
- Last we call the `Transform` method to actually do the transformaiton.

This code will perform the transformation and write an output to output.html file.

```csharp
static void UsingXslCompiledTransform()
{
    // Paths to the XML and XSLT files
    String xmlPath = "books.xml";
    String xsltPath = "transformation.xslt";
    String outputPath = "catalog.html";

    // Create the XslCompiledTransform and load the XSLT
    XslCompiledTransform xslt = new XslCompiledTransform();
    xslt.Load(xsltPath);

    // Transform the XML and output to HTML file
    using (XmlWriter writer = XmlWriter.Create(outputPath, xslt.OutputSettings))
    {
        xslt.Transform(xmlPath, writer);
    }

    Console.WriteLine($"Transformation complete. Output written to {outputPath}");
}
```

## Using XslTransform class

> **Info:** This is only for .NET Framework. This code will not work in .NET Core. I am including this code for the sake completeness but is no longer recommended.
{: .prompt-info }

This code also follows the same logic as earlier, it

- Create XslTransform instance and loads it with XSLT transformation file
- Create a XML reader and writer instances
- Performs transformation

```csharp
/// <summary>
/// Transforms an XML file into an HTML file using the legacy XslTransform class.
/// </summary>
/// <remarks>
/// XslTransform is obsolete and only available in .NET Framework, not .NET Core or .NET 5+.
/// This sample is for legacy reference only.
/// </remarks>
static void UsingXslTransform()
{
#pragma warning disable SYSLIB0016 // Type or member is obsolete
    String xmlPath = "books.xml";
    String xsltPath = "transformation.xslt";
    String outputPath = "catalog_xsltransform.html";

    XslTransform xslt = new XslTransform();
    xslt.Load(xsltPath);

    using (XmlReader reader = XmlReader.Create(xmlPath))
    using (XmlWriter writer = XmlWriter.Create(outputPath))
    {
        xslt.Transform(reader, null, writer);
    }

    Console.WriteLine($"Transformation complete. Output written to {outputPath}");
#pragma warning restore SYSLIB0016
}
```

## Conclusion

.NET and XML offers a very elegant solution to build transformation component. We will take this idea to next level to build a custom integration and transformation engine. Be with me on this journey.
