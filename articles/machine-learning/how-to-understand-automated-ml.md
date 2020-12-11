---
title: 評估 AutoML 實驗結果
titleSuffix: Azure Machine Learning
description: 瞭解如何針對每個自動化機器學習實驗執行，查看和評估圖表和度量。
services: machine-learning
author: gregorybchris
ms.author: chgrego
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 12/09/2020
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q2, automl
ms.openlocfilehash: 747cc88cdea59017483245b59e4b2c56c4b06a40
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032927"
---
# <a name="evaluate-automated-machine-learning-experiment-results"></a>評估自動化機器學習實驗結果

在本文中，您將瞭解如何評估和比較自動化機器學習所定型的模型 (自動化 ML) 實驗。 在自動化 ML 實驗的過程中，會建立許多執行，而且每次執行都會建立一個模型。 針對每個模型，自動化 ML 會產生評估計量和圖表，以協助您測量模型的效能。 

例如，自動化 ML 會根據實驗類型產生下列圖表。

| 分類| 回歸/預測 |
| ----------------------------------------------------------- | ---------------------------------------- |
| [混淆矩陣](#confusion-matrix)                       | [殘差長條圖](#residuals)        |
| [接收者操作特性 (ROC) 曲線](#roc-curve) | [預測與 true](#predicted-vs-true) |
| [精確度-召回 (PR) 曲線](#precision-recall-curve)      |                                          |
| [升力曲線](#lift-curve)                                   |                                          |
| [累積增益曲線](#cumulative-gains-curve)           |                                          |
| [校正曲線](#calibration-curve)                     |                     


## <a name="prerequisites"></a>必要條件

- Azure 訂用帳戶。  (如果您沒有 Azure 訂用帳戶，請在開始之前先 [建立免費帳戶](https://aka.ms/AMLFree)) 
- 以下列其中一種方式建立的 Azure Machine Learning 實驗：
  - [Azure Machine Learning studio](how-to-use-automated-ml-for-ml-models.md) (不需要任何程式碼) 
  - [Azure Machine Learning PYTHON SDK](how-to-configure-auto-train.md)

## <a name="view-run-results"></a>查看執行結果

當您的自動化 ML 實驗完成之後，可以透過下列方式找到執行的歷程記錄：
  - [Azure Machine Learning studio](overview-what-is-machine-learning-studio.md)的瀏覽器
  - 使用[>rundetails Jupyter widget](/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py&preserve-view=true)的 Jupyter 筆記本

下列步驟和影片示範如何在 studio 中查看執行歷程記錄和模型評估計量和圖表：

1. 登[入 studio](https://ml.azure.com/) ，然後流覽至您的工作區。
1. 在左側功能表中，選取 [ **實驗**]。
1. 從實驗清單中選取您的實驗。
1. 在頁面底部的表格中，選取自動化 ML 執行。
1. 在 [ **模型** ] 索引標籤中，選取您要評估之模型的 **演算法名稱** 。
1. 在 [ **計量** ] 索引標籤中，使用左側的核取方塊來查看度量和圖表。

![在 studio 中查看計量的步驟](./media/how-to-understand-automated-ml/how-to-studio-metrics.gif)

## <a name="classification-metrics"></a>分類計量

自動化 ML 會計算針對您的實驗所產生之每個分類模型的效能計量。 這些計量是以 scikit-learn 學習的實作為基礎。 

許多分類度量都是針對兩個類別上的二元分類所定義，而且需要對類別進行平均，以針對多類別分類產生一個分數。 Scikit-learn-learning 提供數個平均值的方法，其中三個是自動化 ML 公開的： **宏**、 **微** 和 **加權**。

- **宏** -計算每個類別的度量，並採用加權平均值
- 藉由計算真正的肯定、誤否定和誤報 (與類別) 無關，來全域計算度量。
- **加權** -計算每個類別的度量，並根據每個類別的樣本數目來取得加權平均值。

雖然每個平均方法都有其優點，但在選取適當的方法時，其中一個常見的考慮是類別不平衡。 如果類別具有不同數目的樣本，則在將少數類別指定為大部分類別的情況下，使用宏平均可能會更有説明。 深入瞭解 [自動化 ML 中的二元與多元計量](#binary-vs-multiclass-classification-metrics)。 

下表摘要說明自動化 ML 針對您的實驗所產生的每個分類模型計算的模型效能計量。 如需詳細資訊，請參閱每個度量的 **計算** 欄位中所連結的 scikit-learn 學習檔。 

|計量|描述|計算|
|--|--|---|
|AUC | AUC 是 [接收器操作特性曲線](#roc-curve)下的區域。<br><br> **目標：** 更接近1 <br> **範圍：** [0，1]<br> <br>支援的度量名稱包括： <li>`AUC_macro`，每個類別的 AUC 算術平均值。<li> `AUC_micro`，其計算方式是結合每個類別的真肯定和假陽性。 <li> `AUC_weighted`，每個類別分數的算術平均值，以每個類別中的 true 實例數目加權。   |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.roc_auc_score.html) | 
|精確度| 精確度是完全符合 true 類別標籤之預測的比率。 <br> <br>**目標：** 更接近1 <br> **範圍：** [0，1]|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|
|average_precision|Average precision 摘要出精確度-召回率曲線，為每個閾值到達的精確度加權平均值，並以上個閾值的召回率中的增值作為權重。 <br><br> **目標：** 更接近1 <br> **範圍：** [0，1]<br> <br>支援的度量名稱包括：<li>`average_precision_score_macro`，這是每個類別平均精確度分數的算術平均值。<li> `average_precision_score_micro`，其計算方式是在每個截止時結合真肯定和假陽性。<li>`average_precision_score_weighted`是每個類別平均精確度分數的算術平均值，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.average_precision_score.html)|
balanced_accuracy|Balanced accuracy 是每個類別其召回率的算術平均值。<br> <br>**目標：** 更接近1 <br> **範圍：** [0，1]|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
f1_score|F1 分數是精確度和召回率的調和平均數。 這是誤報和假否定的良好平衡量值。 不過，它並不會對帳戶有真正的否定。 <br> <br>**目標：** 更接近1 <br> **範圍：** [0，1]<br> <br>支援的度量名稱包括：<li>  `f1_score_macro`：每個類別之 F1 分數的算術平均值。 <li> `f1_score_micro`：藉由計算真肯定、誤否定和誤報的總計來計算。 <li> `f1_score_weighted`：每個類別的加權平均（依類別頻率） F1 分數。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.f1_score.html)|
log_loss|這是 (多維度中使用的遺失函式) 羅吉斯回歸和它的延伸模組（例如類神經網路），其定義為 true 標籤的負面記錄檔可能性（指定概率分類器的預測）。 <br><br> **目標：** 更接近0 <br> **範圍：** [0，inf) |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.log_loss.html)|
norm_macro_recall| 正規化的宏召回是重新叫用宏（平均和正規化），因此隨機效能的分數為0，而最佳效能的分數為1。 <br> <br>**目標：** 更接近1 <br> **範圍：** [0，1] |`(recall_score_macro - R)`&nbsp;/&nbsp;`(1 - R)` <br><br>其中， `R` 是隨機預測的預期值 `recall_score_macro` 。<br><br>`R = 0.5`&nbsp;針對 &nbsp; 二元 &nbsp; 分類。 <br>`R = (1 / C)` 針對 C 類別分類問題。|
matthews_correlation | 馬修斯相互關聯係數是精確度的對稱量值，即使某個類別的樣本數超過另一個類別，也可以使用此量值。 係數1表示完美的預測、0個隨機預測和-1 個反向預測。<br><br> **目標：** 更接近1 <br> **範圍：** [-1，1]|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.matthews_corrcoef.html)|
精確度|精確度是指模型避免將負面樣本標記為正面的能力。 <br><br> **目標：** 更接近1 <br> **範圍：** [0，1]<br> <br>支援的度量名稱包括： <li> `precision_score_macro`，每個類別的算術平均精確度。 <li> `precision_score_micro`，藉由計算真肯定和誤報的整體計算。 <li> `precision_score_weighted`，每個類別的算術平均精確度，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.precision_score.html)|
召回率| 回想一下，您可以使用模型來偵測所有正面樣本。 <br><br> **目標：** 更接近1 <br> **範圍：** [0，1]<br> <br>支援的度量名稱包括： <li>`recall_score_macro`：每個類別的算術平均召回率。 <li> `recall_score_micro`：藉由計算真肯定、誤否定和誤報的總計來計算全域計算。<li> `recall_score_weighted`：每個類別的算術平均數，以每個類別中的 true 實例數目加權。|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.recall_score.html)|
weighted_accuracy|加權精確度為精確度，其中每個樣本都是由屬於相同類別的樣本總數加權。 <br><br>**目標：** 更接近1 <br>**範圍：** [0，1]|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.accuracy_score.html)|

### <a name="binary-vs-multiclass-classification-metrics"></a>二元與多元分類計量

自動化 ML 不會區分二進位和多元計量。 如果資料集有兩個類別或兩個以上的類別，就會報告相同的驗證計量。 不過，某些計量適用于多元分類。 當套用至二進位資料集時，這些計量並不會將任何類別視為 `true` 類別，如您所預期。 顯然適用于多元的計量會加 `micro` 上、 `macro` 或的尾碼 `weighted` 。 範例包括 `average_precision_score` 、 `f1_score` 、 `precision_score` 、 `recall_score` 和 `AUC` 。

例如， `tp / (tp + fn)` 多元平均回想 (`micro` 、 `macro` 或 `weighted`) 兩個二元分類資料集類別的平均值，而不是計算重新叫用。 這相當於計算 `true` 類別和類別的召回 `false` ，然後取得兩者的平均值。

## <a name="confusion-matrix"></a>混淆矩陣

混淆矩陣可提供視覺效果，讓機器學習模型在其分類模型的預測中進行系統化錯誤。 名稱中的「混淆」一詞來自「混淆」模型或標記錯誤範例。 在混淆矩陣中，資料列和資料行的資料格 `i` `j` 包含評估資料集內屬於類別的樣本數目， `C_i` 以及由模型分類為類別的樣本數 `C_j` 。

在 studio 中，較深的資料格表示較高的樣本數目。 選取下拉式清單中的 [正規化視圖] 會在每個矩陣資料列上 **正規化** ，以顯示 `C_i` 預測為類別的類別百分比 `C_j` 。 預設 **原始** 視圖的優點是，您可以查看實體類別的分佈是否不平衡導致模型 misclassify 少數類別中的範例，這是不平衡資料集的常見問題。

良好模型的混淆矩陣會沿著對角線的大部分樣本。

### <a name="confusion-matrix-for-a-good-model"></a>良好模型的混淆矩陣 
![良好模型的混淆矩陣 ](./media/how-to-understand-automated-ml/chart-confusion-matrix-good.png)

### <a name="confusion-matrix-for-a-bad-model"></a>錯誤模型的混淆矩陣
![錯誤模型的混淆矩陣](./media/how-to-understand-automated-ml/chart-confusion-matrix-bad.png)

## <a name="roc-curve"></a>ROC 曲線

收件者操作特性 (ROC) 曲線會繪製真正的正面速率 (TPR) 和 false 正速率 (FPR) 的關聯性，作為決策臨界值的變更。 當您在具有高類別不平衡的資料集上定型模型時，ROC 曲線的資訊可能較少，因為多數類別可以下拉式清單少數類別的貢獻。

曲線 (AUC) 下的區域可以被視為正確分類樣本的比例。 更精確地說，AUC 是分類器順位隨機播放的正值樣本高於隨機播放的負樣本的機率。 曲線的形狀提供 TPR 和 FPR 之間關聯性的直覺，做為分類閾值或判定界限的功能。

接近圖表左上角的曲線會接近 100% TPR，而 0% FPR 則是最可能的模型。 隨機模型會沿著 `y = x` 從左下角到右上角的線條產生 ROC 曲線。 比隨機模型更糟的是，它的 ROC 曲線會低於 `y = x` 該行。
> [!TIP]
> 針對分類實驗，針對自動化 ML 模型所產生的每個折線圖都可以用來評估每個類別的模型，或每個類別的平均值。 您可以按一下圖表右邊圖例中的類別標籤，在這些不同的視圖之間切換。
### <a name="roc-curve-for-a-good-model"></a>良好模型的 ROC 曲線
![良好模型的 ROC 曲線](./media/how-to-understand-automated-ml/chart-roc-curve-good.png)

### <a name="roc-curve-for-a-bad-model"></a>錯誤模型的 ROC 曲線
![錯誤模型的 ROC 曲線](./media/how-to-understand-automated-ml/chart-roc-curve-bad.png)

## <a name="precision-recall-curve"></a>精確度-召回曲線

精確度回收曲線會繪製精確度和召回率之間的關聯性，以作為決策臨界值的變更。 回想一下，模型偵測所有正面樣本和精確度的能力，是模型可以避免將負面樣本標記為正面的能力。 某些商務問題可能需要較高的召回率和更高的精確度，取決於避免錯誤否定與誤報的相對重要性。
> [!TIP]
> 針對分類實驗，針對自動化 ML 模型所產生的每個折線圖都可以用來評估每個類別的模型，或每個類別的平均值。 您可以按一下圖表右邊圖例中的類別標籤，在這些不同的視圖之間切換。
### <a name="precision-recall-curve-for-a-good-model"></a>良好模型的精確度回收曲線
![良好模型的精確度回收曲線](./media/how-to-understand-automated-ml/chart-precision-recall-curve-good.png)

### <a name="precision-recall-curve-for-a-bad-model"></a>錯誤模型的精確度回收曲線
![錯誤模型的精確度回收曲線](./media/how-to-understand-automated-ml/chart-precision-recall-curve-bad.png)

## <a name="cumulative-gains-curve"></a>累積增益曲線

累積增益曲線會繪製正確分類為樣本百分比的函式，並根據預測機率的順序，將樣本視為樣本百分比的函式。

若要計算增益，請先排序模型所預測的最高至最低機率的所有樣本。 然後採用 `x%` 最高的信賴度預測。 將所偵測到的正樣本數目除以 `x%` 正樣本總數，以取得增益。 累積增益是在考慮最可能屬於正類別的一些資料百分比時，所偵測到的正樣本百分比。

完美的模型會將所有正面樣本排名在所有負面樣本上，提供以兩個直接區段組成的累積增益曲線。 第一個是具有斜率的線條 `1 / x` ， `(0, 0)` `(x, 1)` 其中 `x` 是屬於正類別的樣本分數， (`1 / num_classes` if 類別是否平衡) 。 第二個是的水平線條 `(x, 1)` `(1, 1)` 。 在第一個區段中，會正確分類所有正面範例，並在 `100%` 考慮的第一個樣本內進行累積增益 `x%` 。

基準隨機模型將會有一段累積的增益曲線 `y = x` ，其中的 `x%` 樣本只會考慮偵測 `x%` 到的總正面樣本。 完美的模型會有微型的平均曲線，可觸及左上角，以及具有斜率的宏平均線條， `1 / num_classes` 直到累積增益為100%，然後水準直到資料百分比為100為止。
> [!TIP]
> 針對分類實驗，針對自動化 ML 模型所產生的每個折線圖都可以用來評估每個類別的模型，或每個類別的平均值。 您可以按一下圖表右邊圖例中的類別標籤，在這些不同的視圖之間切換。
### <a name="cumulative-gains-curve-for-a-good-model"></a>良好模型的累計增益曲線
![良好模型的累計增益曲線](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-good.png)

### <a name="cumulative-gains-curve-for-a-bad-model"></a>錯誤模型的累計增益曲線
![錯誤模型的累計增益曲線](./media/how-to-understand-automated-ml/chart-cumulative-gains-curve-bad.png)

## <a name="lift-curve"></a>升力曲線

增益曲線會顯示相較于隨機模型，模型執行的次數愈多。 增益是定義為隨機模型累計增益的累計增益比率。

這項相對效能考慮到當您增加類別數目時，分類會變得更困難。 從具有10個類別的資料集（相較于具有兩個類別的資料集）， (隨機模型會錯誤地預測較高分數的樣本) 

基準增益曲線是 `y = 1` 模型效能與隨機模型一致的行。 一般情況下，良好模型的增益曲線會在該圖表上和 X 軸更高，顯示當模型最適合其預測時，會執行比隨機猜測更多的時間。

> [!TIP]
> 針對分類實驗，針對自動化 ML 模型所產生的每個折線圖都可以用來評估每個類別的模型，或每個類別的平均值。 您可以按一下圖表右邊圖例中的類別標籤，在這些不同的視圖之間切換。
### <a name="lift-curve-for-a-good-model"></a>良好模型的增益曲線
![良好模型的增益曲線](./media/how-to-understand-automated-ml/chart-lift-curve-good.png)
 
### <a name="lift-curve-for-a-bad-model"></a>錯誤模型的增益曲線
![錯誤模型的增益曲線](./media/how-to-understand-automated-ml/chart-lift-curve-bad.png)

## <a name="calibration-curve"></a>校正曲線

校正曲線會根據每個信賴等級的正面樣本比例，在其預測中繪製模型的信賴度。 經過妥善校正的模型會正確地將100% 的預測指派給100% 信賴、50% 的預測指派了50% 的信賴度、20% 的預測指派了20% 的信賴度，依此類推。 完全校準的模型會在程式程式碼之後提供校正曲線，讓 `y = x` 模型完全預測樣本屬於每個類別的機率。

過度信賴的模型將會過度預測接近零的機率，而另一種則是不確定每個樣本的類別，而校正曲線看起來會像反向的 "S"。 充滿信心的模型會將平均的機率指派給它所預測的類別，而相關聯的校正曲線看起來會像「S」。 校正曲線不會描繪模型正確分類的能力，而是能夠正確地將信心指派給其預測。 如果模型正確指派低信賴度和高不確定性，則不正確的模型仍會有良好的校正曲線。

> [!NOTE]
> 校正曲線會區分樣本數目，因此小型驗證集可能會產生可能難以解讀的雜訊結果。 這不一定表示模型的校正不正確。

### <a name="calibration-curve-for-a-good-model"></a>良好模型的校正曲線
![良好模型的校正曲線](./media/how-to-understand-automated-ml/chart-calibration-curve-good.png)

### <a name="calibration-curve-for-a-bad-model"></a>錯誤模型的校正曲線
![錯誤模型的校正曲線](./media/how-to-understand-automated-ml/chart-calibration-curve-bad.png)

## <a name="regressionforecasting-metrics"></a>回歸/預測計量

自動化 ML 會針對每個產生的模型計算相同的效能度量，不論它是回歸或預測實驗。 這些計量也會進行正規化，以便在具有不同範圍之資料上定型的模型之間進行比較。 若要深入瞭解，請參閱 [度量](#metric-normalization)正規化。  

下表摘要說明針對回歸和預測實驗產生的模型效能計量。 就像分類計量一樣，這些計量也是以 scikit-learn 學習的實作為基礎。 適當的 scikit-learn 學習檔會在 **計算** 欄位中據以連結。

|計量|描述|計算|
--|--|--|
explained_variance|說明的變異數會測量模型帳戶在目標變數中的變化程度。 它是原始資料其變異數中減少至錯誤變異數的百分比。 當錯誤的平均數為0時，它等於判斷的係數 (請參閱以下) 的 r2_score。 <br> <br> **目標：** 更接近1 <br> **範圍：** (-inf，1]|[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.explained_variance_score.html)|
mean_absolute_error|平均絕對錯誤是目標和預測之間差異絕對值的預期值。<br><br> **目標：** 更接近0 <br> **範圍：** [0，inf)  <br><br> 型別： <br>`mean_absolute_error` <br>  `normalized_mean_absolute_error`，mean_absolute_error 除以資料的範圍。 | [計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_absolute_error.html)|
mean_absolute_percentage_error|平均絕對百分比誤差 (MAPE) 是預測值與實際值之間的平均差異量值。<br><br> **目標：** 更接近0 <br> **範圍：** [0，inf)  ||
median_absolute_error|Median absolute error (中位數絕對誤差) 是目標與預測值之間所有絕對值差異的中位數。 此遺失是強固極端值。<br><br> **目標：** 更接近0 <br> **範圍：** [0，inf) <br><br>型別： <br> `median_absolute_error`<br> `normalized_median_absolute_error`： median_absolute_error 除以資料的範圍。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.median_absolute_error.html)|
r2_score|相較于輸出平均值的基準模型，R ^ 2 是平方誤差的係數或減少的百分比。 <br> <br> **目標：** 更接近1 <br> **範圍：** (-inf，1]|[計算](https://scikit-learn.org/0.16/modules/generated/sklearn.metrics.r2_score.html)|
root_mean_squared_error |Root mean 平方誤差 (RMSE) 是目標和預測之間預期平方差的平方根。 若為非偏誤估算器，RMSE 等於標準差。<br> <br> **目標：** 更接近0 <br> **範圍：** [0，inf) <br><br>型別：<br> `root_mean_squared_error` <br> `normalized_root_mean_squared_error`： root_mean_squared_error 除以資料的範圍。 |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_error.html)|
root_mean_squared_log_error|根 mean 平方對數誤差是預期的平方對數誤差的平方根。<br><br>**目標：** 更接近0 <br> **範圍：** [0，inf)  <br> <br>型別： <br>`root_mean_squared_log_error` <br> `normalized_root_mean_squared_log_error`： root_mean_squared_log_error 除以資料的範圍。  |[計算](https://scikit-learn.org/stable/modules/generated/sklearn.metrics.mean_squared_log_error.html)|
spearman_correlation| Spearman correlation (斯皮爾曼相關性) 是兩個資料集之間關係其單調性的非參數量值。 不同於 Pearson correlation (皮耳森相關性)，Spearman correlation 不假設這兩個資料集為常態分佈。 如同其他相互關聯係數，史皮爾曼會在-1 和1之間變化，0意指沒有相互關聯。 -1 或1的相互關聯表示完全單純的關聯性。 <br><br> 史皮爾曼是順位順序相互關聯計量，這表示如果預測或實際值的變更不會變更預測值或實際值的順位順序，則不會變更史皮爾曼結果。<br> <br> **目標：** 更接近1 <br> **範圍：** [-1，1]|[計算](https://docs.scipy.org/doc/scipy-0.16.1/reference/generated/scipy.stats.spearmanr.html)|

### <a name="metric-normalization"></a>度量正規化

自動化 ML 會將回歸和預測計量標準化，讓在具有不同範圍的資料上定型的模型進行比較。 針對範圍較大的資料定型的模型，其錯誤比以較小範圍的資料定型的相同模型更高，除非該錯誤已正規化。

雖然沒有將錯誤計量正規化的標準方法，但是自動化 ML 會採用將錯誤除以資料範圍的常見方法： `normalized_error = error / (y_max - y_min)`

在評估時間序列資料的預測模型時，自動化 ML 會採取額外的步驟，以確保每個時間序列識別碼的正規化都 (資料細微性) ，因為每個時間序列可能會有不同的目標值散發。
## <a name="residuals"></a>殘差

殘差圖是預測錯誤的長條圖 (針對回歸和預測實驗產生的殘差) 。 殘差的計算方式為 `y_predicted - y_true` 所有樣本，然後顯示為長條圖以顯示模型偏差。

在此範例中，請注意，這兩個模型會稍微偏差，以預測低於實際值。 這對具有扭曲的實際目標的資料集並不常見，但表示較糟的模型效能。 良好的模型會有一個殘差分佈，其尖峰的間隔為零，而極端值為殘差。 較糟的模型會有分散的殘差分佈，其範例大約為零。

### <a name="residuals-chart-for-a-good-model"></a>良好模型的殘差圖
![良好模型的殘差圖](./media/how-to-understand-automated-ml/chart-residuals-good.png)

### <a name="residuals-chart-for-a-bad-model"></a>錯誤模型的殘差圖表
![錯誤模型的殘差圖表](./media/how-to-understand-automated-ml/chart-residuals-bad.png)

## <a name="predicted-vs-true"></a>預測與 true

針對回歸和預測實驗，預測與真正的圖表會繪製目標功能之間的關聯性 (true/實際值) 和模型的預測。 True 值會沿著 X 軸分類收納，而每個 bin 的平均預測值會以誤差線繪製。 這可讓您查看模型是否偏向于預測特定值。 這條線會顯示平均預測，而陰影區域會指出該平均值的預測變異數。

通常，最常見的真正值會具有最精確的預測，且其差異最差。 趨勢線與理想的線條之間的距離， `y = x` 其中有幾個真正的值是較佳的模型效能量值。 您可以使用圖表底部的長條圖，以瞭解實際的資料分佈原因。 包含更多資料範例，其中分佈是稀疏的，可以改善未可見資料的模型效能。

在此範例中，請注意，較佳的模型具有預測的 vs true 行，更接近理想的 `y = x` 行。

### <a name="predicted-vs-true-chart-for-a-good-model"></a>適用于良好模型的預測與真正圖表
![適用于良好模型的預測與真正圖表](./media/how-to-understand-automated-ml/chart-predicted-true-good.png)

### <a name="predicted-vs-true-chart-for-a-bad-model"></a>適用于錯誤模型的預測與真正圖表
![適用于錯誤模型的預測與真正圖表](./media/how-to-understand-automated-ml/chart-predicted-true-bad.png)

## <a name="model-explanations-and-feature-importances"></a>模型說明和功能 importances

雖然模型評估計量和圖表適合用來測量模型的一般品質，但是在練習負責任的 AI 時，檢查哪一個資料集功能用來進行預測的模型是不可或缺的。 這就是為什麼自動化 ML 會提供模型可解譯性儀表板來測量和報告資料集功能的相對貢獻。

![功能 importances](./media/how-to-understand-automated-ml/how-to-feature-importance.gif)

若要在 studio 中查看可解譯性儀表板：

1. 登[入 studio](https://ml.azure.com/)並流覽至您的工作區
2. 在左側功能表中，選取 [**實驗**]
3. 從實驗清單中選取您的實驗
4. 在頁面底部的表格中，選取 AutoML 執行
5. 在 [ **模型** ] 索引標籤中，選取您要說明之模型的 **演算法名稱** 。
6. 在 [ **說明** ] 索引標籤中，如果模型是最佳的，您可能會看到已建立的說明。
7. 若要建立新的說明，請選取 [ **說明模型** ]，然後選取要用來計算說明的遠端計算

> [!NOTE]
> ForecastTCN 模型目前不支援自動化 ML 說明，其他預測模型可能會有可解譯性工具的有限存取權。

## <a name="next-steps"></a>後續步驟
* 試用 [自動化機器學習模型說明範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)。
* 深入瞭解 [自動化 ML 中的負責任 AI 供應](how-to-machine-learning-interpretability-automl.md)專案。
* 如需自動化的 ML 特定問題，請與聯繫 askautomatedml@microsoft.com 。
