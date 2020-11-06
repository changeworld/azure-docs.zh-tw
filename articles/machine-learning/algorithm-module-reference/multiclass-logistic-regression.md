---
title: 多元羅吉斯回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何在 Azure Machine Learning 設計工具中使用多元羅吉斯回歸模組來預測多個值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/22/2020
ms.openlocfilehash: e2bbc28735bcbfd952c4941453956acd0568ea67
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420728"
---
# <a name="multiclass-logistic-regression-module"></a>多元羅吉斯回歸模組

本文描述 Azure Machine Learning 設計工具中的模組。

您可以使用此模組來建立羅吉斯回歸模型，以用來預測多個值。

使用羅吉斯回歸的分類是受監督的學習方法，因此需要已加上標籤的資料集。 您可以藉由提供模型和加上標籤的資料集做為模組的輸入（例如 [定型模型](./train-model.md)）來定型模型。 然後，您可以使用定型的模型來預測新輸入範例的值。

Azure Machine Learning 也提供 [兩個類別的羅吉斯回歸](./two-class-logistic-regression.md) 模組，其適用于二進位或二分變數的分類。

## <a name="about-multiclass-logistic-regression"></a>關於多元羅吉斯回歸

羅吉斯回歸是在統計資料中已知的方法，可用來預測結果的機率，並受限於分類工作。 此演算法會將資料放在羅吉斯函式中，以預測事件發生的機率。 

在多元羅吉斯回歸中，分類器可以用來預測多個結果。

## <a name="configure-a-multiclass-logistic-regression"></a>設定多元羅吉斯回歸

1. 將 **多元羅吉斯回歸** 模組新增至管線。

2. 藉由設定 [ **建立定型模式]** 選項，指定您要如何定型模型。

    + **單一參數** ：如果您知道要如何設定模型，請使用此選項，並提供一組特定值做為引數。

    + **參數範圍** ：如果您不確定最佳參數，且想要執行參數清除，請選取此選項。 選取要逐一查看的值範圍，而 [ [微調模型超參數](tune-model-hyperparameters.md) ] 會逐一查看您提供的所有可能設定組合，以判斷產生最佳結果的超參數。  

3. **優化容錯** ，指定優化工具聚合的臨界值。 換句話說，如果反覆運算之間的改進少於臨界值，此演算法會停止，並傳回目前的模型。

4. **L1 正規化權數** ， **l2 正規化權數** ：輸入用於正規化參數 L1 和 L2 的值。 非零值建議用於兩者。

    正規化是一種方法，可透過具有極端係數值的 penalizing 模型來防止過度學習。 正規化的運作方式是將與係數值相關聯的損失新增至假設的錯誤。 具有極端係數值的精確模型會懲罰更多，但具有更保守值的較不精確模型會懲罰較少。

     L1 與 L2 regularization 有不同的效果，並使用。 L1 可以套用到疏鬆的模型，使用高維度資料時，這是很有用。 相較之下，L2 regularization 是不是疏鬆的資料。  此演算法支援 L1 和 L2 正規化值的線性組合：也就是說，如果 `x = L1` 和，則 `y = L2` 會 `ax + by = c` 定義正規化詞彙的線性範圍。

     已針對羅吉斯回歸模型（例如 [彈性網路正規化](https://wikipedia.org/wiki/Elastic_net_regularization)）設計了 L1 和 L2 詞彙的不同線性組合。

6. **亂數種子** ：如果您想要讓結果可重複執行，請輸入整數值做為演算法的種子。 否則，系統時鐘值會當做種子使用，這可能會在相同管線的執行中產生稍微不同的結果。

8. 連接已加上標籤的資料集，並將模型定型：

    + 如果您將 [ **建立定型模式** ] 設定為 [ **單一參數** ]，請連接已標記的資料集和「 [定型模型](train-model.md) 」模組。  
  
    + 如果您將 [ **建立定型模式** ] 設定為 [ **參數範圍** ]，請連接已加上標籤的資料集，並使用 [微調模型超參數](tune-model-hyperparameters.md)來定型模型。  
  
    > [!NOTE]
    > 
    > 如果您將參數範圍傳遞給 [定型模型](train-model.md)，它只會使用單一參數清單中的預設值。  
    > 
    > 如果您將一組參數值傳遞至 [微調模型超參數](tune-model-hyperparameters.md) 模組，當它預期每個參數的設定範圍時，會忽略這些值，並使用學習模組的預設值。  
    > 
    > 如果您選取 [ **參數範圍** ] 選項，並輸入任何參數的單一值，則整個清除期間都會使用您所指定的單一值，即使其他參數會在某個範圍的值之間變更。

9. 提交管線。



## <a name="next-steps"></a>後續步驟

請參閱 Azure Machine Learning 的[可用模組集](module-reference.md)。 