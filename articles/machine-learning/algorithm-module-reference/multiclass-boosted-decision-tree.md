---
title: 多類提升決策樹：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用多類提升決策樹模組使用標記的資料創建分類器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 7d51e3007b7773e28d846f8d30178426f5668cfb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920054"
---
# <a name="multiclass-boosted-decision-tree"></a>多元促進式決策樹

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以創建基於增強決策樹演算法的機器學習模型。

提升決策樹是一種集合學習方法，其中第二樹糾正第一個樹的錯誤，第三個樹糾正第一和第二樹的錯誤，依此類推。 預測基於樹木的整合。

## <a name="how-to-configure"></a>如何設定 

此模組創建未經訓練的分類模型。 由於分類是一種受監督的學習方法，因此您需要一個*標記的資料集*，該資料集包含一個標籤列，其中包含所有行的值。

您可以使用[訓練模型](././train-model.md)來訓練這種類型的模型。 

1.  將**多類提升決策樹**模組添加到管道中。

1.  通過設置 **"創建培訓師模式"** 選項，指定您希望如何訓練模型。

    + **單一參數**：如果您知道如何配置模型，則可以提供一組特定的值作為參數。
    
    + **參數範圍**：如果不確定最佳參數，並且想要運行參數掃描，請選擇此選項。 選擇要反覆運算的值範圍，[調諧模型 Hyper 參數](tune-model-hyperparameters.md)反覆運算您提供的所有可能組合設置，以確定生成最佳結果的超參數。  

1. **每棵樹的最大葉數**限制可在任何樹中創建的終端節點（葉）的最大數量。
    
        By increasing this value, you potentially increase the size of the tree and achieve higher precision, at the risk of overfitting and longer training time.
  
1. **每個葉節點的最小樣本數**表示在樹中創建任何終端節點（葉）所需的案例數。  

         By increasing this value, you increase the threshold for creating new rules. For example, with the default value of 1, even a single case can cause a new rule to be created. If you increase the value to 5, the training data would have to contain at least five cases that meet the same conditions.

1. **學習速率**定義學習時的步驟大小。 輸入介於 0 和 1 之間的數位。

         The learning rate determines how fast or slow the learner converges on an optimal solution. If the step size is too large, you might overshoot the optimal solution. If the step size is too small, training takes longer to converge on the best solution.

1. **構造的樹數**表示要在集合中創建的決策樹的總數。 藉由建立多個決策樹，您或許能夠有較佳的涵蓋範圍，但是定型時間會拉長。

1. **亂數種子**可以選擇設置非負整數以用作隨機種子值。 指定種子可確保具有相同資料和參數的運行之間的可重現性。  

         The random seed is set by default to 42. Successive runs using different random seeds can have different results.

> [!Note]
> 如果將 **"創建訓練器模式"** 設置為 **"單個參數**"，請連接標記的資料集和[訓練模型](./train-model.md)模組。

## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 
