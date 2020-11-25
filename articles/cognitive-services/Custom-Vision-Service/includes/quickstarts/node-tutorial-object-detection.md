---
author: areddish
ms.author: areddish
ms.service: cognitive-services
ms.date: 10/26/2020
ms.custom: devx-track-js
ms.openlocfilehash: 0c6c80849f48cb4f859bcbaaeb4f072eb575ba74
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021244"
---
本指南提供指示和範例程式碼，可協助您開始使用適用於 Node.js 的自訂視覺用戶端程式庫來建置物件偵測模型。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下，建立和訓練物件偵測模型，請改為參閱[以瀏覽器為基礎的指引](../../get-started-build-detector.md)。

使用適用於 .NET 的自訂視覺用戶端程式庫可執行下列作業：

* 建立新的自訂視覺專案
* 將標記新增至專案
* 上傳和標記影像
* 為專案定型
* 發佈目前的反覆項目
* 測試預測端點

參考文件 [(訓練)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest) [(預測)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest) | 程式庫原始程式碼 [(訓練)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-training) [(預測)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-customvision-prediction) | 套件 (npm) [(訓練)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-training) [(預測)](https://www.npmjs.com/package/@azure/cognitiveservices-customvision-prediction) | [範例](/samples/browse/?products=azure&terms=custom%20vision&languages=javascript)


## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* 最新版的 [Node.js](https://nodejs.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title="建立自訂視覺資源"  target="_blank">建立自訂視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以建立定型和預測資源及取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至自訂視覺。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
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

若要使用適用於 Node.js 的自訂視覺來撰寫影像分析應用程式，您將需要自訂視覺 NPM 套件。 若要加以安裝，請在 PowerShell 中執行下列命令：

```shell
npm install @azure/cognitiveservices-customvision-training
npm install @azure/cognitiveservices-customvision-prediction
```

您應用程式的 `package.json` 檔案會隨著相依性而更新。

建立名為 `index.js` 的檔案，並匯入下列程式庫：

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js) 上找到該檔案，其中包含本快速入門中的程式碼範例。

為資源的 Azure 端點和金鑰建立變數。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的 [產品名稱] 資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 
>
> 完成時，請記得從程式碼中移除金鑰，且不要公開張貼金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../../cognitive-services/cognitive-services-security.md)一文。

同時新增專案名稱的欄位，以及非同步呼叫的逾時參數。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_vars)]

## <a name="object-model"></a>物件模型

|名稱|說明|
|---|---|
|[TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) | 此類別會處理模型的建立、定型和發佈。 |
|[PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest)| 此類別會處理您的模型查詢，以進行物件偵測預測。|
|[預測](/javascript/api/@azure/cognitiveservices-customvision-prediction/prediction?view=azure-node-latest)| 此介面會定義單一影像上的單一預測。 其中包含物件識別碼和名稱的屬性，以及信賴分數。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 JavaScript 的自訂視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [建立新的自訂視覺專案](#create-a-new-custom-vision-project)
* [將標記新增至專案](#add-tags-to-the-project)
* [上傳和標記影像](#upload-and-tag-images)
* [為專案定型](#train-the-project)
* [發佈目前的反覆項目](#publish-the-current-iteration)
* [測試預測端點](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>驗證用戶端

使用端點和金鑰將用戶端物件具現化。 使用金鑰建立 **ApiKeyCredentials** 物件，並使用該物件與您的端點建立 [TrainingAPIClient](/javascript/api/@azure/cognitiveservices-customvision-training/trainingapiclient?view=azure-node-latest) 和 [PredictionAPIClient](/javascript/api/@azure/cognitiveservices-customvision-prediction/predictionapiclient?view=azure-node-latest) 物件。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="add-helper-function"></a>新增協助程式函式

新增下列函數，以協助進行多個非同步呼叫。 您稍後將會用到此資訊。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

啟動新的函式，以包含所有的自訂視覺函式呼叫。 新增下列程式碼，以建立新的自訂視覺服務專案。


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

啟動新的函式，以包含所有的自訂視覺函式呼叫。 新增下列程式碼，以建立新的自訂視覺服務專案。


[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_create)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

首先，下載此專案的範例影像。 將[範例影像資料夾](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images)的內容儲存到您的本機裝置。

若要將範例影像新增到專案，在標記建立之後插入下列程式碼。 此程式碼會上傳每個影像及其對應標記。 為物件偵測專案中的影像加上標記時，您必須使用標準化座標來識別每個加上標記的物件所屬的區域。 本教學課程中的區域是以硬式編碼內嵌於程式碼中。 這些區域會在標準化座標中指定週框方塊，且座標會以下列順序指定：左、上、寬度、高度。 您最多可以在單一批次中上傳 64 個影像。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_upload)]


> [!IMPORTANT]
> 您必須根據認知服務 Python SDK 範例存放庫的下載位置，來變更影像的路徑 (`sampleDataRoot`)。

> [!NOTE]
> 如果您沒有按住並拖曳公用程式可標示區域的座標，您可以使用 [Customvision.ai](https://www.customvision.ai/) 上的 Web UI。 此範例已提供座標。


## <a name="train-the-project"></a>為專案定型

此程式碼會建立預測模型的第一個反覆運算專案。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

此程式碼會將定型的反覆運算發佈至預測端點。 提供給已發佈反覆項目的名稱可用來傳送預測要求。 反覆項目要等到發佈後才可在預測端點中使用。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>測試預測端點

若要將影像傳送到預測端點並擷取預測，請在函式新增以下程式碼。 

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_test)]

然後，關閉您的自訂視覺函式並加以呼叫。

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js?name=snippet_function_close)]

## <a name="run-the-application"></a>執行應用程式

使用快速入門檔案上使用 `node` 命令執行應用程式。

```shell
node index.js
```

應用程式的輸出應會顯示在主控台中。 接著，您可以確認測試影像 (位於 **<sampleDataRoot>/Test/** ) 是否已正確加上標記，以及偵測的區域是否正確。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。


[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>後續步驟

現在，您已完成在程式碼中執行物件偵測程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。 下列指南會處理影像分類，但其原則類似於物件偵測。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* 此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/CustomVision/ObjectDetection/CustomVisionQuickstart.js)
* [SDK 參考文件 (定型)](/javascript/api/@azure/cognitiveservices-customvision-training/?view=azure-node-latest)
* [SDK 參考文件 (預測)](/javascript/api/@azure/cognitiveservices-customvision-prediction/?view=azure-node-latest)
