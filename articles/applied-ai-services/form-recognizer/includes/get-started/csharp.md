---
title: "Get started: Form Recognizer client library for .NET v2.1"
description: Use the Form Recognizer SDK for .NET to create a forms processing app that extracts key/value pairs and table data from your custom documents.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
recommendations: false
ms.custom: ignite-fall-2021
---

<!-- markdownlint-disable MD024 -->

<!-- markdownlint-disable MD033 -->
> [!IMPORTANT]
>
> This quickstart  targets Azure Form Recognizer REST API version **2.1** using cURL to execute REST API calls.
>
>* For simplicity, the code in this article uses synchronous methods and unsecured credentials storage.

[Reference documentation](/dotnet/api/overview/azure/ai.formrecognizer-readme) | [Library source code](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [Package (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

In this quickstart, you'll use the following APIs to extract structured data from forms and documents:

* [Layout](#try-it-layout-model)

* [Invoice](#try-it-prebuilt-model)

## Prerequisites

* Azure subscription - [Create one for free](https://azure.microsoft.com/free/cognitive-services/).

* The current version of [Visual Studio IDE](https://visualstudio.microsoft.com/vs/). <!-- or [.NET Core](https://dotnet.microsoft.com/download). -->

* A Cognitive Services or Form Recognizer resource. Once you have your Azure subscription, create a [single-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) or [multi-service](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) Form Recognizer resource in the Azure portal to get your key and endpoint. You can use the free pricing tier (`F0`) to try the service, and upgrade later to a paid tier for production.

    > [!TIP]
    > Create a Cognitive Services resource if you plan to access multiple cognitive services under a single endpoint/key. For Form Recognizer access only, create a Form Recognizer resource. Please note that you'll need a single-service resource if you intend to use [Azure Active Directory authentication](../../../../active-directory/authentication/overview-authentication.md).

* After your resource deploys, click **Go to resource**. You need the key and endpoint from the resource you create to connect your application to the Form Recognizer API. You'll paste your key and endpoint into the code below later in the quickstart:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: keys and endpoint location in the Azure portal.":::

## Set up

1. Start Visual Studio 2019.

1. On the start page, choose Create a new project.

    :::image type="content" source="../../media/quickstarts/start-window.png" alt-text="Screenshot: Visual Studio start window.":::

1. On the **Create a new project page**, enter **console** in the search box. Choose the **Console Application** template, then choose **Next**.

    :::image type="content" source="../../media/quickstarts/create-new-project.png" alt-text="Screenshot: Visual Studio create new project page.":::

1. In the **Configure your new project** dialog window, enter `formRecognizer_quickstart` in the Project name box. Then choose Next.

    :::image type="content" source="../../media/quickstarts/configure-new-project.png" alt-text="Screenshot: Visual Studio configure new project dialog window.":::

1. In the **Additional information** dialog window, select **.NET 5.0 (Current)**, and then select **Create**.

    :::image type="content" source="../../media/quickstarts/additional-information.png" alt-text="Screenshot: Visual Studio additional information dialog window.":::

### Install the client library with NuGet

 1. Right-click on your **formRecognizer_quickstart** project and select **Manage NuGet Packages...** .

    :::image type="content" source="../../media/quickstarts/select-nuget-package.png" alt-text="Screenshot: select-nuget-package.png":::

 1. Select the Browse tab and type Azure.AI.FormRecognizer.

     :::image type="content" source="../../media/quickstarts/azure-nuget-package.png" alt-text="Screenshot: select-form-recognizer-package.png":::

 1. Select version **3.1.1** from the dropdown menu and select **Install**.

## Build your application

To interact with the Form Recognizer service, you'll need to create an instance of the `FormRecognizerClient` class. To do so, you'll create an `AzureKeyCredential` with your apiKey and a `FormRecognizerClient`  instance with the `AzureKeyCredential` and your Form Recognizer `endpoint`.

1. Open the **Program.cs** file.

1. Include the following using directives:

```csharp
using System;
using Azure;
using Azure.AI.FormRecognizer;
using Azure.AI.FormRecognizer.Models;
using System.Threading.Tasks;
```

1. Set your  `endpoint` and `apiKey`  environment variables and create your `AzureKeyCredential` and `FormRecognizerClient` instance:

```csharp
private static readonly string endpoint = "your-form-recognizer-endpoint";
private static readonly string apiKey = "your-api-key";
private static readonly AzureKeyCredential credential = new AzureKeyCredential(apiKey);
```

1. Delete the line, `Console.Writeline("Hello World!");` , and add one of the **Try It** code samples to the **Main** method in the **Program.cs** file:

    :::image type="content" source="../../media/quickstarts/add-code-here.png" alt-text="Screenshot: add the sample code to the Main method.":::

1. Select a code sample to copy and paste into your application's Main method:

    * [**Layout**](#try-it-layout-model)

    * [**Prebuilt Invoice**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Remember to remove the key from your code when you're done, and never post it publicly. For production, use secure methods to store and access your credentials. See the Cognitive Services [security](.(/azure/cognitive-services/cognitive-services-security.md) article for more information.

## **Try it**: Layout model

Extract text, selection marks, text styles, and table structures, along with their bounding region coordinates from documents.

> [!div class="checklist"]
>
> * For this example, you'll need a **form document file at a URI**. You can use our [sample form document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) for this quickstart.
> * We've added the file URI value to the `formUri` variable.
> * To extract the layout from a given file at a URI, use the `StartRecognizeContentFromUriAsync` method.

### Add the following code to your layout application **Main** method:

```csharp

FormRecognizerClient recognizerClient = AuthenticateClient();

Task recognizeContent = RecognizeContent(recognizerClient);
Task.WaitAll(recognizeContent);
```

### Add the following code below the **Main** method:

```csharp

private static FormRecognizerClient AuthenticateClient()
            {
                var credential = new AzureKeyCredential(apiKey);
                var client = new FormRecognizerClient(new Uri(endpoint), credential);
                return client;
            }

            private static async Task RecognizeContent(FormRecognizerClient recognizerClient)
        {
            string formUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf";
            FormPageCollection formPages = await recognizerClient
        .StartRecognizeContentFromUri(new Uri(formUrl))
        .WaitForCompletionAsync();

            foreach (FormPage page in formPages)
            {
                Console.WriteLine($"Form Page {page.PageNumber} has {page.Lines.Count} lines.");

                for (int i = 0; i < page.Lines.Count; i++)
                {
                    FormLine line = page.Lines[i];
                    Console.WriteLine($"    Line {i} has {line.Words.Count} word{(line.Words.Count > 1 ? "s" : "")}, and text: '{line.Text}'.");
                }

                for (int i = 0; i < page.Tables.Count; i++)
                {
                    FormTable table = page.Tables[i];
                    Console.WriteLine($"Table {i} has {table.RowCount} rows and {table.ColumnCount} columns.");
                    foreach (FormTableCell cell in table.Cells)
                    {
                        Console.WriteLine($"    Cell ({cell.RowIndex}, {cell.ColumnIndex}) contains text: '{cell.Text}'.");
                    }
                }
            }
        }
    }
}

```

## **Try it**: Prebuilt model

This sample demonstrates how to analyze data from certain types of common documents with pre-trained models, using an invoice as an example.

> [!div class="checklist"]
>
> * For this example, we wll analyze an invoice document using a prebuilt model. You can use our [sample invoice document](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) for this quickstart.
> * We've added the file URI value to the `invoiceUri` variable at the top of the Main method.
> * To analyze a given file at a URI, use the `StartRecognizeInvoicesFromUriAsync` method.
> * For simplicity, all the fields that the service returns are not shown here. To see the list of all supported fields and corresponding types, see our [Invoice](../../concept-invoice.md#field-extraction) concept page.

### Choose a prebuilt model

You are not limited to invoices—there are several prebuilt models to choose from, each of which has its own set of supported fields. The model to use for the analyze operation depends on the type of document to be analyzed. Here are the prebuilt models currently supported by the Form Recognizer service:

* [**Invoice**](../../concept-invoice.md): extracts text, selection marks, tables, fields, and key information from invoices.
* [**Receipt**](../../concept-receipt.md): extracts text and key information from receipts.
* [**ID document**](../../concept-id-document.md): extracts text and key information from driver licenses and international passports.
* [**Business-card**](../../concept-business-card.md): extracts text and key information from business cards.

### Add the following code to your prebuilt invoice application **Main** method

```csharp
FormRecognizerClient recognizerClient = AuthenticateClient();

            Task analyzeinvoice = AnalyzeInvoice(recognizerClient, invoiceUrl);
            Task.WaitAll(analyzeinvoice);
```

### Add the following code below the **Main** method:

```csharp
   private static FormRecognizerClient AuthenticateClient() {
     var credential = new AzureKeyCredential(apiKey);
     var client = new FormRecognizerClient(new Uri(endpoint), credential);
     return client;
   }

   static string invoiceUrl = "https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf";

   private static async Task AnalyzeInvoice(FormRecognizerClient recognizerClient, string invoiceUrl) {
     var options = new RecognizeInvoicesOptions() {
       Locale = "en-US"
     };
     RecognizedFormCollection invoices = await recognizerClient.StartRecognizeInvoicesFromUriAsync(new Uri(invoiceUrl), options).WaitForCompletionAsync();

     RecognizedForm invoice = invoices[0];

     FormField invoiceIdField;
     if (invoice.Fields.TryGetValue("InvoiceId", out invoiceIdField)) {
       if (invoiceIdField.Value.ValueType == FieldValueType.String) {
         string invoiceId = invoiceIdField.Value.AsString();
         Console.WriteLine($ "    Invoice Id: '{invoiceId}', with confidence {invoiceIdField.Confidence}");
       }
     }

     FormField invoiceDateField;
     if (invoice.Fields.TryGetValue("InvoiceDate", out invoiceDateField)) {
       if (invoiceDateField.Value.ValueType == FieldValueType.Date) {
         DateTime invoiceDate = invoiceDateField.Value.AsDate();
         Console.WriteLine($ "    Invoice Date: '{invoiceDate}', with confidence {invoiceDateField.Confidence}");
       }
     }

     FormField dueDateField;
     if (invoice.Fields.TryGetValue("DueDate", out dueDateField)) {
       if (dueDateField.Value.ValueType == FieldValueType.Date) {
         DateTime dueDate = dueDateField.Value.AsDate();
         Console.WriteLine($ "    Due Date: '{dueDate}', with confidence {dueDateField.Confidence}");
       }
     }

     FormField vendorNameField;
     if (invoice.Fields.TryGetValue("VendorName", out vendorNameField)) {
       if (vendorNameField.Value.ValueType == FieldValueType.String) {
         string vendorName = vendorNameField.Value.AsString();
         Console.WriteLine($ "    Vendor Name: '{vendorName}', with confidence {vendorNameField.Confidence}");
       }
     }

     FormField vendorAddressField;
     if (invoice.Fields.TryGetValue("VendorAddress", out vendorAddressField)) {
       if (vendorAddressField.Value.ValueType == FieldValueType.String) {
         string vendorAddress = vendorAddressField.Value.AsString();
         Console.WriteLine($ "    Vendor Address: '{vendorAddress}', with confidence {vendorAddressField.Confidence}");
       }
     }

     FormField customerNameField;
     if (invoice.Fields.TryGetValue("CustomerName", out customerNameField)) {
       if (customerNameField.Value.ValueType == FieldValueType.String) {
         string customerName = customerNameField.Value.AsString();
         Console.WriteLine($ "    Customer Name: '{customerName}', with confidence {customerNameField.Confidence}");
       }
     }

     FormField customerAddressField;
     if (invoice.Fields.TryGetValue("CustomerAddress", out customerAddressField)) {
       if (customerAddressField.Value.ValueType == FieldValueType.String) {
         string customerAddress = customerAddressField.Value.AsString();
         Console.WriteLine($ "    Customer Address: '{customerAddress}', with confidence {customerAddressField.Confidence}");
       }
     }

     FormField customerAddressRecipientField;
     if (invoice.Fields.TryGetValue("CustomerAddressRecipient", out customerAddressRecipientField)) {
       if (customerAddressRecipientField.Value.ValueType == FieldValueType.String) {
         string customerAddressRecipient = customerAddressRecipientField.Value.AsString();
         Console.WriteLine($ "    Customer address recipient: '{customerAddressRecipient}', with confidence {customerAddressRecipientField.Confidence}");
       }
     }

     FormField invoiceTotalField;
     if (invoice.Fields.TryGetValue("InvoiceTotal", out invoiceTotalField)) {
       if (invoiceTotalField.Value.ValueType == FieldValueType.Float) {
         float invoiceTotal = invoiceTotalField.Value.AsFloat();
         Console.WriteLine($ "    Invoice Total: '{invoiceTotal}', with confidence {invoiceTotalField.Confidence}");
       }
     }
   }
 }
}
```

## Run your application

Choose the green **Start** button next to formRecognizer_quickstart to build and run your program, or press **F5**.

  :::image type="content" source="../../media/quickstarts/run-visual-studio.png" alt-text="Screenshot: run your Visual Studio program.":::

<!-- --- -->

Congratulations! In this quickstart, you used the Form Recognizer C# SDK to analyze various forms and documents in different ways. Next, explore the reference documentation to learn about Form Recognizer API in more depth.

## Next steps

> [!div class="nextstepaction"]
> [REST API v2.1 reference documentation](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)

> [!div class="nextstepaction"]
> [Form Recognizer C#/.NET reference library](/dotnet/api/overview/azure/AI.FormRecognizer-readme)