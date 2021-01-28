---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 7aa4ba8a29df762ee61fa426f3e60f0956c2956f
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947292"
---
開始使用適用於 .NET 的自訂視覺用戶端程式庫。 請遵循下列步驟來安裝套件，並試用建立影像分類模型的程式碼範例。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下建立和定型分類模型，請參閱[以瀏覽器為基礎的指引](../../getting-started-build-a-classifier.md)。

使用適用於 .NET 的自訂視覺用戶端程式庫可執行下列作業：

* 建立新的自訂視覺專案
* 將標記新增至專案
* 上傳和標記影像
* 為專案定型
* 發佈目前的反覆項目
* 測試預測端點

[參考文件](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | 程式庫原始程式碼 [(定型)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(預測)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | 套件 (NuGet) [(定型)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(預測)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [範例](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) 或目前版本的 [.NET Core](https://dotnet.microsoft.com/download/dotnet-core)。
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="建立自訂視覺資源"  target="_blank">建立自訂視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以建立定型和預測資源及取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至自訂視覺。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="create-a-new-c-application"></a>建立新的 C# 應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

使用 Visual Studio，建立新的 .NET Core 應用程式。 

### <a name="install-the-client-library"></a>安裝用戶端程式庫 

建立新專案後，以滑鼠右鍵按一下 [方案總管] 中的專案解決方案，然後選取 [管理 NuGet 套件]，以安裝用戶端程式庫。 在開啟的套件管理員中，選取 [瀏覽]、核取 [包含發行前版本]，然後搜尋 `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` 和 `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`。 選取最新版本，然後選取 [安裝]。 

#### <a name="cli"></a>[CLI](#tab/cli)

在主控台視窗中 (例如 cmd、PowerShell 或 Bash)，使用 `dotnet new` 命令建立名為 `custom-vision-quickstart` 的新主控台應用程式。 此命令會建立簡單的 "Hello World" C# 專案，內含單一原始程式檔：*program.cs*。 

```console
dotnet new console -n custom-vision-quickstart
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

在應用程式目錄中，使用下列命令安裝適用於 .NET 的自訂視覺用戶端程式庫：

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs) 上找到該檔案，其中包含本快速入門中的程式碼範例。

從專案目錄中中開啟 program.cs 檔案，並新增下列 `using` 指示詞：

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


在應用程式的 **Main** 方法中，為資源的金鑰和端點建立變數。 您也會宣告一些要在稍後使用的基本物件。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的自訂視覺資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 您必須同時取得訓練和預測金鑰，以及訓練資源的端點。
>
> 切記，完成時從程式碼中移除金鑰，且切勿公開發佈金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

在應用程式的 **Main** 方法中，針對本快速入門中使用的方法新增呼叫。 您稍後會實作這些呼叫。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>物件模型

|名稱|描述|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | 此類別會處理模型的建立、定型和發佈。 |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| 此類別會處理您的模型查詢，以進行影像分類預測。|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| 此類別會定義單一影像上的單一預測。 其中包含物件識別碼和名稱的屬性，以及信賴分數。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 .NET 的自訂視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [建立新的自訂視覺專案](#create-a-new-custom-vision-project)
* [將標記新增至專案](#add-tags-to-the-project)
* [上傳和標記影像](#upload-and-tag-images)
* [為專案定型](#train-the-project)
* [發佈目前的反覆項目](#publish-the-current-iteration)
* [測試預測端點](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>驗證用戶端

在新方法中，使用您的端點和金鑰來具現化定型和預測用戶端。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

此下一段程式碼會建立影像分類專案。 所建立的專案會顯示在[自訂視覺網站](https://customvision.ai/)上。 當您建立專案時，請參閱 [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) 方法來指定其他選項 (如[建置分類器](../../getting-started-build-a-classifier.md) Web 入口網站指南中所述)。  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>將標記新增至專案

此方法會定義您將用來定型模型的標記。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

首先，下載此專案的範例影像。 將[範例影像資料夾](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)的內容儲存到您的本機裝置。

> [!NOTE]
> Trove 是 Microsoft Garage 專案，可讓您收集和購買影像集以供訓練使用。 收集影像之後，您可以下載影像，然後以一般方式將其匯入至自訂視覺專案。 如需深入了解，請造訪 [Trove 頁面](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)。

然後定義 Helper 方法，以在此目錄中上傳影像。 您可能需要編輯 **GetFiles** 引數，以指向您影像的儲存位置。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

接下來，定義方法來上傳影像，並根據其資料夾位置來套用標記 (影像已經排序)。 您可以反覆地或以批次 (每個批次最多 64 個) 方式上傳和標記影像。 此程式碼片段包含兩者的範例。 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>為專案定型

此方法會建立專案中的第一個定型反覆運算。 其會查詢服務，直到定型完成為止。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> 使用選取的標記進行訓練
>
> 您可以選擇只在已套用的標記子集上進行訓練。 您可以在尚未套用足夠的特定標記，但其他套用的標記已足夠時執行此操作。 在 [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 呼叫中，使用 *trainingParameters* 參數。 建構 [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters)，並將其 **SelectedTags** 屬性設定為您要使用的標記識別碼清單。 此模型將訓練為僅辨識該清單上的標記。

## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

此方法會讓模型的目前反覆運算可供查詢。 您可以使用模型名稱作為參考，以傳送預測要求。 您必須針對 `predictionResourceId` 輸入自己的值。 您可以在 Azure 入口網站中資源的 [概觀] 索引標籤上找到預測資源識別碼，該識別碼列為 [訂用帳戶識別碼]。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>測試預測端點

這部分的指令碼會載入測試影像、查詢模型端點，並將預測資料輸出至主控台。

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>執行應用程式

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

按一下 IDE 視窗頂端的 [偵錯] 按鈕，以執行應用程式。

#### <a name="cli"></a>[CLI](#tab/cli)

使用 `dotnet run` 命令從您的應用程式目錄執行應用程式。

```dotnet
dotnet run
```

---

應用程式在執行時，應會開啟主控台視窗並寫入下列輸出：

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

接著，您可以確認測試影像 (位於 **Images/Test/** 中) 是否已正確加上標記。 按任意鍵以結束應用程式。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已完成在程式碼中執行影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* 此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [SDK 參考文件](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
