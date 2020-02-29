---
title: 多元羅吉斯回歸：模組參考
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning 中的多元羅吉斯回歸模組來建立羅吉斯回歸模型，以用來預測多個值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 40193a2547959b44c5753cfcc6ccad9344ab9486
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920428"
---
# <a name="multiclass-logistic-regression-module"></a>多元羅吉斯回歸模組

本文說明 Azure Machine Learning 設計工具（預覽）中的模組。

您可以使用此模組來建立羅吉斯回歸模型，以用來預測多個值。

使用羅吉斯回歸的分類是一種受監督的學習方法，因此需要加上標籤的資料集。 您可以藉由提供模型和標示的資料集做為模組的輸入（例如[訓練模型](./train-model.md)）來定型模型。 定型的模型接著可用來預測新輸入範例的值。

Azure Machine Learning 也會提供[兩個類別的羅吉斯回歸](./two-class-logistic-regression.md)模組，其適用于二元或二分變數的分類。

## <a name="about-multiclass-logistic-regression"></a>關於多元羅吉斯回歸

羅吉斯回歸是統計資料中已知的方法，用來預測結果的機率，而且常用於分類工作。 此演算法會藉由將資料與羅吉斯函式進行調整，來預測事件發生的機率。 

在多元羅吉斯回歸中，分類器可以用來預測多個結果。

## <a name="configure-a-multiclass-logistic-regression"></a>設定多元羅吉斯回歸

1. 將**多元羅吉斯回歸**模組新增至管線。

2. 藉由設定 [**建立定型模式]** 選項，指定您要如何訓練模型。

    + **單一參數**：如果您知道要如何設定模型，請使用此選項，並提供一組特定值做為引數。

    + **參數範圍**：如果您不確定最佳參數，而且想要執行參數清理，請選取此選項。 選取要逐一查看的值範圍，[微調模型超參數](tune-model-hyperparameters.md)會逐一查看所提供設定的所有可能組合，以判斷產生最佳結果的超參數。  

3. **優化容錯**，指定優化工具聚合的臨界值。 換句話說，如果反覆運算之間的改進少於臨界值，此演算法會停止，並傳回目前的模型。

4. **L1 正規化權數**， **L2 正規化權數**：輸入用於正規化參數 L1 和 L2 的值。 非零值建議用於兩者。

    正規化是一種防止過度學習的方法，penalizing 具有極端係數值的模型。 正規化的運作方式是將與係數值相關聯的負面影響加入假設的錯誤中。 具有極端係數值的精確模型會懲罰更多，但具有較保守值的較不精確模型會懲罰較少。

     L1 與 L2 regularization 有不同的效果，並使用。 L1 可以套用到疏鬆的模型，使用高維度資料時，這是很有用。 相較之下，L2 regularization 是不是疏鬆的資料。  此演算法支援 L1 和 L2 正規化值的線性組合：也就是說，如果 `x = L1` 和 `y = L2`，`ax + by = c` 會定義正規化詞彙的線性範圍。

     L1 和 L2 詞彙的不同線性組合已設計成羅吉斯回歸模型，例如[彈性網路正規化](https://wikipedia.org/wiki/Elastic_net_regularization)。

6. **亂數字種子**：如果您希望結果可在執行時重複使用，請輸入整數值做為演算法的種子。 否則，系統時鐘值會當做種子使用，這在相同管線的執行中可能會產生稍微不同的結果。

8. 連接已加上標籤的資料集，以及其中一個訓練模組：

    + 如果您將 [**建立訓練員模式]** 設定為 [**單一參數**]，請使用 [[定型模型](./train-model.md)] 模組。

9. 執行管道。



## <a name="next-steps"></a>後續步驟

請參閱可用來 Azure Machine Learning 的[模組集合](module-reference.md)。 