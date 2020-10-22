---
title: 什麼是自動化 ML？ AutoML
titleSuffix: Azure Machine Learning
description: 了解 Azure Machine Learning 如何自動為您挑選演算法，以及如何藉由使用您提供來為模型選取最佳演算法的參數與準則，從其中產生模型來節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: 49c3e5602834576e8d3de86ac7d6683f9b6f7b89
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92367511"
---
# <a name="what-is-automated-machine-learning-automl"></a>什麼是自動化機器學習 (AutoML)？

自動化機器學習 (亦稱為自動化 ML 或 AutoML) 是使機器學習模型開發中耗時的反覆工作自動化的程序。 其可讓資料科學家、分析師和開發人員以高擴充性、效率和生產力來建置 ML 模型，同時維持模型品質。 自動化 ML 是以我們 [Microsoft Research 部門](https://www.microsoft.com/research/project/automl/) \(英文\) 的突破為基礎。

傳統機器學習模型的開發會耗用大量資源，需要大量的領域知識和時間來產生和比較數十種模型。 透過自動化機器學習，您將可以更輕鬆、更高效率地縮短取得生產就緒 ML 模型所需的時間。

## <a name="when-to-use-automl-classify-regression--forecast"></a>使用 AutoML 的時機：分類、迴歸及預測

當您想要 Azure Machine Learning 使用您指定的目標計量來為您定型和調整模型時，請套用自動化 ML。 自動化 ML 會促進機器學習模型的開發程序大眾化，並讓其使用者 (無論其資料科學專業知識如何) 都能找出任何問題的端對端機器學習管道。

跨產業的資料科學家、分析師和開發人員可以使用自動化 ML 來執行下列動作：
+ 不需要豐富的程式設計知識即可實作 ML 解決方案
+ 節省時間和資源
+ 運用資料科學最佳做法
+ 提供敏捷式問題解決

### <a name="classification"></a>分類

分類是一個常見的機器學習工作。 分類是一種監督式學習，其中模型會學習使用定型資料，並將那些學習套用至新的資料。 Azure Machine Learning 特別針對這些工作提供特徵化，例如用於分類的深度類神經網路文字功能。 深入了解[特徵化選項](how-to-configure-auto-features.md#featurization)。 

分類模型的主要目標，是根據從其定型資料中的學習，來預測新資料將屬於哪些類別。 常見的分類範例包括詐騙偵測、手寫辨識和物件偵測。 深入瞭解並查看 [使用自動化 ML 建立分類模型](tutorial-first-experiment-automated-ml.md)的範例。

請參閱下列 Python 筆記本中的分類和自動化機器學習的範例：[詐騙偵測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) \(英文\)、[行銷預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) \(英文\)，以及[新聞群組資料分類](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b) \(英文\)

### <a name="regression"></a>迴歸

與分類類似，迴歸工作也是常見的監督式學習工作。 Azure Machine Learning [針對這些工作提供的特徵化](how-to-configure-auto-features.md#featurization)。

不同於預測輸出值為類別的分類，迴歸模型會根據獨立的預測指標來預測數值輸出值。 在迴歸中，目標是藉由評估一個變數如何影響其他變數，來協助您建立那些獨立預測變數之間的關聯性。 例如，以諸如油耗、安全等級等特徵為基礎的汽車價格。 深入了解並查看[使用自動化機器學習進行迴歸](tutorial-auto-train-models.md)的範例。

請參閱下列 Python 筆記本中的迴歸和自動化機器學習範例以進行預測：[CPU 效能預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb)、 

### <a name="time-series-forecasting"></a>時間序列預測

建置預測是任何企業不可或缺的一部分，無論是收入、庫存、銷售還是客戶需求。 您可以使用自動化 ML 結合技術和方法，並取得建議的高品質時間序列預測。 透過此操作說明深入了解：[適用於時間序列預測的自動化機器學習](how-to-auto-train-forecast.md)。 

自動化時間序列實驗會被視為多元迴歸問題。 過去的時間序列值會進行「樞紐處理」，以與其他預測變量一起成為迴歸輸入變數的附加維度。 與傳統時間序列方法不同的是，這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 自動化 ML 會針對資料集和預測範圍中的所有項目，學習單一但通常是內部分支的模型。 因此，可以使用更多的資料來估計模型參數，並且可以推廣到看不見的序列。

進階的預測設定包括：
* 假日偵測和特徵化
* 時間序列和 DNN 學習模組 (Auto-ARIMA、Prophet、ForecastTCN)
* 許多模型都支援透過群組
* 輪流原始交互驗證
* 可設定延遲
* 移動視窗彙總功能


請參閱下列 Python 筆記本中的迴歸和自動化機器學習範例以進行預測：[銷售預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) \(英文\)、[需求預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) \(英文\)，以及[飲料生產預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) \(英文\)。

## <a name="how-automl-works"></a>AutoML 的運作方式

在定型期間，Azure Machine Learning 會以平行方式建立數個管道，這些管道分別會為您嘗試不同的演算法與參數。 服務會逐一查看與特徵選取項目配對的 ML 演算法，其中每個反覆項目都會產生具有定型分數的模型。 分數越高，模型就越「適合」您的資料。  一旦達到實驗中定義的允出準則，其就會停止。 

使用 **Azure Machine Learning**，您可以使用下列步驟來設計和執行自動化 ML 定型實驗：

1. **識別要解決的 ML 問題**：分類、預測或迴歸

1. **選擇您是否要使用 Python SDK 或 Studio Web 體驗**：了解 [Python SDK 和 Studio Web 體驗](#parity)之間的同位檢查。

   * 針對有限或無程式碼體驗，請嘗試 Azure Machine Learning Studio Web 體驗，網址為：[https://ml.azure.com](https://ml.azure.com/)  
   * 針對 Python 開發人員，請參閱 [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 
    
1. **指定標記定型資料的來源和格式**：Numpy 陣列或 Pandas 資料框架

1. **設定模型定型的計算目標**，例如您的[本機電腦、Azure Machine Learning Compute、遠端 VM 或 Azure Databricks](how-to-set-up-training-targets.md)。  深入了解[遠端資源上](how-to-auto-train-remote.md)的自動化定型。

1. **設定自動化機器學習參數**，這些參數決定不同模型上有多少反覆項目、超參數設定、進階前置處理/特徵化，以及決定最佳模型時要查看的計量。  
1. **提交定型回合。**

1. **檢閱結果** 

下圖說明此程序。 
![自動化機器學習](./media/concept-automated-ml/automl-concept-diagram2.png)


您也可以檢查所記錄的回合資訊，其中[包含回合執行期間所收集的計量](how-to-understand-automated-ml.md)。 定型回合會產生 Python 序列化物件 (`.pkl` 檔案)，其中包含模型與資料前置處理。

在自動化模型建置的同時，您還可以了解對產生的模型而言，[功能的重要性或相關性](how-to-configure-auto-train.md#explain)。

瞭解如何使用 [遠端計算目標](how-to-auto-train-remote.md)。



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>特徵設計

特徵工程是使用資料的領域知識來建立功能，以協助 ML 演算法更深入瞭解的程式。 在 Azure Machine Learning 中，會套用調整規模和正規化技巧，以促進特徵工程。 這些技術和特性工程統稱統稱為特徵化。

針對自動化機器學習實驗，特徵化會自動套用，但也可以根據您的資料進行自訂。 [深入了解包含哪些特徵化](how-to-configure-auto-features.md#featurization)。  

> [!NOTE]
> 自動化機器學習特徵化步驟 (功能標準化、處理遺漏的資料、將文字轉換為數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

### <a name="automatic-featurization-standard"></a>自動特徵化 (標準) 

在每個自動化機器學習實驗中，您的資料都會自動調整或正規化，以協助演算法執行良好。 在模型定型期間，下列其中一個調整或正規化技術將會套用至每個模型。 瞭解 AutoML 如何協助 [防止過度調整及不平衡](concept-manage-ml-pitfalls.md) 模型中的資料。

|調整&nbsp;&&nbsp;正規化| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 藉由移除平均值並調整為單位變異數，將功能標準化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 藉由依該資料行的最小值與最大值，調整每個功能來轉換功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |依最大絕對值來調整每個功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分位數範圍 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |使用資料的奇異值分解將線性維度縮減，以將其投影至較低的維度空間 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |此轉換器會藉由截斷奇異值分解 (SVD) 來執行線性維度縮減。 相對於 PCA，此估算器不會在計算奇異值分解之前，先將資料置中，這表示其可以有效率地使用 scipy.sparse 矩陣 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 具有至少一個非零分量的每個樣本 (即資料矩陣的每個資料列) 會獨立於其他樣本之外進行重新調整，使其範數 (l1 或 l2) 等於 1 |

### <a name="customize-featurization"></a>自訂特徵化

您也可以使用其他功能工程技術，例如，編碼和轉換。 

啟用此設定的方式：

+ Azure Machine Learning Studio：[透過這些步驟](how-to-use-automated-ml-for-ml-models.md#customize-featurization)，在 [檢視其他設定] 區段中啟用 [自動特徵化]。

+ Python SDK： `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` 在您的 [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 物件中指定。 深入瞭解如何 [啟用特徵化](how-to-configure-auto-features.md)。 

## <a name="ensemble-models"></a><a name="ensemble"></a> 集團模型

自動化機器學習支援預設會啟用的集團模型。 集團學習藉由結合多個模型來改善機器學習結果和預測效能，而不是使用單一模型。 集團反覆項目顯示為回合的最終反覆項目。 自動化機器學習會使用投票和堆疊集團方法兩者來結合模型：

* **投票**：根據預測類別機率 (適用於分類工作) 或預測迴歸目標 (適用於迴歸工作) 的加權平均值進行預測。
* **堆疊**：堆疊結合了異質性模型，並根據個別模型的輸出來定型中繼模型。 目前的預設中繼模型是用於分類工作的 LogisticRegression，和用於迴歸/預測工作的 ElasticNet。

包含排序集團初始化的 [Caruana 集團選取項目演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)，用於決定要在集團中使用的模型。 概括而言，此演算法使用最多五個具有最佳個別分數的模型來初始化集團，並確認這些模型是否在最佳分數的 5% 閾值內，以避免不佳的初始集團。 然後，針對每個集團反覆項目，會將新的模型加入至現有的集團，並計算產生的分數。 如果新模型已改善現有的集團分數，則會更新集團以包含新的模型。

請參閱[操作說明](how-to-configure-auto-train.md#ensemble)，以變更自動化機器學習中的預設集團設定。

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>本機與遠端受控 ML 計算目標的指導方針

自動化 ML 的 Web 介面一律會使用遠端[計算目標](concept-compute-target.md)。  但當您使用 Python SDK 時，您將選擇本機計算或遠端計算目標來進行自動化 ML 定型。

* **本機計算**：定型在本機膝上型電腦或 VM 計算上進行。 
* **遠端計算**：定型在 Machine Learning 計算叢集上進行。  

### <a name="choose-compute-target"></a>選擇計算目標
選擇您的計算目標時，請考慮下列因素：

 * **選擇本機計算**：如果您的案例是關於使用小型資料和簡短定型 (也就是每個子回合的秒數或幾分鐘) 進行的初始探勘或示範，則在本機電腦上進行定型可能是一個更好的選擇。  沒有設定時間，可以直接使用基礎結構資源 (您的電腦或 VM)。
 * **選擇遠端 ML 計算**叢集：如果您要使用較大型的資料集進行定型，例如在生產訓練中建立需要較長定型的模型，則遠端計算將可提供更好的端對端時間效能，因為會平行處理叢集 `AutoML` 節點上的訓練。 在遠端計算上，內部基礎結構的啟動時間會在每個子回合大約1.5 分鐘內增加，如果 Vm 尚未啟動並執行，則需要額外幾分鐘的時間來執行叢集基礎結構。

### <a name="pros-and-cons"></a>優缺點
選擇使用本機與遠端時，請考慮這些優缺點。

|  | 優點 (優勢)  |缺點 (障礙)  |
|---------|---------|---------|---------|
|**本機計算目標** |  <li> 沒有任何環境啟動時間   | <li>  功能的子集<li>  無法平行處理回合 <li> 不適用於大型資料。 <li>定型時沒有資料串流 <li>  沒有以 DNN 為基礎的特徵化 <li> 僅限 Python SDK |
|**遠端 ML 計算叢集**|  <li> 完整的功能集 <li> 平行處理子回合 <li>   大型資料支援<li>  以 DNN 為基礎的特徵化 <li>  視需要計算叢集的動態擴充性 <li> 無程式碼體驗 (Web UI) 也可供使用  |  <li> 叢集節點的啟動時間 <li> 每個子執行的啟動時間    |

### <a name="feature-availability"></a>功能可用性 

 下表中顯示了使用遠端計算時可用的更多功能。 

| 功能                                                    | 遠端 | 本機 | 
|------------------------------------------------------------|--------|-------|
| 資料串流 (大型資料支援，最多 100 GB)          | ✓      |       | 
| 以 DNN-BERT 為基礎的文字特徵化和定型             | ✓      |       |
| 現成的 GPU 支援 (定型和推斷)        | ✓      |       |
| 影像分類和標記的支援                  | ✓      |       |
| 適用於預測的 Auto-ARIMA、Prophet 和 ForecastTCN 模型 | ✓      |       | 
| 以平行方式執行多個回合/反覆項目                       | ✓      |       |
| 在 AutoML Studio Web 體驗 UI 中建立具有可解釋性的模型      | ✓      |       |
| Studio Web 體驗 UI 中的特徵工程自訂| ✓      |       |
| Azure ML 超參數調整                             | ✓      |       |
| Azure ML 管線工作流程支援                         | ✓      |       |
| 繼續回合                                             | ✓      |       |
| 預測                                                | ✓      | ✓     |
| 在筆記本中建立及執行實驗                    | ✓      | ✓     |
| 在 UI 中註冊實驗的資訊和計量並加以視覺化 | ✓      | ✓     |
| 資料護欄                                            | ✓      | ✓     |

## <a name="many-models"></a>許多模型 

[Many Models Solution Accelerator](https://aka.ms/many-models) (預覽) 建置在 Azure Machine Learning 中，可讓您使用自動化 ML 定型、操作及管理上百個或甚至數千個機器學習模型。

例如，在下列情況中，針對每個執行個體或個人建置模型可以改善結果：

* 預測個別商店的銷售額
* 對數百個油井進行預測性維護
* 為個別使用者量身打造體驗。

## <a name="automl-in-azure-machine-learning"></a>Azure Machine Learning 中的 AutoML

Azure Machine Learning 提供兩種使用自動化 ML 的體驗：

* 針對有程式碼體驗的客戶，提供 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 

* 針對有限/無程式碼體驗客戶，提供 Azure Machine Learning Studio：[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>實驗設定 

下列設定可讓您設定自動化 ML 實驗。 

| |Python SDK|Studio Web 體驗|
----|:----:|:----:
|**將資料分割成定型/驗證集**| ✓|✓
|**支援 ML 工作：分類、迴歸和預測**| ✓| ✓
|**根據主要計量最佳化**| ✓| ✓
|**支援 Azure ML 計算作為計算目標** | ✓|✓
|**設定預測範圍，目標延遲 & 滾動時間範圍**|✓|✓
|**設定允出準則** |✓|✓ 
|**設定並行反覆項目**| ✓|✓
|**置放資料行**| ✓|✓
|**封鎖演算法**|✓|✓
|**交叉驗證** |✓|✓
|**支援在 Azure Databricks 叢集上進行定型**| ✓|
|**檢視已進行工程特徵名稱**|✓|
|**特徵化摘要**| ✓|
|**假日的特徵化**|✓|
|**記錄檔詳細程度等級**| ✓|

### <a name="model-settings"></a>模型設定

這些設定可套用至最佳模型，作為自動化 ML 實驗的結果。

| |Python SDK|Studio Web 體驗|
|----|:----:|:----:|
|**最佳模型註冊、部署、可解釋性**| ✓|✓|
|**啟用投票集團 & stack 集團模型**| ✓|✓|
|**根據非主要計量顯示最佳模型**|✓||
|**啟用/停用 ONNX 模型相容性**|✓||
|**測試模型** | ✓| |

### <a name="run-control-settings"></a>回合控制設定

這些設定可讓您檢閱及控制您的實驗回合及其子回合。 

| |Python SDK|Studio Web 體驗|
|----|:----:|:----:|
|**回合摘要資料表**| ✓|✓|
|**取消執行 & 子執行**| ✓|✓|
|**取得護欄**| ✓|✓|
|**暫停 & 繼續執行**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML 與 ONNX

使用 Azure Machine Learning，您可以使用自動化 ML 來建置 Python 模型，並將其轉換成 ONNX 格式。 一旦模型採用 ONNX 格式，就可以在各種不同的平台和裝置上執行。 深入了解[使用 ONNX 加速 ML 模型](concept-onnx.md)。

[在此 Jupyter 筆記本範例中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) \(英文\)，了解如何轉換為 ONNX 格式。 了解 [ONNX 支援哪些演算法](how-to-configure-auto-train.md#select-your-experiment-type)。

ONNX 執行階段也支援 C#，因此您可以使用在 C# 應用程式中自動建置的模型，而無需重新編碼或 REST 端點引入的任何網路延遲。 深入了解[使用 ONNX 執行階段 C# API 推斷 ONNX 模型](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md) \(英文\)。 

## <a name="next-steps"></a>後續步驟

有多個資源可讓您開始使用 AutoML。 

### <a name="tutorials-how-tos"></a>教學課程/how-to
教學課程是 AutoML 案例的端對端入門範例。
+ **如需程式碼優先體驗**，請遵循 [教學課程：使用 Azure Machine Learning Python SDK 自動定型回歸模型](tutorial-auto-train-models.md)。

 + **如需低或無程式碼的體驗**，請參閱 [教學課程：使用 Azure Machine Learning STUDIO 建立自動化 ML 分類模型](tutorial-first-experiment-automated-ml.md)。

How to 文章提供 AutoML 所提供之功能的其他詳細資料。 例如， 

+ 設定自動定型實驗的設定
    + 在 Azure Machine Learning Studio 中，[使用這些步驟](how-to-use-automated-ml-for-ml-models.md)。 
    + 針對 Python SDK，[使用這些步驟](how-to-configure-auto-train.md)。

+  瞭解如何使用時間序列資料，以及 [這些步驟](how-to-auto-train-forecast.md)來自動定型。

### <a name="jupyter-notebook-samples"></a>Jupyter 筆記本範例 

請參閱 GitHub 筆記本存放庫中的詳細程式碼範例和使用案例， [以取得自動化機器學習範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)。

### <a name="python-sdk-reference"></a>Python SDK 參考

使用 [AutoML 類別參考檔](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true)，加深 SDK 設計模式與類別規格的專業知識。 

> [!Note]
> 自動化的機器學習功能也可在其他 Microsoft 解決方案中使用，例如 [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、 [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、 [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) 和 [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)

