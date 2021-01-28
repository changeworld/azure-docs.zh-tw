---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/25/2020
ms.openlocfilehash: 11e41437d21ef1e56930934b5b9850f74a269224
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948363"
---
開始使用適用於 Python 的自訂視覺用戶端程式庫。 請遵循下列步驟來安裝套件，並試用建立影像分類模型的程式碼範例。 您將建立專案、新增標籤、將專案定型，並使用專案的預測端點 URL 以程式設計方式加以測試。 請使用此範例作為自行建置影像辨識應用程式的範本。

> [!NOTE]
> 如果您想要在「不用」撰寫程式碼的情況下建立和定型分類模型，請參閱[以瀏覽器為基礎的指引](../../getting-started-build-a-classifier.md)。

使用適用於 Python 的自訂視覺用戶端程式庫可執行下列作業：

* 建立新的自訂視覺專案
* 將標記新增至專案
* 上傳和標記影像
* 為專案定型
* 發佈目前的反覆項目
* 測試預測端點

[參考文件](/python/api/overview/azure/cognitiveservices/customvision) | [程式庫來源程式碼](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-customvision/azure/cognitiveservices/vision/customvision) | [套件 (PyPI)](https://pypi.org/project/azure-cognitiveservices-vision-customvision/) | [範例](/samples/browse/?languages=python&products=azure&term=vision&terms=vision)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - [建立免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* 擁有 Azure 訂用帳戶之後，在 Azure 入口網站中<a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="建立自訂視覺資源"  target="_blank">建立自訂視覺資源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以建立定型和預測資源及取得您的金鑰和端點。 部署完成後，按一下 [移至資源] 按鈕。
    * 您需要來自所建立資源的金鑰和端點，以將應用程式連線至自訂視覺。 您稍後會在快速入門中將金鑰和端點貼到下列程式碼中。
    * 您可以使用免費定價層 (`F0`) 來試用服務，之後可升級至付費層以用於實際執行環境。

## <a name="setting-up"></a>設定

### <a name="install-the-client-library"></a>安裝用戶端程式庫

若要使用適用於 Python 的自訂視覺來撰寫影像分析應用程式，您將需要自訂視覺用戶端程式庫。 安裝 Python 之後，請在 PowerShell 或主控台視窗中執行下列命令：

```powershell
pip install azure-cognitiveservices-vision-customvision
```

### <a name="create-a-new-python-application"></a>建立新的 Python 應用程式

建立新的 Python 檔案，並匯入下列程式庫。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_imports)]

> [!TIP]
> 想要立刻檢視整個快速入門程式碼檔案嗎？ 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py) 上找到該檔案，其中包含本快速入門中的程式碼範例。

為資源的 Azure 端點和訂用帳戶金鑰建立變數。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_creds)]

> [!IMPORTANT]
> 前往 Azure 入口網站。 如果您在 [必要條件] 區段中建立的自訂視覺資源成功部署，請按一下 [後續步驟] 底下的 [前往資源] 按鈕。 您可以在 [資源管理] 底下的 [金鑰和端點] 頁面中找到金鑰和端點。 您必須同時取得訓練和預測金鑰，以及訓練資源的端點。
>
> 您可以在資源的 **概觀** 索引標籤上找到預測資源識別碼值，該識別碼列為 **訂用帳戶識別碼**。
>
> 切記，完成時從程式碼中移除金鑰，且切勿公開發佈金鑰。 在生產環境中，請考慮使用安全的方式來儲存及存取您的認證。 如需詳細資訊，請參閱認知服務[安全性](../../../cognitive-services-security.md)一文。

## <a name="object-model"></a>物件模型

|名稱|描述|
|---|---|
|[CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | 此類別會處理模型的建立、定型和發佈。 |
|[CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| 此類別會處理您的模型查詢，以進行影像分類預測。|
|[ImagePrediction](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| 此類別會定義單一影像上的單一物件預測。 其中包含物件識別碼和名稱的屬性、物件的周框方塊位置，以及信賴分數。|

## <a name="code-examples"></a>程式碼範例

這些程式碼片段說明如何使用適用於 Python 的自訂視覺用戶端程式庫來執行下列工作：

* [驗證用戶端](#authenticate-the-client)
* [建立新的自訂視覺專案](#create-a-new-custom-vision-project)
* [將標記新增至專案](#add-tags-to-the-project)
* [上傳和標記影像](#upload-and-tag-images)
* [為專案定型](#train-the-project)
* [發佈目前的反覆項目](#publish-the-current-iteration)
* [測試預測端點](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>驗證用戶端

使用您的端點和金鑰將訓練具現化並預測用戶端。 使用您的金鑰建立 **ApiKeyServiceClientCredentials** 物件，並與您的端點搭配使用，以建立 [CustomVisionTrainingClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) 和 [CustomVisionPredictionClient](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient) 物件。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>建立新的自訂視覺專案

在指令碼中新增下列程式碼，以建立新的自訂視覺服務專案。 

當您建立專案時，請參閱 [create_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#create-project-name--description-none--domain-id-none--classification-type-none--target-export-platforms-none--custom-headers-none--raw-false----operation-config-) 方法來指定其他選項 (如[建置分類器](../../getting-started-build-a-classifier.md) Web 入口網站指南中所述)。  

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_create)]

## <a name="add-tags-to-the-project"></a>將標記新增至專案

若要在專案中新增分類標記，請新增以下程式碼：

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_tags)]


## <a name="upload-and-tag-images"></a>上傳和標記影像

首先，下載此專案的範例影像。 將[範例影像資料夾](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images)的內容儲存到您的本機裝置。

> [!NOTE]
> Trove 是 Microsoft Garage 專案，可讓您收集和購買影像集以供訓練使用。 收集影像之後，您可以下載影像，然後以一般方式將其匯入至自訂視覺專案。 如需深入了解，請造訪 [Trove 頁面](https://www.microsoft.com/en-us/ai/trove?activetab=pivot1:primaryr3)。

若要將範例影像新增到專案，在標記建立之後插入下列程式碼。 此程式碼會上傳每個影像及其對應標記。 您最多可以在單一批次中上傳 64 個影像。

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_upload)]

> [!NOTE]
> 您必須根據認知服務 Python SDK 範例存放庫的下載位置，來變更影像的路徑。

## <a name="train-the-project"></a>為專案定型

此程式碼會建立預測模型的第一個反覆運算專案。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_train)]

> [!TIP]
> 使用選取的標記進行訓練
>
> 您可以選擇只在已套用的標記子集上進行訓練。 您可以在尚未套用足夠的特定標記，但其他套用的標記已足夠時執行此操作。 在 **[train_project](/python/api/azure-cognitiveservices-vision-customvision/azure.cognitiveservices.vision.customvision.training.operations.customvisiontrainingclientoperationsmixin#train-project-project-id--training-type-none--reserved-budget-in-hours-0--force-train-false--notification-email-address-none--selected-tags-none--custom-headers-none--raw-false----operation-config-&preserve-view=true)** 呼叫中，將選擇性參數 *selected_tags* 設定為您要使用的標記識別碼字串清單。 此模型將訓練為僅辨識該清單上的標記。

## <a name="publish-the-current-iteration"></a>發佈目前的反覆項目

反覆項目要等到發佈後才可在預測端點中使用。 下列程式碼會讓模型的目前反覆運算可供查詢。 

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>測試預測端點

若要將影像傳送到預測端點並擷取預測，在檔案結尾處新增以下程式碼：

[!code-python[](~/cognitive-services-quickstart-code/python/CustomVision/ImageClassification/CustomVisionQuickstart.py?name=snippet_test)]

## <a name="run-the-application"></a>執行應用程式

執行 *CustomVisionQuickstart.py*。

```powershell
python CustomVisionQuickstart.py
```

應用程式的輸出應該會類似下列文字：

```console
Creating project...
Adding images...
Training...
Training status: Training
Training status: Completed
Done!
        Hemlock: 93.53%
        Japanese Cherry: 0.01%
```

接著，您可以確認測試影像 (位於 **<base_image_location>/images/Test/** ) 的標記是否適當。 您也可以返回[自訂視覺網站](https://customvision.ai)，然後查看新建立專案的目前狀態。

## <a name="clean-up-resources"></a>清除資源

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>後續步驟

現在您已經知道如何在程式碼中完成影像分類程序的每個步驟。 此範例會執行單一的訓練反覆項目，但您通常必須對模型進行多次訓練和測試，以便提升其精確度。

> [!div class="nextstepaction"]
> [測試和重新定型模型](../../test-your-model.md)

* 什麼是自訂視覺服務？
* 此範例的原始程式碼位於 [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/CustomVision/ImageClassification/CustomVisionQuickstart.py)
* [SDK 參考文件](/python/api/overview/azure/cognitiveservices/customvision)
