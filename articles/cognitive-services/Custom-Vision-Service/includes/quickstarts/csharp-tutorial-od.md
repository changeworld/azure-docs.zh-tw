---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.openlocfilehash: c9f5b5e84955c1974c19d0ccff1a89560fd3e78a
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604826"
---
本指南提供指示和範例程式碼，可協助您開始使用適用於 C# 的自訂視覺用戶端程式庫來建置物件偵測模型。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下，建立和訓練物件偵測模型，請改為參閱[以瀏覽器為基礎的指引](../../get-started-build-detector.md)。

## <a name="prerequisites"></a>先決條件：

- [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 的任何版本
- [!INCLUDE [create-resources](../../includes/create-resources.md)]

## <a name="install-the-custom-vision-client-library"></a>安裝自訂視覺用戶端程式庫

若要使用適用於 .NET 的自訂視覺來撰寫影像分析應用程式，您將需要自訂視覺 NuGet 套件。 這些套件包含在您將會下載的專案範例內，但您可以在此個別存取這些套件。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

複製或下載[認知服務 .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)專案。 在 Visual Studio 中瀏覽至「CustomVision/ObjectDetection」**** 資料夾並開啟_ObjectDetection.csproj_。

此 Visual Studio 專案會建立名為__我的新專案__的新自訂視覺專案，而此專案可透過[自訂視覺網站](https://customvision.ai/)來存取。 接著，它會上傳影像，以針對物件偵測模型進行定型及測試。 在此專案中，將會為模型定型以偵測影像中的叉子和剪刀。

[!INCLUDE [get-keys](../../includes/get-keys.md)]

## <a name="examine-the-code"></a>檢查程式碼

開啟 _Program.cs_ 檔案，並檢查程式碼。 分別為您名為 `CUSTOM_VISION_TRAINING_KEY` 和 `CUSTOM_VISION_PREDICTION_KEY` 的定型和預測金鑰[建立環境變數](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)。 指令碼會尋找這些變數。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_keys)]

此外，請從自訂視覺網站的 [設定] 頁面取得您的 [端點 URL]。 將它儲存到名為 `CUSTOM_VISION_ENDPOINT` 的環境變數。 指令碼會將它的參考儲存在類別的根目錄。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_endpoint)]

## <a name="create-a-new-custom-vision-service-project"></a>建立新的自訂視覺服務專案

下一段的程式碼會建立物件偵測專案。 所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 當您建立專案時，請參閱 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true) 方法來指定其他選項 (如[建置偵測器](../../get-started-build-detector.md) Web 入口網站指南中所述)。  

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>將標記新增至專案

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

為物件偵測專案中的影像加上標記時，您必須使用標準化座標來識別每個加上標記的物件所屬的區域。 下列程式碼會為每個範例影像及其已加上標記的區域建立關聯。

> [!NOTE]
> 如果您沒有按住並拖曳公用程式可標示區域的座標，您可以使用 [Customvision.ai](https://www.customvision.ai/) 上的 Web UI。 此範例已提供座標。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload_regions)]

然後，此關聯對應會用來上傳每個範例影像及其區域座標。 您最多可以在單一批次中上傳 64 個影像。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_upload)]

此時，您已上傳所有影像範例，並為每個影像 (**叉子**或**剪刀**) 標記上相關聯的像素矩形。

## <a name="train-the-project"></a>為專案定型

此程式碼會建立專案中的第一個訓練反覆項目。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_train)]

> [!TIP]
> 使用選取的標記進行訓練
>
> 您可以選擇只在已套用的標記子集上進行訓練。 您可以在尚未套用足夠的特定標記，但其他套用的標記已足夠時執行此操作。 在 [TrainProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true) 呼叫中，使用 *trainingParameters* 參數。 建構 [TrainingParameters](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters?view=azure-dotnet&preserve-view=true)，並將其 **SelectedTags** 屬性設定為您要使用的標記識別碼清單。 此模型將訓練為僅辨識該清單上的標記。

## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_publish)]

## <a name="create-a-prediction-endpoint"></a>建立預測端點

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction_endpoint)]

## <a name="test-the-prediction-endpoint"></a>測試預測端點

這部分的指令碼會載入測試影像、查詢模型端點，並將預測資料輸出至主控台。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ObjectDetection/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>執行應用程式

應用程式在執行時，應會開啟主控台視窗並寫入下列輸出：

```console
Creating new project:
        Training
Done!

Making a prediction:
        fork: 98.2% [ 0.111609578, 0.184719115, 0.6607002, 0.6637112 ]
        scissors: 1.2% [ 0.112389535, 0.119195729, 0.658031344, 0.7023591 ]
```

接著，您可以確認測試影像 (位於 **Images/Test/** 中) 是否已正確加上標記，以及偵測的區域是否正確。 此時，您可以按任意鍵以結束應用程式。

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已完成在程式碼中執行物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。 下列指南會處理影像分類，但其原則類似於物件偵測。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* [SDK 參考文件](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)