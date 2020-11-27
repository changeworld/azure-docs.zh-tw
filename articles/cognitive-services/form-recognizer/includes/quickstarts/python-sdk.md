---
title: 快速入門：適用於 Python 的表單辨識器用戶端程式庫
description: 使用適用於 Python 的表單辨識器用戶端程式庫建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 534916d81cfb4d3ad1e96d2934f43221067fb94f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95862973"
---
> [!IMPORTANT]
> * 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。 請參閱下列參考文件。 

[參考文件](/python/api/azure-ai-formrecognizer) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer) | [套件 (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/) | [範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 資料夾底下的檔案 (下載 *sample_data.zip* 並將其解壓縮)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

安裝 Python 之後，您可以使用下列方式安裝最新版本的表單辨識器用戶端程式庫：

#### <a name="version-30"></a>[3.0 版](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

#### <a name="version-31-preview"></a>[3.1 版 (預覽)](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

---

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

在您慣用的編輯器或 IDE 中，建立新的 Python 應用程式。 然後匯入下列程式庫。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) 上找到該檔案，其中包含本快速入門中的程式碼範例。


為資源的 Azure 端點和金鑰建立變數。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>物件模型 

透過表單辨識器，您可以建立兩種不同的用戶端類型。 第一個是 `form_recognizer_client`，可用來查詢服務，以辨識出表單欄位和內容。 第二個是 `form_training_client`，可用來建立和管理自訂模型，以供您改善辨識效果。 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` 會提供用於下列目的的作業：

 * 使用已定型的自訂模型辨識表單欄位和內容，以辨識自訂表單。 
 * 辨識表單內容，包括資料表、線條和字組，而不需要將模型定型。 
 * 使用表單辨識器服務上已預先定型的收據模型，辨識收據的常見欄位。

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` 會提供用於下列目的的作業：

* 將自訂模型定型，以辨識在自訂表單中找到的所有欄位和值。 如需建立定型資料集的詳細說明，請參閱[關於未標記模型定型的服務文件](#train-a-model-without-labels)。
* 將自訂模型定型，藉由為自訂表單加上標籤來辨識您指定的特定欄位和值。 如需將標籤套用至定型資料集的詳細說明，請參閱[關於已標記模型定型的服務文件](#train-a-model-with-labels)。
* 管理在您的帳戶中建立的模型。
* 將自訂模型從一個表單辨識器資源複製到另一個。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器標籤工具](../../quickstarts/label-tool.md)) 將模型定型。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 Python 的表單辨識器用戶端程式庫來執行下列工作：

#### <a name="version-30"></a>[3.0 版](#tab/ga)

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

#### <a name="version-31-preview"></a>[3.1 版 (預覽)](#tab/preview)

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [辨識名片](#recognize-business-cards)
* [辨識發票](#recognize-invoices)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>驗證用戶端

在此，您將使用在上方定義的訂用帳戶變數來驗證兩個用戶端物件。 您會使用 **AzureKeyCredential** 物件，這樣當您有需要時，不必建立新的用戶端物件就能更新 API 金鑰。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>取得用於測試的資產

您需要為訓練和測試資料新增 URL 的參考。
* 若要為您的自訂模型訓練資料擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 使用下列範例中所包含的範例表單和收據影像 (也可在 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) 上取得)，或者您也可以使用上述步驟來取得 Blob 儲存體中個別文件的 SAS URL。 

> [!NOTE]
> 本指南中的程式碼片段會使用 URL 所存取的遠端表單。 如果您想要改為處理本機表單文件，請參閱[參考文件](/python/api/azure-ai-formrecognizer)和[範例](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)中的相關方法。

## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。

若要辨識位於指定 URI 的檔案內容，請使用 `begin_recognize_content_from_url` 方法。 傳回的值會是 `FormPage` 物件的集合：提交的文件每頁各一個。 下列程式碼會逐一查看這些物件，並列印擷取到的索引鍵/值組和資料表資料。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> 您也可以從本機映像取得內容。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) 方法，例如 `begin_recognize_content`。 或如需本機映像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的範例程式碼。

### <a name="output"></a>輸出

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。 若要從 URL 辨識收據，請使用 `begin_recognize_receipts_from_url` 方法。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> 您也可以辨識本機收據映像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) 方法，例如 `begin_recognize_receipts`。 或如需本機映像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的範例程式碼。

### <a name="output"></a>輸出

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

#### <a name="version-30"></a>[3.0 版](#tab/ga)

#### <a name="version-31-preview"></a>[3.1 版 (預覽)](#tab/preview)

## <a name="recognize-business-cards"></a>辨識名片

本節示範如何使用預先定型的模型，辨識並擷取英文名片中的常見欄位。 若要從 URL 辨識名片，請使用 `begin_recognize_business_cards_from_url` 方法。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> 您也可以辨識本機名片影像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) 方法，例如 `begin_recognize_business_cards`。 或如需本機映像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的範例程式碼。

## <a name="recognize-invoices"></a>辨識發票

本節示範如何使用預先定型的模型，辨識並擷取銷售發票中的常見欄位。 若要從 URL 辨識發票，請使用 `begin_recognize_invoices_from_url` 方法。 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> 您也可以辨識本機發票影像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) 方法，例如 `begin_recognize_invoices`。 或如需本機映像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的範例程式碼。

---

## <a name="train-a-custom-model"></a>定型自訂模型

本節會示範如何使用自己的資料來訓練模型。 經過訓練的模型能夠輸出結構化資料，且其中包含原始表單文件內的索引鍵/值關係。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器範例標記工具](../../quickstarts/label-tool.md)來訓練模型。

### <a name="train-a-model-without-labels"></a>訓練沒有標籤的模型

訓練自訂模型，以辨識在自訂表單中找到的所有欄位和值，而不需要手動標記訓練文件。

下列程式碼會將訓練用戶端和 `begin_training` 函式搭配使用，在指定的文件集上訓練模型。 傳回的 `CustomFormModel` 物件包含模型可辨識的表單類型資訊，及其可從每個表單類型中擷取的欄位。 下列程式碼區塊會將此資訊列印至主控台。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>輸出

這是使用 [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) 所提供的定型資料所定型模型的輸出。

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 傳回的 `CustomFormModel` 會指出模型可擷取的欄位，及其在每個欄位中的預估正確性。 下列程式碼區塊會將此資訊列印至主控台。

> [!IMPORTANT]
> 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 (`\<filename\>.pdf.labels.json`)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 `begin_training` 方法，且將 *use_training_labels* 參數設為 `true`。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>輸出

這是使用 [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training) 所提供的定型資料所定型模型的輸出。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

本節示範如何使用以自身表單訓練的模型，從您的自訂表單類型中擷取索引鍵/值資訊和其他內容。

> [!IMPORTANT]
> 若要這樣做，您必須先訓練模型，才能將其識別碼傳遞至下列方法。 請參閱[訓練模型](#train-a-model-without-labels)一節。

您會使用 `begin_recognize_custom_forms_from_url` 方法。 傳回的值會是 `RecognizedForm` 物件的集合：提交的文件每頁各一個。 下列程式碼會將分析結果列印至主控台。 列印內容包括辨識到的每個欄位和對應的值，以及信賴分數。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> 您也可以分析本機映像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) 方法，例如 `begin_recognize_custom_forms`。 或如需本機映像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) 上的範例程式碼。


### <a name="output"></a>輸出

使用上一個範例中的模型，會提供下列輸出。

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```

## <a name="manage-your-custom-models"></a>管理您的自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>檢查 FormRecognizer 資源帳戶中的模型數

下列程式碼區塊會檢查您已在表單辨識器帳戶中儲存的模型數，並比對帳戶限制。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>輸出

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出目前儲存於資源帳戶中的模型

下列程式碼區塊會列出您帳戶中目前的模型，並將其詳細資料列印至主控台。 這也會儲存第一個模型的參考。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>輸出

這是測試帳戶的範例輸出。

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>使用模型的識別碼來取得特定模型

下列程式碼區塊會使用上一節中儲存的模型識別碼，並用其來擷取模型相關的詳細資料。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>輸出

這是上一個範例中所建立自訂模型的範例輸出。

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。 此程式碼會刪除上一節所使用的模型。

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `python` 命令執行應用程式。

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑難排解

### <a name="general"></a>一般

表單辨識器用戶端程式庫會引發 [Azure Core](https://aka.ms/azsdk-python-azure-core) 中定義的例外狀況。

### <a name="logging"></a>記錄

此程式庫會使用[標準記錄程式庫](https://docs.python.org/3/library/logging.html)進行記錄。 HTTP 工作階段的基本資訊 (URL、標頭等) 會記錄在「資訊」層級。

您可以在具備 `logging_enable` 關鍵字引數的用戶端啟用詳細的「偵錯」層級記錄，包括要求/回應本文和未刪改的標頭：

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


同樣地，`logging_enable` 可對單一作業啟用詳細記錄，即使未對用戶端啟用也可行：

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用了表單辨識器的 Python 用戶端程式庫，以不同方式訓練模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
> [建置訓練資料集](../../build-training-data-set.md)

* [什麼是表單辨識器？](../../overview.md)
* 您可在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py) 取得本指南中的範例程式碼。
