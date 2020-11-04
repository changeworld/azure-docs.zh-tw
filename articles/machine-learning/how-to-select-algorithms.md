---
title: 如何選取機器學習演算法
titleSuffix: Azure Machine Learning
description: 如何在群集、分類或回歸實驗中，為受監督和非監督式的學習選取 Azure Machine Learning 演算法。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
author: FrancescaLazzeri
ms.author: lazzeri
ms.reviewer: cgronlun
ms.date: 05/07/2020
ms.openlocfilehash: 27b18fdc2dda40f8361483e6ecce28d0ccbd0310
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93308224"
---
# <a name="how-to-select-algorithms-for-azure-machine-learning"></a>如何選取 Azure Machine Learning 的演算法

常見的問題是「我應該使用哪一種機器學習服務演算法？」 您所選取的演算法主要取決於資料科學案例的兩個不同層面：

 - **您要如何處理資料？** 具體而言，從過去的資料學習，您想要回答的商務問題是什麼？

 - **您的資料科學案例有哪些需求？** 具體而言，您的解決方案所支援的精確度、定型時間、線性、參數數目和功能數目為何？

 ![選擇演算法的考慮：您要知道什麼？ 案例需求為何？](./media/how-to-select-algorithms/how-to-select-algorithms.png)

## <a name="business-scenarios-and-the-machine-learning-algorithm-cheat-sheet"></a>商務案例和 Machine Learning 演算法功能提要

[Azure Machine Learning 演算法](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)的功能提要可協助您進行第一次考慮： **您要** 如何處理資料？ 在 Machine Learning 演算法的功能提要上，尋找您想要進行的工作，然後尋找預測性分析解決方案的 [Azure Machine Learning 設計](./concept-designer.md?WT.mc_id=docs-article-lazzeri) 工具演算法。 

Machine Learning 設計工具提供完整的演算法組合，例如 [多元決策樹](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri)系、 [建議系統](./algorithm-module-reference/evaluate-recommender.md?WT.mc_id=docs-article-lazzeri)、 [類神經網路回歸](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri)、 [多元類神經網路](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri)，以及 [K 表示](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri)叢集。 每個演算法都是設計來解決不同類型的機器學習服務問題。 如需完整清單的詳細資訊，請參閱 [Machine Learning 設計工具演算法和模組參考](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri) ，以及有關每個演算法如何運作以及如何調整參數以將演算法優化的相關檔。

> [!NOTE]
> 若要下載機器學習演算法的功能提要，請移至 [Azure machine learning 演算法](./algorithm-cheat-sheet.md?WT.mc_id=docs-article-lazzeri)功能提要頁面。
> 
> 

除了 Azure Machine Learning 演算法的相關指引，也請記住在為方案選擇機器學習演算法時的其他需求。 以下是要考慮的其他因素，例如精確度、定型時間、線性、參數數目和功能數目。

## <a name="comparison-of-machine-learning-algorithms"></a>機器學習演算法的比較

有些學習演算法會對資料結構或想要的結果做出特定假設。 如果可以找到符合需求的假設，您就能獲得更實用的結果、更精確的預測或更快的定型時間。

下表摘要說明來自分類、回歸和叢集系列之演算法的一些最重要特性：

| **演算法** | **精確度** | **定型時間** | **線性** | **參數** | **注意事項** |
| --- |:---:|:---:|:---:|:---:| --- |
| **分類系列** | | | | | |
| [雙類別羅吉斯回歸](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |好  |快速 |是 |4 | |
| [雙類別決策樹系](./algorithm-module-reference/two-class-decision-forest.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |否 |5 |顯示較慢的評分時間。 建議不要使用「一對多」多元分類，因為在累積的樹狀結構預測中，藉由踏板鎖定所造成的評分時間較慢 |
| [雙類別促進式決策樹](./algorithm-module-reference/two-class-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |否 |6 |高記憶體使用量 |
| [二級類神經網路](./algorithm-module-reference/two-class-neural-network.md?WT.mc_id=docs-article-lazzeri) |好 |中 |否 |8 | |
| [雙類別平均認知](./algorithm-module-reference/two-class-averaged-perceptron.md?WT.mc_id=docs-article-lazzeri) |好 |中 |是 |4 | |
| [雙類別支援向量機器](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |5 |適用於大型特徵集 |
| [多元羅吉斯回歸](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |4 | |
| [多元決策樹系](./algorithm-module-reference/multiclass-decision-forest.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |否 |5 |顯示較慢的評分時間 |
| [多元促進式決策樹](./algorithm-module-reference/multiclass-boosted-decision-tree.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |否 |6 | 通常會以較少的風險來改善精確度 |
| [多元類神經網路](./algorithm-module-reference/multiclass-neural-network.md?WT.mc_id=docs-article-lazzeri) |好 |中 |否 |8 | |
| [一對多多元](./algorithm-module-reference/one-vs-all-multiclass.md?WT.mc_id=docs-article-lazzeri) | - | - | - | - |請參閱選取的兩個類別方法的屬性 |
| **回歸系列** | | | | | |
| [線性回歸](./algorithm-module-reference/linear-regression.md?WT.mc_id=docs-article-lazzeri) |好 |快速 |是 |4 | |
| [決策樹系回歸](./algorithm-module-reference/decision-forest-regression.md?WT.mc_id=docs-article-lazzeri)|非常好 |中 |否 |5 | |
| [促進式決策樹回歸](./algorithm-module-reference/boosted-decision-tree-regression.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |否 |6 |高記憶體使用量 |
| [類神經網路回歸](./algorithm-module-reference/neural-network-regression.md?WT.mc_id=docs-article-lazzeri) |好 |中 |否 |8 | |
| **群集系列** | | | | | |
| [K 表示群集](./algorithm-module-reference/k-means-clustering.md?WT.mc_id=docs-article-lazzeri) |非常好 |中 |是 |8 |叢集演算法 |

## <a name="requirements-for-a-data-science-scenario"></a>資料科學案例的需求

一旦知道您想要如何處理資料，您需要判斷解決方案的其他需求。 

進行下列需求的選擇和可能取捨：

- 精確度
- 定型時間
- 線性
- 參數數目
- 特徵數目

## <a name="accuracy"></a>精確度

機器學習的精確度可將模型的效能，視為整體案例的真實結果比例。 在 Machine Learning 表設計工具中，[ [評估模型] 模組](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri) 會計算一組業界標準的評估度量。 您可以使用此模組來測量定型模型的精確度。

可能不一定需要取得最準確的答案。 視您的用途而定，有時候近似值便已足夠。 如果是這種情況，您可以藉由堅持使用更近似的方法來大幅縮短處理時間。 大約的方法也傾向于避免過度學習。

使用「評估模型」模組的方式有三種：

- 產生定型資料的分數以評估模型
- 在模型上產生分數，但在保留的測試集上比較分數與分數
- 使用相同的資料集，比較兩個不同但相關模型的分數

如需可用來評估機器學習模型精確度的計量和方法完整清單，請參閱 [評估模型模組](./algorithm-module-reference/evaluate-model.md?WT.mc_id=docs-article-lazzeri)。

## <a name="training-time"></a>定型時間

在監督式學習中，定型表示使用歷程記錄資料來建立機器學習模型，以將錯誤降至最低。 定型出一個模型可能需要幾分鐘或幾小時，這在各個演算法間有很大的差異。 定型時間通常會緊密地系結至精確度;其中一個通常會隨附于另一個。 

此外，有些演算法對資料點的數目較為敏感。 您可以選擇特定的演算法，因為您有時間限制，尤其是當資料集很大時。

在 Machine Learning 設計工具中，建立和使用機器學習模型通常是三個步驟的程式：

1.  藉由選擇特定類型的演算法，然後定義其參數或超參數，來設定模型。 

2.  提供標記且資料集與演算法相容的資料集。 將資料和模型連接到 [定型模型模組](./algorithm-module-reference/train-model.md?WT.mc_id=docs-article-lazzeri)。

3.  定型完成之後，請使用定型的模型搭配其中一個 [評分模組](./algorithm-module-reference/score-model.md?WT.mc_id=docs-article-lazzeri) 來對新資料進行預測。

## <a name="linearity"></a>線性

統計資料和機器學習中的線性表示資料集內的變數和常數之間有線性關聯性。 例如，線性分類演算法假設類別可以用直線分隔 (或其較高維度的類比) 。

許多機器學習演算法都會使用線性。 在 Azure Machine Learning 設計工具中，它們包含： 

- [多元羅吉斯回歸](./algorithm-module-reference/multiclass-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [雙類別羅吉斯回歸](./algorithm-module-reference/two-class-logistic-regression.md?WT.mc_id=docs-article-lazzeri)
- [支援向量機器](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri)  

線性迴歸演算法會假設資料趨勢依循著一條直線。 對某些問題來說，這種假設不是不良的，但對其他問題來說，它會降低精確度 儘管有其缺點，線性演算法也是最常見的一種策略。 這種演算法定型起來通常又快又簡單。

![非線性類別界限](./media/how-to-select-algorithms/nonlinear-class-boundary.png)

**_非線性類別界限_* _：線性分類演算法上的 _Relying 會導致低精確度。 *

![具有非線性趨勢的資料](./media/how-to-select-algorithms/nonlinear-trend.png)

***具有非線性趨勢的資料** _： _Using 線性回歸方法會產生比所需更大的錯誤。 *

## <a name="number-of-parameters"></a>參數數目

參數是資料科學家在設定演算法時的必經之路。 它們是影響演算法行為的數位，例如容錯或反覆運算次數，或演算法行為的變異變數之間的選項。 演算法的定型時間和精確度有時可能會受到敏感性，以取得正確的設定。 通常，具有大量參數的演算法需要最多的試用和錯誤才能找到良好的組合。

或者，Machine Learning 設計工具中有 [微調模型超參數模組](./algorithm-module-reference/tune-model-hyperparameters.md?WT.mc_id=docs-article-lazzeri) ：此課程模組的目標是要判斷機器學習模型的最佳超參數。 此模組會使用不同的設定組合來建立及測試多個模型。 它會比較所有模型的計量，以取得設定的組合。 

雖然這是確定您已跨越參數空間的絕佳方法，但是訓練模型所需的時間會隨著參數數目以指數方式增加。 一般而言，具有許多參數的優點是可讓演算法有更大的彈性。 如果您可以找到正確的參數設定組合，通常可以達到非常好的精確度。

## <a name="number-of-features"></a>特徵數目

在機器學習中，功能是您嘗試分析之現象的可量化變數。 就特定的資料類型而言，可能會有比資料點數目更龐大的特徵數目。 基因學或文字資料通常屬於這種情況。 

大量的功能可以 bog 一些學習演算法，讓訓練時間 unfeasibly 長。 [支援向量機器](./algorithm-module-reference/two-class-support-vector-machine.md?WT.mc_id=docs-article-lazzeri) 特別適用于具有大量功能的案例。 基於這個理由，許多應用程式都使用了它們，從資訊抓取到文字和影像分類。 支援向量機器可用於分類和回歸工作。

特徵選取是指在指定的輸出中，將統計測試套用至輸入的進程。 目標是要判斷哪些資料行是更具預測性的輸出。 Machine Learning 設計工具中的 [以 [篩選為基礎的特徵選取] 模組](./algorithm-module-reference/filter-based-feature-selection.md?WT.mc_id=docs-article-lazzeri) 提供多個特徵選取演算法，可供選擇。 此模組包含相互關聯方法，例如皮耳森相互關聯和卡方值。

您也可以使用 [排列功能重要性模組](./algorithm-module-reference/permutation-feature-importance.md?WT.mc_id=docs-article-lazzeri) 來計算資料集的一組特徵重要性分數。 然後您可以利用這些分數來協助您判斷要在模型中使用的最佳功能。

## <a name="next-steps"></a>後續步驟

 - [深入瞭解 Azure Machine Learning 設計工具](./concept-designer.md?WT.mc_id=docs-article-lazzeri)
 - 如需 Azure Machine Learning 設計工具中可用的所有機器學習演算法的說明，請參閱 [Machine Learning 設計工具演算法和模組參考](./algorithm-module-reference/module-reference.md?WT.mc_id=docs-article-lazzeri)
 - 若要探索深度學習、機器學習和 AI 之間的關聯性，請參閱 [深度學習與 Machine Learning](./concept-deep-learning-vs-machine-learning.md?WT.mc_id=docs-article-lazzeri)