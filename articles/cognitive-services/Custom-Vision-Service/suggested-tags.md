---
title: 使用智慧型標籤人員加快映射標籤
titleSuffix: Azure Cognitive Services
description: 在本指南中，您將瞭解如何使用智慧型標籤人員來為影像產生建議的標籤。 這可讓您在定型自訂視覺模型時，更快速地標記大量影像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "73647759"
---
# <a name="label-images-faster-with-smart-labeler"></a>使用智慧型標籤人員加快映射標籤

在本指南中，您將瞭解如何使用智慧型標籤人員來為影像產生建議的標籤。 這可讓您在定型自訂視覺模型時，更快速地標記大量影像。

當您標記自訂視覺模型的影像時，服務會使用模型的最新定型反覆運算來預測未標記影像的標籤。 然後，它會根據選取的信賴閾值和預測不確定性，將這些預測顯示為建議的標記。 然後，您可以確認或變更建議，以加速手動標記影像以進行定型的流程。

## <a name="when-to-use-smart-labeler"></a>使用智慧型標籤人員的時機

請記住下列限制：

* 您應該只針對內容已定型一次的影像要求建議的標記。 請勿針對您剛開始訓練的新標記取得建議。

> [!IMPORTANT]
> Smart 標籤人員功能會使用與一般預測相同的 [定價模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) 。 當您第一次觸發一組影像的建議標籤時，將會向您收取與預測通話相同的費用。 之後，服務會將所選映射的結果儲存在資料庫中30天，您可以在該期間內免費存取。 30天之後，如果您再次要求其建議的標記，則會向您收費。

## <a name="smart-labeler-workflow"></a>智慧型標籤人員工作流程

下列步驟示範如何使用智慧型標籤人員：

1. 將您所有的訓練影像上傳至自訂視覺專案。
1. 標記部分資料集，並為每個標籤選擇相同數量的影像。
    > [!TIP]
    > 請務必在稍後使用您想要的所有標記。
1. 開始訓練流程。
1. 定型完成時，請流覽至未標記 **的視圖，** 然後選取左窗格中的 [ **取得建議** 的標籤] 按鈕。
    > [!div class="mx-imgBorder"]
    > ![[未標記的影像] 索引標籤下會顯示 [建議的標籤] 按鈕。](./media/suggested-tags/suggested-tags-button.png)
1. 在出現的快顯視窗中，設定您想要建議的影像數目。 您應該只針對部分未標記的影像取得初始標記建議。 當您逐一查看此程式時，您將會得到更好的標記建議。
1. 確認建議的標籤，修正不正確的標記。
    > [!TIP]
    > 具有建議標記的影像會依預測不確定性排序 (較低的值表示較高的信賴度) 。 您可以使用 [ **依不確定性排序** ] 選項來變更排序次序。 如果您將順序設定為 [ **高] 到 [低**]，您可以先更正高不確定性的預測，然後快速確認不確定性的預測。
    * 在影像分類專案中，您可以批次選取並確認標記。 依指定的建議標記篩選視圖，取消選取標記不正確的影像，然後在批次中確認其餘部分。
        > [!div class="mx-imgBorder"]
        > ![建議的標記會以批次模式顯示，且具有篩選器。](./media/suggested-tags/ic-batch-mode.png)

        您也可以選取資源庫中的影像，在個別影像模式中使用建議的標記。

        ![建議的標記會以個別的內部的影像模式顯示。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在物件偵測專案中，不支援批次確認，但您仍然可以依建議的標記進行篩選和排序，以取得更有條理的標記體驗。 未標記影像的縮圖會顯示周框方塊的重迭，指出建議標記的位置。 如果您未選取建議的標籤篩選，則會顯示所有未標記的影像，而不會覆蓋周框方塊。
        > [!div class="mx-imgBorder"]
        > ![建議的標記會以批次模式顯示，並以篩選顯示。](./media/suggested-tags/od-batch-mode.png)

        若要確認物件偵測標記，您必須將它們套用至資源庫中的每個個別映射。

        ![建議的標記會顯示在 OD 的個別影像模式中。](./media/suggested-tags/od-individual-image-mode.png)
1. 再次開始訓練流程。
1. 重複上述步驟，直到您滿意建議品質為止。

## <a name="next-steps"></a>後續步驟

遵循快速入門，開始建立及訓練自訂視覺專案。

* [建置分類器](getting-started-build-a-classifier.md)
* [建置物件偵測器](get-started-build-detector.md)