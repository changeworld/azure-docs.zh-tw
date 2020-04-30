---
title: 快速入門：建置分類器 - 自訂視覺服務
titleSuffix: Azure Cognitive Services
description: 在本快速入門中，您將了解如何使用自訂視覺網站建置影像分類模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 56bdaa324420bf274e7cda8ac1c6506e4bc9ad21
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81404062"
---
# <a name="quickstart-how-to-build-a-classifier-with-custom-vision"></a>快速入門：如何使用自訂視覺建置分類器

在本快速入門中，您將了解如何透過自訂視覺網站建置分類器。 在建置分類器模型後，您可以使用自訂視覺服務進行影像分類。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

- 一組用來定型分類的影像。 如需選擇影像的秘訣，請參閱下文。

## <a name="create-custom-vision-resources-in-the-azure-portal"></a>在 Azure 入口網站中建立自訂視覺資源

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>建立新專案

在網頁瀏覽器中，瀏覽到 [自訂視覺網頁](https://customvision.ai)，並選取 [登入]  。 請使用您用來登入 Azure 入口網站的相同帳戶進行登入。

![[登入] 頁面的影像](./media/browser-home.png)


1. 若要建立第一個專案，請選取 [新增專案]  。 [建立新專案]  對話方塊隨即出現。

    ![[新增專案] 對話方塊提供名稱、描述和領域的欄位。](./media/getting-started-build-a-classifier/new-project.png)

1. 輸入專案的名稱和描述。 然後選取一個 [資源群組]。 如果您登入的帳戶與 Azure 帳戶相關聯，[資源群組] 下拉式清單會顯示所有的 Azure 資源群組，包括自訂視覺服務資源。 

   > [!NOTE]
   > 如果沒有資源群組可用，請確認您已使用您用來登入 [Azure 入口網站](https://portal.azure.com/)的相同帳戶登入 [customvision.ai](https://customvision.ai)。 此外，請確認您所選取的自訂視覺入口網站「目錄」，與 Azure 入口網站中的目錄相同，也就是您自訂視覺資源的位置。 在這兩個網站中，您可以從畫面右上角的下拉式帳戶功能表選取您的目錄。 

1. 選取 [專案類型]  下的 [分類]  。 然後，在 [分類類型]  下，端視您的使用案例而定，選擇 [多標籤]  或 [多類別]  。 多標籤分類會將任意數目的標記套用至影像 (零或多個)，而多類別分類會將影像排序成單一類別 (您提交的每個影像將排序到最可能的標記)。 如果您想要，可以於稍後變更分類類型。

1. 然後，選取其中一個可用領域。 每個領域都會針對特定類型的影像來將分類器最佳化，如下表所述。 如果您想要，可以稍後變更網域。

    |網域|目的|
    |---|---|
    |__泛型__| 已針對廣泛的影像分類工作進行最佳化。 如果沒有其他適用的領域，或您不確定要選擇哪一個領域，請選取「泛型」領域。 |
    |__食物__|已針對菜餚相片進行最佳化，如同您在餐廳菜單上看見的一樣。 如果您想要將個別水果或蔬菜的相片分類，請使用「食物」領域。|
    |__地標__|已針對可辨識的地標 (包括自然和人工) 進行最佳化。 地標在相片中清楚顯示時，此領域的效果最佳。 即使地標前面的人稍微阻擋到該地標，此領域還是能夠運作。|
    |__零售__|已針對在購物目錄或購物網站上找到的影像進行最佳化。 如果您想在連衣裙、褲子和襯衫之間進行高精確度的分類，請使用此領域。|
    |__精簡領域__| 已針對行動裝置上的即時分類條件約束進行最佳化。 精簡領域所產生的模型可以匯出到本機執行。|

1. 最後，選取 [建立專案]  。

## <a name="choose-training-images"></a>選擇定型影像

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>上傳和標記影像

在本節中，您將上傳並手動標記影像，以便定型分類器。 

1. 若要新增影像，請按一下 [新增影像]  按鈕，然後選取 [瀏覽本機檔案]  。 選取 [開啟]  以移至標記。 標記選取將套用到您選取要上傳的整組影像，因此根據所需的標記以個別的群組上傳影像較容易。 上傳個別影像之後，您也可以變更影像的標記。

    ![[新增影像] 控制項會顯示於左上方，並在正下方顯示為按鈕。](./media/getting-started-build-a-classifier/add-images01.png)


1. 若要建立標記，請在 [我的標記]  欄位中輸入文字，然後按下 [Enter]。 如果標籤已經存在，會出現在下拉式功能表中。 在多標籤的專案中，您可以將多個標記新增到影像，但是，在多類別專案中，只能新增一個標記。 若要完成上傳影像，請使用 [上傳 [number] 個檔案]  按鈕。 

    ![標記和上傳頁面的影像](./media/getting-started-build-a-classifier/add-images03.png)

1. 一旦上傳影像之後，請選取 [完成]  。

    ![進度列會顯示所有工作都已完成。](./media/getting-started-build-a-classifier/add-images04.png)

若要上傳另一組影像，請返回本小節的最上端並重複進行步驟。

## <a name="train-the-classifier"></a>為分類器定型

若要為分類器定型，請選取 [定型]  按鈕。 分類器會使用所有目前的影像建立模型，以識別每個標籤的視覺品質。

![網頁標題工具列右上角的 [定型] 按鈕](./media/getting-started-build-a-classifier/train01.png)

此定型程序應該只需要幾分鐘的時間。 在此期間，[效能]  索引標籤會顯示定型程序的相關資訊。

![定型對話方塊出現在主要區段的瀏覽器視窗](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>評估分類器

定型完成之後，將預估並顯示模型的效能。 自訂視覺服務會利用名為 [K 次交叉驗證](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) \(英文\) 的程序，使用您送出來定型的影像計算精確度和回收。 精確度和回收是衡量分類器效率的兩個不同標準：

- **精確度**表示識別的正確分類所得到的分數。 例如，如果模型識別 100 張影像為狗，而實際上有 99 張為狗，則精確度為 99%。
- **回收**表示正確識別實際分類所得到的分數。 例如，如果實際上有 100 張影像為蘋果，而模型識別 80 張為蘋果，則回收為 80%。

![定型結果會顯示整體的精確度與記憶度，以及分類器中每個標記的精確度與記憶度。](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>機率閾值

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>管理定型反覆項目

每次您定型分類器時，都會使用更新的效能度量建立新_反覆項目_。 您可以在 [效能]  索引標籤的左窗格中檢視所有的反覆項目。您也會找到 [刪除]  按鈕，以用來刪除已經過時的反覆項目。 您刪除反覆項目時，會刪除唯一與它相關聯的任何影像。

若要了解如何以程式設計方式存取已定型的模型，請參閱[使用您的模型搭配預測 API](./use-prediction-api.md)。

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已了解如何使用自訂視覺網站建立並定型影像分類模型。 接下來，深入瞭解改進模型的反覆程序。

> [!div class="nextstepaction"]
> [測試和重新定型模型](test-your-model.md)

