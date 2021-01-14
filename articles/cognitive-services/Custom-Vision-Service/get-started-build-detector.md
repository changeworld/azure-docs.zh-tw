---
title: 快速入門：使用自訂視覺網站建置物件偵測器
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用自訂視覺網站建立、定型及測試物件偵測器模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 影像辨識, 影像辨識應用程式, 自訂視覺
ms.openlocfilehash: b27864fc1fd1f94f99fccacd90a66120e6d044c4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132574"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>快速入門：使用自訂視覺網站建置物件偵測器

在本快速入門中，您將了解如何使用自訂視覺網站建立物件偵測器模型。 一旦建置了模型，您就可以使用新的影像進行測試，並最終將其整合到您自己的影像辨識應用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>Prerequisites

- 一組用來定型偵測器模型的影像。 您可以使用 GitHub 上的[範例影像](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)集合。 或者，您可以使用下列秘訣來選擇自己的影像。

## <a name="create-custom-vision-resources"></a>建立自訂視覺資源

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>建立新專案

在網頁瀏覽器中，瀏覽到 [自訂視覺網頁](https://customvision.ai)，並選取 [登入]  。 請使用您用來登入 Azure 入口網站的相同帳戶進行登入。

![[登入] 頁面的影像](./media/browser-home.png)


1. 若要建立第一個專案，請選取 [新增專案]  。 [建立新專案]  對話方塊隨即出現。

    ![[新增專案] 對話方塊提供名稱、描述和領域的欄位。](./media/get-started-build-detector/new-project.png)

1. 輸入專案的名稱和描述。 然後選取一個 [資源群組]。 如果您登入的帳戶與 Azure 帳戶相關聯，[資源群組] 下拉式清單會顯示所有的 Azure 資源群組，包括自訂視覺服務資源。 

   > [!NOTE]
   > 如果沒有資源群組可用，請確認您已使用您用來登入 [Azure 入口網站](https://portal.azure.com/)的相同帳戶登入 [customvision.ai](https://customvision.ai)。 此外，請確認您在自訂視覺網站中選取的「目錄」，與 Azure 入口網站中的目錄相同，也就是您自訂視覺資源的位置。 在這兩個網站中，您可以從畫面右上角的下拉式帳戶功能表選取您的目錄。 

1. 選取 [專案類型]  之下的 [物件偵測]  。

1. 然後，選取其中一個可用領域。 每個領域都會針對特定類型的影像來將偵測器最佳化，如下表所述。 如果您想要，可以稍後變更網域。

    |網域|目的|
    |---|---|
    |__一般__| 已針對廣泛的物件偵測工作進行最佳化。 如果沒有其他適用的領域，或您不確定要選擇哪一個領域，請選取「泛型」領域。 |
    |__標誌__|已針對尋找影像中的品牌標誌進行最佳化。|
    |__貨架上的產品__|已針對在貨架上偵測和分類產品進行最佳化。|
    |__精簡領域__| 已針對行動裝置上的即時物件偵測條件約束進行最佳化。 精簡領域所產生的模型可以匯出到本機執行。|

1. 最後，選取 [建立專案]  。

## <a name="choose-training-images"></a>選擇定型影像

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

在本節中，您將上傳並手動標記影像，以便定型偵測器。 

1. 若要新增影像，請按一下 [新增影像] 按鈕，然後選取 [瀏覽本機檔案]。 選取 [開啟] 以上傳影像。

    ![[新增影像] 控制項會顯示於左上方，並在正下方顯示為按鈕。](./media/get-started-build-detector/add-images.png)

1. 您會在 UI 的 [未標記] 區段中看到您上傳的影像。 下一步是手動標記您希望偵測器學會辨識的物件。 按一下第一個影像以開啟標記對話方塊視窗。 

    ![在 [未標記] 區段中上傳的影像](./media/get-started-build-detector/images-untagged.png)

1. 按一下並拖曳影像中物件周圍的矩形。 然後，使用 **+** 按鈕輸入新的標記名稱，或從下拉式清單中選取現有的標記。 務必標記所要偵測物件的每個執行個體，因為偵測器會使用未標記的背景區域作為負面定型範例。 當您完成標記時，按一下右側的箭號以儲存標記並移至下一個影像。

    ![以矩形選取範圍標記物件](./media/get-started-build-detector/image-tagging.png)

若要上傳另一組影像，請返回本小節的最上端並重複進行步驟。

## <a name="train-the-detector"></a>定型偵測器

若要將偵測器模型定型，請選取 [定型] 按鈕。 偵測器會使用所有目前的影像及其標記來建立模型，以識別每個標記的物件。

![網頁標題工具列右上角的 [定型] 按鈕](./media/getting-started-build-a-classifier/train01.png)

此定型程序應該只需要幾分鐘的時間。 在此期間，[效能]  索引標籤會顯示定型程序的相關資訊。

![定型對話方塊出現在主要區段的瀏覽器視窗](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>評估偵測器

定型完成之後，就會計算並顯示模型的效能。 自訂視覺服務會使用您為了定型提交的影像，計算精確度、召回率和平均精度均值。 精確度和召回率是衡量偵測器效率的兩個不同標準：

- **精確度** 表示識別的正確分類所得到的分數。 例如，如果模型識別 100 張影像為狗，而實際上有 99 張為狗，則精確度為 99%。
- **回收** 表示正確識別實際分類所得到的分數。 例如，如果實際上有 100 張影像為蘋果，而模型識別 80 張為蘋果，則回收為 80%。
- **平均精度均值** 是平均精確度 (AP) 的平均值。 AP 是精確度/召回曲線下的區域 (針對每個預測所繪製的精確度與召回之間的關係)。

![定型結果會顯示整體精確度和召回率以及平均精度均值。](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>機率閾值

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>重疊閾值

**重疊閾值** 滑杆會決定物件預測要多正確，才能在訓練中視為「正確」。 其會在預測的物件周框方塊和實際使用者輸入的周框方塊之間設定允許的最小重疊。 如果周框方塊不會與此程度重疊，預測就不會被視為正確。

## <a name="manage-training-iterations"></a>管理定型反覆項目

每次您定型偵測器時，都會使用更新的效能度量建立新「反覆項目」。 您可以在 [效能] 索引標籤的左窗格中檢視所有的反覆項目。在左窗格中，您也可以找到 [刪除] 按鈕，可用來刪除已經過時的反覆項目。 您刪除反覆項目時，會刪除唯一與它相關聯的任何影像。

若要了解如何以程式設計方式存取已定型的模型，請參閱[使用您的模型搭配預測 API](./use-prediction-api.md)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用自訂視覺網站，建立並定型物件偵測器模型。 接下來，深入瞭解改進模型的反覆程序。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)

* [什麼是自訂視覺服務？](./overview.md)
