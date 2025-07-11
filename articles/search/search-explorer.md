---
title: "Quickstart: Search Explorer Query Tool"
titleSuffix: Azure AI Search
description: Search explorer is a query tool in the Azure portal that sends query requests to a search index in Azure AI Search. Use it to learn syntax, test query expressions, or inspect a search document.
manager: nitinme
author: haileytap
ms.author: haileytapia
ms.service: azure-ai-search
ms.topic: quickstart
ms.date: 05/22/2025
ms.custom:
  - mode-ui
---

# Quickstart: Use Search explorer to run queries in the Azure portal

In this quickstart, you learn how to use **Search explorer**, a built-in query tool in the Azure portal for running queries against an Azure AI Search index. Use it to test a query or filter expression, or confirm whether content exists in the index.

This quickstart uses an existing index to demonstrate Search explorer.

## Prerequisites

+ An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/).

+ An Azure AI Search service. [Create a service](search-create-service-portal.md) or [find an existing service](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in your current subscription. For this quickstart, you can use a free service.

+ This quickstart uses the realestate-us-sample index. To create the index, run the [**Import data wizard**](search-import-data-portal.md), select the built-in sample data, and step through the wizard using all the default values.

  :::image type="content" source="media/search-explorer/search-explorer-sample-data.png" alt-text="Screenshot of the sample data sets available in the Import data wizard." border="true" lightbox="media/search-explorer/search-explorer-sample-data.png":::

## Start Search explorer

1. In the [Azure portal](https://portal.azure.com), find your search service and open the **Overview** page.

1. On the command bar, select **Search explorer**:

   :::image type="content" source="media/search-explorer/search-explorer-cmd2.png" alt-text="Screenshot of the Search explorer command in portal." border="true":::

   Alternatively, select the **Search explorer** tab on an open index.

   :::image type="content" source="media/search-explorer/search-explorer-tab.png" alt-text="Screenshot of the Search explorer tab." border="true" lightbox="media/search-explorer/search-explorer-tab.png":::

## Query three ways

There are three approaches to querying in Search explorer:

+ Query view provides a default search bar. It accepts an empty query or free-text query with Booleans, such as `seattle condo + parking`.

+ Image view provides a window to browse or drag and drop PNG, JPG, or JPEG files. Unless your index has an [image vectorizer and an equivalent skill](vector-search-how-to-configure-vectorizer.md#supported-embedding-models), this view is unavailable.

+ JSON view supports parameterized queries. Filters, orderby, select, count, searchFields, and all other parameters must be set in JSON view.

   :::image type="content" source="media/search-explorer/three-query-views.png" alt-text="Screenshot of the three views for querying in Search explorer." border="true" lightbox="media/search-explorer/three-query-views.png":::

## Example: Image query

Search explorer accepts images as query inputs through **Image view**, which requires that you use a supported vectorizer–skill pair. For more information, see [Configure a vectorizer in a search index](vector-search-how-to-configure-vectorizer.md).

The realestate-us-sample index isn't configured for image vectorization. If you want to run image queries, create an index as described in [Quickstart: Vector search in the Azure portal](search-get-started-portal-import-vectors.md). The quickstart relies on text-based sample data, so you must use documents that contain images.

To run an image query, select or drag an image to the search area, and then select **Search**. Search explorer vectorizes the image and sends the vector to the search engine for query execution. The search engine returns documents that are sufficiently similar to the input image, up to the specified `k` number of results. <!-- This is enabled by [vector search](vector-search-overview.md), which matches documents based on the similarity of their vector representations. -->

:::image type="content" source="media/search-get-started-portal-images/image-search.png" alt-text="Screenshot of search results for image search." border="true" lightbox="media/search-get-started-portal-images/image-search.png":::

## Examples: JSON queries

The following are examples of JSON queries you can run using Search explorer. To follow these examples, switch to **JSON view**. You can paste each JSON example into the text area.

> [!TIP]
> The JSON view provides intellisense for parameter name completion. Place your cursor inside the JSON view and type a space character to see all query parameters. You can also type a letter, like "s," to see only the query parameters that begin with that letter. Intellisense doesn't exclude invalid parameters, so use your best judgment.

### Run an unspecified query

In Search explorer, POST requests are formulated internally using the [Search POST REST API](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true), with responses returned as verbose JSON documents.

For a first look at content, execute an empty search by selecting **Search** with no terms provided. An empty search is useful as a first query because it returns entire documents so that you can review document composition. On an empty search, there's no search score and documents are returned in arbitrary order (`"@search.score": 1` for all documents). By default, 50 documents are returned in a search request.

Equivalent syntax for an empty search is `*` or `"search": "*"`.

   ```json
   {
      "search": "*",
      "count": true
   }
   ```

   **Results**
   
   :::image type="content" source="media/search-explorer/search-explorer-example-empty.png" alt-text="Screenshot of unqualified or empty query example." border="true" lightbox="media/search-explorer/search-explorer-example-empty.png":::

### Run a free-text query

Free-form search, with or without operators, is useful for simulating user-defined queries sent from a custom app to Azure AI Search. Only those fields attributed as "searchable" in the index definition are scanned for matches.

You don't need JSON view for a free text query, but we provide it in JSON for consistency with other examples in this article.

Notice that when you provide search criteria, such as query terms or expressions, search rank comes into play. The following example illustrates a free text search. The "@search.score" is a relevance score computed for the match using the [default scoring algorithm](index-ranking-similarity.md#default-scoring-algorithm).

   ```json
   {
       "search": "Seattle townhouse `Lake Washington` miele OR thermador appliance"
   }
   ```

   **Results**

   You can use Ctrl-F to search within results for specific terms of interest.

   :::image type="content" source="media/search-explorer/search-explorer-example-freetext.png" alt-text="Screenshot of a free text query example." border="true" lightbox="media/search-explorer/search-explorer-example-freetext.png":::

### Count matching documents

Add `"count": true` to get the number of matches found in an index. On an empty search, count is the total number of documents in the index. On a qualified search, it's the number of documents matching the query input. Recall that the service returns the top 50 matches by default, so the count might indicate more matches in the index than what's returned in the results.

   ```json
   {
       "search": "Seattle townhouse `Lake Washington` miele OR thermador appliance",
       "count": true
   }
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-count.png" alt-text="Screenshot of a count example." border="true" lightbox="media/search-explorer/search-explorer-example-count.png":::

### Limit fields in search results

Add ["select"`](search-query-odata-select.md) to limit results to the explicitly named fields for more readable output in **Search explorer**. Only fields marked as "retrievable" in the search index can show up in results.

   ```json
   {
      "search": "seattle condo",
      "count": true,
      "select": "listingId, beds, baths, description, street, city, price"
   }
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-selectfield.png" alt-text="Screenshot of restrict fields in search results example." border="true" lightbox="media/search-explorer/search-explorer-example-selectfield.png":::

### Return next batch of results

Azure AI Search returns the top 50 matches based on the search rank. To get the next set of matching documents, append `"top": 100` and `"skip": 50` to increase the result set to 100 documents (default is 50, maximum is 1000), skipping the first 50 documents. You can check the document key (listingID) to identify a document.

Recall that you need to provide search criteria, such as a query term or expression, to get ranked results. Notice that search scores decrease the deeper you reach into search results.

   ```json
   {
      "search": "seattle condo",
      "count": true,
      "select": "listingId, beds, baths, description, street, city, price",
      "top": 100,
      "skip": 50
   }
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-topskip.png" alt-text="Screenshot of returning next batch of search results example." border="true" lightbox="media/search-explorer/search-explorer-example-topskip.png":::

### Filter expressions (greater than, less than, equal to)

Use the [`filter`](search-query-odata-filter.md) parameter to specify inclusion or exclusion criteria. The field must be attributed as "filterable" in the index. This example searches for bedrooms greater than 3:

   ```json
   {
       "search": "seattle condo",
       "count": true,
       "select": "listingId, beds, baths, description",
       "filter": "beds gt 3"
   }
   ```
   
   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-filter.png" alt-text="Screenshot of a filter example." border="true" lightbox="media/search-explorer/search-explorer-example-filter.png":::

### Sort results

Add [`orderby`](search-query-odata-orderby.md) to sort results by another field besides search score. The field must be attributed as "sortable" in the index. In situations where the filtered value is identical (for example, same price), the order is arbitrary, but you can add more criteria for deeper sorting. An example expression you can use to test this out is:

   ```json
   {
       "search": "seattle condo",
       "count": true,
       "select": "listingId, price, beds, baths, description",
       "filter": "beds gt 3",
       "orderby": "price asc"
   }
   ```

   **Results**

   :::image type="content" source="media/search-explorer/search-explorer-example-orderby.png" alt-text="Screenshot of a sorting example." border="true" lightbox="media/search-explorer/search-explorer-example-orderby.png":::

## Takeaways

In this quickstart, you used **Search explorer** to query an index using the REST API.

+ Results are returned as verbose JSON documents so that you can view document construction and content, in entirety. The `select` parameter in a query expression can limit which fields are returned.

+ Search results are composed of all fields marked as "retrievable" in the index. Select the adjacent **Fields** tab to review attributes.

+ Keyword search, similar to what you might enter in a commercial web browser, are useful for testing an end-user experience. For example, assuming the built-in real estate sample index, you could enter "Seattle apartments lake washington," and then you can use Ctrl-F to find terms within the search results.

+ Query and filter expressions are articulated in a syntax implemented by Azure AI Search. The default is a [simple syntax](/rest/api/searchservice/simple-query-syntax-in-azure-search), but you can optionally use [full Lucene](/rest/api/searchservice/lucene-query-syntax-in-azure-search) for more powerful queries. [Filter expressions](/rest/api/searchservice/odata-expression-syntax-for-azure-search) are articulated in an OData syntax.

## Clean up resources

When you're working in your own subscription, it's a good idea at the end of a project to decide whether you still need the resources you created. Resources left running can cost you money. You can delete resources individually or delete the resource group to delete the entire set of resources.

You can find and manage resources in the Azure portal, using the **All resources** or **Resource groups** link in the left-navigation pane.

If you're using a free service, remember that you're limited to three indexes, indexers, and data sources. You can delete individual items in the Azure portal to stay under the limit. 

## Next step

To learn more about query structures and syntax, use a REST client to create query expressions that use more parts of the API. The [Search POST REST API](/rest/api/searchservice/documents/search-post?view=rest-searchservice-2024-05-01-preview&preserve-view=true) is especially helpful for learning and exploration.

> [!div class="nextstepaction"]
> [Quickstart: Full-text search using REST](search-get-started-text.md)
