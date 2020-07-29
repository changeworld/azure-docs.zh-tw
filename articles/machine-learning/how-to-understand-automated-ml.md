---
title: 瞭解自動化 ML 結果
titleSuffix: Azure Machine Learning
description: 瞭解如何針對每個自動化機器學習服務執行，查看並瞭解其圖表和計量。
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/05/2019
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 89fe1d80fb7282a72bde6bcafa070f2d7461b02f
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320828"
---
# <a name="understand-automated-machine-learning-results"></a>瞭解自動化機器學習結果
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何查看並瞭解每個自動化機器學習服務執行的圖表和計量。 

深入了解：
+ [分類模型的計量和圖表](#classification)
+ [回歸模型的計量和圖表](#regression)
+ [模型 interpretability 和功能重要性](#explain-model)

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 使用 SDK 或在 Azure Machine Learning studio 中，為您的自動化機器學習執行建立實驗。

    * 使用 SDK 來建立[分類模型](how-to-auto-train-remote.md)或[回歸模型](tutorial-auto-train-models.md)
    * 使用[Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) ，藉由上傳適當的資料來建立分類或回歸模型。

## <a name="view-the-run"></a>查看執行

執行自動化機器學習實驗之後，您可以在機器學習服務工作區中找到回合的歷程記錄。 

1. 移至工作區。

1. 在工作區的左面板中，選取 [**實驗**]。

   ![實驗功能表的螢幕擷取畫面](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 在實驗清單中，選取您想要探索的測試。

   [![實驗清單](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 在底部的資料表中，選取 [**執行**]。

   [ ![ 實驗執行](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)）

1. 在 [模型] 中，選取您想要進一步探索之模型的**演算法名稱**。

   [![實驗模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

當您使用 `RunDetails` [Jupyter widget](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)時，您也會在執行期間看到這些相同的結果。

## <a name="classification-results"></a><a name="classification"></a>分類結果

您可以使用 Azure Machine Learning 的自動化機器學習功能，針對您建立的每個分類模型提供下列計量和圖表三個

+ [計量](#classification-metrics)
+ [混淆矩陣](#confusion-matrix)
+ [精確度-重新叫用圖表](#precision-recall-chart)
+ [接收者操作特徵 (或 ROC)](#roc)
+ [升力曲線](#lift-curve)
+ [增益曲線](#gains-curve)
+ [校正圖](#calibration-plot)

### <a name="classification-metrics"></a>分類計量

針對分類工作，會在每次執行反復專案中儲存下列計量。

計量|描述|計算|額外的參數
--|--|--|--
AUC_macro| AUC 是「接收者作業特性曲線」下方的面積。 Macro 是每個類別 AUC 的算術平均值。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC 是「接收者作業特性曲線」下方的面積。 微的計算方式是將每個類別的真肯定和假陽性結合在一起。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC 是「接收者作業特性曲線」下方的面積。 加權是每個類別的分數算術平均值，以每個類別中 true 實例的數目加權。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
精確度|精確度是完全符合 true 標籤的預測標籤百分比。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |無|
average_precision_score_macro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Macro 是每個類別之平均精確度分數的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 微的計算方式是在每個截止時結合真肯定和誤報。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 加權是每個類別平均精確度分數的算術平均值，以每個類別中 true 實例的數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 分數是精確度和召回率的調和平均數。 Macro 是每個類別的 F1 分數算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 分數是精確度和召回率的調和平均數。 微運算是透過計算真肯定、誤否定和誤報的總計來計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 分數是精確度和召回率的調和平均數。 以每個類別的 F1 分數其類別頻率將平均值加權|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|這是（多維度）羅吉斯回歸和其延伸模組（例如類神經網路）中所使用的損失函式，其定義為 true 標籤的負對數可能性，指定概率分類器的預測。 針對具有 true 標籤 yt 的單一範例， {0,1} 以及 yt = 1 的估計機率 yp，記錄遺失為-Log P （yt&#124;yp） =-（yt log （yp） + （1-yt） log （1-yp））。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|無|
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 這是藉由 norm_macro_recall： = （recall_score_macro-R）/（1-R）來達成，其中 R 是隨機預測的預期 recall_score_macro 值（亦即，二元分類的 R = 0.5 和 C 類別分類問題的 R = （1/C））。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "宏" |
precision_score_macro|「精確度」是已正確標示之明確預測元素的百分比。 Macro 是每個類別之精確度的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|「精確度」是已正確標示之明確預測元素的百分比。 微運算會藉由計算真肯定和誤報的總計來計算全域。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|「精確度」是已正確標示之明確預測元素的百分比。 加權是每個類別的精確度算術平均值，並依每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|回想是特定類別的正確標記專案的百分比。 Macro 是每個類別的召回算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|回想是特定類別的正確標記專案的百分比。 微運算會藉由計算真肯定、誤否定和誤報的總計來計算全域|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|回想是特定類別的正確標記專案的百分比。 加權是每個類別的召回算術平均值，並依每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加權精確度是精確度，其中每個範例所提供的權數等於該範例的 true 類別中 true 實例的比例。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

### <a name="binary-vs-multiclass-metrics"></a>二進位與多元計量的比較

AutoML 不會區分二進位和多元計量。 無論資料集有兩個類別或兩個以上的類別，都會報告相同的驗證計量。 不過，某些計量適用于多元分類。 套用至二進位資料集時，這些計量並不會將任何類別視為 `true` 類別，如您所預期。 明顯用於多元的計量會加 `micro` 上、或尾碼 `macro` `weighted` 。 範例包括 `average_precision_score` 、 `f1_score` 、 `precision_score` 、 `recall_score` 和 `AUC` 。

具體範例讓這項區別更清楚，而不是將重新叫用計算為 `tp / (tp + fn)` ，多元平均回收（ `micro` 、 `macro` 或 `weighted` ）二元分類資料集的兩個類別的平均值。 這相當於 `true` 分別計算類別和類別的召回 `false` ，然後取得兩者的平均值。

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>混淆矩陣

#### <a name="what-is-a-confusion-matrix"></a>什麼是混淆矩陣？
混淆矩陣可用來說明分類模型的效能。 每個資料列都會顯示 true 的實例或您資料集中的實體類別，而每個資料行都代表模型所預測之類別的實例。 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>自動化 ML 會對混淆矩陣執行什麼動作？
針對分類問題，Azure Machine Learning 會針對每個已建置的模型自動提供混淆矩陣。 針對每個混淆矩陣，自動化 ML 會顯示每個預測標籤（資料行）與 true 標籤（資料列）相較之下的頻率。 顏色愈深，表示矩陣的特定部分中的計數愈高。 

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
我們會比較資料集的實際值與模型所提供的預測值。 因此，如果模型中有大部分的值沿著對角線，機器學習模型就會有更高的精確度，這表示模型會預測正確的值。 如果模型的類別不平衡，混淆矩陣將有助於偵測偏差模型。

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>範例1：精確度不佳的分類模型
![精確度不佳的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>範例2：高準確度的分類模型 
![高準確度的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>範例3：在模型預測中具有高精確度和高偏差的分類模型
![在模型預測中具有高精確度和高偏差的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>精確度與召回率圖表
#### <a name="what-is-a-precision-recall-chart"></a>什麼是精確度回收圖表？
精確度重新叫用曲線會顯示從模型精確度和召回之間的關聯性。 「精確度」一詞表示模型能夠正確標記所有實例的能力。 召回率代表分類器能夠針對特定標籤找到的所有執行個體。

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>自動化 ML 如何使用精確度回收圖表？

利用此圖表，您可以比較每個模型的精確度與召回率曲線，以針對您的特定商務問題來判斷哪一個模型在精確度與召回率之間具有可接受的關聯性。 此圖表顯示宏平均精確度與召回率、微平均精確度與召回率，以及與模型之所有類別相關聯的精確度與召回率。 

「宏-平均值」會獨立計算每個類別的計量，然後採用平均，將所有類別視為相同。 不過，微平均會匯總所有類別的貢獻來計算平均值。 如果資料集內有類別不平衡的情況，最好採用微平均。

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
根據商務問題的目標而定，理想的精確度召回曲線可能會有所不同。 以下提供一些範例

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>範例1：具有低精確度與低召回率的分類模型
![具有低精確度與低召回率的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>範例2：具有 ~ 100% 精確度和 ~ 100% 召回率的分類模型 
![高精確度和召回率的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC 圖表

#### <a name="what-is-a-roc-chart"></a>什麼是 ROC 圖表？
「接收者」作業特性（或 ROC）是正確分類標籤的繪圖，與特定模型的錯誤分類標籤。 在具有高類別不平衡的資料集上定型模型時，ROC 曲線的資訊可能較少，因為多數類別可以下拉式清單來自少數類別的貢獻。

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>自動化 ML 會使用 ROC 圖表來執行什麼作業？
您可以將 ROC 圖表下的區域視覺化為正確分類範例的比例。 ROC 圖表的 advanced user 可能會看起來超出曲線下的面積，並以分類臨界值或決策界限的功能，取得真正的正面和誤報的直覺。

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
使用左上角的 ROC 曲線，其具有100% 的真肯定速率，而 0% false 的正面速率則是最佳模型。 隨機模型會顯示為從左下到右上角的平面線條。 差于隨機會在 y = x 行底下的 dip。

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>範例1：具有低 true 標籤和高 false 標籤的分類模型
![具有低 true 標籤和高 false 標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>範例2：具有高 true 標籤和低 false 標籤的分類模型
![具有高 true 標籤和低 false 標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>增益圖
#### <a name="what-is-a-lift-chart"></a>什麼是增益圖？
增益圖可用來評估分類模型的效能。 增益圖會顯示相較于隨機模型，模型的執行效能愈多。 這可提供您一個相對的效能，考慮到當您增加類別數目時，分類會變得困難。 相較于具有兩個類別的資料集，隨機模型會不正確地預測來自具有10個類別之資料集的更高樣本分數。

#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>自動化 ML 如何使用增益圖？
您可以將使用 Azure Machine Learning 自動建置的模型升力與基準進行比較，以檢視該特定模型的值增益。
#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>範例1：比隨機選取模型更糟的分類模型
![比隨機選取模型更糟的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>範例2：比隨機選取模型執行效能更佳的分類模型
![執行效能更佳的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="cumulative-gains-chart"></a>累計增益圖表
#### <a name="what-is-a-cumulative-gains-chart"></a>什麼是累計增益圖表？

累計增益圖表會依資料的每個部分來評估分類模型的效能。 針對資料集的每個百分位數，圖表會顯示已正確分類的樣本數。

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>自動化 ML 如何使用增益圖？
使用累計增益圖，可協助您使用對應至模型中所需增益的百分比來選擇分類截止。 此資訊提供另一種方式來查看隨附升力圖中的結果。

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>範例1：具有最少增益的分類模型
![具有最少增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>範例2：具有重大增益的分類模型
![具有重大增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>校正圖表

#### <a name="what-is-a-calibration-chart"></a>什麼是校正圖表？
校正圖可用來顯示預測模型的信賴度。 它會藉由顯示預測機率與實際機率之間的關聯性來完成這項工作，其中「機率」代表特定實例屬於某個標籤的可能性。
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>自動化 ML 會使用校正圖表來執行什麼作業？
針對所有分類問題，您可以檢閱微平均、宏平均及指定預測模型中每個類別的校正線。

「宏-平均值」會獨立計算每個類別的計量，然後採用平均，將所有類別視為相同。 不過，微平均會匯總所有類別的貢獻來計算平均值。 
#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
校正良好的模型會與 y = x 線對齊，其中會正確預測樣本屬於每個類別的機率。 過度信賴的模型會過度預測接近零的機率，而一，很少不會不確定每個範例的類別。


##### <a name="example-1-a-well-calibrated-model"></a>範例1：正確校正的模型
![ 更妥善校正的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>範例2：過度信賴的模型
![過度信賴的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>回歸結果

三個下列計量和圖表適用于您使用的自動化機器學習功能所建立的每個回歸模型 Azure Machine Learning

+ [計量](#reg-metrics)
+ [預測與 True](#pvt)
+ [殘差直方圖](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>回歸計量

下列計量會儲存在回歸或預測工作的每次執行反復專案中。

|計量|描述|計算|額外的參數
--|--|--|--|
explained_variance|Explained variance 是所給予資料集其變化的數學模型帳戶的比例。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均值為 0 時，它會等於 Explained variance。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|無|
r2_score|R2 是與輸出平均值的基線模型相比的確定係數，或平方誤差減少的百分比。 |[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|無|
spearman_correlation|Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他的相關係數，此相關係數的變化在 -1 到 +1 之間，其中 0 代表不相關。 -1 或 + 1 的相互關聯表示真正單純的關聯性。 正相關是指隨著 x 增加，y 也會增加。 負相關是指隨著 x 增加，y 會減少。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|無|
mean_absolute_error|Mean absolute error (平均絕對誤差) 是目標與預測值之間差異絕對值的預期值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|無|
normalized_mean_absolute_error|Normalized mean absolute error (正規化平均絕對誤差) 是平均絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以資料範圍|
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|無|
normalized_median_absolute_error|Normalized median absolute error (正規化中位數絕對誤差) 是中位數絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以資料範圍|
root_mean_squared_error|Root mean squared error (均方根誤差) 是目標與預測值之間預期平方差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|無|
normalized_root_mean_squared_error|Normalized root mean squared error (正規化均方根誤差) 是均方根誤差防以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以資料範圍|
root_mean_squared_log_error|Root mean squared log error (均方根對數誤差) 是預期平方對數誤差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|無|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error (正規化均方根對數誤差) 是均方根對數誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以資料範圍|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>預測與 True 圖表
#### <a name="what-is-a-predicted-vs-true-chart"></a>什麼是預測與真正的圖表？
[已預測] 與 [True] 會顯示預測值與回歸問題的相關 True 值之間的關聯性。 此圖表可用來測量模型的效能，因為預設值愈接近 y=x 線，預測模型的準確度就愈好。

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>自動化 ML 如何使用預測的 vs True 圖表？
在每個回合之後，您都能查看每個迴歸模型的預測與真值圖。 為了保護資料隱私權，會將值組合在一起，而每組的大小均會顯示為圖表區域下半部的長條圖。 您可以根據模型所在的理想值，將預測模型與顯示誤差幅度且顏色較淡的陰影區域進行比較。

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>範例1：具有低精確度的分類模型
![預測中低精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>範例2：高準確度的回歸模型 
[![預測中具有高精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>殘差圖表的長條圖
#### <a name="what-is-a-residuals-chart"></a>什麼是殘差圖表？
「剩餘」是預測與實際值（）之間的差異 `y_pred - y_true` 。 若要顯示低偏差的錯誤幅度，殘差直方圖應該會形成以 0 為中心的鐘形曲線。 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>自動化 ML 會對殘差圖表執行什麼作業？
自動化 ML 會自動提供殘差圖表，以顯示預測中錯誤的分佈。
#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
良好的模型通常會將殘差以零為中心的距離。

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>範例1：在其錯誤中有偏差的回歸模型
![其錯誤中有偏差的 SA 回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>範例2：有更多分配錯誤的回歸模型
![有更多分配錯誤的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>模型 interpretability 和功能重要性
自動化 ML 會為您的回合提供機器學習服務 interpretability 儀表板。
如需有關啟用 interpretability 功能的詳細資訊，請參閱[如何](how-to-machine-learning-interpretability-automl.md)啟用自動化 ML 實驗中的 interpretability。

> [!NOTE]
> 說明用戶端目前不支援 ForecastTCN 模型。 如果將 [說明] 儀表板當做最佳模型傳回，且不支援隨選說明執行，此模型將不會傳回。

## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Azure Machine Learning 中的[自動化機器學習](concept-automated-ml.md)。
+ 試用[自動化的 Machine Learning 模型說明](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)範例筆記本。
