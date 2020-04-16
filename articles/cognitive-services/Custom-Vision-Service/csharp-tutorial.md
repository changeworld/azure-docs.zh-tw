---
title: 快速入門：使用適用於 C# 的自訂視覺 SDK 建立影像分類專案
titleSuffix: Azure Cognitive Services
description: 建立專案、新增標籤、上傳影像、為您的專案定型，以及使用 .NET SDK 和 C# 進行預測。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7c71077deff4a7c01a1c982dc41f477c4c19f69e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404218"
---
# <a name="quickstart-create-an-image-classification-project-with-the-custom-vision-net-sdk"></a>快速入門：使用自訂視覺 .NET SDK 建立影像分類專案

本文提供資訊和範例程式碼，可協助您開始使用自訂視覺 SDK 與 C# 來建置影像分類模型。 建立它之後，您就可以新增標記、上傳影像、為專案定型、取得專案的預設預測端點 URL，並使用端點以程式設計方式測試影像。 請使用此範例作為範本來建置您自己的 .NET 應用程式。 如果您想要進行「不用」  程式碼來建置及使用分類模型的程序，請改為參閱[以瀏覽器為基礎的指引](getting-started-build-a-classifier.md)。

## <a name="prerequisites"></a>Prerequisites

- [Visual Studio 2015 或 2017](https://www.visualstudio.com/downloads/) 的任何版本
- [!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="get-the-custom-vision-sdk-and-sample-code"></a>取得自訂視覺 SDK 與程式碼範例

若要撰寫使用自訂視覺的 .NET 應用程式，您需要有自訂視覺 NuGet 套件。 這些套件包含在您將會下載的專案範例內，但您可以在此個別存取這些套件。

- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/)
- [Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/)

複製或下載[認知服務 .NET 範例](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)專案。 在 Visual Studio 中瀏覽至 **CustomVision/ImageClassification** 資料夾並開啟 _ImageClassification.csproj_。

此 Visual Studio 專案會建立名為__我的新專案__的新自訂視覺專案，而此專案可透過[自訂視覺網站](https://customvision.ai/)來存取。 然後上傳影像以便為分類器定型並加以測試。 在此專案中，分類器會用來判斷影像是「鐵杉」  還是「日本櫻」  。

[!INCLUDE [get-keys](includes/get-keys.md)]

## <a name="understand-the-code"></a>了解程式碼

開啟 _Program.cs_ 檔案，並檢查程式碼。 分別為您名為 [ 和 ](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) 的定型和預測金鑰`CUSTOM_VISION_TRAINING_KEY`建立環境變數`CUSTOM_VISION_PREDICTION_KEY`。 指令碼會尋找這些變數。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_keys)]

此外，請從自訂視覺網站的 [設定] 頁面取得您的 [端點 URL]。 將它儲存到名為 `CUSTOM_VISION_ENDPOINT` 的環境變數。 指令碼會將它的參考儲存在類別的根目錄。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_endpoint)]

以下幾行程式碼會執行專案的主要功能。

### <a name="create-a-new-custom-vision-service-project"></a>建立新的自訂視覺服務專案

所建立的專案會顯示在您稍早瀏覽過的[自訂視覺網站](https://customvision.ai/)上。 當您建立專案時，請參閱 [CreateProject](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__) 方法來指定其他選項 (如[建置分類器](getting-started-build-a-classifier.md) Web 入口網站指南中所述)。   

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_create)]

### <a name="create-tags-in-the-project"></a>在專案中建立標記

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_tags)]

### <a name="upload-and-tag-images"></a>上傳和標記影像

此專案的影像會包含在其中。 它們會在 **Program.cs** 的 _LoadImagesFromDisk_ 方法中受到參考。 您最多可以在單一批次中上傳 64 個影像。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]

### <a name="train-the-classifier-and-publish"></a>訓練分類器並發佈

此程式碼會在預測模型中建立第一個反覆項目，然後將該反覆項目發佈至預測端點。 您可以使用反覆項目的名稱來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

### <a name="set-the-prediction-endpoint"></a>設定預測端點

預測端點可作為參考供您用來提交影像給目前的模型，並取得分類預測。

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction_endpoint)]

### <a name="submit-an-image-to-the-default-prediction-endpoint"></a>將影像提交至預設預測端點

在此指令碼中，會以 **LoadImagesFromDisk** 方法載入測試影像，且會在主控台中顯示模型的預測輸出。 `publishedModelName` 變數的值應該對應至位在自訂視覺入口網站 [效能]  索引標籤上的 [發行形式] 值。 

[!code-csharp[](~/cognitive-services-dotnet-sdk-samples/CustomVision/ImageClassification/Program.cs?name=snippet_prediction)]

## <a name="run-the-application"></a>執行應用程式

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

[!INCLUDE [clean-ic-project](includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已了解如何在程式碼中執行影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)
