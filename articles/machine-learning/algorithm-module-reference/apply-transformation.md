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
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395260"
---
# <a name="apply-transformation-module"></a>套用轉換模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來根據先前計算的轉換來修改輸入資料集。

例如，如果您使用 z 分數以正規化**資料**模組來將定型資料正規化，您會想要使用在評分階段進行定型所計算的 z 分數值。 在 Azure Machine Learning 中，您可以將正規化方法儲存為轉換，然後使用 [套用**轉換**] 將 z 分數套用至輸入資料，然後再進行評分。

## <a name="how-to-save-transformations"></a>如何儲存轉換

此設計工具可讓您將資料轉換儲存為**dataset** ，讓您可以在其他管線中使用它們。

1. 選取已成功執行的資料轉換模組。

1. 選取 [**輸出 + 記錄**] 索引標籤。

1. 選取**儲存圖示**以儲存**結果轉換**。

## <a name="how-to-use-apply-transformation"></a>如何使用套用轉換  
  
1. 將 [套用**轉換**] 模組新增至您的管線。 您可以在模組選擇區的**模型評分 & 評估**一節中找到此模組。 
  
1. 在模組選擇區中，尋找您要在**資料集** > [**我的資料集**] 下使用的已儲存轉換。

1. 將已儲存轉換的輸出連接到 [套用**轉換**] 模組的左側輸入埠。

    資料集應具有與第一次設計轉換的資料集完全相同的架構（資料行數目、資料行名稱、資料類型）。  
  
1. 將所需模組的資料集輸出連接到 [套用**轉換**] 模組的右側輸入埠。
  
1. 若要將轉換套用至新的資料集，請執行管線。  

## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 