---
title: Document Extraction cognitive skill
titleSuffix: Azure AI Search
description: Extracts content from a file within the enrichment pipeline.

author: gmndrg
ms.author: gimondra

ms.service: azure-ai-search
ms.custom:
  - ignite-2023
ms.topic: reference
ms.date: 05/27/2025
---

# Document Extraction cognitive skill

The **Document Extraction** skill extracts content from a file within the enrichment pipeline. By default, content extraction or retrieval is built into the indexer pipeline. However, by using the Document Extraction skill, you can control how parameters are set, and how extracted content is named in the enrichment tree.

For [vector](vector-search-overview.md) and [multimodal search](multimodal-search-overview.md), Document Extraction combined with the [Text Split skill](cognitive-search-skill-textsplit.md) is more affordable than other [data chunking approaches](vector-search-how-to-chunk-documents.md). The following tutorials demonstrate skill usage for different scenarios:

+ [Tutorial: Index mixed content using multimodal embeddings and the Document Extraction skill](tutorial-document-extraction-multimodal-embeddings.md)

+ [Tutorial: Index mixed content using image verbalizations and the Document Extraction skill](tutorial-document-extraction-image-verbalization.md)

> [!NOTE]
> This skill isn't bound to Azure AI services and has no Azure AI services key requirement.
>
> This skill extracts text and images. Text extraction is free. Image extraction is [billable by Azure AI Search](https://azure.microsoft.com/pricing/details/search/). On a free search service, the cost of 20 transactions per indexer per day is absorbed so that you can complete quickstarts, tutorials, and small projects at no charge. For basic and higher tiers, image extraction is billable.
>

## @odata.type

Microsoft.Skills.Util.DocumentExtractionSkill

## Supported document formats

The DocumentExtractionSkill can extract text from the following document formats:

[!INCLUDE [search-blob-data-sources](./includes/search-blob-data-sources.md)]

## Skill parameters

Parameters are case-sensitive.

| Inputs | Allowed Values | Description |
|-----------------|----------------|-------------|
| `parsingMode`   | `default` </br>`text` </br>`json`  | Set to `default` for document extraction from files that aren't pure text or json. For source files that contain mark up (such as PDF, HTML, RTF, and Microsoft Office files), use the default to extract just the text, minus any markup language or tags. If `parsingMode` isn't defined explicitly, it will be set to `default`. </p>Set to `text` if source files are TXT. This parsing mode improves performance on plain text files. If files include markup, this mode will preserve the tags in the final output. </p>Set to `json` to extract structured content from json files.  |
| `dataToExtract` | `contentAndMetadata` </br>`allMetadata` | Set to `contentAndMetadata` to extract all metadata and textual content from each file. If `dataToExtract` isn't defined explicitly, it will be set to `contentAndMetadata`. </p>Set to `allMetadata` to extract only the [metadata properties for the content type](search-blob-metadata-properties.md) (for example, metadata unique to just .png files).  |
| `configuration` | See below. | A dictionary of optional parameters that adjust how the document extraction is performed. See the below table for descriptions of supported configuration properties. |

| Configuration Parameter	| Allowed Values | Description |
|-------------------------|----------------|-------------|
| `imageAction`           | `none` </br>`generateNormalizedImages` </br>`generateNormalizedImagePerPage` | Set to `none` to ignore embedded images or image files in the data set, or if the source data doesn't include image files. This is the default. </p>For [OCR and image analysis](cognitive-search-concept-image-scenarios.md), set to `generateNormalizedImages` to have the skill create an array of normalized images as part of [document cracking](search-indexer-overview.md#document-cracking). This action requires that `parsingMode` is set to `default` and `dataToExtract` is set to `contentAndMetadata`. A normalized image refers to extra processing resulting in uniform image output, sized and rotated to promote consistent rendering when you include images in visual search results (for example, same-size photographs in a graph control as seen in the [JFK demo](https://github.com/Microsoft/AzureSearch_JFK_Files)). This information is generated for each image when you use this option. </p>If you set to `generateNormalizedImagePerPage`, PDF files are treated differently in that instead of extracting embedded images, each page is rendered as an image and normalized accordingly.  Non-PDF file types are treated the same as if `generateNormalizedImages` was set.
| `normalizedImageMaxWidth` | Any integer between 50-10000 | The maximum width (in pixels) for normalized images generated. The default is 2000. | 
| `normalizedImageMaxHeight` | Any integer between 50-10000 | The maximum height (in pixels) for normalized images generated. The default is 2000. |

> [!NOTE]
> The default of 2000 pixels for the normalized images maximum width and height is based on the maximum sizes supported by the [OCR skill](cognitive-search-skill-ocr.md) and the [image analysis skill](cognitive-search-skill-image-analysis.md). The [OCR skill](cognitive-search-skill-ocr.md) supports a maximum width and height of 4200 for non-English languages, and 10000 for English.  If you increase the maximum limits, processing could fail on larger images depending on your skillset definition and the language of the documents.

## Skill inputs

| Input name | Description |
|--------------------|-------------|
| `file_data` | The file that content should be extracted from. |

The "file_data" input must be an object defined as:

```json
{
  "$type": "file",
  "data": "BASE64 encoded string of the file"
}
```

Alternatively, it can be defined as:

```json
{
  "$type": "file",
  "url": "URL to download file",
  "sasToken": "OPTIONAL: SAS token for authentication if the URL provided is for a file in blob storage"
}
```

The file reference object can be generated one of three ways:

+ Setting the `allowSkillsetToReadFileData` parameter on your indexer definition to "true".  This creates a path `/document/file_data` that is an object representing the original file data downloaded from your blob data source. This parameter only applies to files in Blob storage.

+ Setting the `imageAction` parameter on your indexer definition to a value other than `none`.  This creates an array of images  that follows the required convention for input to this skill if passed individually (that is, `/document/normalized_images/*`).

+ Having a custom skill return a json object defined EXACTLY as above.  The `$type` parameter must be set to exactly `file` and the `data` parameter must be the base 64 encoded byte array data of the file content, or the `url` parameter must be a correctly formatted URL with access to download the file at that location.

## Skill outputs

| Output name	 | Description |
|--------------|-------------|
| `content` | The textual content of the document. |
| `normalized_images`	| When the `imageAction` is set to a value other than `none`, the new *normalized_images* field contains an array of images. See [Extract text and information from images](cognitive-search-concept-image-scenarios.md) for more details on the output format. |

## Sample definition

```json
 {
    "@odata.type": "#Microsoft.Skills.Util.DocumentExtractionSkill",
    "parsingMode": "default",
    "dataToExtract": "contentAndMetadata",
    "configuration": {
        "imageAction": "generateNormalizedImages",
        "normalizedImageMaxWidth": 2000,
        "normalizedImageMaxHeight": 2000
    },
    "context": "/document",
    "inputs": [
      {
        "name": "file_data",
        "source": "/document/file_data"
      }
    ],
    "outputs": [
      {
        "name": "content",
        "targetName": "extracted_content"
      },
      {
        "name": "normalized_images",
        "targetName": "extracted_normalized_images"
      }
    ]
  }
```

## Sample input

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
      {
        "file_data": {
          "$type": "file",
          "data": "aGVsbG8="
        }
      }
    }
  ]
}
```

## Sample output

```json
{
  "values": [
    {
      "recordId": "1",
      "data": {
        "content": "hello",
        "normalized_images": []
      }
    }
  ]
}
```

## See also

+ [Built-in skills](cognitive-search-predefined-skills.md)
+ [How to define a skillset](cognitive-search-defining-skillset.md)
+ [How to process and extract information from images](cognitive-search-concept-image-scenarios.md)
