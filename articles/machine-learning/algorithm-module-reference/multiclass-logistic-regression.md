---
title: 多類邏輯回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用多類邏輯回歸模組創建可用於預測多個值的邏輯回歸模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456160"
---
# <a name="multiclass-logistic-regression-module"></a>多類邏輯回歸模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以創建可用於預測多個值的邏輯回歸模型。

使用邏輯回歸分類是一種受監督的學習方法，因此需要標記的資料集。 通過提供模型和標記的資料集作為輸入模組（如[訓練模型](./train-model.md)）來訓練模型。 然後，訓練模型可用於預測新輸入示例的值。

Azure 機器學習還提供[雙類邏輯回歸](./two-class-logistic-regression.md)模組，該模組適用于二進位或二分變數的分類。

## <a name="about-multiclass-logistic-regression"></a>關於多類邏輯回歸

邏輯回歸是統計中用於預測結果概率的已知方法，在分類任務中很受歡迎。 該演算法通過將資料擬合到物流函數來預測事件發生的概率。 

在多類邏輯回歸中，分類器可用於預測多個結果。

## <a name="configure-a-multiclass-logistic-regression"></a>配置多類邏輯回歸

1. 將**多類邏輯回歸**模組添加到管道中。

2. 通過設置 **"創建培訓師模式"** 選項，指定如何訓練模型。

    + **單一參數**：如果您知道如何配置模型，並提供一組特定的值作為參數，請使用此選項。

    + **參數範圍**：如果不確定最佳參數，並且想要運行參數掃描，請選擇此選項。 選擇要反覆運算的值範圍，[調諧模型 Hyper 參數](tune-model-hyperparameters.md)反覆運算您提供的所有可能組合設置，以確定生成最佳結果的超參數。  

3. **優化容差**，指定優化器收斂的閾值。 換句話說，如果反覆運算之間的改進少於臨界值，此演算法會停止，並傳回目前的模型。

4. **L1 正化權重****，L2 正化權重**：鍵入用於正則參數 L1 和 L2 的值。 非零值建議用於兩者。

    正規化是一種通過懲罰具有極端係數值的模型來防止過度學習的方法。 正規化的工作原理是將與係數值相關的懲罰添加到假設的錯誤中。 具有極端係數值的準確模型將受到更多懲罰，但具有更保守值的較不精確的模型將受到的懲罰更少。

     L1 與 L2 regularization 有不同的效果，並使用。 L1 可以套用到疏鬆的模型，使用高維度資料時，這是很有用。 相較之下，L2 regularization 是不是疏鬆的資料。  此演算法支援 L1 和 L2 正規化值的線性組合：`x = L1`即`y = L2`，`ax + by = c`如果 和 定義正規化術語的線性範圍。

     為邏輯回歸模型設計了不同的線性組合L1和L2術語，如[彈性淨正則](https://wikipedia.org/wiki/Elastic_net_regularization)化。

6. **亂數種子**：鍵入一個整數值，以用作演算法的種子，如果希望結果在運行中可重複。 否則，系統時鐘值將用作種子，這可能會在同一管道的運行中產生略有不同的結果。

8. 連接標記的資料集和其中一個訓練模組：

    + 如果將 **"創建訓練器模式"** 設置為 **"單一參數**"，請使用[訓練器模型](./train-model.md)模組。

9. 提交管道。



## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 