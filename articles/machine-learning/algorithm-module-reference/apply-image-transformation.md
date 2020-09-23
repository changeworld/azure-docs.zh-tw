---
title: 套用映像轉換
titleSuffix: Azure Machine Learning
description: 瞭解如何使用「套用影像轉換」模組，將影像轉換套用至映射目錄。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90898903"
---
# <a name="apply-image-transformation"></a>套用映像轉換 

本文描述如何使用 Azure Machine Learning 設計工具中的 [套用影像轉換] 模組，根據先前指定的影像轉換來修改輸入映射目錄。  

您必須連接 [初始映射轉換](init-image-transformation.md) 模組以指定轉換，然後將這類轉換套用至「套用影像轉換」模組的輸入映射目錄。

## <a name="how-to-use-apply-image-transformation"></a>如何使用套用影像轉換  

1. 將 [套用 **影像轉換** ] 模組新增至您的管線。 您可以在 *電腦視覺/Image 資料轉換* 類別下找到此模組。 

2. 將 **初始映射轉換** 的輸出連接到套用 **影像轉換**的左側輸入。

     > [!NOTE]
     > 只有 [初始映射轉換](init-image-transformation.md) 模組所產生的映射轉換可在此課程模組中接受。 針對其他類型的轉換，請將其連接以套用 [轉換](apply-transformation.md)，否則會擲回 ' InvalidTransformationDirectoryError '。


3. 連接您想要轉換的映射目錄。

4. 在 **模式**中，指定您使用輸入轉換的用途：「用於定型」或「用於推斷」。 

   如果您選取 [ **定型**]，將會套用您在 [初始映射轉換] 中指定的所有轉換。

   如果您選取 **進行推斷**，則在套用之前，會先排除轉換，例如隨機建立新的範例。 這是因為用來建立新範例的轉換作業會隨機（例如「隨機水準翻轉」）用於定型中的資料增強，因為必須修正推斷範例以進行精確的預測和評估。

   > [!NOTE]
   > 將在 **推斷** 模式中排除的轉換如下：隨機調整大小的裁剪、隨機裁剪、隨機水準翻轉、隨機垂直翻轉、隨機旋轉、隨機仿射、隨機灰階、隨機的觀點、隨機清除。

5. 若要將影像轉換套用至新的映射目錄，請提交管線。  

### <a name="module-parameters"></a>模組參數

| Name | 範圍 | 類型 | 預設                   | 描述                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| [模式] | 任意   | 模式 |  (需要使用者指定)  | 您使用輸入轉換的目的。 您應該在推斷中排除「隨機」轉換作業，但將它們保留在定型中 |

### <a name="expected-inputs"></a>預期的輸入  

| 名稱                       | 類型                    | 描述                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 輸入影像轉換 | TransformationDirectory | 輸入影像轉換        |
| 輸入影像目錄      | ImageDirectory          | 要轉換的映射目錄 |

### <a name="outputs"></a>輸出  

| 名稱                   | 類型           | 描述            |
| ---------------------- | -------------- | ---------------------- |
| 輸出映射目錄 | ImageDirectory | 輸出映射目錄 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
