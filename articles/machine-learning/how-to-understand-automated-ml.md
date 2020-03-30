---
title: 瞭解自動 ML 結果
titleSuffix: Azure Machine Learning
description: 瞭解如何查看和理解每個自動機器學習運行的圖表和指標。
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: c5f12da3606361b504d4581916d9645fa3cd24f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283455"
---
# <a name="understand-automated-machine-learning-results"></a>了解自動化機器學習結果
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何查看和理解每個自動機器學習運行的圖表和指標。 

深入了解：
+ [分類模型的指標、圖表和曲線](#classification)
+ [回歸模型的指標、圖表和圖形](#regression)
+ [模型可解釋性和功能重要性](#explain-model)

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 使用 SDK 或在 Azure 機器學習工作室中為自動機器學習運行創建實驗。

    * 使用 SDK 構建[分類模型](how-to-auto-train-remote.md)或[回歸模型](tutorial-auto-train-models.md)
    * 使用[Azure 機器學習工作室](how-to-use-automated-ml-for-ml-models.md)通過上載適當的資料來創建分類或回歸模型。

## <a name="view-the-run"></a>查看運行

運行自動機器學習實驗後，可以在機器學習工作區中找到執行歷程記錄。 

1. 移至工作區。

1. 在工作區的左側面板中，選擇 **"實驗**"。

   ![實驗功能表的螢幕擷取畫面](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-menu.png)

1. 在實驗清單中，選擇要探索的。

   [![實驗清單](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-list-expanded.png)

1. 在底部表中，選擇 **"運行**"。

   [ ![試運行](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-run-expanded.png)）

1. 在模型中，為要進一步探索的模型選擇**演算法名稱**。

   [![實驗模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-experiment-model-expanded.png)

當您使用`RunDetails` [Jupyter 小部件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)時，您也會在運行過程中看到相同的結果。

## <a name="classification-results"></a><a name="classification"></a>分類結果

使用 Azure 機器學習的自動機器學習功能構建的每個分類模型都可以使用以下指標和圖表

+ [計量](#classification-metrics)
+ [混淆矩陣](#confusion-matrix)
+ [精度-召回圖表](#precision-recall-chart)
+ [接收者操作特徵 (或 ROC)](#roc)
+ [升力曲線](#lift-curve)
+ [增益曲線](#gains-curve)
+ [校正圖](#calibration-plot)

### <a name="classification-metrics"></a>分類計量

以下指標保存在分類任務的每次運行反覆運算中。

計量|描述|計算|額外的參數
--|--|--|--
AUC_Macro| AUC 是「接收者作業特性曲線」下方的面積。 Macro 是每個類別 AUC 的算術平均值。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_Micro| AUC 是「接收者作業特性曲線」下方的面積。 Micro 通過合併每個類的真實正值和誤報進行全域計算。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_Weighted  | AUC 是「接收者作業特性曲線」下方的面積。 加權是每個類分數的算術平均值，按每個類中真實實例數加權。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
精確度|精確度是完全符合 true 標籤的預測標籤百分比。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |None|
average_precision_score_macro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 宏是每個類平均精度分數的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 Micro 通過結合每次截止時的真實正值和誤報進行全域計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 加權是每個類的平均精度分數的算術平均值，按每個類中真實實例數加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 分數是精確度和召回率的調和平均數。 宏是每個類 F1 分數的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 分數是精確度和召回率的調和平均數。 Micro 通過計算總真實正值、假負數和誤報進行全域計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 分數是精確度和召回率的調和平均數。 以每個類別的 F1 分數其類別頻率將平均值加權|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|這是（多元）邏輯回歸及其擴展（如神經網路）中使用的損耗函數，定義為給定概率分類器預測的真實標籤的負日誌可能性。 對於具有真實標籤 yt 的{0,1}單個樣本，估計 yt = 1 的概率 yp，日誌損失為 -log P（yt&#124;yp） = -（yt log（yp） = （1 - yt） 日誌（1 - yp）。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|None|
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 這由norm_macro_recall ：* （recall_score_macro - R）/（1 - R）來實現，其中 R 是隨機預測recall_score_macro的預期值（即二進位分類的 R=0.5 和 C 類分類問題的 R=（1/C）。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|平均值 = "宏" |
precision_score_macro|精度是正確標記的正預測元素的百分比。 宏是每個類的精度的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|精度是正確標記的正預測元素的百分比。 Micro 通過計算總真實正數和誤報進行全域計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|精度是正確標記的正預測元素的百分比。 加權是每個類的精度的算術平均值，按每個類中真實實例數加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|撤回是特定類正確標記的元素的百分比。 宏是每個類的調用的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|撤回是特定類正確標記的元素的百分比。 通過計算總真實正數、假負數和誤報，在全球範圍內計算 Micro|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|撤回是特定類正確標記的元素的百分比。 加權是每個類的調用的算術平均值，按每個類中真實實例數加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加權精度是給每個示例的權重等於該示例真實類中真實實例的比例的精度。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

<a name="confusion-matrix"></a>

### <a name="confusion-matrix"></a>混淆矩陣

#### <a name="what-is-a-confusion-matrix"></a>什麼是混淆矩陣？
混淆矩陣可用來說明分類模型的效能。 每行在資料集中顯示真實類或實際類的實例，每一清單示模型預測的類實例。 

#### <a name="what-does-automated-ml-do-with-the-confusion-matrix"></a>自動 ML 如何處理混淆矩陣？
針對分類問題，Azure Machine Learning 會針對每個已建置的模型自動提供混淆矩陣。 對於每個混淆矩陣，自動 ML 將顯示每個預測標籤（列）與真實標籤（行）進行比較的頻率。 顏色越深，矩陣中該特定部分的計數越高。 

#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
我們將資料集的實際值與模型給出的預測值進行比較。 因此，如果模型具有大多數沿對角線的值，則機器學習模型具有更高的精度，這意味著模型預測了正確的值。 如果模型具有類不平衡，則混淆矩陣將有助於檢測偏置模型。

##### <a name="example-1-a-classification-model-with-poor-accuracy"></a>示例 1：精度差的分類模型
![精度差的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

##### <a name="example-2-a-classification-model-with-high-accuracy"></a>示例 2：高精度分類模型 
![高精度分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>示例3：模型預測中高精度、高偏置的分類模型
![模型預測中高精度、高偏置的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>
### <a name="precision-recall-chart"></a>精確度與召回率圖表
#### <a name="what-is-a-precision-recall-chart"></a>什麼是精確召回圖表？
精度召回曲線顯示了模型中精度和召回之間的關係。 術語精度表示模型正確標記所有實例的能力。 召回率代表分類器能夠針對特定標籤找到的所有執行個體。

#### <a name="what-does-automated-ml-do-with-the-precision-recall-chart"></a>自動 ML 與精密召回圖表有何用處？

利用此圖表，您可以比較每個模型的精確度與召回率曲線，以針對您的特定商務問題來判斷哪一個模型在精確度與召回率之間具有可接受的關聯性。 此圖表顯示宏平均精確度與召回率、微平均精確度與召回率，以及與模型之所有類別相關聯的精確度與召回率。 

宏平均數將獨立于每個類計算指標，然後獲取平均值，平等對待所有類。 但是，微平均值將聚合所有類的貢獻以計算平均值。 如果資料集中存在類不平衡，則最好使用微平均值。

#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
根據業務問題的目標，理想的精度召回曲線可能有所不同。 下面給出了一些示例

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>示例 1：精度低、召回率低的分類模型
![精度低、召回率低的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>示例 2：精度為 ±100% 且召回 ±100% 的分類模型 
![分類模型高精度和召回](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)
<a name="roc"></a>
### <a name="roc-chart"></a>ROC 圖表

#### <a name="what-is-a-roc-chart"></a>什麼是 ROC 圖表？
接收者操作特徵 (或 ROC) 是對於特定模型之分類正確標籤與分類不正確標籤的繪圖。 在具有高偏差的資料集上將模型定型時，ROC 曲線可提供的資訊較少，因為它將不會顯示誤判標籤。

#### <a name="what-does-automated-ml-do-with-the-roc-chart"></a>自動 ML 如何處理 ROC 圖表？
自動 ML 生成與模型的所有類關聯的宏平均精度調用、微平均精度調用以及與精度調用關聯的精度調用。 

宏平均數將獨立于每個類計算指標，然後獲取平均值，平等對待所有類。 但是，微平均值將聚合所有類的貢獻以計算平均值。 如果資料集中存在類不平衡，則最好使用微平均值。

#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
理想情況下，該模型將具有接近 100% 的正率和接近 0% 的誤報率。 

##### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>示例 1：低真實標籤和高假標籤的分類模型
![低真實標籤和高假標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

##### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>示例 2：具有高真實標籤和低假標籤的分類模型
![具有高真實標籤和低假標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)
<a name="lift-curve"></a>
### <a name="lift-chart"></a>增益圖
#### <a name="what-is-a-lift-chart"></a>什麼是增益圖？
升力圖可用來評估分類模型的效能。 它顯示了與沒有模型相比，在準確性方面，您期望對生成的模型執行更好的操作。
#### <a name="what-does-automated-ml-do-with-the-lift-chart"></a>自動 ML 如何處理增益圖？
您可以將使用 Azure Machine Learning 自動建置的模型升力與基準進行比較，以檢視該特定模型的值增益。
#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？

##### <a name="example-1-a-classification-model-that-does-worse-than-a-random-selection-model"></a>示例 1：比隨機播放模型差的分類模型
![比隨機播放模型差的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)
##### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>示例 2：比隨機播放模型性能更好的分類模型
![性能更好的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)
<a name="gains-curve"></a>
### <a name="gains-chart"></a>收益圖表
#### <a name="what-is-a-gains-chart"></a>什麼是收益圖表？

增益圖會依每個部分的資料來評估分類模型的效能。 它會顯示資料集的每個百分位數值，相較於隨機選取模型，您能夠預期它會執行得更好。

#### <a name="what-does-automated-ml-do-with-the-gains-chart"></a>自動 ML 如何處理收益圖表？
使用累計增益圖，可協助您使用對應至模型中所需增益的百分比來選擇分類截止。 此資訊提供另一種方式來查看隨附升力圖中的結果。

#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
##### <a name="example-1-a-classification-model-with-minimal-gain"></a>示例 1：增益最小的分類模型
![增益最小的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>示例 2：具有顯著增益的分類模型
![具有顯著增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)
<a name="calibration-plot"></a>
### <a name="calibration-chart"></a>校準圖表

#### <a name="what-is-a-calibration-chart"></a>什麼是校準圖表？
校正圖可用來顯示預測模型的信賴度。 它通過顯示預測概率和實際概率之間的關係來達到此點，其中"概率"表示特定實例屬於某個標籤下的可能性。
#### <a name="what-does-automated-ml-do-with-the-calibration-chart"></a>自動 ML 如何處理校準圖表？
針對所有分類問題，您可以檢閱微平均、宏平均及指定預測模型中每個類別的校正線。

宏平均數將獨立于每個類計算指標，然後獲取平均值，平等對待所有類。 但是，微平均值將聚合所有類的貢獻以計算平均值。 
#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
 校準良好的模型與 y_x 線對齊，其預測相當有信心。 過度信賴的模型會與 y=0 線對齊，其會顯示預測機率，但沒有任何實際機率。 


##### <a name="example-1-a-well-calibrated-model"></a>示例 1：校準良好的模型
![ 校準更良好的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

##### <a name="example-2-an-over-confident-model"></a>示例 2：過於自信的模型
![過於自信的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)

## <a name="regression-results"></a><a name="regression"></a>回歸結果

使用 Azure 機器學習的自動機器學習功能構建的每個回歸模型都可以使用以下指標和圖表

+ [計量](#reg-metrics)
+ [預測與真實](#pvt)
+ [殘差直方圖](#histo)


### <a name="regression-metrics"></a><a name="reg-metrics"></a>回歸指標

以下指標保存在回歸或預測任務的每次運行反覆運算中。

|計量|描述|計算|額外的參數
--|--|--|--|
explained_variance|Explained variance 是所給予資料集其變化的數學模型帳戶的比例。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均值為 0 時，它會等於 Explained variance。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|None|
r2_score|R2 是與輸出平均值的基線模型相比的確定係數，或平方誤差減少的百分比。 |[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|None|
spearman_correlation|Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他的相關係數，此相關係數的變化在 -1 到 +1 之間，其中 0 代表不相關。 -1 或 + 1 的相互關聯表示真正單純的關聯性。 正相關是指隨著 x 增加，y 也會增加。 負相關是指隨著 x 增加，y 會減少。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|None|
mean_absolute_error|Mean absolute error (平均絕對誤差) 是目標與預測值之間差異絕對值的預期值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|None|
normalized_mean_absolute_error|Normalized mean absolute error (正規化平均絕對誤差) 是平均絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以資料範圍|
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|None|
normalized_median_absolute_error|Normalized median absolute error (正規化中位數絕對誤差) 是中位數絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以資料範圍|
root_mean_squared_error|Root mean squared error (均方根誤差) 是目標與預測值之間預期平方差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|None|
normalized_root_mean_squared_error|Normalized root mean squared error (正規化均方根誤差) 是均方根誤差防以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以資料範圍|
root_mean_squared_log_error|Root mean squared log error (均方根對數誤差) 是預期平方對數誤差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|None|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error (正規化均方根對數誤差) 是均方根對數誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以資料範圍|

### <a name="predicted-vs-true-chart"></a><a name="pvt"></a>預測與真實圖表
#### <a name="what-is-a-predicted-vs-true-chart"></a>什麼是預測與真實圖表？
預測值與 True 顯示了預測值與其相關回歸問題的真實值之間的關係。 此圖表可用來測量模型的效能，因為預設值愈接近 y=x 線，預測模型的準確度就愈好。

#### <a name="what-does-automated-ml-do-with-the-predicted-vs-true-chart"></a>自動 ML 如何處理預測與真實圖表？
在每個回合之後，您都能查看每個迴歸模型的預測與真值圖。 為了保護資料隱私權，會將值組合在一起，而每組的大小均會顯示為圖表區域下半部的長條圖。 您可以根據模型所在的理想值，將預測模型與顯示誤差幅度且顏色較淡的陰影區域進行比較。

#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
##### <a name="example-1-a-classification-model-with-low-accuracy"></a>示例 1：精度較低的分類模型
![預測精度低的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

##### <a name="example-2-a-regression-model-with-high-accuracy"></a>示例 2：高精度回歸模型 
[![預測中高精度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2-expanded.png)



### <a name="histogram-of-residuals-chart"></a><a name="histo"></a>殘差圖的長條圖
#### <a name="what-is-a-residuals-chart"></a>什麼是殘差圖表？
殘差代表觀察到的 y - 預測的 y。 若要顯示低偏差的錯誤幅度，殘差直方圖應該會形成以 0 為中心的鐘形曲線。 
#### <a name="what-does-automated-ml-do-with-the-residuals-chart"></a>自動 ML 如何處理殘差圖表？
自動 ML 自動提供殘差圖表，以顯示預測中錯誤的分佈。
#### <a name="what-does-a-good-model-look-like"></a>一個好的模型是什麼樣子的？
好的模型通常有一個鐘形曲線或零周圍的誤差。

##### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>示例 1：錯誤中具有偏差的回歸模型
![具有誤差偏差的 SA 回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

##### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>示例 2：錯誤分佈更均勻的回歸模型
![具有更均勻分佈錯誤的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

## <a name="model-interpretability-and-feature-importance"></a><a name="explain-model"></a>模型可解釋性和功能重要性
自動 ML 為您的跑步提供機器學習可解釋性儀表板。
有關啟用可解釋性功能的詳細資訊，請參閱在自動 ML 實驗中啟用可解釋性[操作操作。](how-to-machine-learning-interpretability-automl.md)

## <a name="next-steps"></a>後續步驟

+ 詳細瞭解 Azure 機器學習中的[自動 ml。](concept-automated-ml.md)
+ 嘗試[自動機器學習模型說明](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)示例筆記本。
