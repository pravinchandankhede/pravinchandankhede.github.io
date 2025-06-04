---
title: XML Processing using C# and .NET.
date: 2018-10-25 10:30:30 +/-TTTT
categories: [Transformation, ETL, XML, XSLT, middleware, .net, azure, ]
tags: [api, background, headless service, xml, datasets, xslt, .net, middleware]     # TAG names should always be lowercase
description: In this post I will describe the basics of processing an XML using the classes avaialble in .NET. This article is part of multi-part series focusing on building a sustainable integration engine.
mermaid: true
---

## The Importance of Data Transformation

In modern enterprise applications, **data transformation** is a critical process that enables systems to **communicate, integrate, and operate cohesively**. As organizations increasingly rely on diverse systems—ERP, CRM, databases, APIs, and third-party services—data often arrives in **inconsistent formats**. In large enterprises, it is critical that the data is accessible in a common format at one place. To make this data usable, it must be transformed into a **standardized, canonical format**.

In this artical, we will focus on understanding the foundational technology XML & .NET. These we are going to use to build our gateway engine in upcoming series of blog.

## Understanding the Core Technologies

Lets start with understanding these technologies in detail.

### XML Basics

#### What is XML?

**[XML (eXtensible Markup Language)](https://www.w3schools.com/xml/)** is a markup language designed to store and transport data. It is both human-readable and machine-readable, making it ideal for data exchange between systems.

**[XML (eXtensible Markup Language)](https://en.wikipedia.org/wiki/XML)** is a widely adopted standard for structured data representation.

##### Key Features

- **Self-descriptive**: Data is wrapped in tags that describe its meaning.
- **Platform-independent**: Works across different systems and technologies.
- **Hierarchical structure**: Data is organized in a tree-like format.
- **Extensible**: You can define your own tags and structure.

##### Basic Syntax

```xml
<person>
  <name>John Doe</name>
  <age>30</age>
  <email>john@example.com</email>
</person>
```

### XSLT Basics

#### What is XSLT?

**[XSLT (eXtensible Stylesheet Language Transformations)](https://en.wikipedia.org/wiki/XSLT)** is a declarative language designed specifically for transforming XML documents. It excels in:

- **Mapping one XML structure to another**.
- **Filtering, sorting, and restructuring data**.
- **Generating different output formats** (XML, HTML, plain text, JSON).

It is part of the **XSL (Extensible Stylesheet Language)** family and works by applying transformation rules defined in an XSLT stylesheet.

#### How XSLT Works

- XSLT uses **templates** to match elements in the source XML.
- It applies **XPath expressions** to navigate and select nodes.
- The transformation engine processes the XML and outputs a new document based on the rules.

#### Basic Syntax Example

```xml
<!-- Source XML -->
<person>
  <name>John</name>
</person>

<!-- XSLT Stylesheet -->
<xsl:stylesheet version="1.0"
  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/person">
    <html>
      <body>
        <h1><xsl:value-of select="name"/></h1>
      </body>
    </html>
  </xsl:template>
</xsl:stylesheet>
```

Output file after transformation

```html
<html>
  <body>
    <h1>John<h1>
  </body>
</html>
```

### C# Support for XML and XSLT

#### Overview

C# and the .NET Framework provide robust, high-performance support for working with XML and XSLT. These capabilities are essential for building transformation pipelines, especially in enterprise applications.

#### Key Namespaces and Classes

| Namespace | Purpose |
|-----------|---------|
| `System.Xml` | Core XML support (DOM, readers, writers) |
| `System.Xml.XPath` | XPath querying support |
| `System.Xml.Xsl` | XSLT transformation engine |
| `System.Xml.Schema` | XML Schema validation |

#### Optimized Classes for Performance

- **`XmlReader` / `XmlWriter`**: Forward-only, streaming access to XML. Ideal for large files.
- **`XslCompiledTransform`**: Compiles XSLT stylesheets for fast, reusable transformations.
- **`XPathNavigator`**: Efficient, read-only cursor for XPath queries.

#### Sample: Transform XML Using XSLT with XPath

##### Sample XML (`input.xml`)

```xml
<employees>
  <employee>
    <id>101</id>
    <name>Jane Doe</name>
    <department>Engineering</department>
  </employee>
</employees>
```

##### Sample XSLT ('transform.xslt')

```xslt
<xsl:stylesheet version="1.0"
  xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:template match="/employees">
    <html>
      <body>
        <h2>Employee List</h2>
        <ul>
          <xsl:for-each select="employee">
            <li>
              <xsl:value-of select="name"/> - 
              <xsl:value-of select="department"/>
            </li>
          </xsl:for-each>
        </ul>
      </body>
    </html>
  </xsl:template>
</xsl:stylesheet>
```

##### C# Code

```csharp
using System;
using System.Xml;
using System.Xml.Xsl;
using System.Xml.XPath;

class Program
{
    static void Main()
    {
        // Load XML using XmlReader for performance
        using XmlReader xmlReader = XmlReader.Create("input.xml");

        // Load and compile the XSLT
        XslCompiledTransform xslt = new XslCompiledTransform();
        xslt.Load("transform.xslt");

        // Create output writer
        using XmlWriter writer = XmlWriter.Create("output.html");

        // Apply transformation
        xslt.Transform(xmlReader, writer);

        Console.WriteLine("Transformation complete. Output saved to output.html");
    }
}
```

##### Output of the XSLT Transformation

The transformation will generate an HTML file (`output.html`) with the following content:

```html
<html>
  <body>
    <h2>Employee List</h2>
    <ul>
      <li>Jane Doe - Engineering</li>
    </ul>
  </body>
</html>
```

## Combining XSLT + .NET for a middlwware developmemt

.NET provides **native support** for XML and XSLT manipulations through namespaces like `System.Xml`, `System.Xml.XPath`, and `System.Xml.Xsl`. It has rich support for reading, wirting, transforming and searching XML based documents. This enables:

- **Efficient transformation** using `XslCompiledTransform`, which compiles XSLT for performance.
- **XPath queries** for precise data extraction and manipulation.
- **Streaming support** for processing large XML files without loading them entirely into memory.

This makes the combination of XML, XSLT, and C# ideal for building **high-volume, high-performance transformation pipelines**.

## Conclusion

.NET and XML offers a very elegant solution to build transformation component. We will take this idea to next level to build a custom integration and transformation engine. Be with me on this journey.
