---
title: 快速入門：適用於 .NET 的表單辨識器用戶端程式庫
description: 使用適用於 .NET 的表單辨識器用戶端程式庫建立表單處理應用程式，從您的自訂文件中擷取索引鍵/值組和資料表資料。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: 9e0bdbc9cc197deb5028848731f031ff19d5ebf7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897796"
---
> [!IMPORTANT]
> 為求簡化，本文中的程式碼使用同步方法和未受保護的認證儲存體。

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/ai.formrecognizer-readme) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src) | [套件 (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer) | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或目前版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 包含一組訓練資料的 Azure 儲存體 Blob。 請參閱[為自訂模型建置訓練資料集](../../build-training-data-set.md) (機器翻譯)，以獲得產生訓練資料集的提示和選項。 在本快速入門中，您可使用 [範例資料集](https://go.microsoft.com/fwlink/?linkid=2090451)的 **Train** 資料夾底下的檔案 (下載 *sample_data.zip* 並將其解壓縮)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title="建立表單辨識器資源"  target="_blank">建立表單辨識器資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至表單辨識器 API。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio，建立新的 .NET Core 應用程式。 

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

建立新專案後，以滑鼠右鍵按一下 [方案總管] 中的專案解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]、核取 [包含發行前版本]，然後搜尋 `Azure.AI.FormRecognizer`。 選取版本 `3.0.0`，然後 **安裝** 。 

#### <a name="cli"></a>[CLI](#tab/cli)

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `formrecognizer-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔： *program.cs* 。 

```console
dotnet new console -n formrecognizer-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```console
dotnet build
```

建置輸出應該不會有警告或錯誤。 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```
---

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

在應用程式目錄中，使用下列命令來安裝適用於 .NET 的 [產品名稱] 用戶端程式庫：

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 上找到該檔案，其中包含本快速入門中的程式碼範例。

從專案目錄，在慣用的編輯器或 IDE 中開啟 *Program.cs* 檔案。 新增下列 `using` 指示詞：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

在應用程式的 **Program** 類別中，為資源的金鑰和端點建立變數。

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的表單辨識器資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security)一文。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

在應用程式的 **Main** 方法中，新增對本快速入門中所用非同步工作的呼叫。 您稍後會加以實作。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]


## <a name="object-model"></a>物件模型 

透過表單辨識器，您可以建立兩種不同的用戶端類型。 第一個是 `FormRecognizerClient`，可用來查詢服務，以辨識出表單欄位和內容。 第二個是 `FormTrainingClient`，可用來建立和管理自訂模型，以供您改善辨識效果。 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` 會提供用於下列目的的作業：

 - 使用已定型的自訂模型辨識表單欄位和內容，以辨識自訂表單。  這些值會在 `RecognizedForm` 物件的集合中傳回。 請參閱[分析自訂表單](#analyze-forms-with-a-custom-model)範例。
 - 辨識表單內容，包括資料表、線條和字組，而不需要將模型定型。  表單內容會在 `FormPage` 物件的集合中傳回。 請參閱[辨識表單內容](#recognize-form-content)範例。
 - 使用表單辨識器服務上已預先定型的收據模型，辨識美國收據的常見欄位。 這些欄位和中繼資料會在 `RecognizedForm` 物件的集合中傳回。 請參閱[辨識收據](#recognize-receipts)範例。

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` 會提供用於下列目的的作業：

- 將自訂模型定型，以辨識在自訂表單中找到的所有欄位和值。  傳回的 `CustomFormModel` 會指出模型將辨識的表單類型，以及其會針對每個表單類型來擷取的欄位。
- 將自訂模型定型，藉由為自訂表單加上標籤來辨識您指定的特定欄位和值。  傳回的 `CustomFormModel` 會指出模型將會擷取的欄位，以及每個欄位的預估正確性。
- 管理在您的帳戶中建立的模型。
- 將自訂模型從一個表單辨識器資源複製到另一個。

請參閱[將模型定型](#train-a-custom-model)和[管理自訂模型](#manage-custom-models)的範例。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器標籤工具](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/label-tool)) 將模型定型。

## <a name="code-examples"></a>程式碼範例

這些程式碼片段會示範如何使用適用於 .NET 的表單辨識器用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [辨識表單內容](#recognize-form-content)
* [辨識收據](#recognize-receipts)
* [訓練自訂模型](#train-a-custom-model)
* [使用自訂模型分析表單](#analyze-forms-with-a-custom-model)
* [管理您的自訂模型](#manage-your-custom-models)


## <a name="authenticate-the-client"></a>驗證用戶端

在 **Main`AuthenticateClient` 下方，建立名為**  的新方法。 您將在其他工作中使用此功能，以驗證向表單辨識器服務提出的要求。 此方法會使用 `AzureKeyCredential` 物件，這樣當您有需要時，不必建立新的用戶端物件就能更新 API 金鑰。

> [!IMPORTANT]
> 從 Azure 入口網站取得您的金鑰和端點。 如果您在 [必要條件] 區段中建立的表單辨識器資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 例如，[Azure金鑰保存庫](https://docs.microsoft.com/azure/key-vault/key-vault-overview)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>取得用於測試的資產 

您也需要為訓練和測試資料新增 URL 的參考。 將這些專案新增至 **Program** 類別的根目錄。

* 若要為您的自訂模型訓練資料擷取 SAS URL，請開啟 Microsoft Azure 儲存體總管、以滑鼠右鍵按一下您的容器，然後選取 [取得共用存取簽章]。 確定 [讀取] 和 [列出] 權限均已勾選，再按一下 [建立]。 然後，複製 [URL] 區段的值。 其格式應該為：`https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`。
* 然後，使用上述步驟來取得 Blob 儲存體中個別文件的 SAS URL。
* 最後，儲存下列範例中包含的範例收據影像 URL (也可在 [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) 上取得)。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]


## <a name="recognize-form-content"></a>辨識表單內容

您可以使用表單辨識器來辨識文件中的資料表、行和字組，而不需要訓練模型。 傳回的值會是 **FormPage** 物件的集合：提交的文件每頁各一個。 

若要辨識位於指定 URI 的檔案內容，請使用 `StartRecognizeContentFromUri` 方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> 您也可以從本機檔案取得內容。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) 方法，例如 **StartRecognizeContent** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 上的範例程式碼。

此工作的其餘部分會將內容資訊列印至主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>輸出

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>辨識收據

本節示範如何使用預先訓練的收據模型，辨識並擷取美國收據中的常見欄位。

若要從 URL 辨識收據，請使用 `StartRecognizeReceiptsFromUri` 方法。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> 您也可以辨識本機收據映像。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) 方法，例如 **StartRecognizeReceipts** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 上的範例程式碼。

傳回的值會是 `RecognizedReceipt` 物件的集合：提交的文件每頁各一個。 下列程式碼會處理位於指定 URI 的收據，並將主要欄位和值列印至主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>輸出 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="train-a-custom-model"></a>定型自訂模型

本節會示範如何使用自己的資料來訓練模型。 經過訓練的模型能夠輸出結構化資料，且其中包含原始表單文件內的索引鍵/值關係。 定型模型後，您就可以測試及重新定型模型，並於最終根據您的需求用此模型來從更多的表單可靠地擷取資料。

> [!NOTE]
> 您也可以使用圖形化使用者介面 (例如[表單辨識器範例標記工具](../../quickstarts/label-tool.md)來訓練模型。

### <a name="train-a-model-without-labels"></a>訓練沒有標籤的模型

訓練自訂模型，以辨識在自訂表單中找到的所有欄位和值，而不需要手動標記訓練文件。 下列方法會在指定文件集上訓練模型，並將模型的狀態列印至主控台。 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


傳回的 `CustomFormModel` 物件包含模型可辨識的表單類型資訊，及其可從每個表單類型中擷取的欄位。 下列程式碼區塊會將此資訊列印至主控台。


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

最後，傳回定型的模型識別碼，以便在稍後的步驟中使用。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>輸出

為了方便閱讀，此回應已截斷。

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>訓練具備標籤的模型

您也可以手動標記訓練文件來訓練自訂模型。 在某些情況下，使用標籤來訓練會讓效能更佳。 若要使用標籤來訓練，您的 Blob 儲存體容器中除了訓練文件以外，還必須要有特殊的標籤資訊檔案 (`\<filename\>.pdf.labels.json`)。 [表單辨識器範例標籤工具](../../quickstarts/label-tool.md)提供可協助您建立這些標籤檔案的 UI。 只要有了這些檔案，即可呼叫 `StartTrainingAsync` 方法，且將 `uselabels` 參數設為 `true`。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

傳回的 `CustomFormModel` 會指出模型可擷取的欄位，及其在每個欄位中的預估正確性。 下列程式碼區塊會將此資訊列印至主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>輸出

為了方便閱讀，此回應已截斷。

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>使用自訂模型分析表單

本節示範如何使用以自身表單訓練的模型，從您的自訂表單類型中擷取索引鍵/值資訊和其他內容。

> [!IMPORTANT]
> 若要這樣做，您必須先訓練模型，才能將其識別碼傳遞至下列方法。

您會使用 `StartRecognizeCustomFormsFromUri` 方法。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> 您也可以分析本機檔案。 請參閱 [FormRecognizerClient](https://docs.microsoft.com/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) 方法，例如 **StartRecognizeCustomForms** 。 或如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 上的範例程式碼。

傳回的值會是 `RecognizedForm` 物件的集合：提交的文件每頁各一個。 下列程式碼會將分析結果列印至主控台。 列印內容包括辨識到的每個欄位和對應的值，以及信賴分數。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>輸出

為了方便閱讀，此回應已截斷。

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>管理自訂模型

本節示範如何管理您的帳戶中儲存的自訂模型。 您將會在下列方法內執行多項作業：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>檢查 FormRecognizer 資源帳戶中的模型數

下列程式碼區塊會檢查您已在表單辨識器帳戶中儲存的模型數，並比對帳戶限制。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>輸出 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>列出目前儲存於資源帳戶中的模型

下列程式碼區塊會列出您帳戶中目前的模型，並將其詳細資料列印至主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>輸出 

為了方便閱讀，此回應已截斷。

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>使用模型的識別碼來取得特定模型

下列程式碼區塊會訓練新的模型 (如[訓練模型](#train-a-model-without-labels)一節中所述)，然後使用其識別碼來擷取第二個參考。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>輸出 

為了方便閱讀，此回應已截斷。

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>從資源帳戶中刪除模型

您也可以參考模型的識別碼，從帳戶中將其刪除。 此步驟也會關閉方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


## <a name="run-the-application"></a>執行應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

按一下 IDE 視窗頂端的 [偵錯] 按鈕，以執行應用程式。

#### <a name="cli"></a>[CLI](#tab/cli)

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>疑難排解

使用 .NET SDK 與認知服務表單辨識器用戶端程式庫互動時，該服務所傳回的錯誤會導致 `RequestFailedException`。 這些錯誤會包含 REST API 要求所傳回的相同 HTTP 狀態碼。

例如，如果您提交 URI 無效的收據影像，就會傳回 `400` 錯誤，指出「錯誤的要求」。

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

您會發現系統記錄了額外資訊，例如作業的用戶端要求識別碼。

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您使用了表單辨識器的 .NET 用戶端程式庫，以不同方式來訓練模型和分析表單。 接下來，您將了解建立更好的訓練資料集有何秘訣，然後產生更精確的模型。

> [!div class="nextstepaction"]
> [建置訓練資料集](../../build-training-data-set.md)

* [什麼是表單辨識器？](../../overview.md)
* 您可在 [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) 取得本指南中的範例程式碼 (及其他資源)。
