---
title: AzureAIAgent Foundry GA Migration Guide
description: Describes the steps for developers to update their usage of AzureAIAgent for a GA Foundry Project.
zone_pivot_groups: programming-languages
author: crickman
ms.topic: conceptual
ms.author: crickman
ms.date: 05/16/2025
ms.service: semantic-kernel
---

# `AzureAIAgent` Foundry GA Migration Guide

::: zone pivot="programming-language-csharp"

In Semantic Kernel .NET 1.53.0+, .NET and Python developers using `AzureAIAgent` must to update the patterns they use to interact with the Azure AI Foundry in response to its move to GA.

### GA Foundry Project

- Connect programatically using the _Foundry Project's_ endpoint url.
- Requires Semantic Kernel version 1.53.0 and above.
- Must be created on or after May 19th, 2025

### Pre-GA Foundry Project

- Connect programatically using the _Foundry Project's_ connection string.
- Continue to use Semantic Kernel versions prior to version 1.53.0
- Created prior to May 19th, 2025


## Creating an Client

### Old Way

```c#
AIProjectClient client = AzureAIAgent.CreateAzureAIClient("<connection string>", new AzureCliCredential());
AgentsClient agentsClient = client.GetAgentsClient();
```

### New Way

``` c#
PersistentAgentsClient agentsClient = AzureAIAgent.CreateAgentsClient("<endpoint>", new AzureCliCredential());```
```

## Creating an Agent

### Old Way

```c#
Agent agent = await agentsClient.CreateAgentAsync(...);
```

### New Way

``` c#
PersistentAgent agent = await agentsClient.Administration.CreateAgentAsync(
```

## Deleting an Agent

### Old Way

```c#
await agentsClient.DeleteAgentAsync("<agent id>");
```

### New Way

``` c#
await agentsClient.Administration.DeleteAgentAsync("<agent id>");
```

## Uploading Files

### Old Way

```c#
AgentFile fileInfo = await agentsClient.UploadFileAsync(stream, AgentFilePurpose.Agents, "<file name>");
```

### New Way

``` c#
PersistentAgentFileInfo fileInfo = await agentsClient.Files.UploadFileAsync(stream, PersistentAgentFilePurpose.Agents, "<file name>");
```

## Deleting Files

### Old Way

```c#
await agentsClient.DeleteFileAsync("<file id>");
```

### New Way

``` c#
await agentsClient.Files.DeleteFileAsync("<file id>");
```

## Creating a VectorStore

### Old Way

```c#
VectorStore fileStore = await agentsClient.CreateVectorStoreAsync(...);
```

### New Way

``` c#
PersistentAgentsVectorStore fileStore = await agentsClient.VectorStores.CreateVectorStoreAsync(...);
```

## Deleting a VectorStore

### Old Way

```c#
await agentsClient.DeleteVectorStoreAsync("<store id>");
```

### New Way

``` c#
await agentsClient.VectorStores.DeleteVectorStoreAsync("<store id>");
```

::: zone-end


::: zone pivot="programming-language-python"

## `AzureAIAgent`

In Semantic Kernel Python 1.31.0+, `AzureAIAgent` packages have been updated following the Azure AI Foundry's move to GA. This necessitates minor adjustments for developers.

The `project_connection_string` and its environment variable `AZURE_AI_AGENT_PROJECT_CONNECTION_STRING` have been removed. You must now configure an `endpoint` (`AZURE_AI_AGENT_ENDPOINT`). Find the `AzureAIAgent` endpoint on your Azure Foundry project page, formatted as: `https://<resource>.services.ai.azure.com/api/projects/<project-name>`.

Additionally, as shown in the updated [getting started with AzureAIAgent samples](https://github.com/microsoft/semantic-kernel/tree/main/python/samples/getting_started_with_agents/azure_ai_agent), several tool-related imports have shifted from `azure.ai.projects.models` to `azure.ai.agents.models`.

Updated Imports for Tools:

### Old Way

```python
from azure.ai.projects.models import CodeInterpreterTool, FileSearchTool, OpenApiAnonymousAuthDetails, OpenApiTool
```

##### New Way

```python
from azure.ai.agents.models import CodeInterpreterTool, FileSearchTool, OpenApiAnonymousAuthDetails, OpenApiTool
```

Uploading Files:

### Old Way

```python
from azure.ai.projects.models import FilePurpose

file = await client.agents.upload_file_and_poll(file_path="<file-path>", purpose=FilePurpose.AGENTS)
```

##### New Way

```python
from azure.ai.agents.models import FilePurpose

file = await client.agents.files.upload_and_poll(file_path="<file-path>", purpose=FilePurpose.AGENTS)
```

Deleting Files:

### Old Way

```python
await client.agents.delete_file(file.id)
```

##### New Way

```python
await client.agents.files.delete(file.id)
```

Creating Vector Stores:

### Old Way

```python
from azure.ai.projects.models import VectorStore

vector_store: VectorStore = await client.agents.create_vector_store_and_poll(
    file_ids=[file.id], name="<vector-store-name>"
)
```

##### New Way

```python
from azure.ai.agents.models import VectorStore

vector_store: VectorStore = await client.agents.vector_stores.create_and_poll(
    file_ids=[file.id], name="<vector-store-name>"
)
```

Deleting Vector Stores:

### Old Way

```python
from azure.ai.projects.models import VectorStore

await client.agents.delete_vector_store(vector_store.id)
```

##### New Way

```python
from azure.ai.agents.models import VectorStore

await client.agents.vector_stores.delete(vector_store.id)
```

::: zone-end

::: zone pivot="programming-language-java"

> AzureAIAgent is currently unavailable in Java.

::: zone-end
