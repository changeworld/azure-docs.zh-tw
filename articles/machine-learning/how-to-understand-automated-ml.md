---
title: 評估 AutoML 實驗結果
titleSuffix: Azure Machine Learning
description: 瞭解如何針對每個自動化機器學習實驗執行，查看和評估圖表和度量。
services: machine-learning
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/09/2020
ms.topic: conceptual
ms.custom: how-to, contperfq2
ms.openlocfilehash: d27c65938d10f9061961ebb585327bc77d8b2859
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092455"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>評估自動化機器學習實驗結果

在本文中，您將瞭解如何查看和評估自動化機器學習、AutoML、實驗的結果。 這些實驗包含多個回合，其中每個執行都會建立一個模型。 為了協助您評估每個模型，AutoML 會自動產生您的實驗類型專用的效能度量和圖表。 

例如，AutoML 會為分類和回歸模型提供不同的圖表。 

|分類|迴歸
|---|---|
|<li> [混淆矩陣](#confusion-matrix) <li>[精確度與召回率圖表](#precision-recall-chart) <li> [接收者操作特徵 (或 ROC)](#roc) <li> [升力曲線](#lift-curve)<li> [增益曲線](#gains-curve)<li> [校正圖](#calibration-plot) | <li> [預測與 True](#pvt) <li> [殘差直方圖](#histo)|

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前先建立免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

* 使用 SDK 或在 Azure Machine Learning studio 中，為您的自動化機器學習執行建立實驗。

    * 使用 SDK 建立 [分類模型](how-to-auto-train-remote.md) 或 [回歸模型](tutorial-auto-train-models.md)
    * 使用 [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) ，藉由上傳適當的資料來建立分類或回歸模型。

## <a name="view-run-results"></a>查看執行結果

當您的自動化機器學習實驗完成之後，您可以透過 [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)，在您的 machine learning 工作區中找到執行的歷程記錄。 

針對 SDK 實驗，當您使用 `RunDetails` [Jupyter 小工具](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py&preserve-view=true)時，可以在執行期間看到這些相同的結果。

下列步驟和動畫示範如何在 studio 中查看特定模型的執行歷程記錄和效能度量和圖表。

![查看執行歷程記錄和模型效能計量和圖表的步驟](./media/how-to-understand-automated-ml/view-run-metrics-ui.gif)

若要在 studio 中查看執行歷程記錄和模型效能度量和圖表： 

1. 登[入 studio](https://ml.azure.com/) ，然後流覽至您的工作區。
1. 在工作區的左側面板中，選取 [ **執行**]。
1. 在實驗清單中，選取您要探索的測試。
1. 在下表中，選取 [ **執行**]。
1. 在 [ **模型** ] 索引標籤中，選取您要探索之模型的 **演算法名稱** 。
1. 在 [ **計量** ] 索引標籤上，選取您要針對該模型評估的計量和圖表。 


<a name="classification"></a> 

## <a name="classification-performance-metrics"></a>分類效能計量

下表摘要說明 AutoML 針對您的實驗所產生的每個分類模型計算的模型效能計量。 

計量|描述|計算|額外的參數
--|--|--|--
AUC_macro| AUC 是「接收者作業特性曲線」下方的面積。 Macro 是每個類別 AUC 的算術平均值。  | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="macro"|
AUC_micro| AUC 是「接收者作業特性曲線」下方的面積。 微運算是藉由結合每個類別的真肯定和誤報來全域計算。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | average="micro"|
AUC_weighted  | AUC 是「接收者作業特性曲線」下方的面積。 加權是每個類別分數的算術平均值，以每個類別中的 true 實例數目加權。| [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html)|average="weighted"
精確度|精確度是完全符合 true 標籤的預測標籤百分比。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html) |無|
average_precision_score_macro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 宏是每個類別平均精確度分數的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="macro"|
average_precision_score_micro|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 微的計算方式是在每個截止時結合真肯定和誤報。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="micro"|
average_precision_score_weighted|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 加權是每個類別平均精確度分數的算術平均值，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|average="weighted"|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
f1_score_macro|F1 分數是精確度和召回率的調和平均數。 Macro 是每個類別的 F1 分數算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="macro"|
f1_score_micro|F1 分數是精確度和召回率的調和平均數。 微運算是透過計算真肯定、誤否定和誤報的總計來全域計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="micro"|
f1_score_weighted|F1 分數是精確度和召回率的調和平均數。 以每個類別的 F1 分數其類別頻率將平均值加權|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|average="weighted"|
log_loss|這是 (多維度中使用的遺失函式) 羅吉斯回歸和它的延伸模組（例如類神經網路），其定義為 true 標籤的負面記錄檔可能性（指定概率分類器的預測）。 若為具有 true label yt 的單一範例 {0,1} ，以及 yt = 1 的估計機率 yp，記錄檔遺失為-Log P (yt&#124;yp) =- (yt log (yp) + (1-yt) log (1-yp) # A10。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|無|
norm_macro_recall|Normalized Macro Recall 是正常化的 Macro Recall，因此隨機效能的分數為 0，完美效能的分數為 1。 這是藉由 norm_macro_recall： = (recall_score_macro-R) / (1-R) 來達成，其中 R 是隨機預測的預期值 recall_score_macro 例如，二元分類的 R = 0.5 和 C 類別分類問題 (的 R = (1/C) 。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average = "宏" |
precision_score_macro|有效位數是正確標示的正確預測元素百分比。 Macro 是每個類別的精確度算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="macro"|
precision_score_micro|有效位數是正確標示的正確預測元素百分比。 微運算是藉由計算真肯定和誤報的總計來全域計算。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="micro"|
precision_score_weighted|有效位數是正確標示的正確預測元素百分比。 加權是每個類別之精確度的算術平均值，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|average="weighted"|
recall_score_macro|回想是特定類別的正確標記專案的百分比。 宏是每個類別之召回率的算術平均值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="macro"|
recall_score_micro|回想是特定類別的正確標記專案的百分比。 微運算是透過計算真肯定、誤否定和誤報的總計來全域計算|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="micro"|
recall_score_weighted|回想是特定類別的正確標記專案的百分比。 加權是每個類別之召回率的算術平均值，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|average="weighted"|
weighted_accuracy|加權精確度是指指定給每個範例的權數等於該範例 true 類別中 true 實例比例的精確度。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|sample_weight 是一種向量，等於目標中每個元素為該類別的比例|

### <a name="binary-vs-multiclass-metrics"></a>二進位與多元計量的比較

AutoML 不會區分二進位和多元計量。 如果資料集有兩個類別或兩個以上的類別，就會報告相同的驗證計量。 不過，某些計量適用于多元分類。 當套用至二進位資料集時，這些計量並不會將任何類別視為 `true` 類別，如您所預期。 顯然適用于多元的計量會加 `micro` 上、 `macro` 或的尾碼 `weighted` 。 範例包括 `average_precision_score` 、 `f1_score` 、 `precision_score` 、 `recall_score` 和 `AUC` 。

例如， `tp / (tp + fn)` 多元平均回想 (`micro` 、 `macro` 或 `weighted`) 兩個二元分類資料集類別的平均值，而不是計算重新叫用。 這相當於計算 `true` 類別和類別的召回 `false` ，然後取得兩者的平均值。

## <a name="confusion-matrix"></a>混淆矩陣

混淆矩陣描述分類模型的效能。 每個資料列都會在您的資料集中顯示 true 或實體類別的實例，而每個資料行代表模型所預測之類別的實例。 

針對每個混淆矩陣，自動化 ML 會顯示每個預測標籤 (資料行) 相較于 true 標籤 (資料列) 的頻率。 色彩愈深，表示矩陣的特定部分中的計數愈高。 

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？

混淆矩陣會將資料集的實際值與模型提供的預測值進行比較。 因此，如果模型的最大值沿著對角線，則機器學習模型會有較高的精確度，這表示模型會預測正確的值。 如果模型具有類別不平衡，混淆矩陣有助於偵測偏差模型。

#### <a name="example-1-a-classification-model-with-poor-accuracy"></a>範例1：精確度不佳的分類模型
![精確度不良的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix1.png)

#### <a name="example-2-a-classification-model-with-high-accuracy"></a>範例2：具有高精確度的分類模型 
![具有高精確度的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-confusion-matrix2.png)

##### <a name="example-3-a-classification-model-with-high-accuracy-and-high-bias-in-model-predictions"></a>範例3：模型預測中具有高精確度和高偏差的分類模型
![在模型預測中具有高精確度和高偏差的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-biased-model.png)

<a name="precision-recall-chart"></a>

## <a name="precision-recall-chart"></a>精確度與召回率圖表

精確度重新叫用曲線會顯示模型之間的精確度與召回性之間的關聯性。 「詞彙精確度」（precision）表示模型能夠正確標示所有實例的能力。 召回率代表分類器能夠針對特定標籤找到的所有執行個體。

利用此圖表，您可以比較每個模型的精確度與召回率曲線，以針對您的特定商務問題來判斷哪一個模型在精確度與召回率之間具有可接受的關聯性。 此圖表顯示宏平均精確度與召回率、微平均精確度與召回率，以及與模型之所有類別相關聯的精確度與召回率。 

**宏平均** 會計算每個類別的度量，然後平均地將所有類別視為相同。 不過， **微平均** 會匯總所有類別的投稿來計算平均值。 如果資料集中存在類別不平衡，則最好採用微平均。

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
根據商務問題的目標而定，理想的精確度召回曲線可能會不同。 

##### <a name="example-1-a-classification-model-with-low-precision-and-low-recall"></a>範例1：具有低精確度和低召回率的分類模型
![具有低精確度與低召回率的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall1.png)

##### <a name="example-2-a-classification-model-with-100-precision-and-100-recall"></a>範例2：具有 ~ 100% precision 和 ~ 100% 召回率的分類模型 
![分類模型的高精確度和召回率](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-precision-recall2.png)

<a name="roc"></a>

## <a name="roc-chart"></a>ROC 圖表

收件者操作特性 (或 ROC) 是已正確分類標籤的繪圖，以及特定模型的分類不正確的標籤。 當您在具有高類別不平衡的資料集上定型模型時，ROC 曲線的資訊可能較少，因為多數類別可以下拉式清單少數類別的貢獻。

您可以將 ROC 圖表下的區域視覺化為正確分類樣本的比例。 ROC 圖的 advanced user 可能會看起來不到曲線下的區域，並取得真正的正值和 false 正數的直覺，做為分類閾值或判定界限的功能。

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
一種 ROC 曲線，其接近左上角並具有100% 的正面比率，而0% 的正面比率則是最佳的模型。 隨機模型會顯示為從左下至右上角的平面線條。 比隨機更糟的 dip 會低於 y = x 行。

#### <a name="example-1-a-classification-model-with-low-true-labels-and-high-false-labels"></a>範例1：具有低 true 標籤和高假標籤的分類模型
![具有低真正標籤和高假標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-1.png)

#### <a name="example-2-a-classification-model-with-high-true-labels-and-low-false-labels"></a>範例2：具有高 true 標籤和低 false 標籤的分類模型

![具有高 true 標籤和低 false 標籤的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-roc-2.png)


<a name="lift-curve"></a>

## <a name="lift-chart"></a>增益圖

增益圖會評估分類模型的效能。 增益圖會顯示相較于隨機模型，模型執行的次數愈多。 這可為您提供相關的效能，以考慮當您增加類別數目時，分類會變得更困難。 相較于具有兩個類別的資料集，隨機模型會不正確地從具有十個類別的資料集預測較高分數的樣本。

您可以將自動建立的模型與 Azure Machine Learning 的 (隨機模型) 的增益進行比較，以查看該特定模型的價值增益。

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？

較高的增益曲線（也就是您的模型高於基準的較高）表示效能較佳。 

#### <a name="example-1-a-classification-model-that-performs-poorly-compared-to-a-random-selection-model"></a>範例1：相較于隨機選取模型，執行效能不佳的分類模型
![比隨機選取模型更糟的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve1.png)

#### <a name="example-2-a-classification-model-that-performs-better-than-a-random-selection-model"></a>範例2：執行效能優於隨機選取模型的分類模型
![執行效能更好的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-lift-curve2.png)

<a name="gains-curve"></a>

## <a name="cumulative-gains-chart"></a>累積增益圖

累積增益圖會依據資料的每個部分來評估分類模型的效能。 針對資料集的每個百分位數，圖表會顯示已正確分類的樣本數目，與一律不正確的模型比較。 此資訊提供另一種方式來查看隨附升力圖中的結果。

累積增益圖可協助您使用對應至模型中所需增益的百分比，來選擇分類截止。 您可以比較累計增益圖與基準 (不正確的模型) ，以查看在每個信賴百分位數正確分類的樣本百分比。

#### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？

類似增益圖，您的累計增益曲線高於基準，越好您的模型執行效能就越好。 此外，您的累積增益曲線越接近圖表的左上角，您模型的達成量就愈大，與基準比較。 

##### <a name="example-1-a-classification-model-with-minimal-gain"></a>範例1：具有低增益的分類模型
![具有極低增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve2.png)

##### <a name="example-2-a-classification-model-with-significant-gain"></a>範例2：具有大幅增益的分類模型
![具有大幅增益的分類模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-gains-curve1.png)

<a name="calibration-plot"></a>

## <a name="calibration-chart"></a>校正圖表

校正繪圖會顯示預測性模型的信賴度。 其運作方式是顯示預測機率與實際機率之間的關聯性，其中「機率」表示特定實例屬於某個標籤的可能性。

針對所有分類問題，您可以檢閱微平均、宏平均及指定預測模型中每個類別的校正線。

**宏平均** 會計算每個類別的度量，然後平均地將所有類別視為相同。 不過， **微平均** 會匯總所有類別的投稿來計算平均值。 

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
妥善校正的模型會與 y = x 線對齊，以正確預測樣本屬於每個類別的機率。 過度信賴的模型會過度預測接近零的機率，而不會有每個樣本的類別。

#### <a name="example-1-a-well-calibrated-model"></a>範例1：妥善校正的模型
![ 更妥善校正的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve1.png)

#### <a name="example-2-an-over-confident-model"></a>範例2：過度信賴的模型
![過度信賴的模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-calib-curve2.png)


<a name="regression"></a> 

## <a name="regression-performance-metrics"></a>回歸效能度量

下表摘要說明 AutoML 針對您的實驗產生的每個回歸或預測模型計算的模型效能計量。 

|計量|描述|計算|額外的參數
--|--|--|--|
explained_variance|Explained variance 是所給予資料集其變化的數學模型帳戶的比例。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均數為0時，它等於判斷的係數 (請參閱以下) 的 r2_score。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|無|
r2_score|相較于輸出平均值的基準模型，R ^ 2 是平方誤差的係數或減少的百分比。 |[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|無|
spearman_correlation|Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他的相關係數，此相關係數的變化在 -1 到 +1 之間，其中 0 代表不相關。 -1 或 + 1 的相互關聯表示真正單純的關聯性。 正相關是指隨著 x 增加，y 也會增加。 負相關是指隨著 x 增加，y 會減少。|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|無|
mean_absolute_error|Mean absolute error (平均絕對誤差) 是目標與預測值之間差異絕對值的預期值|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|無|
normalized_mean_absolute_error|Normalized mean absolute error (正規化平均絕對誤差) 是平均絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|除以資料範圍|
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|無|
normalized_median_absolute_error|Normalized median absolute error (正規化中位數絕對誤差) 是中位數絕對誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|除以資料範圍|
root_mean_squared_error|Root mean squared error (均方根誤差) 是目標與預測值之間預期平方差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|無|
normalized_root_mean_squared_error|Normalized root mean squared error (正規化均方根誤差) 是均方根誤差防以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|除以資料範圍|
root_mean_squared_log_error|Root mean squared log error (均方根對數誤差) 是預期平方對數誤差的平方根|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|無|
normalized_root_mean_squared_log_error|Noramlized Root mean squared log error (正規化均方根對數誤差) 是均方根對數誤差除以資料範圍|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|除以資料範圍|

<a name="pvt"></a>

## <a name="predicted-vs-true-chart"></a>預測與真正的圖表

預測與 True 會顯示預測值與其相互關聯的 true 值在回歸問題之間的關聯性。 

在每個回合之後，您都能查看每個迴歸模型的預測與真值圖。 為了保護資料隱私權，會將值組合在一起，而每組的大小均會顯示為圖表區域下半部的長條圖。 您可以根據模型所在的理想值，將預測模型與顯示誤差幅度且顏色較淡的陰影區域進行比較。

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
此圖表可用來測量模型的效能，因為預設值愈接近 y=x 線，預測模型的準確度就愈好。

#### <a name="example-1-a-classification-model-with-low-accuracy"></a>範例1：具有低精確度的分類模型
![在預測中具有低精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression1.png)

#### <a name="example-2-a-regression-model-with-high-accuracy"></a>範例2：精確度較高的回歸模型 
![在其預測中具有高精確度的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression2.png)

<a name="histo"></a> 

## <a name="histogram-of-residuals-chart"></a>殘差圖的長條圖

自動化 ML 會自動提供殘差圖，以顯示回歸模型預測中的錯誤分佈。 剩餘的是預測和實際值 () 之間的差異 `y_pred - y_true` 。 

### <a name="what-does-a-good-model-look-like"></a>良好的模型看起來是什麼樣子？
若要以低偏差顯示錯誤的邊界，則應該將殘差的長條圖塑造為鐘曲線，並以零為中心。

#### <a name="example-1-a-regression-model-with-bias-in-its-errors"></a>範例1：在其錯誤中有偏差的回歸模型
![錯誤中有偏差的 SA 回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression3.png)

#### <a name="example-2-a-regression-model-with-more-even-distribution-of-errors"></a>範例2：具有更多平均分佈錯誤的回歸模型
![有更多分配錯誤的回歸模型](./media/how-to-understand-automated-ml/azure-machine-learning-auto-ml-regression4.png)

<a name="explain-model"></a>

## <a name="model-interpretability-and-feature-importance"></a>模型可解譯性和特徵重要性
自動化 ML 會為您的執行提供機器學習可解譯性儀表板。

如需啟用可解譯性功能的詳細資訊，請參閱 [可解譯性：自動化機器學習中的模型說明](how-to-machine-learning-interpretability-automl.md)。

> [!NOTE]
> 說明用戶端目前不支援 ForecastTCN 模型。 如果此模型傳回做為最佳模型，而且不支援隨選說明執行，則不會傳回說明儀表板。

## <a name="next-steps"></a>後續步驟

+ 深入瞭解 Azure Machine Learning 中的[自動化機器學習](concept-automated-ml.md)。
+ 試用 [自動化機器學習模型說明](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) 範例筆記本。
