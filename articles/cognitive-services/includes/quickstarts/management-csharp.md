---
title: 快速入門：適用於 .NET 的 Azure 管理用戶端程式庫
description: 在本快速入門中，開始使用適用於 .NET 的 Azure 管理用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 93ea0ffc94b78e014b30ab1d45d589eba50fe524
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88607602"
---
[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [範例](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="prerequisites"></a>必要條件

* 有效的 Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/)。
* 最新版 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。

## <a name="create-an-azure-service-principal"></a>建立 Azure 服務主體

若要讓您的應用程式與您的 Azure 帳戶互動，您需要 Azure 服務主體來管理權限。 依照[建立 Azure 服務主體](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)中的指示進行。

建立服務主體時，您會看到服務主體有一個祕密值、一個識別碼和一個應用程式識別碼。 將應用程式識別碼和祕密儲存至暫存位置，以供後續步驟使用。

## <a name="create-a-resource-group"></a>建立資源群組

建立認知服務資源之前，您的帳戶必須有包含資源的 Azure 資源群組。 如果還沒有資源群組，請在開始之前，先在 [Azure 入口網站](https://ms.portal.azure.com/)中建立一個。

## <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

建立新的 .NET Core 應用程式。 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `azure-management-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。 

```console
dotnet new console -n azure-management-quickstart
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

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Azure 管理用戶端程式庫：

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

### <a name="import-libraries"></a>匯入程式庫

開啟 Program.cs，並將下列 `using` 陳述式新增至檔案的最上方：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>驗證用戶端

將下列欄位新增至 program.cs 的根目錄，並使用您所建立的服務主體和您的 Azure 帳戶資訊填入其值。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

然後，在您的 **Main** 方法中，使用這些值來建構 **CognitiveServicesManagementClient** 物件。 您所有的 Azure 管理作業都需要此物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>呼叫管理方法

將下列程式碼新增至您的 **Main** 方法，以列出可用的資源、建立範例資源、列出您擁有的資源，然後刪除範例資源。 您會在稍後的步驟中定義這些方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

### <a name="choose-a-service-and-pricing-tier"></a>選擇服務和定價層

建立新的資源時，必須知道您要使用的服務「種類」，以及想要的[定價層](https://azure.microsoft.com/pricing/details/cognitive-services/) (或 SKU)。 建立資源時，您將使用此資訊和其他資訊作為參數。 您可以在指令碼中呼叫下列方法，以尋找可用的認知服務「種類」清單：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

請參閱下方的 SKU 和定價資訊清單。 

#### <a name="multi-service"></a>多服務

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 多種服務。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/)頁面。            | `CognitiveServices`     |


#### <a name="vision"></a>視覺

| 服務                    | 種類                      |
|----------------------------|---------------------------|
| 電腦視覺            | `ComputerVision`          |
| 自訂視覺 - 預測 | `CustomVision.Prediction` |
| 自訂視覺 - 訓練   | `CustomVision.Training`   |
| 臉部                       | `Face`                    |
| 表單辨識器            | `FormRecognizer`          |
| 筆跡辨識器             | `InkRecognizer`           |

#### <a name="search"></a>搜尋

| 服務            | 種類                  |
|--------------------|-----------------------|
| Bing 自動建議   | `Bing.Autosuggest.v7` |
| Bing 自訂搜尋 | `Bing.CustomSearch`   |
| Bing 實體搜尋 | `Bing.EntitySearch`   |
| Bing 搜尋        | `Bing.Search.v7`      |
| Bing 拼字檢查   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>語音

| 服務            | 種類                 |
|--------------------|----------------------|
| 語音服務    | `SpeechServices`     |
| 語音辨識 | `SpeakerRecognition` |

#### <a name="language"></a>Language

| 服務            | 種類                |
|--------------------|---------------------|
| Form Understanding | `FormUnderstanding` |
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| 文字分析     | `TextAnalytics`     |
| 文字翻譯   | `TextTranslation`   |

#### <a name="decision"></a>決策

| 服務           | 種類               |
|-------------------|--------------------|
| 異常偵測器  | `AnomalyDetector`  |
| 內容仲裁 | `ContentModerator` |
| 個人化工具      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>定價層和計費

定價層 (以及您支付的金額) 是根據您使用驗證資訊傳送的交易數目而定。 每個定價層都會指定：
* 每秒允許的交易數目上限 (TPS)。
* 在該定價層中啟用的服務功能。
* 預先定義的交易數目成本。 超過此數目會產生額外的費用，如您服務的[定價詳細資料](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)中所述。

> [!NOTE]
> 許多認知服務都具有免費層，您可以用來試用服務。 若要使用免費層，請使用 `F0` 作為資源的 SKU。

## <a name="create-a-cognitive-services-resource"></a>建立認知服務資源

若要建立並訂閱新的認知服務資源，請使用 **Create** 方法。 這個方法會將新的可計費資源新增至您傳入的資源群組。 建立新的資源時，必須知道您要使用的服務「種類」，以及其定價層 (或 SKU) 和 Azure 位置。 下列方法會將這些項目全部當作引數，並建立資源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

## <a name="view-your-resources"></a>檢視資源

若要檢視您 Azure 帳戶下的所有資源 (跨所有資源群組)，請使用下列方法：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>刪除資源

下列方法會從給定的資源群組中刪除指定的資源。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

## <a name="see-also"></a>另請參閱

* [Azure 管理 SDK 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/management?view=azure-dotnet)
* [Azure 認知服務是什麼？](../../Welcome.md)
* [驗證 Azure 認知服務要求](../../authentication.md)
* [使用 Azure 入口網站建立新的資源](../../cognitive-services-apis-create-account.md)