---
author: laujan
manager: nitinme
ms.service: azure-ai-language
ms.topic: include
ms.date: 02/16/2025
ms.author: lajanuar
ms.custom: language-service-pii
---

## Prerequisites

* [Create a Project in Foundry in the Azure AI Foundry Portal](../../../../../ai-foundry/how-to/create-projects.md)

## Navigate to the Azure AI Foundry Playground

Using the left side pane, select **Playgrounds**. Then select the **Try the Language Playground** button.

:::image type="content" source="../../media/quickstarts/azure-ai-foundry/foundry-playground-navigation.png" alt-text="The development lifecycle" lightbox="../../media/quickstarts/azure-ai-foundry/foundry-playground-navigation.png":::

## Use PII in the Azure AI Foundry Playground

The **Language Playground** consists of four sections:

* Top banner: You can select any of the currently available Language services here.
* Right pane: This pane is where you can find the **Configuration** options for the service, such as the API and model version, along with features specific to the service.
* Center pane: This pane is where you enter your text for processing. After the operation is run, some results will be shown here.
* Right pane: This pane is where **Details** of the run operation are shown.

Here you can select from two Personally Identifying Information (PII) detection capabilities by choosing the top banner tiles, **Extract PII from conversation** or **Extract PII from text**. Each is for a different scenario.

### Extract PII from conversation

**Extract PII from conversation** is designed to identify and mask personally identifying information in conversational text.

In **Configuration** there are the following options:

|Option              |Description                              |
|--------------------|-----------------------------------------|
|Select API version  | Select which version of the API to use.    |
|Select model version| Select which version of the model to use.|
|Select text language| Select which language the language is input in.|
|Select types to include| Select they types of information you want to redact.|
|Specify redaction policy| Select the method of redaction.|
|Specify redaction character| Select which character is used for redaction. Only available with the **CharacterMask** redaction policy.|

After your operation is completed, the type of entity is displayed beneath each entity in the center pane and the **Details** section contains the following fields for each entity:

|Field | Description                |
|------|----------------------------|
|Entity|The detected entity.|
|Category| The type of entity that was detected.|
|Offset| The number of characters that the entity was detected from the beginning of the line.|
|Length| The character length of the entity.|
|Confidence| How confident the model is in the correctness of identification of entity's type.|

:::image type="content" source="../../media/quickstarts/azure-ai-foundry/conversation-pii.png" alt-text="A screenshot of an example of extract PII from conversation in Azure AI Foundry portal." lightbox="../../media/quickstarts/azure-ai-foundry/conversation-pii.png":::

### Extract PII from text

**Extract PII from text** is designed to identify and mask personally identifying information in text.

In **Configuration** there are the following options:

|Option              |Description                              |
|--------------------|-----------------------------------------|
|Select API version  | Select which version of the API to use.    |
|Select model version| Select which version of the model to use.|
|Select text language| Select which language the language is input in.|
|Select types to include| Select they types of information you want to redact.|
|Specify redaction policy| Select the method of redaction.|
|Specify redaction character| Select which character is used for redaction. Only available with the **CharacterMask** redaction policy.|

After your operation is completed, the type of entity is displayed beneath each entity in the center pane and the **Details** section contains the following fields for each entity:

|Field | Description                |
|------|----------------------------|
|Entity|The detected entity.|
|Category| The type of entity that was detected.|
|Offset| The number of characters that the entity was detected from the beginning of the line.|
|Length| The character length of the entity.|
|Confidence| How confident the model is in the correctness of identification of entity's type.|
|Tags| How confident the model is in the correctness for each identified entity type.|

:::image type="content" source="../../media/quickstarts/azure-ai-foundry/text-pii.png" alt-text="A screenshot of an example of extract PII from text in Azure AI Foundry portal." lightbox="../../media/quickstarts/azure-ai-foundry/text-pii.png":::

