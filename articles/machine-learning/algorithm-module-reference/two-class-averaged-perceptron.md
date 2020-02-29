---
title: 決策樹系回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 中使用雙類別的平均認知模組，根據平均認知演算法建立機器學習模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920768"
---
# <a name="two-class-averaged-perceptron-module"></a>雙類別平均認知模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

使用此模組來建立以平均認知演算法為基礎的機器學習模型。  
  
此分類演算法是一種受監督的學習方法，而且需要包含標籤資料行的已*標記資料集*。 您可以藉由提供模型和加上標籤的資料集做為[定型模型](./train-model.md)的輸入，來定型模型。 定型的模型接著可用來預測新輸入範例的值。  

### <a name="about-averaged-perceptron-models"></a>關於平均認知模型

*平均認知方法*是早期和簡單的類神經網路版本。 在此方法中，輸入會根據線性函式分類成幾個可能的輸出，然後結合一組衍生自特徵向量的加權，因此名稱為 "認知"。

較簡單的認知模型適合以線性方式學習可區分的模式，而類神經網路 (尤其是深度神經網路) 可以模型化更複雜的級別界限。 不過，感知器較快速，因為它們會循序處理案例，適用於連續定型。

## <a name="how-to-configure-two-class-averaged-perceptron"></a>如何設定雙類別的平均認知

1.  將**兩個類別的平均認知**模組新增至您的管線。  

2.  藉由設定 [**建立定型模式]** 選項，指定您要如何訓練模型。  
  
    -   **單一參數**：如果您知道要如何設定模型，請提供一組特定值做為引數。

    -   **參數範圍**：如果您不確定最佳參數，而且想要執行參數清理，請選取此選項。 選取要逐一查看的值範圍，[微調模型超參數](tune-model-hyperparameters.md)會逐一查看所提供設定的所有可能組合，以判斷產生最佳結果的超參數。  
  
3.  針對 [**學習速率**]，指定*學習速率*的值。 學習速率值可控制每次測試和更正模型時，用於隨機梯度下降的步驟大小。
  
     藉由讓速率變小，您可以更頻繁地測試模型，但有可能會卡在本機高原中的風險。 如果階梯較大，聚合速度會較快，但會有超過實際最小值的風險。
  
4.  在 [**反覆運算數目上限**] 中，輸入您想要演算法檢查定型資料的次數。  
  
     早期停止常可帶來較佳的一般化。 增加反覆運算次數目可以改善配適性，但有可能過度配適。
  
5.  針對 [**亂數字種子**]，選擇性地輸入整數值做為種子。 如果您想要確保跨回合的管線重現性，建議使用種子。  
  
1.  連接訓練資料集和其中一個訓練課程模組：
  
    -   如果您將 [**建立訓練員模式]** 設定為 [**單一參數**]，請使用 [[定型模型](train-model.md)] 模組。




## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 