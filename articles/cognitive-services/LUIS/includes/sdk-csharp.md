---
title: 包含檔案
description: 包含檔案
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 09/01/2020
ms.topic: include
ms.custom: include file, devx-track-dotnet, cog-serv-seo-aug-2020
ms.author: diberry
ms.openlocfilehash: fff4e8c43263dfcc49be6cb6269078643118e8df
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323065"
---
使用適用於 .NET 的 Language Understanding (LUIS) 用戶端程式庫可以：
* 建立應用程式
* 使用範例語句新增意圖、機器學習實體
* 定型和發佈應用程式
* 查詢預測執行階段

[參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/languageunderstanding?view=azure-dotnet) | [製作](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Authoring)和[預測](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.LUIS.Runtime) 程式庫原始程式碼 | [製作](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)和[預測](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime/) NuGet | [C# 範例](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/sdk-3x//Program.cs)

## <a name="prerequisites"></a>先決條件

* [.NET Core](https://dotnet.microsoft.com/download/dotnet-core) 和 [.NET Core CLI](https://docs.microsoft.com/dotnet/core/tools/) 的目前版本。
* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services)
* 擁有 Azure 訂用帳戶之後，請在 Azure 入口網站中[建立 Language Understanding 撰寫資源](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)，以取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要您[建立](../luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal)之資源的金鑰和端點，以將應用程式連線至 Language Understanding 撰寫。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。 您可以使用免費定價層 (`F0`) 來試用服務。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

在您慣用的編輯器或 IDE 中，建立新的 .NET Core 應用程式。

1. 在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 dotnet `new` 命令建立名為 `language-understanding-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：`Program.cs`。

    ```dotnetcli
    dotnet new console -n language-understanding-quickstart
    ```

1. 將目錄變更為新建立的應用程式資料夾。

    ```dotnetcli
    cd language-understanding-quickstart
    ```

1. 您可以使用下列命令來建置應用程式：

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

### <a name="install-the-nuget-libraries"></a>安裝 NuGet 程式庫

在應用程式目錄中，使用下列命令安裝適用於 .NET 的 Language Understanding (LUIS) 用戶端程式庫：

```dotnetcli
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring --version 3.2.0-preview.3
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.1.0-preview.1
```

## <a name="authoring-object-model"></a>製作物件模型

Language Understanding (LUIS) 撰寫用戶端是向 Azure 進行驗證的 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet)物件，其中包含您的撰寫金鑰。

## <a name="code-examples-for-authoring"></a>可供撰寫的程式碼範例

建立用戶端之後，請使用此用戶端來存取功能，包括：

* 應用程式 - [建立](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet)、[刪除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.deleteasync?view=azure-dotnet)、[發佈](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet)
* 範例語句 - [新增](https://docs.microsoft.com//dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.addasync?view=azure-dotnet)、[依識別碼刪除](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.deleteasync?view=azure-dotnet)
* 功能 - 管理[片語清單](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.featuresextensions.addphraselistasync?view=azure-dotnet)
* 模型 - 管理[意圖](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)和實體
* 模式 - 管理[模式](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.patternextensions?view=azure-dotnet)
* 定型 - [定型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.trainversionasync?view=azure-dotnet)應用程式及輪詢[定型狀態](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet)
* [版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.versionsextensions?view=azure-dotnet) - 使用複製、匯出和刪除進行管理

## <a name="prediction-object-model"></a>預測物件模型

Language Understanding (LUIS) 預測執行階段用戶端是向 Azure 進行驗證的 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet)物件，其中包含您的資源金鑰。

## <a name="code-examples-for-prediction-runtime"></a>預測執行階段的程式碼範例

建立用戶端之後，請使用此用戶端來存取功能，包括：

* [依預備或生產位置](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getslotpredictionasync?view=azure-dotnet)進行預測
* 依[版本](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.predictionoperationsextensions.getversionpredictionasync?view=azure-dotnet)進行預測


[!INCLUDE [Bookmark links to same article](sdk-code-examples.md)]

## <a name="add-the-dependencies"></a>新增相依性

從專案目錄，在慣用的編輯器或 IDE 中開啟 *Program.cs* 檔案。 將現有的 `using` 程式碼取代為下列 `using` 指示詞：

[!code-csharp[Add NuGet libraries to code file](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=Dependencies)]

## <a name="add-boilerplate-code"></a>新增未定案程式碼

1. 將 `Main` 方法的簽章變更為允許非同步呼叫：

    ```csharp
    public static async Task Main()
    ```

1. 除非另有指定，否則請在 `Program` 類別的 `Main` 方法中新增其餘程式碼。

## <a name="create-variables-for-the-app"></a>為應用程式建立變數

建立兩組變數：您可變更第一組，第二組則保留為其出現在程式碼範例中的樣子。 

1. 建立變數來保存您的製作金鑰和資源名稱。

    [!code-csharp[Variables you need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouChange)]

1. 建立變數來保存您的端點、應用程式名稱、版本和意圖名稱。

    [!code-csharp[Variables you don't need to change](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=VariablesYouDontNeedToChangeChange)]

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的金鑰建立 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點建立 [LUISAuthoringClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.luisauthoringclient?view=azure-dotnet) 物件。

[!code-csharp[Authenticate the client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateClient)]

## <a name="create-a-luis-app"></a>建立 LUIS 應用程式

LUIS 應用程式包含納入意圖、實體和範例語句的自然語言處理 (NLP) 模型。

建立 [ApplicationCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.applicationcreateobject?view=azure-dotnet)。 名稱和語言文化特性是必要屬性。 呼叫 [Apps.AddAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.addasync?view=azure-dotnet) 方法。 回應為應用程式識別碼。

[!code-csharp[Create a LUIS app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringCreateApplication)]

## <a name="create-intent-for-the-app"></a>建立應用程式的意圖
LUIS 應用程式模型中的主要物件為意圖。 意圖會與使用者語句「意圖」的群組相對應。 使用者可以詢問問題，或發出預期會由 Bot (或其他用戶端應用程式) 提供特定回應的陳述。 意圖的範例包括預約航班、詢問目的地城市的天氣，以及詢問客戶服務的連絡人資訊。

建立具有唯一意圖名稱的 [ModelCreateObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modelcreateobject?view=azure-dotnet)，然後將應用程式識別碼、版本識別碼和 ModelCreateObject 傳至 [Model.AddIntentAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addintentasync?view=azure-dotnet) 方法。 回應為意圖識別碼。

`intentName` 值已硬式編碼為 `OrderPizzaIntent`，作為 [為應用程式建立變數](#create-variables-for-the-app)區段中的一部分變數。

[!code-csharp[Create intent for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AddIntent)]

## <a name="create-entities-for-the-app"></a>建立應用程式的實體

雖然實體並非必要，但大部分的應用程式中都會有實體。 實體會從使用者語句中擷取滿足使用者意圖所需的資訊。 目前有數種類型的[預建](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions.addprebuiltasync?view=azure-dotnet)和自訂實體，每一種分別有其本身的資料轉換物件 (DTO) 模型。  會新增至應用程式的常見預建實體包括 [number](../luis-reference-prebuilt-number.md)、[datetimeV2](../luis-reference-prebuilt-datetimev2.md)、[geographyV2](../luis-reference-prebuilt-geographyv2.md)、[ordinal](../luis-reference-prebuilt-ordinal.md)。

請務必了解，實體上不會標示意圖。 它們可以且通常會套用至許多意圖。 只有範例使用者語句會標示特定的單一意圖。

實體的建立方法是[模型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.modelextensions?view=azure-dotnet)類別的一部分。 每個實體類型都有本身的資料轉換物件 (DTO) 模型，而[模型](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models?view=azure-dotnet)命名空間中通常會包含 `model` 這個字。

實體建立程式碼會建立機器學習實體，其中包含套用至 `Quantity` 子實體的子實體和功能。

:::image type="content" source="../media/quickstart-sdk/machine-learned-entity.png" alt-text="入口網站中顯示所建立實體的部分螢幕擷取畫面，這是一個機器學習實體，其中包含套用至 `Quantity` 子實體的子實體和功能。":::

[!code-csharp[Create entities for the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddEntities)]

使用下列類別方法尋找數量子實體列的識別碼，以將功能指派給該子實體。

[!code-csharp[Find subentity id](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringSortModelObject)]

## <a name="add-example-utterance-to-intent"></a>將範例語句新增至意圖

為了判斷語句的意圖及擷取實體，應用程式需要語句的範例。 這些範例必須以特定的單一意圖為目標，且應標示所有的自訂實體。 預建實體不需要標示。

藉由建立 [ExampleLabelObject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.examplelabelobject?view=azure-dotnet) 物件的清單來新增範例語句，每個範例語句各有一個物件。 每個範例都應以實體名稱和實體值的名稱/值配對字典來標示所有實體。 實體值應與範例語句的文字中所顯示的完全相同。

:::image type="content" source="../media/quickstart-sdk/labeled-example-machine-learned-entity.png" alt-text="在入口網站中顯示標記範例語句的部分螢幕擷取畫面。":::

使用應用程式識別碼、版本識別碼和範例呼叫 [Examples.AddAsync](https://docs.microsoft.com//dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.examplesextensions.addasync?view=azure-dotnet)。

[!code-csharp[Add example utterance to intent](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=AuthoringAddLabeledExamples)]

## <a name="train-the-app"></a>進行應用程式定型

建立模型之後，必須針對此版本的模型將 LUIS 應用程式定型。 已定型的模型可用於[容器](../luis-container-howto.md)中，或[發佈](../luis-how-to-publish-app.md)至預備或產品位置。

[Train.TrainVersionAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions?view=azure-dotnet) 方法需要應用程式識別碼和版本識別碼。

非常小的模型 (如本快速入門所示) 會非常快速地進行定型。 針對生產層級的應用程式，在為應用程式定型時，必須進行 [GetStatusAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.trainextensions.getstatusasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_TrainExtensions_GetStatusAsync_Microsoft_Azure_CognitiveServices_Language_LUIS_Authoring_ITrain_System_Guid_System_String_System_Threading_CancellationToken_) 方法的輪詢呼叫，以確認定型成功的時間或是否成功。 其回應是 [ModelTrainingInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.models.modeltraininginfo?view=azure-dotnet) 物件的清單，每個物件各有不同的狀態。 所有物件都必須成功，才會將定型視為完成。

[!code-csharp[Train the app](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=TrainAppVersion)]

## <a name="publish-app-to-production-slot"></a>將應用程式發佈至生產位置

使用 [PublishAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.authoring.appsextensions.publishasync?view=azure-dotnet) 方法發佈 LUIS 應用程式。 這會將目前的定型版本發佈至端點上的指定位置。 您的用戶端應用程式會使用此端點來傳送使用者語句，以進行意圖和實體擷取的預測。

[!code-csharp[Publish app to production slot](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PublishVersion)]

## <a name="authenticate-the-prediction-runtime-client"></a>驗證預測執行階段用戶端

以您的金鑰使用 [ApiKeyServiceClientCredentials](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.apikeyserviceclientcredentials?view=azure-dotnet) 物件，並使用該物件與您的端點建立 [LUISRuntimeClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-dotnet) 物件。

[!INCLUDE [Caution about using authoring key](caution-authoring-key.md)]

[!code-csharp[Authenticate the prediction runtime client](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=PredictionCreateClient)]


## <a name="get-prediction-from-runtime"></a>從執行階段取得預測

新增下列程式碼，以建立對預測執行階段的要求。

使用者語句是 [PredictionRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-dotnet) 物件的一部分。

**GetSlotPredictionAsync** 方法需要數個參數，例如應用程式識別碼、位置名稱，以及用來完成要求的預測要求物件。 其他選項 (例如詳細資訊、顯示所有意圖和記錄) 是選擇性的。

[!code-csharp[Get prediction from runtime](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/sdk-3x//Program.cs?name=QueryPredictionEndpoint)]

[!INCLUDE [Prediction JSON response](sdk-json.md)]

## <a name="run-the-application"></a>執行應用程式

使用 `dotnet run` 命令，從您的應用程式目錄執行應用程式。

```dotnetcli
dotnet run
```
