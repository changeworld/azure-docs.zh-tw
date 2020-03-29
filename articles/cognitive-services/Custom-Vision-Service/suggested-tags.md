---
title: 使用智慧貼標機更快地標注圖像
titleSuffix: Azure Cognitive Services
description: 在本指南中，您將學習如何使用智慧標注器為圖像生成建議的標籤。 這樣，在訓練自訂視覺模型時，您可以更快地標記大量圖像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "73647759"
---
# <a name="label-images-faster-with-smart-labeler"></a>使用智慧貼標機更快地標注圖像

在本指南中，您將學習如何使用智慧標注器為圖像生成建議的標籤。 這樣，在訓練自訂視覺模型時，您可以更快地標記大量圖像。

為自訂視覺模型標記圖像時，服務使用模型的最新訓練反覆運算來預測未標記圖像的標籤。 然後，根據所選的置信度閾值和預測不確定性將這些預測作為建議標記顯示。 然後，您可以確認或更改建議，從而加快手動標記圖像以進行培訓的過程。

## <a name="when-to-use-smart-labeler"></a>何時使用智慧貼標機

請記住以下限制：

* 對於內容已訓練過一次的圖像，應僅請求建議的標記。 不要收到您剛剛開始訓練的新標籤的建議。

> [!IMPORTANT]
> 智慧貼標器功能使用與常規預測相同的[定價模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。 首次為一組圖像觸發建議標記時，將向您收取與預測調用相同的費用。 之後，該服務將所選圖像的結果存儲在資料庫中 30 天，您可以在該時間段內隨時免費訪問它們。 30 天后，如果您再次請求他們建議的標籤，將向您收費。

## <a name="smart-labeler-workflow"></a>智慧貼標器工作流程

以下步驟演示如何使用智慧貼標機：

1. 將所有培訓圖像上傳到自訂視覺專案。
1. 標記資料集的一部分，為每個標記選擇相同數量的圖像。
    > [!TIP]
    > 請確保使用以後需要建議的所有標記。
1. 開始培訓過程。
1. 訓練完成後，導航到 **"未標記**"視圖，然後選擇左側窗格上的"**獲取建議標記**"按鈕。
    > [!div class="mx-imgBorder"]
    > ![建議的標記按鈕顯示在未標記的圖像選項卡下。](./media/suggested-tags/suggested-tags-button.png)
1. 在顯示的快顯視窗中，設置需要建議的圖像數。 您應該只獲取未標記圖像的一部分的初始標記建議。 在反覆運算此過程時，您將獲得更好的標記建議。
1. 確認建議的標記，修復任何不正確標記。
    > [!TIP]
    > 具有建議標記的圖像按其預測不確定性進行排序（值越低表示置信度越高）。 您可以使用"**按不確定性排序"** 選項更改排序次序。 如果將順序設置為**高到低**，則可以首先更正高不確定性預測，然後快速確認低不確定性預測。
    * 在圖像分類專案中，可以選擇和確認標記。 按給定建議標記篩選視圖，取消選擇標記錯誤的圖像，然後確認批次處理中的其餘圖像。
        > [!div class="mx-imgBorder"]
        > ![建議的標記以批次處理模式顯示，用於帶有篩選器的 IC。](./media/suggested-tags/ic-batch-mode.png)

        還可以通過從庫中選擇圖像，在單個圖像模式下使用建議標記。

        ![建議的標記以 IC 的單個圖像模式顯示。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在物件檢測專案中，不支援批次處理確認，但您仍可以通過建議的標記篩選和排序，以便獲得更有條理的標籤體驗。 未標記圖像的縮略圖將顯示一個邊界框的疊加，指示建議標記的位置。 如果未選擇建議的標記篩選器，則所有未標記的圖像都將顯示，而不會疊加邊界框。
        > [!div class="mx-imgBorder"]
        > ![建議的標記以批次處理模式顯示，用於帶篩選器的 OD。](./media/suggested-tags/od-batch-mode.png)

        要確認物件檢測標記，您需要將它們應用於庫中的每個單個圖像。

        ![建議的標記以 OD 的單個圖像模式顯示。](./media/suggested-tags/od-individual-image-mode.png)
1. 重新開始培訓過程。
1. 重複上述步驟，直到您對建議品質感到滿意。

## <a name="next-steps"></a>後續步驟

遵循快速入門開始創建和培訓自訂願景專案。

* [建置分類器](getting-started-build-a-classifier.md)
* [建置物件偵測器](get-started-build-detector.md)