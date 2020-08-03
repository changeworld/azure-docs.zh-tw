---
title: 快速入門：適用於 .NET 的電腦視覺用戶端程式庫
description: 在本快速入門中，開始使用適用於 .NET 的電腦視覺用戶端程式庫。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: c1406b5e7297b1d48b23d9dfa684e0d76b68139f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102539"
---
<a name="HOLTop"></a>

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [套件 (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [範例](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [.NET Core SDK](https://dotnet.microsoft.com/download/)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="建立電腦視覺資源"  target="_blank">建立電腦視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以取得您的金鑰和端點。 在其部署後，按一下 [前往資源]。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至 電腦視覺服務。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。
* 為金鑰和端點 URL [建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)，名稱分別為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT`。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。 

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `computer-vision-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*ComputerVisionQuickstart.cs*。

```dotnetcli
dotnet new console -n computer-vision-quickstart
```

將目錄變更為新建立的應用程式資料夾。 您可以使用下列命令來建置應用程式：

```dotnetcli
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

從專案目錄，在慣用的編輯器或 IDE 中開啟 *ComputerVisionQuickstart.cs* 檔案。 新增下列 `using` 指示詞：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using)]

在應用程式的 [程式] 類別中，為資源的 Azure 端點和金鑰建立變數。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_vars)]

### <a name="install-the-client-library"></a>安裝用戶端程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的電腦視覺用戶端程式庫：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0-preview.1
```

如果您使用 Visual Studio IDE，則可以取得可下載 NuGet 套件形式的用戶端程式庫。

## <a name="object-model"></a>物件模型

下列類別和介面會處理電腦視覺 .NET SDK 的一些主要功能。

|名稱|描述|
|---|---|
| [ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet) | 所有電腦視覺功能都需要此類別。 您可以使用訂用帳戶資訊來具現化此類別，並使用它來進行大部分的影像作業。|
|[ComputerVisionClientExtensions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions?view=azure-dotnet)| 此類別包含 ComputerVisionClient 的其他方法。|
|[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)| 此列舉會定義可在標準分析作業中完成的不同影像分析類型。 視您的需求而定，您可以指定一組 VisualFeatureTypes 值。 |

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 .NET 的電腦視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [分析影像](#analyze-an-image)
* [讀取印刷和手寫文字](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>驗證用戶端

> [!NOTE]
> 本快速入門假設您已針對名為 `COMPUTER_VISION_SUBSCRIPTION_KEY` 和 `COMPUTER_VISION_ENDPOINT` 的電腦視覺金鑰和端點[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。

在新方法中，使用端點和金鑰來具現化用戶端。 使用您的金鑰建立 **[ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials?view=azure-dotnet)** 物件，並使用該物件與您的端點建立 **[ComputerVisionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient?view=azure-dotnet)** 物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

您可以在 `Main`方法中呼叫此方法。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

## <a name="analyze-an-image"></a>分析影像

下列程式碼會定義 `AnalyzeImageUrl` 方法，以使用用戶端物件來分析遠端影像並輸出結果。 該方法會傳回文字描述、分類、標記清單、偵測到的臉部、成人內容旗標、主要色彩和影像類型。

在 `Main` 方法中新增方法呼叫。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyzeinmain)]

### <a name="set-up-test-image"></a>設定測試影像

在 [程式] 類別中，儲存您要分析之影像的 URL 參考。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_url)]

> [!NOTE]
> 您也可以分析本機影像。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上的範例程式碼。

### <a name="specify-visual-features"></a>指定視覺特徵

定義影像分析的新方法。 新增下面的程式碼，以指定您要在分析中擷取的視覺功能。 如需完整清單，請參閱 **[VisualFeatureTypes](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.models.visualfeaturetypes?view=azure-dotnet)** 列舉。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_visualfeatures)]

將下列任何程式碼區塊插入您的 **AnalyzeImageUrl** 方法中，以實作其功能。 請記得在結尾加上右中括弧。

```csharp
}
```

### <a name="analyze"></a>分析

**AnalyzeImageAsync** 方法會傳回包含所有已擷取資訊的 **ImageAnalysis** 物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_analyze_call)]

下列各節將詳細說明如何剖析這項資訊。

### <a name="get-image-description"></a>取得影像說明

下列程式碼會取得為影像產生的標題清單。 如需詳細資訊，請參閱[描述影像](../../concept-describing-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_describe)]

### <a name="get-image-category"></a>取得影像類別

下列程式碼會取得已偵測到的影像類別。 如需詳細資訊，請參閱[將影像分類](../../concept-categorizing-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_categorize)]

### <a name="get-image-tags"></a>取得影像標籤

下列程式碼會取得影像中已偵測到的一組標籤。 如需詳細資訊，請參閱[內容標籤](../../concept-tagging-images.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_tags)]

### <a name="detect-objects"></a>偵測物件

下列程式碼會偵測影像中的一般物件，並將其輸出到主控台。 如需詳細資訊，請參閱[物件偵測](../../concept-object-detection.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_objects)]

### <a name="detect-brands"></a>偵測品牌

下列程式碼會偵測影像中的公司品牌和標誌，並將其輸出到主控台。 如需詳細資訊，請參閱[品牌偵測](../../concept-brand-detection.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_brands)]

### <a name="detect-faces"></a>偵測臉部

下列程式碼會傳回影像中偵測到的臉部及其矩形座標，然後選取臉部特性。 如需詳細資訊，請參閱[臉部偵測](../../concept-detecting-faces.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_faces)]

### <a name="detect-adult-racy-or-gory-content"></a>偵測成人、猥褻或暴力內容

下列程式碼會列印影像中偵測到的成人內容。 如需詳細資訊，請參閱[成人、猥褻、暴力內容](../../concept-detecting-adult-content.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_adult)]

### <a name="get-image-color-scheme"></a>取得影像色彩配置

下列程式碼會列印影像中偵測到的色彩特性，例如主色和輔色。 如需詳細資訊，請參閱[色彩配置](../../concept-detecting-color-schemes.md)。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_color)]

### <a name="get-domain-specific-content"></a>取得特定領域內容

電腦視覺可以使用特製化模型對影像執行進一步的分析。 如需詳細資訊，請參閱[特定領域內容](../../concept-detecting-domain-content.md)。 

下列程式碼會剖析影像中偵測到的名人相關資料。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_celebs)]

下列程式碼會剖析影像中偵測到的地標相關資料。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_landmarks)]

### <a name="get-the-image-type"></a>取得影像類型

下列程式碼可列印影像類型的相關資訊&mdash;不論是美工圖案還是線條繪圖。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_type)]

## <a name="read-printed-and-handwritten-text"></a>讀取印刷和手寫文字

電腦視覺可以讀取影像中的可見文字，並將它轉換成字元資料流。 如需文字辨識的詳細資訊，請參閱[光學字元辨識 (OCR)](../../concept-recognizing-text.md#read-api) 概念文件。本節中的程式碼會定義 `BatchReadFileUrl` 方法，其使用用戶端物件來偵測及擷取影像中的文字。

在 `Main` 方法中新增方法呼叫。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

### <a name="set-up-test-image"></a>設定測試影像

在 [程式] 類別中，儲存您要從中擷取文字的影像 URL 參考。 此程式碼片段包含印刷和手寫文字的範例影像。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttext_url)]

> [!NOTE]
> 您也可以從本機影像擷取文字。 如需本機影像的相關案例，請參閱 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上的範例程式碼。

### <a name="call-the-read-api"></a>呼叫讀取 API

定義用來讀取文字的新方法。 新增下面的程式碼，針對指定的影像呼叫 **ReadAsync** 方法。 這會傳回作業識別碼並啟動非同步程序來讀取影像的內容。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_call)]

### <a name="get-read-results"></a>取得讀取結果

接下來，取得從 **ReadAsync** 呼叫傳回的作業識別碼，並使用該識別碼來查詢服務中的作業結果。 下列程式碼會以一秒的間隔檢查作業，直到傳回結果為止。 然後，它會將已解壓縮的文字資料輸出到主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_response)]

### <a name="display-read-results"></a>顯示讀取結果

新增下列程式碼，以剖析並顯示已擷取的文字資料，並完成方法定義。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extract_display)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnetcli
dotnet run
```

## <a name="clean-up-resources"></a>清除資源

如果您想要清除和移除認知服務訂用帳戶，則可以刪除資源或資源群組。 刪除資源群組也會刪除其關聯的任何其他資源。

* [入口網站](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
>[電腦視覺 API 參考 (.NET)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet)

* [什麼是電腦視覺？](../../Home.md)
* 此範例的原始程式碼可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) 上找到。
