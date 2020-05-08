---
title: 什麼是自動化 ML/AutoML
titleSuffix: Azure Machine Learning
description: 瞭解 Azure Machine Learning 如何自動為您挑選演算法，並使用您提供的參數和條件來為您的模型產生模型，以節省您的時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: ce51a1b25453a5bbacbd268b37f2bd21cfe37fea
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983460"
---
# <a name="what-is-automated-machine-learning-automl"></a>何謂自動化機器學習（AutoML）？

自動化機器學習服務（也稱為自動化 ML 或 AutoML）是將機器學習模型開發的耗時、反復工作自動化的程式。 它可讓資料科學家、分析師和開發人員以高擴充性、效率和生產力來建立 ML 模型，同時維持模型品質。 自動化 ML 是以[Microsoft Research 部門](https://arxiv.org/abs/1705.05355)的突破性為基礎。

傳統機器學習模型的開發會耗用大量資源，因此需要大量的領域知識和時間來產生和比較數十種模型。 透過自動化機器學習，您可以加快取得生產環境就緒的 ML 模型所需的時間，並提供絕佳的輕鬆和效率。

## <a name="when-to-use-automl-classify-regression--forecast"></a>使用 AutoML 的時機：分類、回歸、& 預測

當您想要 Azure Machine Learning 使用您指定的目標度量來為您定型和調整模型時，請套用自動化 ML。 自動化 ML 會促進大眾化機器學習模型開發程式，並讓其使用者無論其資料科學專業知識，都能找出任何問題的端對端機器學習管線。

跨產業的資料科學家、分析師和開發人員可以使用自動化 ML 來執行下列動作：
+ 不需要豐富的程式設計知識即可執行 ML 解決方案
+ 節省時間和資源
+ 運用資料科學最佳做法
+ 提供敏捷式問題解決

### <a name="classification"></a>分類

分類是一項常見的機器學習工作。 分類是一種監督式學習，其中的模型會學習使用定型資料，並將這些學習套用至新的資料。 Azure Machine Learning 特別針對這些工作提供 featurizations，例如分類的深度類神經網路文字有。 深入瞭解[特徵化選項](how-to-use-automated-ml-for-ml-models.md#featurization)。 

分類模型的主要目標是要根據學習的定型資料，預測哪些類別的新資料會落入何處。 常見的分類範例包括詐騙偵測、手寫辨識和物件偵測。  深入瞭解並查看[使用自動化機器學習進行分類](tutorial-train-models-with-aml.md)的範例。

請參閱下列 Python 筆記本中的分類和自動化機器學習的範例：[詐騙偵測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)、[行銷預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)和[新聞群組資料分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>迴歸
與分類類似，回歸工作也是常見的監督式學習工作。 Azure Machine Learning 會[特別針對這些](how-to-use-automated-ml-for-ml-models.md#featurization)工作提供 featurizations。

不同于預測輸出值為類別的分類，回歸模型會根據獨立的預測指標來預測數值輸出值。 在回歸中，其目標是要藉由評估一個變數對其他變數的影響，協助您建立這些獨立預測變數之間的關聯性。 例如，以諸如的功能、天然氣、安全性評等等特徵為依據的汽車價格。 深入瞭解並查看[自動化機器學習的回歸](tutorial-auto-train-models.md)範例。

請參閱下列 Python 筆記本中預測的回歸和自動化機器學習範例： [CPU 效能預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)、 

### <a name="time-series-forecasting"></a>時間序列預測

建立預測是任何企業不可或缺的一部分，不論是收入、庫存、銷售或客戶需求。 您可以使用自動化 ML 結合技術和方法，並取得建議的高品質時間序列預測。 深入瞭解如何：[自動化機器學習以進行時間序列預測](how-to-auto-train-forecast.md)。 

自動化的時間序列實驗會被視為多變數回歸問題。 過去的時間序列值會「切換」為回歸輸入變數的其他維度與其他預測指標。 與傳統時間序列方法不同的是，這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 自動化 ML 會針對資料集和預測視野中的所有專案學習單一但通常會在內部分支的模型。 因此，有更多的資料可用於估計模型參數，而一般化的數列則會變成可行的。

先進的預測設定包括：
* 假日偵測和特徵化
* 時間序列和 DNN 學習工具（自動 ARIMA、Prophet、ForecastTCN）
* 許多模型都支援透過群組
* 復原原始的交叉驗證
* 可設定延遲
* 滾動視窗匯總功能


請參閱下列 Python 筆記本中的預測回歸和自動化機器學習的範例：[銷售預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)、[需求預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)和[飲料生產預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

## <a name="how-automl-works"></a>AutoML 的運作方式

在定型期間，Azure Machine Learning 會以平行方式建立多個管線，以便為您嘗試不同的演算法和參數。 服務會逐一查看與特徵選取配對的 ML 演算法，其中每個反復專案都會產生具有定型分數的模型。 分數越高，模型就越能「配合」您的資料。  一旦達到實驗中定義的結束準則，它就會停止。 

使用**Azure Machine Learning**，您可以使用下列步驟來設計和執行自動化 ML 訓練實驗：

1. **找出要解決的 ML 問題**：分類、預測或回歸

1. **選擇您想要使用 PYTHON sdk 還是 studio web 體驗**：瞭解[python sdk 與 studio web 體驗](#parity)之間的同位檢查。

   * 針對有限或無程式碼體驗，請嘗試 Azure Machine Learning studio web 體驗，網址為：[https://ml.azure.com](https://ml.azure.com/)  
   * 針對 Python 開發人員，請參閱[Azure Machine Learning PYTHON SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **指定標示定型資料的來源和格式**： Numpy 陣列或 Pandas 資料框架

1. **設定用於模型定型的計算目標**，例如[本機電腦、Azure Machine Learning 計算、遠端 vm 或 Azure Databricks](how-to-set-up-training-targets.md)。  瞭解[遠端資源](how-to-auto-train-remote.md)的自動化訓練。

1. **設定自動化機器學習參數**，以決定在決定最佳模型時，要在不同模型、超參數設定、先進前置處理/特徵化，以及要查看的度量有多少次。  
1. **提交定型回合。**

1. **檢閱結果** 

下圖說明此程序。 
![自動化機器學習服務](./media/concept-automated-ml/automl-concept-diagram2.png)


您也可以檢查已記錄的執行資訊，其中包含執行期間所收集的[計量](how-to-understand-automated-ml.md)。 定型回合會產生 Python 序列化物件（`.pkl` file），其中包含模型和資料前置處理。

雖然模型建立是自動化的，您也可以瞭解產生之模型的[重要或相關功能](how-to-configure-auto-train.md#explain)。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前置處理

在每個自動化機器學習實驗中，您的資料會使用預設方法進行前置處理，並選擇性地透過 advanced 前置處理。

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="automatic-preprocessing-standard"></a>自動前置處理（標準）

在每個自動化機器學習實驗中，您的資料都會自動調整或正規化，以協助演算法執行得很好。  在模型定型期間，下列其中一個調整或正規化技術將會套用至每個模型。

|&nbsp; &調整&nbsp;正規化| 描述 |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 藉由移除平均值並調整為單位變異數，將功能標準化  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 藉由依該資料行的最小值和最大值調整每項功能來轉換功能  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |依最大絕對值來調整每個功能 |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此 Scaler 功能的分量範圍 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |線性維度會使用資料的單一值分解，將其投影至較低的維度空間 |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |這個轉換器會藉由截斷的單值分解（SVD）來執行線性維度縮減。 相對於 PCA，此估計工具不會在計算單數值分解之前，先將資料置中，這表示它可以有效率地使用 scipy. sparse 矩陣 |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 具有至少一個非零元件的每個範例（也就是資料矩陣的每個資料列）重新調整獨立于其他範例，使其標準（l1 或 l2）等於一 |

### <a name="advanced-preprocessing--featurization"></a>Advanced 前置處理 & 特徵化

另外還提供額外的先進前置處理和特徵化，例如資料護欄、編碼和轉換。 [深入瞭解包含的特徵化](how-to-use-automated-ml-for-ml-models.md#featurization)。 啟用此設定的方式：

+ Azure Machine Learning studio：在 [ **View 其他**設定] 區段中[使用下列步驟](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)啟用**自動特徵化**。

+ Python SDK：指定`"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` [ `AutoMLConfig`類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)的。 



## <a name="ensemble-models"></a><a name="ensemble"></a>集團模型

自動化機器學習支援預設會啟用的集團模型。 集團 learning 藉由結合多個模型來改善機器學習結果和預測效能，而不是使用單一模型。 集團反復專案會顯示為您執行的最後一次反覆運算。 自動化機器學習會使用投票和堆疊集團方法來結合模型：

* **投票**：根據預測類別機率（適用于分類工作）或預測回歸目標（用於回歸工作）的加權平均值進行預測。
* **堆疊**：堆疊結合了異類模型，並根據個別模型的輸出來訓練中繼模型。 目前的預設中繼模型是針對分類工作和回歸/預測工作的 ElasticNet 而 LogisticRegression 的。

具有已排序集團初始化的[Caruana 集團選取演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)會用來決定要在集團中使用的模型。 就高層級而言，此演算法會使用最多五個具有最佳個別分數的模型來初始化集團，並確認這些模型是在最佳分數的5% 臨界值內，以避免發生不佳的初始集團。 然後針對每個集團反復專案，將新的模型加入至現有的集團，並計算產生的分數。 如果新模型已改善現有的集團分數，集團會更新以包含新的模型。

請參閱[如何](how-to-configure-auto-train.md#ensemble)變更自動化機器學習服務中的預設集團設定。

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>本機與遠端受控 ML 計算目標的指引

自動化 ML 的 web 介面一律會使用遠端[計算目標](concept-compute-target.md)。  但當您使用 Python SDK 時，您會選擇本機計算或遠端計算目標來進行自動化 ML 訓練。

* **本機計算**：在您的本機膝上型電腦或 VM 計算上進行定型。 
* **遠端計算**：在 Machine Learning 計算叢集上進行定型。  

### <a name="choose-compute-target"></a>選擇計算目標
選擇您的計算目標時，請考慮下列因素：

 * **選擇本機計算**：如果您的案例是關於使用小型資料和簡短訓練（也就是每個子回合的秒數或幾分鐘）的初始探勘或示範，則本機電腦上的訓練可能是較佳的選擇。  沒有設定時間，可以直接使用基礎結構資源（您的電腦或 VM）。
 * **選擇遠端 ML 計算**叢集：如果您要使用較大的資料集進行定型，例如在生產訓練中建立需要較長訓練的模型，則遠端計算會提供更好的`AutoML`端對端時間效能，因為會在叢集的節點上平行處理訓練。 在遠端計算上，內部基礎結構的啟動時間大約會加上每個子回合1.5 分鐘，而如果 Vm 尚未啟動並執行，叢集基礎結構也會增加額外的分鐘數。

### <a name="pros-and-cons"></a>優缺點
選擇使用本機與遠端時，請考慮這些優缺點。

|  | 優點（優缺點）  |缺點（Handicaps）  |
|---------|---------|---------|---------|
|**本機計算目標** |  <li> 沒有任何環境啟動時間   | <li>  功能子集<li>  無法平行執行 <li> 更糟的是大型資料。 <li>定型時沒有資料串流 <li>  沒有以 DNN 為基礎的特徵化 <li> 僅適用于 Python SDK |
|**遠端 ML 計算叢集**|  <li> 完整的功能集 <li> 平行處理子執行 <li>   大型資料支援<li>  以 DNN 為基礎的特徵化 <li>  視需要計算叢集的動態擴充性 <li> 無程式碼體驗（web UI）也可供使用  |  <li> 叢集節點的啟動時間 <li> 每個子回合的啟動時間    |

### <a name="feature-availability"></a>功能可用性 

 當您使用遠端計算時，會有更多的功能，如下表所示。 其中一些功能僅適用于企業工作區。

| 功能                                                    | 遠端 | 本機 | 具備 <br>企業工作區 |
|------------------------------------------------------------|--------|-------|-------------------------------|
| 資料串流（大型資料支援，最多 100 GB）          | ✓      |       | ✓                             |
| DNN-經理 BERT 為基礎的文字特徵化和訓練             | ✓      |       | ✓                             |
| 現成的 GPU 支援（訓練和推斷）        | ✓      |       | ✓                             |
| 影像分類和標籤支援                  | ✓      |       | ✓                             |
| 自動 ARIMA、Prophet 和 ForecastTCN 模型以進行預測 | ✓      |       | ✓                             |
| 以平行方式執行多個回合/反復專案                       | ✓      |       | ✓                             |
| 在 AutoML studio web 體驗 UI 中使用 interpretability 建立模型      | ✓      |       | ✓                             |
| Studio web 體驗 UI 中的功能工程自訂                        | ✓      |       | ✓                              |
| Azure ML 超參數微調                             | ✓      |       |                               |
| Azure ML 管線工作流程支援                         | ✓      |       |                               |
| 繼續執行                                             | ✓      |       |                               |
| 預測                                                | ✓      | ✓     | ✓                             |
| 在筆記本中建立及執行實驗                    | ✓      | ✓     |                               |
| 在 UI 中註冊實驗的資訊和計量並加以視覺化 | ✓      | ✓     |                               |
| 資料護欄                                            | ✓      | ✓     |                               |


## <a name="automated-ml-in-azure-machine-learning"></a>Azure Machine Learning 中的自動化 ML

Azure Machine Learning 提供兩種使用自動化 ML 的體驗

* 適用于程式碼經驗的客戶， [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 適用于有限/無程式碼體驗客戶，Azure Machine Learning studio[https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>實驗設定 

下列設定可讓您設定自動化 ML 實驗。 

| |Python SDK|Studio web 體驗|
----|:----:|:----:
將資料分割成定型/驗證集| ✓|✓
支援 ML 工作：分類、回歸和預測| ✓| ✓
根據主要度量優化| ✓| ✓
支援 AML 計算作為計算目標 | ✓|✓
設定預測水準，目標延遲 & 滾動時間範圍|✓|✓
設定結束準則 |✓|✓ 
設定並行反覆運算| ✓|✓
卸載資料行| ✓|✓
封鎖演算法|✓|✓
交叉驗證 |✓|✓
支援在 Azure Databricks 叢集上進行訓練| ✓|
查看設計的功能名稱|✓|
特徵化摘要| ✓|
假日的特徵化|✓|
記錄檔詳細資訊層級| ✓|

### <a name="model-settings"></a>模型設定

這些設定可套用至最佳模型，做為自動化 ML 實驗的結果。

| |Python SDK|Studio web 體驗|
|----|:----:|:----:|
|最佳模型註冊、部署、可解釋性| ✓|✓|
|啟用投票集團 & stack 集團模型| ✓|✓|
|根據非主要度量顯示最佳模型|✓||
|啟用/停用 ONNX 模型相容性|✓||
|測試模型 | ✓| |

### <a name="run-control-settings"></a>執行控制設定

這些設定可讓您審查及控制您的實驗執行及其子執行。 

| |Python SDK|Studio web 體驗|
|----|:----:|:----:|
|執行摘要資料表| ✓|✓|
|取消執行 & 子執行| ✓|✓|
|取得護欄| ✓|✓|
|暫停 & 繼續執行| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

使用 Azure Machine Learning，您可以使用自動化 ML 來建立 Python 模型，並將它轉換成 ONNX 格式。 一旦模型採用 ONNX 格式，就可以在各種不同的平臺和裝置上執行。 深入瞭解[使用 ONNX 加速 ML 模型](concept-onnx.md)。

請參閱[此 Jupyter 筆記本範例中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)的如何轉換為 ONNX 格式。 瞭解[ONNX 中支援的演算法](how-to-configure-auto-train.md#select-your-experiment-type)。

ONNX 執行時間也支援 c #，因此，您可以使用在 c # 應用程式中自動建立的模型，而不需要進行任何預先編碼或 REST 端點所引進的任何網路延遲。 深入瞭解[使用 ONNX Runtime c # API 推斷 ONNX 模型](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)。 

## <a name="next-steps"></a>後續步驟

請參閱範例，並瞭解如何使用自動化機器學習來建立模型：

+ 設定自動訓練實驗的設定：
  + 在 Azure Machine Learning studio 中，請[使用下列步驟](how-to-use-automated-ml-for-ml-models.md)。
  + 透過 Python SDK，請[使用下列步驟](how-to-configure-auto-train.md)。

+ 瞭解如何使用[遠端計算目標](how-to-auto-train-remote.md)

+ 遵循[教學課程：使用 Azure Machine Learning 自動定型回歸模型](tutorial-auto-train-models.md) 

+ 若要瞭解如何使用時間序列資料來自動定型，請[使用下列步驟](how-to-auto-train-forecast.md)。

+ 試用[自動化機器學習 Jupyter Notebook 範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)
* 自動化 ML 也適用于其他 Microsoft 解決方案，例如[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)、 [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md)、 [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
