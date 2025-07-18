---
title: "Use Document Intelligence SDK for JavaScript / .NET (REST API v3.0)"
description: 'Use the Document Intelligence SDK for JavaScript (REST API v3.0) to create a forms processing app that extracts key data from documents.'
author: laujan
manager: nitinme
ms.service: azure-ai-document-intelligence
ms.topic: include
ms.date: 02/10/2025
ms.author: lajanuar
ms.custom: devx-track-csharp, linux-related-content
---

<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

[Client library](/javascript/api/%40azure-rest/ai-document-intelligence/?view=azure-node-latest&preserve-view=true) | [REST API reference](/rest/api/aiservices/operation-groups?view=rest-aiservices-v4.0%20(2024-11-30)&preserve-view=true) | [Package (npm)](https://www.npmjs.com/package/@azure-rest/ai-document-intelligence/v/1.0.0) | [Samples](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/documentintelligence/ai-document-intelligence-rest/samples/v1/javascript) |[Supported REST API version](../../../sdk-overview-v4-0.md)

## Prerequisites

- An Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/).
- The latest version of [Visual Studio Code](https://code.visualstudio.com/) or your preferred IDE. For more information, see [Node.js in Visual Studio Code](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial).
- The latest `LTS` version of [Node.js](https://nodejs.org/).

- An Azure AI services or Document Intelligence resource. Create a <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="Create a Document Intelligence resource." target="_blank">single-service</a> or <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesAIFoundry" title="Create a multiple Document Intelligence resource." target="_blank">multi-service</a>. You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

  > [!TIP]
  > Create an Azure AI Foundry resource if you plan to access multiple Azure AI services by using a single endpoint and key. For Document Intelligence access only, create a Document Intelligence resource. You need a single-service resource if you intend to use [Microsoft Entra authentication](/azure/active-directory/authentication/overview-authentication).

- The key and endpoint from the resource you create to connect your application to the Azure Document Intelligence service.

  1. After your resource deploys, select **Go to resource**.
  1. In the left pane, select **Keys and Endpoint**.
  1. Copy one of the keys and the **Endpoint** for use later in this article.

  :::image type="content" source="../../../media/containers/keys-and-endpoint.png" alt-text="Screenshot of keys and endpoint location in the Azure portal.":::

- A document file at a URL. For this project, you can use the sample forms provided in the following table for each feature:

  | Feature   | modelID   | document-url |
  | --- | --- |--|
  | **Read model** | prebuilt-read | [Sample brochure](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/read.png) |
  | **Layout model** | prebuilt-layout | [Sample booking confirmation](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/layout.png) |
  | **W-2 form model**  | prebuilt-tax.us.w2 | [Sample W-2 form](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/w2.png) |
  | **Invoice model**  | prebuilt-invoice | [Sample invoice](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/raw/master/curl/form-recognizer/rest-api/invoice.pdf) |
  | **Receipt model**  | prebuilt-receipt | [Sample receipt](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/receipt.png) |
  | **ID document model**  | prebuilt-idDocument | [Sample ID document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/identity_documents.png) |

[!INCLUDE [environment-variables](../set-environment-variables.md)]

## Set up your programming environment

Create a Node.js Express application.

1. In a console window, create and navigate to a new directory for your app named `doc-intel-app`.

    ```console
    mkdir doc-intel-app
    cd doc-intel-app
    ```

1. Run the `npm init` command to initialize the application and scaffold your project.

    ```console
    npm init
    ```

1. Specify your project's attributes using the prompts presented in the terminal.

   - The most important attributes are name, version number, and entry point.
   - We recommend that you keep `index.js` for the entry point name. The description, test command, GitHub repository, keywords, author, and license information are optional attributes. You can skip them for this project.
   - Select **Enter** to accept the suggestions in parentheses.

   After you complete the prompts, the command creates a `package.json` file in your *doc-intel-app* directory.

1. Install the `ai-document-intelligence` client library and `azure/identity` npm packages:

    ```console
    npm i @azure-rest/ai-document-intelligence@1.0.0 @azure/identity
    ```

  Your app's *package.json* file is updated with the dependencies.

1. Create a file named *index.js* in the application directory.

   > [!TIP]
   >
   > You can create a new file by using PowerShell. Open a PowerShell window in your project directory by holding down the **Shift** key and right-clicking the folder, then type the following command: *New-Item index.js*.

## Build your application

To interact with the Document Intelligence service, you need to create an instance of the `DocumentIntelligenceClient` class. To do so, you create an `AzureKeyCredential` with your key from the Azure portal and a `DocumentIntelligenceClient` instance with the `AzureKeyCredential` and your Document Intelligence endpoint.

Open the `index.js` file in Visual Studio Code or your favorite IDE and select one of the following code samples and copy/paste into your application:

- The [prebuilt-read](#use-the-read-model) model is at the core of all Document Intelligence models and can detect lines, words, locations, and languages. The layout, general document, prebuilt, and custom models all use the `read` model as a foundation for extracting texts from documents.
- The [prebuilt-layout](#use-the-layout-model) model extracts text and text locations, tables, selection marks, and structure information from documents and images.
- The [prebuilt-tax.us.w2](#use-the-w-2-tax-model) model extracts information reported on US Internal Revenue Service (IRS) tax forms.
- The [prebuilt-invoice](#use-the-invoice-model) model extracts information reported on US Internal Revenue Service tax forms.
- The [prebuilt-receipt](#use-the-receipt-model) model extracts key information from printed and handwritten sales receipts.
- The [prebuilt-idDocument](#use-the-id-document-model) model extracts key information from US Drivers Licenses; international passport biographical pages; US state IDs; social security cards; and permanent resident cards.

## Use the Read model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample document
const documentUrlRead = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/read.png"

// helper function
function* getTextOfSpans(content, spans) {
    for (const span of spans) {
        yield content.slice(span.offset, span.offset + span.length);
    }
}

async function main() {
    // create your `DocumentIntelligenceClient` instance and `AzureKeyCredential` variable
    const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));
    const poller = await client.beginAnalyzeDocument("prebuilt-read", documentUrlRead);

    const {
        content,
        pages,
        languages,
        styles
    } = await poller.pollUntilDone();

    if (pages.length <= 0) {
        console.log("No pages were extracted from the document.");
    } else {
        console.log("Pages:");
        for (const page of pages) {
            console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
            console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
            console.log(`  ${page.lines.length} lines, ${page.words.length} words`);

            if (page.lines.length > 0) {
                console.log("  Lines:");

                for (const line of page.lines) {
                    console.log(`  - "${line.content}"`);

                    // The words of the line can also be iterated independently. The words are computed based on their
                    // corresponding spans.
                    for (const word of line.words()) {
                        console.log(`    - "${word.content}"`);
                    }
                }
            }
        }
    }

    if (languages.length <= 0) {
        console.log("No language spans were extracted from the document.");
    } else {
        console.log("Languages:");
        for (const languageEntry of languages) {
            console.log(
                `- Found language: ${languageEntry.languageCode} (confidence: ${languageEntry.confidence})`
            );
            for (const text of getTextOfSpans(content, languageEntry.spans)) {
                const escapedText = text.replace(/\r?\n/g, "\\n").replace(/"/g, '\\"');
                console.log(`  - "${escapedText}"`);
            }
        }
    }

    if (styles.length <= 0) {
        console.log("No text styles were extracted from the document.");
    } else {
        console.log("Styles:");
        for (const style of styles) {
            console.log(
                `- Handwritten: ${style.isHandwritten ? "yes" : "no"} (confidence=${style.confidence})`
            );

            for (const word of getTextOfSpans(content, style.spans)) {
                console.log(`  - "${word}"`);
            }
        }
    }
}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

Visit the Azure samples repository on GitHub and view the [`read` model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/read-model-output.md).

## Use the Layout model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample document
const layoutUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/layout.png"

async function main() {
    const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

    const poller = await client.beginAnalyzeDocument(
      "prebuilt-layout", layoutUrl);

    // Layout extraction produces basic elements such as pages, words, lines, etc. as well as information about the
    // appearance (styles) of textual elements.
    const { pages, tables } = await poller.pollUntilDone();

    if (!pages || pages.length <= 0) {
      console.log("No pages were extracted from the document.");
    } else {
      console.log("Pages:");
      for (const page of pages) {
        console.log("- Page", page.pageNumber, `(unit: ${page.unit})`);
        console.log(`  ${page.width}x${page.height}, angle: ${page.angle}`);
        console.log(
          `  ${page.lines && page.lines.length} lines, ${page.words && page.words.length} words`
        );

        if (page.lines && page.lines.length > 0) {
          console.log("  Lines:");

          for (const line of page.lines) {
            console.log(`  - "${line.content}"`);

            // The words of the line can also be iterated independently. The words are computed based on their
            // corresponding spans.
            for (const word of line.words()) {
              console.log(`    - "${word.content}"`);
            }
          }
        }
      }
    }

    if (!tables || tables.length <= 0) {
      console.log("No tables were extracted from the document.");
    } else {
      console.log("Tables:");
      for (const table of tables) {
        console.log(
          `- Extracted table: ${table.columnCount} columns, ${table.rowCount} rows (${table.cells.length} cells)`
        );
      }
    }
  }

  main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
  });

```

Visit the Azure samples repository on GitHub and view the [layout model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/layout-model-output.md).

## Use the General document model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample document
const documentUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf"

async function main() {
    const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

    const poller = await client.beginAnalyzeDocument("prebuilt-document", documentUrl);

    const {
        keyValuePairs
    } = await poller.pollUntilDone();

    if (!keyValuePairs || keyValuePairs.length <= 0) {
        console.log("No key-value pairs were extracted from the document.");
    } else {
        console.log("Key-Value Pairs:");
        for (const {
                key,
                value,
                confidence
            } of keyValuePairs) {
            console.log("- Key  :", `"${key.content}"`);
            console.log("  Value:", `"${(value && value.content) || "<undefined>"}" (${confidence})`);
        }
    }

}

main().catch((error) => {
    console.error("An error occurred:", error);
    process.exit(1);
});

```

Visit the Azure samples repository on GitHub and view the [general document model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/general-document-model-output.md).

## Use the W-2 tax model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

const w2DocumentURL = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/w2.png"

async function main() {
 const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

 const poller = await client.beginAnalyzeDocument("prebuilt-tax.us.w2", w2DocumentURL);

 const {
   documents: [result]
 } = await poller.pollUntilDone();

  if (result) {
    const { Employee, Employer, ControlNumber, TaxYear, AdditionalInfo } = result.fields;

    if (Employee) {
      const { Name, Address, SocialSecurityNumber } = Employee.properties;
      console.log("Employee:");
      console.log("  Name:", Name && Name.content);
      console.log("  SSN/TIN:", SocialSecurityNumber && SocialSecurityNumber.content);
      if (Address && Address.value) {
        const { streetAddress, postalCode } = Address.value;
        console.log("  Address:");
        console.log("    Street Address:", streetAddress);
        console.log("    Postal Code:", postalCode);
      }
    } else {
      console.log("No employee information extracted.");
    }

    if (Employer) {
      const { Name, Address, IdNumber } = Employer.properties;
      console.log("Employer:");
      console.log("  Name:", Name && Name.content);
      console.log("  ID (EIN):", IdNumber && IdNumber.content);

      if (Address && Address.value) {
        const { streetAddress, postalCode } = Address.value;
        console.log("  Address:");
        console.log("    Street Address:", streetAddress);
        console.log("    Postal Code:", postalCode);
      }
    } else {
      console.log("No employer information extracted.");
    }

    console.log("Control Number:", ControlNumber && ControlNumber.content);
    console.log("Tax Year:", TaxYear && TaxYear.content);

    if (AdditionalInfo) {
      console.log("Additional Info:");

      for (const info of AdditionalInfo.values) {
        const { LetterCode, Amount } = info.properties;
        console.log(`- ${LetterCode && LetterCode.content}: ${Amount && Amount.content}`);
      }
    }
  } else {
    throw new Error("Expected at least one document in the result.");
  }
}

main().catch((error) => {
  console.error(error);
  process.exit(1);
});

```

Visit the Azure samples repository on GitHub and view the [W-2 tax model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/w2-tax-model-output.md).

## Use the Invoice model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample url
const invoiceUrl = "https://github.com/Azure-Samples/cognitive-services-REST-api-samples/raw/master/curl/form-recognizer/rest-api/invoice.pdf";

async function main() {

  const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

  const poller = await client.beginAnalyzeDocument("prebuilt-invoice", invoiceUrl);

  const {
      documents: [result]
  } = await poller.pollUntilDone();

  if (result) {
      const invoice = result.fields;

      console.log("Vendor Name:", invoice.VendorName?.content);
      console.log("Customer Name:", invoice.CustomerName?.content);
      console.log("Invoice Date:", invoice.InvoiceDate?.content);
      console.log("Due Date:", invoice.DueDate?.content);

      console.log("Items:");
      for (const {
              properties: item
          } of invoice.Items?.values ?? []) {
          console.log("-", item.ProductCode?.content ?? "<no product code>");
          console.log("  Description:", item.Description?.content);
          console.log("  Quantity:", item.Quantity?.content);
          console.log("  Date:", item.Date?.content);
          console.log("  Unit:", item.Unit?.content);
          console.log("  Unit Price:", item.UnitPrice?.content);
          console.log("  Tax:", item.Tax?.content);
          console.log("  Amount:", item.Amount?.content);
      }

      console.log("Subtotal:", invoice.SubTotal?.content);
      console.log("Previous Unpaid Balance:", invoice.PreviousUnpaidBalance?.content);
      console.log("Tax:", invoice.TotalTax?.content);
      console.log("Amount Due:", invoice.AmountDue?.content);
  } else {
      throw new Error("Expected at least one receipt in the result.");
  }
}

main().catch((error) => {
  console.error("An error occurred:", error);
  process.exit(1);
});

```

Visit the Azure samples repository on GitHub and view the [invoice model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/invoice-model-output.md).

## Use the Receipt model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample url
const receiptUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/receipt.png";

async function main() {

    const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

    const poller = await client.beginAnalyzeDocument("prebuilt-receipt", receiptUrl);

    const {
        documents: [result]
    } = await poller.pollUntilDone();

    if (result) {
        const {
            MerchantName,
            Items,
            Total
        } = result.fields;

        console.log("=== Receipt Information ===");
        console.log("Type:", result.docType);
        console.log("Merchant:", MerchantName && MerchantName.content);

        console.log("Items:");
        for (const item of (Items && Items.values) || []) {
            const {
                Description,
                TotalPrice
            } = item.properties;

            console.log("- Description:", Description && Description.content);
            console.log("  Total Price:", TotalPrice && TotalPrice.content);
        }

        console.log("Total:", Total && Total.content);
    } else {
        throw new Error("Expected at least one receipt in the result.");
    }

}

main().catch((err) => {
    console.error("The sample encountered an error:", err);
});

```

Visit the Azure samples repository on GitHub and view the [receipt model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/receipt-model-output.md).

## Use the ID document model

```javascript
const { DocumentIntelligenceClient } = require("@azure-rest/ai-document-intelligence");
const  { AzureKeyCredential } = require("@azure/core-auth");

//use your `key` and `endpoint` environment variables
const key = process.env['DI_KEY'];
const endpoint = process.env['DI_ENDPOINT'];

// sample document
const idDocumentURL = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/rest-api/identity_documents.png"

async function main() {
 const client = DocumentIntelligence(endpoint, new AzureKeyCredential(key));

 const poller = await client.beginAnalyzeDocument("prebuilt-idDocument", idDocumentURL);

 const {
   documents: [result]
 } = await poller.pollUntilDone();

  if (result) {
// The identity document model has multiple document types, so we need to know which document type was actually
    extracted.
    if (result.docType === "idDocument.driverLicense") {
      const { FirstName, LastName, DocumentNumber, DateOfBirth, DateOfExpiration, Height, Weight, EyeColor, Endorsements, Restrictions, VehicleClassifications} = result.fields;

// For the sake of the example, we'll only show a few of the fields that are produced.
      console.log("Extracted a Driver License:");
      console.log("  Name:", FirstName && FirstName.content, LastName && LastName.content);
      console.log("  License No.:", DocumentNumber && DocumentNumber.content);
      console.log("  Date of Birth:", DateOfBirth && DateOfBirth.content);
      console.log("  Expiration:", DateOfExpiration && DateOfExpiration.content);
      console.log("  Height:", Height && Height.content);
      console.log("  Weight:", Weight && Weight.content);
      console.log("  Eye color:", EyeColor && EyeColor.content);
      console.log("  Restrictions:", Restrictions && Restrictions.content);
      console.log("  Endorsements:", Endorsements && Endorsements.content);
      console.log("  Class:", VehicleClassifications && VehicleClassifications.content);
    } else if (result.docType === "idDocument.passport") {
// The passport document type extracts and parses the Passport's machine-readable zone
      if (!result.fields.machineReadableZone) {
        throw new Error("No Machine Readable Zone extracted from passport.");
      }

      const {
        FirstName,
        LastName,
        DateOfBirth,
        Nationality,
        DocumentNumber,
        CountryRegion,
        DateOfExpiration,
      } = result.fields.machineReadableZone.properties;

      console.log("Extracted a Passport:");
      console.log("  Name:", FirstName && FirstName.content, LastName && LastName.content);
      console.log("  Date of Birth:", DateOfBirth && DateOfBirth.content);
      console.log("  Nationality:", Nationality && Nationality.content);
      console.log("  Passport No.:", DocumentNumber && DocumentNumber.content);
      console.log("  Issuer:", CountryRegion && CountryRegion.content);
      console.log("  Expiration Date:", DateOfExpiration && DateOfExpiration.content);
    } else {
// The only reason this would happen is if the client library's schema for the prebuilt identity document model is
      out of date, and a new document type has been introduced.
      console.error("Unknown document type in result:", result);
    }
  } else {
    throw new Error("Expected at least one receipt in the result.");
  }
}

main().catch((error) => {
  console.error("An error occurred:", error);
  process.exit(1);
});

```

Visit the Azure samples repository on GitHub and view the [ID document model output](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/how-to-guide/id-document-output.md).
