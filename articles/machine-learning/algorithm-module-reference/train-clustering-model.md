---
title: 列車聚類模型：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用訓練群集模型模組來訓練群集模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477420"
---
# <a name="train-clustering-model"></a>定型叢集模型

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組訓練聚類模型。

該模組採用已使用[K-Means 聚類](k-means-clustering.md)模組配置的未訓練的群集模型，並使用標記或未標記的資料集對模型進行訓練。 該模組既創建可用於預測訓練的已訓練模型，又為訓練資料中的每個情況創建一組群集分配。

> [!NOTE]
> 無法使用[訓練模型](train-model.md)模組（用於訓練機器學習模型的通用模組）訓練群集模型。 這是因為[訓練模型](train-model.md)僅適用于受監督的學習演算法。 K-表示和其他聚類演算法允許無監督學習，這意味著演算法可以從未標記的資料中學習。  
  
## <a name="how-to-use-train-clustering-model"></a>如何使用列車群集模型  

1.  將**訓練群集模型**模組添加到設計器中的管道中。 您可以在 **"培訓"** 類別中找到**機器學習模組**下的模組。  
  
2. 添加[K-意味著聚類](k-means-clustering.md)模組或其他創建相容聚類模型的自訂模組，並設置聚類模型的參數。  
    
3.  將訓練資料集附加到**訓練群集模型**的右側輸入。
  
5.  在**列集中**，從資料集中選擇要用於生成群集的列。 請務必選擇具有良好功能的列：例如，避免使用 ID 或其他具有唯一值的列，或者使用具有相同值的列。

    如果標籤可用，則可以將其用作功能，也可以將其保留。  
  
6. 如果要將訓練資料與新群集標籤一起輸出，請選擇"**僅檢查追加或取消檢查結果**"選項。

    如果取消選擇此選項，則僅輸出群集分配。 

7. 提交管道，或按一下 **"訓練群集模型"** 模組並選擇"**運行選定**"。  
  
### <a name="results"></a>結果

培訓完成後：

+ 要保存已訓練的模型的快照，請在 **"訓練"模型**模組的右側面板中選擇 **"輸出**"選項卡。 選擇 **"註冊"資料集**圖示以將模型保存為可重用模組。

+ 要從模型生成分數，請使用[將資料分配給群集](assign-data-to-clusters.md)。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 