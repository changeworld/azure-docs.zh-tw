---
title: 什麼是自動 ML / 自動ML
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習如何自動為您選擇演演演算法,並從中生成模型,以便使用提供的參數和條件為模型選擇最佳演演演算法來節省時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082004"
---
# <a name="what-is-automated-machine-learning-automl"></a>什麼是自動機器學習 (自動ML)?

自動化機器學習,也稱為自動 ML 或 AutoML,是機器學習模型開發中耗時的反覆運算任務的自動化過程。 它允許數據科學家、分析師和開發人員在保持模型品質的同時,構建具有高規模、高效率和生產力的 ML 模型。 自動化ML是基於我們[微軟研究部門](https://arxiv.org/abs/1705.05355)的突破。

傳統的機器學習模型開發是資源密集型的,需要大量的領域知識和時間來生成和比較數十種模型。 通過自動化機器學習,您可以輕鬆、高效地加快獲得生產就緒 ML 模型所需的時間。

## <a name="when-to-use-automl-classify-regression--forecast"></a>何時使用自動ML:分類、回歸、&预测

當您希望 Azure 機器學習使用指定的目標指標為您訓練和調整模型時,應用自動 ML。 自動化 ML 使機器學習模型開發過程民主化,並使用戶能夠識別端到端機器學習管道以找出任何問題,無論其數據科學專業知識如何。

跨行業的數據科學家、分析師和開發人員可以使用自動 ML:
+ 無需豐富的程式設計知識即可實施 ML 解決方案
+ 節省時間與資源
+ 利用資料科學最佳實務
+ 提供敏捷問題解決

### <a name="classification"></a>分類

分類是一項常見的機器學習任務。 分類是一種監督學習,其中模型使用培訓數據學習,並將這些學習應用於新數據。 Azure 機器學習專門針對這些任務提供壯舉,例如用於分類的深層神經網路文本函數。 瞭解有關[壯舉選項](how-to-use-automated-ml-for-ml-models.md#featurization)的詳細資訊。 

分類模型的主要目標是根據培訓數據中的學習來預測新數據將屬於哪些類別。 常見的分類示例包括欺詐檢測、手寫識別和對象檢測。  瞭解更多資訊,並查看[自動機器學習的分類](tutorial-train-models-with-aml.md)範例。

請參考 Python 筆記型電腦中的分類與自動機器學習範例:[詐騙偵](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)測、[行銷預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)和[新聞組資料類別](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)

### <a name="regression"></a>迴歸
與分類類似,回歸任務也是一項常見的受監督學習任務。 Azure 機器學習[專為這些任務提供壯舉](how-to-use-automated-ml-for-ml-models.md#featurization)。

回歸模型不同於預測值為分類的分類,回歸模型基於獨立預測變數預測數值。 在回歸中,目標是通過估計一個變數如何影響其他變數來説明建立這些獨立預測變數之間的關係。 例如,汽車價格基於功能,如汽油里程,安全評級等。 瞭解更多,並查看[自動機器學習的回歸](tutorial-auto-train-models.md)示例。

有關這些 Python 筆記本中的預測,請參閱回歸和自動機器學習的範例[:CPU 性能預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>時間序列預測

構建預測是任何業務不可或缺的一部分,無論是收入、庫存、銷售還是客戶需求。 您可以使用自動 ML 組合技術和方法,並獲得推薦的高品質時間序列預測。 通過此"手":[用於時間序列預測的自動機器學習](how-to-auto-train-forecast.md),瞭解更多資訊。 

自動時間序列實驗被視為多變數回歸問題。 過去的時間序列值被「透視」,成為回歸者與其他預測變數的其他維度。 與傳統的時間序列方法不同,這種方法具有在訓練期間自然地合併多個上下文變數及其相互關係的優點。 自動 ML 為資料集和預測視野中的所有項學習單個但通常是內部分支模型。 因此,有更多的數據可用於估計模型參數,並可以對看不見的系列進行概括。

進階預測設定包括:
* 假日檢測和壯舉
* 時間序列和 DNN 學員(自動 ARIMA、先知、預測TCN)
* 許多模型透過分組支援
* 捲動源交叉驗證
* 可設定延遲
* 捲動視窗集合功能


有關這些 Python 筆記本中的預測,請參閱回歸和自動機器學習的範例:[銷售預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)、[需求預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)和[飲料生產預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

## <a name="how-automl-works"></a>自動ML的工作原理

在培訓期間,Azure 機器學習會創建多個並行管道,嘗試不同的演演演算法和參數。 該服務通過 ML 演演演算法與要素選擇配對進行反覆運算,其中每個反覆運算生成具有訓練分數的模型。 分數越高,模型就越能"適合"您的數據。  一旦達到實驗中定義的退出條件,它將停止。 

使用**Azure 機器學習**,可以使用以下步驟設計和運行自動 ML 培訓實驗:

1. **確定**要解決的 ML 問題:分類、預測或回歸

1. **選擇是使用 Python SDK 還是工作室 Web 體驗**:瞭解[Python SDK 和工作室 Web 體驗](#parity)之間的奇偶校驗。

   * 要獲得有限或無代碼體驗,請嘗試 Azure 機器學習工作室 Web 體驗,[https://ml.azure.com](https://ml.azure.com/)  
   * 對於 Python 開發人員,請檢視[Azure 機器學習 Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **指定標記的訓練資料的來源與格式**:數位陣元或熊貓資料框

1. **設定模型培訓的計算目標**,例如[本地電腦、Azure 機器學習計算、遠端 VM 或 Azure 資料塊](how-to-set-up-training-targets.md)。  瞭解[遠端資源的](how-to-auto-train-remote.md)自動培訓。

1. **設定自動機器學習參數**,以確定不同模型、超參數設置、高級預處理/預處理次數化的反覆運算次數,以及確定最佳模型時要查看的指標。  
1. **提交培訓運行。**

1. **檢閱結果** 

下圖說明此程序。 
![自動機器學習](./media/concept-automated-ml/automl-concept-diagram2.png)


您還可以檢查紀錄的執行資訊,其中包含在執行過程中收集的[指標](how-to-understand-automated-ml.md)。 訓練執行生成包含模型和數據預處理的`.pkl`Python 序列化物件(檔案)。

雖然模型構建是自動化的,但您還可以瞭解生成的模型[的重要性或相關功能。](how-to-configure-auto-train.md#explain)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前置處理

在每個自動機器學習實驗中,您的數據都使用預設方法進行預處理,並選擇性地通過高級預處理進行。

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="automatic-preprocessing-standard"></a>自動預處理 (標準)

在每個自動機器學習實驗中,您的數據都會自動縮放或規範化,以説明演演演算法執行良好性能。  在模型培訓期間,以下縮放或規範化技術之一將應用於每個模型。

|&nbsp;&調整&nbsp;調整化| 描述 |
| ------------- | ------------- |
| [標準尺規包裝器](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 以刪除均值並將縮放為單位方差來標準化要素  |
| [明馬克斯斯卡拉爾](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通過縮放每個要素,按該列的最小和最大值來轉換要素  |
| [馬克斯卡斯比例尺](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |依其最大絕對值縮放每個要素 |
| [堅固的斯卡爾](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此縮放器按其分位數範圍進行功能 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |線性維數降低使用奇異值分解資料並將其投影到較低維度空間 |
| [截斷SVD包裝器](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |該變壓器通過截斷的單數值分解 (SVD) 實現線性維數降低。 與 PCA 相反,此估計器在計算奇異值分解之前不會將資料居中,這意味著它可以有效地使用 scipy.稀疏矩陣 |
| [稀疏規範化器](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每個樣本(即資料矩陣的每一行)至少有一個非零分量,獨立於其他樣本重新縮放,以便其規範 (l1 或 l2) 等於一個 |

### <a name="advanced-preprocessing--featurization"></a>先進的預處理&壯舉

還提供其他高級預處理和壯舉化,如數據護欄、編碼和轉換。 [詳細瞭解包含哪些壯舉](how-to-use-automated-ml-for-ml-models.md#featurization)。 使用:

+ Azure 機器學習工作室:[使用這些步驟](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)在 **'查看其他設定**' 部分中啟用**自動工作化**。

+ Python SDK:`"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`為[`AutoMLConfig`類別](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)指定 。 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>工作室與 SDK

瞭解透過 Python SDK 提供的進階自動 ML 功能與 Azure 機器學習中的工作室之間的奇偶校驗和差異。 

### <a name="experiment-settings"></a>實驗設定 

以下設定允許您設定自動 ML 實驗。 

| |Python SDK|工作室網路體驗|
----|:----:|:----:
將資料拆分為訓練/驗證集| ✓|✓
支援 ML 工作:分類、回歸和預測| ✓| ✓
基於主要指標進行最佳化| ✓| ✓
支援 AML 計算為計算目標 | ✓|✓
設定預測範圍、目標滯後&滾動視窗|✓|✓
設定離開準則 |✓|✓ 
設定並發反覆運算| ✓|✓
刪除欄位| ✓|✓
塊演演算法|✓|✓
交叉驗證 |✓|✓
支援 Azure 資料磚塊群集的培訓| ✓|
檢視專案特徵名稱|✓|
技術化摘要| ✓|
假期的節制|✓|
紀錄檔案詳細程度等級| ✓|

### <a name="model-settings"></a>型號設定

這些設置可以應用於最佳模型,這是自動 ML 實驗的結果。

| |Python SDK|工作室網路體驗|
|----|:----:|:----:|
|最佳模型註冊、部署、可解釋性| ✓|✓|
|開啟投票群組&堆疊集合模型| ✓|✓|
|基於非主指標顯示最佳模型|✓||
|開啟/關閉 ONNX 模型相容性|✓||
|測試模型 | ✓| |

### <a name="run-control-settings"></a>執行控制設定

這些設置允許您查看和控制實驗運行及其子運行。 

| |Python SDK|工作室網路體驗|
|----|:----:|:----:|
|執行摘要表| ✓|✓|
|取消執行&子執行| ✓|✓|
|取得護欄| ✓|✓|
|暫停&繼續執行| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>組合模型

自動機器學習支援默認情況下啟用的合奏模型。 與使用單一模型相比,組合學習通過組合多個模型來提高機器學習結果和預測性能。 集合反覆運算顯示為運行的最後反覆運算。 自動機器學習使用投票和堆疊組合方法組合模型:

* **投票**:根據預測類概率(分類任務)或預測回歸目標的加權平均值(對於回歸任務)進行預測。
* **堆疊**:堆疊結合了異構模型,並根據各個模型的輸出訓練元模型。 當前預設元模型是分類任務的"邏輯回歸"和"回歸/預測任務彈性網"

[Caruana 組合選擇演演演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)具有排序的集合初始化,用於決定在集合中使用哪些模型。 在高級別上,此演演演算法使用最多五個具有最佳個人分數的模型初始化了該集合,並驗證這些模型是否在最佳分數的 5% 閾值內,以避免初始集合不佳。 然後,對於每個集合反覆運算,將新模型添加到現有集合中,並計算結果分數。 如果新模型改進了現有的合奏分數,則將更新該集合以包括新模型。

請參考自動機器學習中改變預設集合設定的[操作操作](how-to-configure-auto-train.md#ensemble)。

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>自動ML & ONNX

使用 Azure 機器學習,可以使用自動 ML 構建 Python 模型並將其轉換為 ONNX 格式。 模型採用 ONNX 格式後,可以在各種平台和設備上運行。 瞭解有關使用[ONNX 加速 ML 模型](concept-onnx.md)的更多資訊。

讓您如何轉換到 ONNX 格式[在這個Jupyter 筆記的範例](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)。 瞭解[ONNX 中支援哪些演演算法](how-to-configure-auto-train.md#select-your-experiment-type)。

ONNX 執行時還支援 C#,因此您可以使用在 C# 應用中自動建構的模型,而無需重新編碼或 REST 終結點引入的任何網路延遲。 瞭解有關使用[ONNX 執行時 C# API 推斷 ONNX 模型](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md)的更多資訊。 



## <a name="next-steps"></a>後續步驟

檢視範例並瞭解如何使用自動機器學習建構模型:

+ 遵循[教學:使用 Azure 機器學習自動訓練回歸模型](tutorial-auto-train-models.md)

+ 設定自動訓練實驗的設定:
  + 在 Azure 機器學習工作室中,[使用這些步驟](how-to-use-automated-ml-for-ml-models.md)。
  + 使用 Python SDK,[請使用這些步驟](how-to-configure-auto-train.md)。

+ 瞭解如何使用時間序列資料自動訓練,[請使用這些步驟](how-to-auto-train-forecast.md)。

+ 嘗試[聚居筆記本示例,實現自動機器學習](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* 自動 ML 還可用於其他[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)Microsoft[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)解決方案,如[ML.NET、HDInsight、Power](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和 SQL 伺服器
