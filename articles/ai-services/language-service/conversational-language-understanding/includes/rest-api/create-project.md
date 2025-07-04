---
author: laujan
manager: nitinme
ms.service: azure-ai-language
ms.topic: include
ms.date: 06/30/2025
ms.author: lajanuar
---

Submit a `PATCH` request by using the following URL, headers, and JSON body to create a new project.

### Request URL

Use the following URL when you create your API request. Replace the placeholder values with your own values.

```rest
{ENDPOINT}/language/authoring/analyze-conversations/projects/{PROJECT-NAME}?api-version={API-VERSION}
```

|Placeholder  |Value  | Example |
|---------|---------|---------|
|`{ENDPOINT}`     | The endpoint for authenticating your API request. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | The name for your project. This value is case sensitive. | `myProject` |
|`{API-VERSION}`     | The [version](../../../concepts/model-lifecycle.md#api-versions) of the API you're calling. | `2023-04-01` |

### Headers

Use the following header to authenticate your request.

|Key|Value|
|--|--|
|`Ocp-Apim-Subscription-Key`| The key to your resource. Used for authenticating your API requests.|
|`Content-Type`| `application/merge-patch+json` |

### Body

Use the following sample JSON as your body.

```json
{
  "projectName": "{PROJECT-NAME}",
  "language": "{LANGUAGE-CODE}",
  "projectKind": "Conversation",
  "description": "Project description",
  "multilingual": true
}
```

|Key  |Placeholder  |Value  | Example |
|---------|---------|----------|--|
| `projectName` | `{PROJECT-NAME}` | The name of your project. This value is case sensitive. | `EmailApp` |
| `language` | `{LANGUAGE-CODE}` | A string that specifies the language code for the utterances used in your project. If your project is a multilingual project, choose the [language code](../../language-support.md) of most of the utterances. |`en-us`|
| `multilingual` | `true`| A Boolean value that enables you to have documents in multiple languages in your dataset. When your model is deployed, you can query the model in any supported language (not necessarily included in your training documents). To learn more about multilingual support, see [Language support](../../language-support.md#multi-lingual-option). | `true`|