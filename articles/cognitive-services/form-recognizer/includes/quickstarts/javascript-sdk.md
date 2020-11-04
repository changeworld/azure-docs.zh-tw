---
title: 快速入門：適用於 JavaScript 的表單辨識器用戶端程式庫
description: 使用適用於 JavaScript 的表單辨識器用戶端程式庫建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: c2a49e10f4407c59a8e4e2bfbb78d889560c7136
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/29/2020
ms.locfileid: "92918672"
---
> [!IMPORTANT]
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 請參閱下列參考文件。 

[參考文件](../../index.yml) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/) | [套件 (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer) | [範例](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 最新版的 [Node.js](https://nodejs.org/)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 資料夾底下的檔案 (下載 *sample_data.zip* 並將其解壓縮)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-nodejs-application"></a>建立新的 Node.js 應用程式

在主控台視窗 (例如 cmd、PowerShell 或 Bash) 中，為您的應用程式建立新的目錄，並瀏覽至該目錄。 

```console
mkdir myapp && cd myapp
```

執行命令 `npm init`，以使用 `package.json` 檔案建立節點應用程式。 

```console
npm init
```

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 `ai-form-recognizer` NPM 套件：

```console
npm install @azure/ai-form-recognizer
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

建立名為 `index.js` 的檔案，將其開啟並匯入下列程式庫：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]


> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js) 上找到該檔案，其中包含本快速入門中的程式碼範例。

為資源的 Azure 端點和金鑰建立變數。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 [產品名稱] 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)一文。

## <a name="object-model"></a>物件模型 

透過表單辨識器，您可以建立兩種不同的用戶端類型。 第一個是 `FormRecognizerClient`，可用來查詢服務，以辨識出表單欄位和內容。 第二個是 `FormTrainingClient`，可用來建立和管理自訂模型，以供您改善辨識效果。 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`FormRecognizerClient` 會提供用於下列目的的作業：

 * 使用已定型的自訂模型辨識表單欄位和內容，以辨識自訂表單。 這些值會在 `RecognizedForm` 物件的集合中傳回。
 * 辨識表單內容，包括資料表、線條和字組，而不需要將模型定型。 表單內容會在 `FormPage` 物件的集合中傳回。
 * 使用表單辨識器服務上已預先定型的收據模型，辨識收據的常見欄位。 這些欄位和中繼資料會在 `RecognizedReceipt` 的集合中傳回。

### <a name="formtrainingclient"></a>FormTrainingClient
`FormTrainingClient` 會提供用於下列目的的作業：

* 將自訂模型定型，以辨識在自訂表單中找到的所有欄位和值。 傳回的 `CustomFormModel` 會指出模型將辨識的表單類型，以及其會針對每個表單類型來擷取的欄位。 如需建立定型資料集的詳細說明，請參閱[關於未標記模型定型的服務文件](#train-a-model-without-labels)。
* 將自訂模型定型，藉由為自訂表單加上標籤來辨識您指定的特定欄位和值。 傳回的 `CustomFormModel` 會指出模型將會擷取的欄位，以及每個欄位的預估正確性。 如需將標籤套用至定型資料集的詳細說明，請參閱[關於已標記模型定型的服務文件](#train-a-model-with-labels)。
* 管理在您的帳戶中建立的模型。
* 將自訂模型從一個表單辨識器資源複製到另一個。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器標籤工具](../../quickstarts/label-tool.md)) 將模型定型。


## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 JavaScript 的表單辨識器用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>驗證用戶端



使用您定義的訂用帳戶變數來驗證用戶端物件。 您會使用 `AzureKeyCredential` 物件，這樣當您有需要時，不必建立新的用戶端物件就能更新 API 金鑰。 您也會建立訓練用戶端物件。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>取得用於測試的資產

您也需要為訓練和測試資料新增 URL 的參考。
* 若要為您的自訂模型訓練資料擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 使用下列範例中所包含的範例表單和收據影像 (也可在 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets) 上取得)，或者您也可以使用上述步驟來取得 Blob 儲存體中個別文件的 SAS URL。 


## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。 若要辨識位於指定 URI 的檔案內容，請使用 `beginRecognizeContentFromUrl` 方法。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]


> [!TIP]
> 您也可以從本機檔案取得內容。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) 方法，例如 **beginRecognizeContent** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的範例程式碼。

### <a name="output"></a>輸出

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。

若要從 URI 辨識收據，請使用 `beginRecognizeReceiptsFromUrl` 方法。 下列程式碼會處理位於指定 URI 的收據，並將主要欄位和值列印至主控台。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> 您也可以辨識本機收據映像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) 方法，例如 **beginRecognizeReceipts** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的範例程式碼。

### <a name="output"></a>輸出

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="train-a-custom-model"></a>定型自訂模型

本節會示範如何使用自己的資料來訓練模型。 經過訓練的模型能夠輸出結構化資料，且其中包含原始表單文件內的索引鍵/值關係。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器範例標記工具](../../quickstarts/label-tool.md)來訓練模型。

### <a name="train-a-model-without-labels"></a>訓練沒有標籤的模型

訓練自訂模型，以辨識在自訂表單中找到的所有欄位和值，而不需要手動標記訓練文件。

下列函式會在指定文件集上訓練模型，並將模型的狀態列印至主控台。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]


### <a name="output"></a>輸出

這是使用 [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) 所提供的定型資料所定型模型的輸出。為了方便閱讀，此範例輸出已截斷。

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: 
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: 
...
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 (`\<filename\>.pdf.labels.json`)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 `beginTraining` 方法，且將 `uselabels` 參數設為 `true`。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]


### <a name="output"></a>輸出 

這是使用 [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 所提供的定型資料所定型模型的輸出。為了方便閱讀，此範例輸出已截斷。

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

本節示範如何使用以自身表單訓練的模型，從您的自訂表單類型中擷取索引鍵/值資訊和其他內容。

> [!IMPORTANT]
> 若要這樣做，您必須先訓練模型，才能將其識別碼傳遞至下列方法。 請參閱[訓練模型](#train-a-model-without-labels)一節。

您會使用 `beginRecognizeCustomFormsFromUrl` 方法。 傳回的值會是 `RecognizedForm` 物件的集合：提交的文件每頁各一個。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> 您也可以分析本機檔案。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/javascript/api/@azure/ai-form-recognizer/formrecognizerclient?view=azure-node-latest) 方法，例如 **beginRecognizeCustomForms** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) 上的範例程式碼。


### <a name="output"></a>輸出

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>管理您的自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 下列程式碼範例會在單一函式中執行所有模型管理工作。 

### <a name="get-number-of-models"></a>取得模型數目

下列程式碼區塊會取得目前在您帳戶中的模型數目。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]


### <a name="get-list-of-models-in-account"></a>取得帳戶中的模型清單

下列程式碼區塊會提供您帳戶中可用模型的完整清單，包括模型建立時間和其目前狀態的相關資訊。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]


### <a name="output"></a>輸出

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>依頁面取得模型識別碼的清單

此程式碼區塊會提供模型和模型識別碼的分頁清單。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]


### <a name="output"></a>輸出

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>依識別碼取得模型

下列函式會採用模型識別碼，並取得相符的模型物件。 預設不會呼叫此函式。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]


### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。 此函式會刪除具有指定識別碼的模型。 預設不會呼叫此函式。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]


### <a name="output"></a>輸出

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
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

在本快速入門中，您使用了表單辨識器的 JavaScript 用戶端程式庫，以不同方式訓練模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
> [建置訓練資料集](../../build-training-data-set.md)

## <a name="see-also"></a>請參閱

* [什麼是表單辨識器？](../../overview.md)
* 您可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js) 取得本指南中的範例程式碼。
