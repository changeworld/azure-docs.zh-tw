---
title: 套用映像轉換
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 [套用影像轉換] 模組，將影像轉換套用至影像目錄。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 7ff135911742c49c2c52ce30d1dca00bc89b3a56
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84450092"
---
# <a name="apply-image-transformation"></a>套用映像轉換 

本文說明如何使用 Azure Machine Learning 設計工具（預覽）中的 [套用映射轉換] 模組，根據先前指定的影像轉換來修改輸入影像目錄。  

您必須連接[Init Image 轉換](init-image-transformation.md)模組來指定轉換，然後才能將這類轉換套用至 [套用影像轉換] 模組的輸入影像目錄。

## <a name="how-to-use-apply-image-transformation"></a>如何使用套用影像轉換  

1. 將 [套用**映射轉換**] 模組新增至您的管線。 您可以在 [*電腦視覺/影像資料轉換*] 類別底下找到此模組。 

2. 將**Init 影像轉換**的輸出連接到 [套用**影像轉換**] 的左側輸入。

     > [!NOTE]
     > 此模組只接受[Init 映射轉換](init-image-transformation.md)模組所產生的影像轉換。 針對其他類型的轉換，請將其連接以套用[轉換](apply-transformation.md)，否則會擲回 ' InvalidTransformationDirectoryError '。


3. 連接您想要轉換的映射目錄。

4. 針對 [**模式]**，指定您使用輸入轉換的目的： [用於定型] 或 [用於推斷]。 

   如果您選取 [**進行定型**]，則會套用您在 Init 映射轉換中指定的所有轉換。

   如果您選取 [**進行推斷**]，則會在套用之前，先排除轉換（如隨機建立新的範例）。 這是因為在定型中，隨機建立新範例的轉換作業會用來進行資料增強，這應該在推斷中移除，因為必須修正推斷範例，才能精確預測和評估。

   > [!NOTE]
   > 要在**推斷**模式中排除的轉換包括：隨機調整大小的裁剪、隨機裁剪、隨機水準翻轉、隨機垂直翻轉、隨機旋轉、隨機仿射、隨機灰階、隨機透視圖、隨機抹除。

5. 若要將影像轉換套用至新的映射目錄，請提交管線。  

### <a name="module-parameters"></a>模組參數

| Name | 範圍 | 類型 | 預設                   | 描述                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| [模式] | 任意   | [模式] | （需要使用者指定） | 適用于您使用輸入轉換的目的。 您應該排除推斷中的「隨機」轉換作業，但將其保留在定型中 |

### <a name="expected-inputs"></a>預期的輸入  

| 名稱                       | 類型                    | Description                       |
| -------------------------- | ----------------------- | --------------------------------- |
| 輸入影像轉換 | TransformationDirectory | 輸入影像轉換        |
| 輸入影像目錄      | ImageDirectory          | 要轉換的影像目錄 |

### <a name="outputs"></a>輸出  

| 名稱                   | 類型           | Description            |
| ---------------------- | -------------- | ---------------------- |
| 輸出影像目錄 | ImageDirectory | 輸出影像目錄 |

## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 
