---
title: 如何選取機器學習演算法
titleSuffix: Azure Machine Learning
description: 如何在叢集、分類或回歸實驗中，針對受監督和不受監督的學習選取 Azure Machine Learning 演算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 03/05/2020
ms.openlocfilehash: e0482bac9569a834adf3e1cdef2b3f702980eac0
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328658"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>如何選取 Azure Machine Learning 的演算法

常見的問題是「我應該使用哪一種機器學習演算法？」 您所選取的演算法主要取決於資料科學案例的兩個不同層面：

 - **您想要如何處理您的資料？** 具體而言，從過去的資料中學習，您想要回答的商務問題是什麼？

 - **您的資料科學案例有哪些需求？** 具體而言，其精確度、定型時間、線性、參數數目，以及您的解決方案支援的功能數目為何？

 ![選擇演算法的考慮：您要知道什麼？ 案例需求為何？](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>商務案例和 Machine Learning 演算法功能提要

[Azure Machine Learning 演算法](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)功能提要可協助您進行第一個考慮：**您想要如何處理資料**？ 在 [Machine Learning 演算法] 功能提要中，尋找您想要執行的工作，然後尋找預測性分析解決方案的[Azure Machine Learning 設計](https://docs.microsoft.com/azure/machine-learning/concept-designer?WT.mc_id=docs-article-lazzeri)工具演算法。 

Machine Learning 設計工具提供完整的演算法組合，例如[多元決策樹](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-decision-forest?WT.mc_id=docs-article-lazzeri)系、[建議系統](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-recommender?WT.mc_id=docs-article-lazzeri)、[類神經網路回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/neural-network-regression?WT.mc_id=docs-article-lazzeri)、[多元類神經網路](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-neural-network?WT.mc_id=docs-article-lazzeri)，以及[K 表示](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/k-means-clustering?WT.mc_id=docs-article-lazzeri)叢集。 每個演算法都是設計來處理不同類型的機器學習服務問題。 如需完整的清單，請參閱[Machine Learning 設計工具演算法和模組參考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)，以及有關每個演算法的運作方式，以及如何調整參數以優化演算法的相關檔。

> [!NOTE]
> 若要下載機器學習演算法功能提要，請移至[Azure machine learning 演算法](https://docs.microsoft.com/azure/machine-learning/algorithm-cheat-sheet?WT.mc_id=docs-article-lazzeri)功能提要。
> 
> 

除了 Azure Machine Learning 演算法功能提要中的指引，請記住為解決方案選擇機器學習演算法時的其他需求。 以下是要考慮的其他因素，例如精確度、定型時間、線性、參數數目和功能數目。

## <a name="additional-requirements-for-a-data-science-scenario"></a>資料科學案例的其他需求

一旦知道您想要如何處理資料之後，您必須決定解決方案的其他需求。 

針對下列需求做出選擇和可能的取捨：

- 精確度
- 定型時間
- 線性
- 參數數目
- 特徵數目

## <a name="accuracy"></a>精確度

機器學習服務的精確度會將模型的效益測量為實際案例的整體結果比例。 在 Machine Learning 設計工具中，「[評估模型」模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)會計算一組業界標準的評估度量。 您可以使用此模組來測量已定型模型的精確度。

不一定需要取得最精確的答案。 視您的用途而定，有時候近似值便已足夠。 如果是這種情況，您可以藉由使用更近似的方法，大幅縮短處理時間。 大致的方法也傾向于避免過度學習。

有三種方式可以使用 [評估模型] 模組：

- 產生訓練資料的分數以評估模型
- 在模型上產生分數，但將這些分數與保留測試集的分數進行比較
- 使用相同的資料集來比較兩個不同但相關模型的分數

如需可用來評估機器學習模型正確性的計量和方法的完整清單，請參閱[評估模型模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/evaluate-model?WT.mc_id=docs-article-lazzeri)。

## <a name="training-time"></a>定型時間

在監督式學習中，訓練意味著使用歷程記錄資料來建立機器學習模型，將錯誤降至最低。 定型出一個模型可能需要幾分鐘或幾小時，這在各個演算法間有很大的差異。 定型時間通常與精確度緊密關聯;其中一個通常會伴隨另一個。 

此外，有些演算法對資料點的數目較為敏感。 您可以選擇特定的演算法，因為您有時間限制，特別是當資料集很大時。

在 Machine Learning 設計工具中，建立和使用機器學習模型通常是三個步驟的程式：

1.  設定模型，方法是選擇特定類型的演算法，然後定義其參數或超參數。 

2.  提供標示為的資料集，並具有與演算法相容的資料。 將資料和模型連接到[訓練模型模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/train-model?WT.mc_id=docs-article-lazzeri)。

3.  定型完成之後，使用定型模型搭配其中一個[評分模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/score-model?WT.mc_id=docs-article-lazzeri)來對新資料進行預測。

## <a name="linearity"></a>線性

統計資料和機器學習中的線性表示在資料集內的變數和常數之間有線性關聯性。 例如，線性分類演算法會假設類別可以用直線（或其較高維度的類比）分隔。

許多機器學習演算法都會使用線性。 在 Azure Machine Learning 設計工具中，它們包括： 

- [多元羅吉斯回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/multiclass-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [雙類別羅吉斯回歸](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-logistic-regression?WT.mc_id=docs-article-lazzeri)
- [支援向量機器](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)  

線性迴歸演算法會假設資料趨勢依循著一條直線。 對某些問題而言，這種假設並不正確，但對其他問題而言，它會降低精確度。 除了其缺點之外，線性演算法也是最常見的一種策略。 這種演算法定型起來通常又快又簡單。

![非線性類別界限](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

***非線性類別界限***：*依賴線性分類演算法會導致低準確度。*

![具有非線性趨勢的資料](./media/how-to-select-algorithms/nonlinear-trend.png)

***具有非線性趨勢的資料***：*使用線性回歸方法會產生比所需更大的錯誤。*

## <a name="number-of-parameters"></a>參數數目

參數是資料科學家在設定演算法時的必經之路。 這些是影響演算法行為的數位，例如錯誤容錯或反復專案的數目，或是演算法運作方式的變化之間的選項。 演算法的定型時間和精確度有時可能會受到敏感性，而只是取得正確的設定。 通常，具有大量參數的演算法需要最多的試用和錯誤，才能找到良好的組合。

或者，Machine Learning 設計工具中有[微調模型超參數模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/tune-model-hyperparameters?WT.mc_id=docs-article-lazzeri)：此課程模組的目標是要判斷機器學習模型的最佳超參數。 此模組會使用不同的設定組合來建立及測試多個模型。 它會比較所有模型的計量，以取得設定的組合。 

雖然這是確定您已跨越參數空間的絕佳方式，但定型模型所需的時間會以指數方式增加，並加上參數的數目。 一般而言，具有許多參數的優點是可讓演算法有更大的彈性。 如果您可以找到正確的參數設定組合，它通常可以達到極佳的精確度。

## <a name="number-of-features"></a>特徵數目

在機器學習中，功能是您嘗試分析之現象的可量化變數。 就特定的資料類型而言，可能會有比資料點數目更龐大的特徵數目。 基因學或文字資料通常屬於這種情況。 

大量的功能可以向下 bog 一些學習演算法，讓定型時間 unfeasibly 長。 [支援向量機器](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/two-class-support-vector-machine?WT.mc_id=docs-article-lazzeri)特別適合具有大量功能的案例。 基於這個理由，許多應用程式中都使用它們，從資訊抓取到文字和影像分類。 支援向量機器可以用於分類和回歸工作。

特徵選取指的是將統計測試套用至輸入的程式（指定的輸出）。 目標是要判斷哪些資料行比較適合輸出的預測。 Machine Learning 設計工具中的以[篩選為基礎的特徵選取模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/filter-based-feature-selection?WT.mc_id=docs-article-lazzeri)，可提供多個特徵選取演算法供您選擇。 此模組包含相互關聯方法，例如皮耳森相互關聯和卡方的值。

您也可以使用[排列功能重要性模組](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/permutation-feature-importance?WT.mc_id=docs-article-lazzeri)來計算資料集的一組功能重要性分數。 接著，您可以利用這些分數來協助您判斷要在模型中使用的最佳功能。


## <a name="next-steps"></a>後續步驟

 - [深入瞭解 Azure Machine Learning 設計工具](https://docs.microsoft.com/azure/machine-learning/service/concept-designer?WT.mc_id=docs-article-lazzeri)
 - 如需 Azure Machine Learning 設計工具中可用的所有機器學習演算法的說明，請參閱[Machine Learning 設計工具演算法和模組參考](https://docs.microsoft.com/azure/machine-learning/algorithm-module-reference/module-reference?WT.mc_id=docs-article-lazzeri)
 - 若要探索深度學習、機器學習服務和 AI 之間的關聯性，請參閱[深度學習與 Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-deep-learning-vs-machine-learning?WT.mc_id=docs-article-lazzeri)
