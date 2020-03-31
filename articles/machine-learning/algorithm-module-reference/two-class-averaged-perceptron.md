---
title: 決策林回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure 機器學習中使用雙類平均感知器模組，以基於平均感知器演算法創建機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920768"
---
# <a name="two-class-averaged-perceptron-module"></a>雙類平均感知器模組

本文介紹 Azure 機器學習設計器（預覽）中的模組。

使用此模組可以創建基於平均感知器演算法的機器學習模型。  
  
此分類演算法是一種受監督的學習方法，需要*標記的資料集*，其中包括標籤列。 您可以通過提供模型和標記的資料集作為訓練模型的輸入來[訓練模型](./train-model.md)。 然後，訓練模型可用於預測新輸入示例的值。  

### <a name="about-averaged-perceptron-models"></a>關於平均感知器模型

*平均感知器方法是*神經網路的早期簡單版本。 在此方法中，輸入根據線性函數分類為多個可能的輸出，然後與從要素向量派生的一組權重組合在一起，因此名稱為"感知器"。

較簡單的認知模型適合以線性方式學習可區分的模式，而類神經網路 (尤其是深度神經網路) 可以模型化更複雜的級別界限。 不過，感知器較快速，因為它們會循序處理案例，適用於連續定型。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何配置雙類平均感知器

1.  將**雙類平均感知器**模組添加到管道中。  

2.  通過設置 **"創建培訓師模式"** 選項，指定如何訓練模型。  
  
    -   **單一參數**：如果您知道如何配置模型，請提供一組特定的值作為參數。

    -   **參數範圍**：如果不確定最佳參數，並且想要運行參數掃描，請選擇此選項。 選擇要反覆運算的值範圍，[調諧模型 Hyper 參數](tune-model-hyperparameters.md)反覆運算您提供的所有可能組合設置，以確定生成最佳結果的超參數。  
  
3.  對於**學習速率**，指定*學習速率*的值。 每次測試和校正模型時，學習速率值控制在隨機梯度下降中使用的步長的大小。
  
     通過降低速率，您可以更頻繁地測試模型，並存在卡在本地高原的風險。 如果階梯較大，聚合速度會較快，但會有超過實際最小值的風險。
  
4.  對於**最大反覆運算次數**，鍵入您希望演算法檢查訓練資料的次數。  
  
     早期停止常可帶來較佳的一般化。 增加反覆運算次數目可以改善配適性，但有可能過度配適。
  
5.  對於**亂數種子**，可以選擇鍵入一個整數值以用作種子。 如果要確保管道在運行中可重現性，建議使用種子。  
  
1.  連接訓練資料集和其中一個培訓模組：
  
    -   如果將 **"創建訓練器模式"** 設置為 **"單一參數**"，請使用[訓練器模型](train-model.md)模組。




## <a name="next-steps"></a>後續步驟

請參閱 Azure 機器學習[可用的模組集](module-reference.md)。 