---
title: 對您的模型進行偵錯
titleSuffix: ML Studio (classic) - Azure
description: 如何在 Azure Machine Learning Studio （傳統）中，針對定型模型和計分模型模組所產生的錯誤進行偵錯工具。
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 320eba16511036df77dcdbb7ddb628eaa34b2450
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153548"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>以 Azure Machine Learning Studio （傳統）來對模型進行 Debug

執行模型時，您可能會遇到下列錯誤：

* [定型模型][train-model]模組產生錯誤 
* [評分模型][score-model]模組產生不正確的結果 

這篇文章會說明這些錯誤的可能原因。


## <a name="train-model-module-produces-an-error"></a>定型模型模組產生錯誤

![image1](./media/debug-models/train_model-1.png)

[定型模型][train-model]模組預期會有兩個輸入：

1. 來自 Azure Machine Learning Studio （傳統）所提供的模型集合的機器學習服務模型類型。
2. 含有指定 [標籤] 資料行的定型資料，可指定要預測的變數值 (假設另一個資料行為 [功能])。

此模組會在下列情況中產生錯誤：

1. [標籤] 資料行的指定不正確。 如果選取了多個資料行做為 [標籤]，或選取了不正確的資料行索引，即會發生此情況。 例如，如果使用的資料行索引為 30，但輸入資料集只有 25 個資料行，則適用第二個狀況。

2. 資料集不包含任何 [功能] 資料行。 例如，如果輸入資料集只有 1 個標示為 [標籤] 資料行的資料行，則沒有用來建置模型的功能。 在此情況下，「[定型模型][train-model]」模組會產生錯誤。

3. 輸入資料集 ([功能] 或 [標籤]) 含有無限大值。

## <a name="score-model-module-produces-incorrect-results"></a>計分模型模組產生不正確的結果

![image2](./media/debug-models/train_test-2.png)

在受監督之學習的典型訓練/測試實驗中，[分割資料][split]模組會將原始資料集分成兩個部分：一個部分用來定型模型，一個部分用來對定型模型的執行程度進行評分。 接著，使用定型模型為測試資料評分，然後再評估結果以判斷模型的準確性。

[評分模型][score-model]模組需要兩個輸入：

1. [定型模型][train-model]模組的定型模型輸出。
2. 與用來定型模型之資料集不同的計分資料集。

即使實驗成功，[計分模型][score-model]模組還是可能會產生不正確的結果。 有幾個情況可能會導致這個問題發生：

1. 如果指定的標籤為類別，且回歸模型已針對資料定型，則「[計分模型][score-model]」模組會產生不正確的輸出。 這是因為迴歸需要持需回應變數。 在此情況下，更適合使用分類模型。 

2. 同樣地，如果分類模型針對 [標籤] 資料行中包含浮點數的資料集進行訓練，它可能會產生非預期的結果。 這是因為分類需要離散回應變數，該變數僅允許涉及一組有限且小的類別的值。

3. 如果評分資料集不包含用來定型模型的所有功能，[評分模型][score-model]就會產生錯誤。

4. 如果計分資料集中的某一列包含遺漏值或其任何功能的無限值，則[計分模型][score-model]不會產生對應于該資料列的任何輸出。

5. 評分[模型][score-model]可能會針對評分資料集中的所有資料列產生相同的輸出。 例如，使用決策樹系嘗試分類時，如果選擇每個分葉節點範例的最小數目大於可用的訓練範例數目時，可能會發生這個情況。

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

