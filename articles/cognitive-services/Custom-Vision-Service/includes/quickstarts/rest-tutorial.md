---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 12/09/2020
ms.topic: include
ms.openlocfilehash: a53b316f6d942f070ef925b369bcea8476fbfffd
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/01/2021
ms.locfileid: "97853702"
---
開始使用自訂視覺 REST API。 請遵循這些步驟來呼叫 API，並建置影像分類模型。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 自訂視覺最容易透過用戶端程式庫 SDK，或透過[瀏覽器架構的指導方針](../../get-started-build-detector.md)使用。

使用適用於 .NET 的自訂視覺用戶端程式庫可執行下列作業：

* 建立新的自訂視覺專案
* 將標記新增至專案
* 上傳和標記影像
* 為專案定型
* 發佈目前的反覆項目
* 測試預測端點

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="建立自訂視覺資源"  target="_blank">建立自訂視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以建立定型和預測資源及取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至自訂視覺。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。


## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

您將使用如下所示的命令來建立影像分類專案。 所建立的專案會顯示在[自訂視覺網站](https://customvision.ai/)上。


:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createproject":::

將命令複製到文字編輯器，並進行下列變更：

* 將 `{subscription key}` 換成您的有效 Face 訂用帳戶金鑰。
* 將 `{endpoint}` 取代為對應至您訂用帳戶金鑰的端點。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
* 將 `{name}` 取代為您的專案名稱。
* 依需求設定其他 URL 參數，以設定專案將使用的模型類型。 如需選項資訊，請參閱[建立專案 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)。

您會收到如下的 JSON 回應。 將專案的 `"id"` 值儲存至暫存位置。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "settings": {
    "domainId": "00000000-0000-0000-0000-000000000000",
    "classificationType": "Multiclass",
    "targetExportPlatforms": [
      "CoreML"
    ],
    "useNegativeSet": true,
    "detectionParameters": "string",
    "imageProcessingSettings": {
      "augmentationMethods": {}
    }
  },
  "created": "string",
  "lastModified": "string",
  "thumbnailUri": "string",
  "drModeEnabled": true,
  "status": "Succeeded"
}
```

## <a name="add-tags-to-the-project"></a>將標記新增至專案

使用下列命令定義您用來定型模型的標籤。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="createtag":::

* 再次插入您自己的金鑰和端點 URL。
* 以您自己的專案識別碼取代 `{projectId}`。
* 請將 `{name}` 取代為您要使用的名稱。

針對您想要在專案中使用的所有標籤重複此流程。 如果您使用的是所提供的範例影像，請新增 `"Hemlock"` 和 `"Japanese Cherry"` 標籤。

您會取得如下的 JSON 回應。 將每個標籤的 `"id"` 值儲存至暫存位置。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "description": "string",
  "type": "Regular",
  "imageCount": 0
}
```

## <a name="upload-and-tag-images"></a>上傳和標記影像

接下來，下載此專案的範例影像。 將[範例影像資料夾](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)的內容儲存到您的本機裝置。

使用下列命令來上傳影像並套用標籤，一次用於「鐵杉」的影像，而其他分別用於「日本櫻花」的影像。 如需更多選項，請參閱[從資料建立影像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeb5) API。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="uploadimages":::

* 再次插入您自己的金鑰和端點 URL。
* 以您自己的專案識別碼取代 `{projectId}`。
* 使用標籤識別碼來取代 `{tagArray}`。
* 然後，使用您要標記之影像的二進位資料填入要求的主體。

## <a name="train-the-project"></a>為專案定型

這個方法會將您上傳之標籤影像中的模型定型，並傳回目前專案反覆項目的識別碼。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="trainproject":::

* 再次插入您自己的金鑰和端點 URL。
* 以您自己的專案識別碼取代 `{projectId}`。
* 使用標籤識別碼來取代 `{tagArray}`。
* 然後，使用您要標記之影像的二進位資料填入要求的主體。
* 選用其他 URL 參數。 如需選項資訊，請參閱[定型專案](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fddee1) API。

> [!TIP]
> 使用選取的標記進行訓練
>
> 您可以選擇只在已套用的標記子集上進行訓練。 您可以在尚未套用足夠的特定標記，但其他套用的標記已足夠時執行此操作。 將選用的 JSON 內容新增至要求的主體。 以您要使用的標籤識別碼填入 `"selectedTags"` 陣列。
> ```json
> {
>   "selectedTags": [
>     "00000000-0000-0000-0000-000000000000"
>   ]
> }
> ```

JSON 回應包含已定型專案的相關資訊，包括反覆項目識別碼 (`"id"`)。 複製此值供下一個步驟使用。

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "status": "string",
  "created": "string",
  "lastModified": "string",
  "trainedAt": "string",
  "projectId": "00000000-0000-0000-0000-000000000000",
  "exportable": true,
  "exportableTo": [
    "CoreML"
  ],
  "domainId": "00000000-0000-0000-0000-000000000000",
  "classificationType": "Multiclass",
  "trainingType": "Regular",
  "reservedBudgetInHours": 0,
  "trainingTimeInMinutes": 0,
  "publishName": "string",
  "originalPublishResourceId": "string"
}
```

## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

此方法會讓模型的目前反覆運算可供查詢。 您可以使用傳回的模型名稱作為參考，以傳送預測要求。 

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再次插入您自己的金鑰和端點 URL。
* 以您自己的專案識別碼取代 `{projectId}`。
* 將 `{iterationId}` 取代為上一個步驟中所傳回的 ID。
* 以要指派給預測模型的名稱取代 `{publishedName}`。
* 以您自己的預測資源識別碼取代 `{predictionId}`。 您可以在 Azure 入口網站中預測資源的 **概觀** 索引標籤上找到預測資源識別碼，該識別碼列為 **訂用帳戶識別碼**。
* 選用其他 URL 參數。 請參閱[發佈反覆項目](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc7548b571998fdded5) API。

## <a name="test-the-prediction-endpoint"></a>測試預測端點

最後，使用此命令測試您的已定型模型，方式是上傳新的影像，以使用標籤分類。 您可以使用稍早下載之範例檔案的「測試」資料夾中的影像。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/custom-vision/image-classifier.sh" ID="publish":::

* 再次插入您自己的金鑰和端點 URL。
* 以您自己的專案識別碼取代 `{projectId}`。
* 將 `{publishedName}` 取代為上一個步驟中使用的名稱。
* 將本機影像的二進位資料新增至要求主體。
* 選用其他 URL 參數。 請參閱[分類影像](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.1/operations/5eb37d24548b571998fde5f3) API。

傳回的 JSON 回應至少會包含套用至影像之模型的每個標標籤，以及每個標籤的可能性分數。 

```json
{
  "id": "00000000-0000-0000-0000-000000000000",
  "project": "00000000-0000-0000-0000-000000000000",
  "iteration": "00000000-0000-0000-0000-000000000000",
  "created": "string",
  "predictions": [
    {
      "probability": 0.0,
      "tagId": "00000000-0000-0000-0000-000000000000",
      "tagName": "string",
      "boundingBox": {
        "left": 0.0,
        "top": 0.0,
        "width": 0.0,
        "height": 0.0
      },
      "tagType": "Regular"
    }
  ]
}
```

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已完成使用 REST API 執行影像分類流程的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* [API 參考文件 (訓練)](/dotnet/api/overview/azure/cognitiveservices/client/customvision?view=azure-dotnet)
* [API 參考文件 (預測)](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.3/operations/5eb0bcc6548b571998fddeae)
