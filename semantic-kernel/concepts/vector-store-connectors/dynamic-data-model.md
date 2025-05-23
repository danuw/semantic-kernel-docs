---
title: Using Vector Store abstractions without defining your own data model (Preview)
description: Describes how to use Vector Store abstractions without defining your own data model.
zone_pivot_groups: programming-languages
author: westey-m
ms.topic: reference
ms.author: westey
ms.date: 10/16/2024
ms.service: semantic-kernel
---
# Using Vector Store abstractions without defining your own data model (Preview)

::: zone pivot="programming-language-csharp"

::: zone-end
::: zone pivot="programming-language-python"

> [!WARNING]
> The Semantic Kernel Vector Store functionality is in preview, and improvements that require breaking changes may still occur in limited circumstances before release.

::: zone-end
::: zone pivot="programming-language-java"

> [!WARNING]
> The Semantic Kernel Vector Store functionality is in preview, and improvements that require breaking changes may still occur in limited circumstances before release.

::: zone-end

::: zone pivot="programming-language-csharp"

## Overview

The Semantic Kernel Vector Store connectors use a model first approach to interacting with databases. This makes using the connectors easy and simple, since
your data model reflects the schema of your database records and to add any additional schema information required, you can simply add attributes to your data model properties.

There are cases though where it is not desirable or possible to define your own data model. E.g. let's say that you do not know at compile time what your
database schema looks like, and the schema is only provided via configuration. Creating a data model that reflects the schema would be impossible in this case.

To cater for this scenario, we allow using a `Dictionary<string, object?>` for the record type.
Properties are added to the Dictionary with key as the property name and the value as the property value.

## Supplying schema information when using the Dictionary

When using the Dictionary, connectors still need to know what the database schema looks like. Without the schema information
the connector would not be able to create a collection, or know how to map to and from the storage representation that each database uses.

A record definition can be used to provide the schema information. Unlike a data model, a record definition can be created from configuration
at runtime, providing a solution for when schema information is not known at compile time.

> [!TIP]
> To see how to create a record definition, refer to [defining your schema with a record definition](./schema-with-record-definition.md).

## Example

To use the Dictionary with a connector, simply specify it as your data model when creating a collection, and simultaneously provide a record definition.

```csharp
// Create the definition to define the schema.
VectorStoreCollectionDefinition definition = new()
{
    Properties = new List<VectorStoreProperty>
    {
        new VectorStoreKeyProperty("Key", typeof(string)),
        new VectorStoreDataProperty("Term", typeof(string)),
        new VectorStoreDataProperty("Definition", typeof(string)),
        new VectorStoreVectorProperty("DefinitionEmbedding", typeof(ReadOnlyMemory<float>), dimensions: 1536)
    }
};

// When getting your collection instance from a vector store instance
// specify the Dictionary, using object as the key type for your database
// and also pass your record definition.
// Note that you have to use GetDynamicCollection instead of the regular GetCollection method
// to get an instance of a collection using Dictionary<string, object?>.
var dynamicDataModelCollection = vectorStore.GetDynamicCollection(
    "glossary",
    definition);

// Since we have schema information available from the record definition
// it's possible to create a collection with the right vectors, dimensions,
// indexes and distance functions.
await dynamicDataModelCollection.EnsureCollectionExistsAsync();

// When retrieving a record from the collection, key, data and vector values can
// now be accessed via the dictionary entries.
var record = await dynamicDataModelCollection.GetAsync("SK");
Console.WriteLine(record["Definition"]);
```

When constructing a collection instance directly, the record definition
is passed as an option. E.g. here is an example of constructing
an Azure AI Search collection instance with the Dictionary.

Note that each vector store collection implementation has a separate `*DynamicCollection`
class that can be used with Dictionary<string, object?>.
This is because these implementations may support NativeAOT/Trimming.

```csharp
new AzureAISearchDynamicCollection(
    searchIndexClient,
    "glossary",
    new() { Definition = definition });
```

::: zone-end

::: zone pivot="programming-language-python"

## Overview

In python you can use the VectorStoreRecordDefinition in combination with any generic type, like a dict or a (Pandas) dataframe. See more info here: [Defining your schema with a record definition](./schema-with-record-definition.md).

::: zone-end

::: zone pivot="programming-language-java"

## Coming soon

More info coming soon.

::: zone-end
