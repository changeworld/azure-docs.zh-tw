---
title: 如何選擇機器學習演算法
titleSuffix: Azure Machine Learning
description: 如何在聚類、分類或回歸實驗中為受監督和無監督學習選擇 Azure 機器學習演算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328658"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>如何為 Azure 機器學習選擇演算法

一個常見的問題是"我應該使用哪種機器學習演算法？ 您選擇的演算法主要取決於資料科學方案的兩個不同方面：

 - **您想如何處理您的資料？** 具體來說，您希望通過從過去的資料中學習來回答哪些業務問題？

 - **資料科學方案有哪些要求？** 具體而言，解決方案支援的準確性、訓練時間、線性度、參數數和要素數是多少？

 ![選擇演算法的注意事項：您想知道什麼？ 方案要求是什麼？](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>業務場景和機器學習演算法備忘表

[Azure 機器學習演算法備忘表](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)可説明您首先考慮：**您希望如何處理資料**？ 在機器學習演算法備忘表上，查找要執行的任務，然後查找預測分析解決方案的[Azure 機器學習設計器](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)演算法。 

機器學習設計器提供了一個全面的演算法組合，如[多類決策林](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri)、[推薦系統](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri)、[神經網路回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri)、[多類神經網路](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)和[K-手段聚類](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)。 每種演算法都旨在解決不同類型的機器學習問題。 有關完整清單，請參閱[機器學習設計器演算法和模組參考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)，以及有關每種演算法的工作原理以及如何調整參數以優化演算法的文檔。

> [!NOTE]
> 要下載機器學習演算法備忘單，請轉到[Azure 機器學習演算法備忘單](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)。
> 
> 

除了 Azure 機器學習演算法備選表中的指南外，在為解決方案選擇機器學習演算法時，請記住其他要求。 以下是需要考慮的其他因素，如精度、訓練時間、線性度、參數數和要素數。

## <a name="additional-requirements-for-a-data-science-scenario"></a>資料科學方案的其他要求

瞭解要對資料執行哪些操作後，需要確定解決方案的其他要求。 

針對以下要求做出選擇和可能的權衡：

- 精確度
- 定型時間
- 線性
- 參數數目
- 特徵數目

## <a name="accuracy"></a>精確度

機器學習的準確性衡量模型的有效性與真實結果與總案例的比例。 在機器學習設計器中，[評估模型模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)計算一組行業標準評估指標。 您可以使用此模組測量經過訓練的模型的準確性。

獲得盡可能準確的答案並不總是必要的。 視您的用途而定，有時候近似值便已足夠。 如果是這樣的話，你也許能夠通過堅持更多的近似方法來顯著縮短您的處理時間。 近似方法也自然傾向于避免過度學習。

有三種使用評估模型模組的方法：

- 在培訓資料上生成分數，以評估模型
- 生成模型上的分數，但將這些分數與保留測試集上的分數進行比較
- 使用同一組資料比較兩個不同但相關的模型的分數

有關可用於評估機器學習模型準確性的指標和方法的完整清單，請參閱[評估模型模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)。

## <a name="training-time"></a>定型時間

在監督學習中，培訓意味著使用歷史資料構建機器學習模型，以儘量減少錯誤。 定型出一個模型可能需要幾分鐘或幾小時，這在各個演算法間有很大的差異。 訓練時間往往與準確性密切相關;一個通常伴隨著另一個。 

此外，有些演算法對資料點的數目較為敏感。 您可以選擇特定演算法，因為您有時間限制，尤其是在資料集較大時。

在機器學習設計器中，創建和使用機器學習模型通常是一個三步過程：

1.  通過選擇特定類型的演算法，然後定義其參數或超參數來配置模型。 

2.  提供已標記且資料與演算法相容的資料集。 將資料和模型連接到[訓練模型模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)。

3.  培訓完成後，使用經過訓練的模型與其中一個[評分模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri)一起對新資料進行預測。

## <a name="linearity"></a>線性

統計和機器學習中的線性化意味著資料集中的變數和常量之間存線上性關係。 例如，線性分類演算法假定類可以通過直線（或其更高維類比）分隔。

許多機器學習演算法都會使用線性。 在 Azure 機器學習設計器中，它們包括： 

- [多類邏輯回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [雙類邏輯回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [支援向量機](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

線性迴歸演算法會假設資料趨勢依循著一條直線。 這種假設對一些問題並不壞，但對於另一些假設則降低了準確性。 儘管存在缺點，線性演算法還是作為第一策略廣受歡迎的。 這種演算法定型起來通常又快又簡單。

![非線性類邊界](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***非線性類邊界***：*依靠線性分類演算法會導致精度低。*

![具有非線性趨勢的資料](./media/how-to-select-algorithms/nonlinear-trend.png)

***具有非線性趨勢的資料***：*使用線性回歸方法會產生比必要更大的誤差。*

## <a name="number-of-parameters"></a>參數數目

參數是資料科學家在設定演算法時的必經之路。 它們是影響演算法行為的數位，如容錯或反覆運算次數，或演算法行為變體之間的選項。 演算法的訓練時間和準確性有時對獲得正確的設置非常敏感。 通常，具有大量參數的演算法需要大多數的試驗和錯誤才能找到良好的組合。

或者，機器學習設計器中有[Tune 模型超參數模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri)：此模組的目標是確定機器學習模型的最佳超參數。 該模組使用不同的設置組合生成和測試多個模型。 它比較所有模型的指標，以獲得設置的組合。 

雖然這是確保跨越參數空間的好方法，但訓練模型所需的時間會隨著參數數成倍增長而增加。 一般而言，具有許多參數的優點是可讓演算法有更大的彈性。 只要能找到正確的參數設置組合，它通常可以達到很好的精度。

## <a name="number-of-features"></a>特徵數目

在機器學習中，一個功能是您嘗試分析的現象的可量化變數。 就特定的資料類型而言，可能會有比資料點數目更龐大的特徵數目。 基因學或文字資料通常屬於這種情況。 

大量的功能會拖累一些學習演算法，使訓練時間變得無法用。 [支援向量機](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)特別適用于具有大量功能的方案。 因此，它們已被廣泛應用於從資訊檢索到文本和圖像分類的許多應用。 支援向量電腦可用於分類和回歸任務。

特徵選取是指在給定指定輸出的情況下對輸入應用統計測試的過程。 目標是確定哪些列對輸出的預測性更大。 機器學習設計器中的[基於篩選器的功能選擇模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri)提供了多種功能選擇演算法供您選擇。 該模組包括相關方法，如 Pearson 相關性和奇方值。

您還可以使用["排列要素重要性"模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri)計算資料集的一組要素重要性分數。 然後，您可以利用這些分數來説明確定在模型中使用的最佳功能。


## <a name="next-steps"></a>後續步驟

 - [瞭解有關 Azure 機器學習設計器的更多](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - 有關 Azure 機器學習設計器中提供的所有機器學習演算法的說明，請參閱[機器學習設計器演算法和模組參考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - 要探索深度學習、機器學習和 AI 之間的關係，請參閱[深度學習與機器學習](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
