---
title: 套用轉換：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用 [套用轉換] 模組，根據先前計算的轉換來修改輸入資料集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137572"
---
# <a name="apply-transformation-module"></a>套用轉換模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來根據先前計算的轉換來修改輸入資料集。  
  
例如，如果您使用 z 分數以正規化**資料**模組來將定型資料正規化，您會想要使用在評分階段進行定型所計算的 z 分數值。 在 Azure Machine Learning 中，您可以將正規化方法儲存為轉換，然後使用 [套用**轉換**] 將 z 分數套用至輸入資料，然後再進行評分。
  
Azure Machine Learning 提供建立和套用許多不同類型之自訂轉換的支援。 例如，您可能會想要儲存並重複使用轉換來執行下列動作：  
  
- 使用**清除遺漏的資料**來移除或取代遺漏值
- 標準化資料，使用正規化**資料**
  

## <a name="how-to-use-apply-transformation"></a>如何使用套用轉換  
  
1. 將 [套用**轉換**] 模組新增至您的管線。 您可以在**模型評分 & 評估**分類中找到此模組。 
  
2. 找出要當做輸入使用的現有轉換。 先前儲存的轉換可以在左側模組樹狀目錄中的 [**資料集**類別] 下的 [**我的資料集**] 群組中找到。  
  
   
  
3. 連接您想要轉換的資料集。 資料集應具有與第一次設計轉換的資料集完全相同的架構（資料行數目、資料行名稱、資料類型）。  
  
4. 不需要設定其他參數，因為在定義轉換時，會完成所有自訂。  
  
5. 若要將轉換套用至新的資料集，請執行管線。  

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 