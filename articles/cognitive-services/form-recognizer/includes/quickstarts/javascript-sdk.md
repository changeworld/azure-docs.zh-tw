---
title: 快速入門：適用於 JavaScript 的表單辨識器用戶端程式庫
description: 在本快速入門中，開始使用適用於 JavaScript 的表單辨識器用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/08/2020
ms.author: pafarley
ms.openlocfilehash: f5bf8a0420614b69f4022891e76b5d959f5782f3
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86035751"
---
[參考文件](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [套件 (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可以使用[範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)中 **Train** 資料夾底下的檔案。
* 最新版的 [Node.js](https://nodejs.org/)

## <a name="setting-up"></a>設定

### <a name="create-a-form-recognizer-azure-resource"></a>建立表單辨識器 Azure 資源

[!INCLUDE [create resource](../create-resource.md)]

### <a name="create-environment-variables"></a>建立環境變數

[!INCLUDE [environment-variables](../environment-variables.md)]

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init
```

建立名為 `index.js` 的檔案，將其開啟並匯入下列程式庫：

```javascript
const { FormRecognizerClient, AzureKeyCredential } = require("@azure/ai-form-recognizer");
const fs = require("fs");
```

並請載入環境變數檔案。

```javascript
// Load the .env file if it exists
require("dotenv").config();
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `ai-form-recognizer` 和 `dotenv` NPM 套件：

```console
npm install @azure/ai-form-recognizer dotenv
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。


<!-- 
    Object model
-->

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 JavaScript 的表單辨識器用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>驗證用戶端

在 `main` 函式中，為資源的 Azure 端點和金鑰建立變數。 如果您在啟動應用程式後才建立環境變數，則必須關閉編輯器、IDE 或殼層，再重新開啟，才能存取該變數。

```javascript
// You will need to set these environment variables or edit the following values
const endpoint = process.env["FORM_RECOGNIZER_ENDPOINT"] || "<cognitive services endpoint>";
const apiKey = process.env["FORM_RECOGNIZER_KEY"] || "<api key>";
```

然後，使用您定義的訂用帳戶變數來驗證用戶端物件。 您會使用 **AzureKeyCredential** 物件，這樣當您有需要時，不必建立新的用戶端物件就能更新 API 金鑰。 您也會建立訓練用戶端物件。

```javascript
const trainingClient = new FormTrainingClient(endpoint, new AzureKeyCredential(apiKey));

const client = new FormRecognizerClient(endpoint, new AzureKeyCredential(apiKey));
```

### <a name="call-client-specific-functions"></a>呼叫用戶端專用函式

`main` 中的下一個程式碼區塊會使用用戶端物件，針對表單辨識器 SDK 中的每個主要工作來呼叫函式。 您稍後將會定義這些函式。

您也需要為訓練和測試資料新增 URL 的參考。
* 若要為您的自訂模型訓練資料擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 若要取得要測試的表單 URL，您可以使用上述步驟來取得 Blob 儲存體中個別文件的 SAS URL， 或使用位於他處的文件 URL。
* 另請使用上述方法取得收據影像的 URL，或使用提供的範例影像 URL。

> [!NOTE]
> 本指南中的程式碼片段會使用 URL 所存取的遠端表單。 如果您想要改為處理本機表單文件，請參閱[參考文件](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)中的相關方法。


```javascript
const trainingDataUrl = "<url/path to the labeled training documents>";
const formUrl = "<SAS-URL-of-a-form-in-blob-storage>";
const receiptUrl = "https://docs.microsoft.com/azure/cognitive-services/form-recognizer/media/contoso-allinone.jpg";


// Call Form Recognizer scenarios:
await GetContent(recognizerClient, formUrl);
await AnalyzeReceipt(recognizerClient, receiptUrl);
modelId = await TrainModel(trainingClient, trainingDataUrl);
await AnalyzePdfForm(recognizerClient, modelId, formUrl);
await ManageModels(trainingClient, trainingDataUrl);
```

## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。 若要辨識位於指定 URI 的檔案內容，請使用 **beginRecognizeContentFromUrl** 方法。

```javascript
async function GetContent( recognizerClient, invoiceUri)
{
    const poller = await client.beginRecognizeContentFromUrl(invoiceUri);
    await poller.pollUntilDone();
    const response = poller.getResult();
```

傳回的值會是 **FormPage** 物件的集合：提交的文件每頁各一個。 下列程式碼會逐一查看這些物件，並列印擷取到的索引鍵/值組和資料表資料。

```javascript
    for (const page of response.pages) {
    console.log(
        `Page ${page.pageNumber}: width ${page.width} and height ${page.height} with unit ${page.unit}`
    );
        for (const table of page.tables) {
            for (const row of table.rows) {
                for (const cell of row.cells) {
                    console.log(`cell [${cell.rowIndex},${cell.columnIndex}] has text ${cell.text}`);
                }
            }
        }
    }
}
```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。

若要從 URI 辨識收據，請使用 **beginRecognizeReceiptsFromUrl** 方法。 傳回的值會是 **RecognizedReceipt** 物件的集合：提交的文件每頁各一個。 下列程式碼會處理位於指定 URI 的收據，並將主要欄位和值列印至主控台。

```javascript
async function AnalyzeReceipt( client, receiptUri)
{
    const poller = await client.beginRecognizeReceiptsFromUrl(url, {
        includeTextDetails: true,
        onProgress: (state) => {
            console.log(`analyzing status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();


    const receipt = receipts[0];
    console.log("First receipt:");
    // For supported fields recognized by the service, please refer to https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/GetAnalyzeReceiptResult.
    const receiptTypeField = receipt.recognizedForm.fields["ReceiptType"];
    if (receiptTypeField.valueType === "string") {
        console.log(`  Receipt Type: '${receiptTypeField.value || "<missing>"}', with confidence of ${receiptTypeField.confidence}`);
    }
    const merchantNameField = receipt.recognizedForm.fields["MerchantName"];
    if (merchantNameField.valueType === "string") {
        console.log(`  Merchant Name: '${merchantNameField.value || "<missing>"}', with confidence of ${merchantNameField.confidence}`);
    }
    const transactionDate = receipt.recognizedForm.fields["TransactionDate"];
    if (transactionDate.valueType === "date") {
        console.log(`  Transaction Date: '${transactionDate.value || "<missing>"}', with confidence of ${transactionDate.confidence}`);
    }
```

下一個程式碼區塊會逐一查看在收據上偵測到的個別項目，並將其詳細資料列印至主控台。

```javascript
    const itemsField = receipt.recognizedForm.fields["Items"];
    if (itemsField.valueType === "array") {
        for (const itemField of itemsField.value || []) {
            if (itemField.valueType === "object") {
                const itemNameField = itemField.value["Name"];
                if (itemNameField.valueType === "string") {
                    console.log(`    Item Name: '${itemNameField.value || "<missing>"}', with confidence of ${itemNameField.confidence}`);
                }
            }
        }
    }
}
```


## <a name="train-a-custom-model"></a>定型自訂模型

本節會示範如何使用自己的資料來訓練模型。 經過訓練的模型能夠輸出結構化資料，且其中包含原始表單文件內的索引鍵/值關係。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器範例標記工具](../../quickstarts/label-tool.md)來訓練模型。

### <a name="train-a-model-without-labels"></a>訓練沒有標籤的模型

訓練自訂模型，以辨識在自訂表單中找到的所有欄位和值，而不需要手動標記訓練文件。

下列函式會在指定文件集上訓練模型，並將模型的狀態列印至主控台。 

```javascript
async function TrainModel(trainingClient, trainingDataUrl)
{
    const poller = await trainingClient.beginTraining(trainingDataUrl, false, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
    
    if (!response) {
        throw new Error("Expecting valid response!");
    }
    
    console.log(`Model ID: ${response.modelId}`);
    console.log(`Status: ${response.status}`);
    console.log(`Created on: ${response.requestedOn}`);
    console.log(`Last modified: ${response.completedOn}`);
```

傳回的 **CustomFormModel** 物件包含模型可辨識的表單類型資訊，及其可從每個表單類型中擷取的欄位。 下列程式碼區塊會將此資訊列印至主控台。

```javascript
    if (response.submodels) {
        for (const submodel of response.submodels) {
            // since the training data is unlabeled, we are unable to return the accuracy of this model
            console.log("We have recognized the following fields");
            for (const key in submodel.fields) {
                const field = submodel.fields[key];
                console.log(`The model found field '${field.name}'`);
            }
        }
    }
```

最後，此方法會傳回模型的唯一識別碼。

```csharp
    return response.modelId;
}
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 ( *\<filename\>.pdf.labels.json*)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 **beginTraining** 方法，且將 *uselabels* 參數設為 `true`。

```javascript
async function TrainModelWithLabelsAsync(
    const poller = await trainingClient.beginTraining(trainingDataUrl, true, {
        onProgress: (state) => {
            console.log(`training status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const model = poller.getResult();
    
    return model.modelId;
}
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

本節示範如何使用以自身表單訓練的模型，從您的自訂表單類型中擷取索引鍵/值資訊和其他內容。

> [!IMPORTANT]
> 若要這樣做，您必須先訓練模型，才能將其識別碼傳遞至下列方法。 請參閱[訓練模型](#train-a-model-without-labels)一節。

您會使用 **beginRecognizeCustomFormsFromUrl** 方法。 傳回的值會是 **RecognizedForm** 物件的集合：提交的文件每頁各一個。

```javascript
// Analyze PDF form document at an accessible URL
async function AnalyzePdfForm(client, modelId, formUrl)
{    
    const poller = await client.beginRecognizeCustomFormsFromUrl(modelId, formUrl, {
        onProgress: (state) => {
            console.log(`status: ${state.status}`);
        }
    });
    await poller.pollUntilDone();
    const response = poller.getResult();
```

下列程式碼會將分析結果列印至主控台。 列印內容包括辨識到的每個欄位和對應的值，以及信賴分數。

```javascript
    console.log("Fields:");
    for (const fieldName in form.fields) {
        // each field is of type FormField
        const field = form.fields[fieldName];
        console.log(
            `Field ${fieldName} has value '${field.value}' with a confidence score of ${field.confidence}`
        );
    }
}
```

## <a name="manage-your-custom-models"></a>管理您的自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 下列程式碼範例會在單一函式中執行所有模型管理工作。 請先複製下面的函式簽章：

```javascript
async function ManageModels(trainingClient, trainingFileUrl)
{
```

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>檢查 FormRecognizer 資源帳戶中的模型數

下列程式碼區塊會檢查您已在表單辨識器帳戶中儲存的模型數，並比對帳戶限制。

```csharp
    // First, we see how many custom models we have, and what our limit is
    const accountProperties = await trainingClient.getAccountProperties();
    console.log(
        `Our account has ${accountProperties.customModelCount} custom models, and we can have at most ${accountProperties.customModelLimit} custom models`
    );
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出目前儲存於資源帳戶中的模型

下列程式碼區塊會列出您帳戶中目前的模型，並將其詳細資料列印至主控台。 這也會儲存第一個模型的參考。

```javascript
    // Next, we get a paged async iterator of all of our custom models
    const result = trainingClient.listCustomModels();

    // We could print out information about first ten models
    // and save the first model id for later use
    let i = 0;
    let firstModel;
    for await (const model of result) {
        console.log(`model ${i++}:`);
        console.log(model);
        if (i === 1) {
            firstModel = model;
        }
        if (i > 10) {
            break;
        }
    }
```

### <a name="get-a-specific-model-using-the-models-id"></a>使用模型的識別碼來取得特定模型

下列程式碼區塊會使用上一節中儲存的模型識別碼，並用其來擷取模型相關的詳細資料。

```csharp
    // Now we'll get the first custom model in the paged list
    const model = await trainingClient.getModel(firstModel.modelId);
    console.log(`Model Id: ${model.modelId}`);
    console.log(`Status: ${model.status}`);
    console.log("Documents used in training: [");
    for (const doc of model.trainingDocuments || []) {
        console.log(`  ${doc.documentName}`);
    }
    console.log("]");
```

### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。 此程式碼會刪除上一節所使用的模型。

```javascript
    await client.deleteModel(firstModel.modelId);
    try {
        const deleted = await trainingClient.deleteModel(firstModel.modelId);
        console.log(deleted);
    } catch (err) {
        // Expected
        console.log(`Model with id ${firstModel.modelId} has been deleted`);
    }
}
```


## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```console
node index.js
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑難排解

### <a name="enable-logs"></a>啟用記錄
使用此程式庫時，您可以設定下列環境變數來查看偵錯記錄。

```
export DEBUG=azure*
```

如需如何啟用記錄的詳細指示，請參閱 [@azure/logger 套件文件](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger)。


## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用了表單辨識器的 Python 用戶端程式庫，以不同方式訓練模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
> [建置訓練資料集](../../build-training-data-set.md)

* [什麼是表單辨識器？](../../overview.md)
* 您可在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 取得本指南中的範例程式碼 (及其他資源)。