---
title: 什麼是自動 ML / 自動ML
titleSuffix: Azure Machine Learning
description: 瞭解 Azure 機器學習如何自動為您選擇演算法，並從中生成模型，以便使用提供的參數和條件為模型選擇最佳演算法來節省時間。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 02/28/2020
ms.openlocfilehash: c8864e00be9f491d87478c253286070b9334a26e
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383186"
---
# <a name="what-is-automated-machine-learning"></a>什麼是自動化機器學習服務？

自動化機器學習，也稱為自動化ML，是機器學習模型開發中耗時的反覆運算任務的自動化過程。 它允許資料科學家、分析師和開發人員在保持模型品質的同時，構建具有高規模、高效率和生產力的 ML 模型。 自動化ML是基於我們[微軟研究部門](https://arxiv.org/abs/1705.05355)的突破。

傳統的機器學習模型開發是資源密集型的，需要大量的領域知識和時間來生成和比較數十種模型。 通過自動化機器學習，您可以輕鬆、高效地加快獲得生產就緒 ML 模型所需的時間。

 


## <a name="when-to-use-automated-ml"></a>何時使用自動 ML

當您希望 Azure 機器學習使用指定的目標指標為您訓練和調整模型時，應用自動 ML。 自動化 ML 使機器學習模型開發過程民主化，並使使用者能夠識別端到端機器學習管道以找出任何問題，無論其資料科學專業知識如何。

跨行業的資料科學家、分析師和開發人員可以使用自動 ML：

+ 無需豐富的程式設計知識即可實施機器學習解決方案
+ 節省時間和資源
+ 利用資料科學最佳實踐
+ 提供敏捷問題解決

下表列出了常見的自動 ML 用例。 

分類| 時間序列預測 | 迴歸
---|---|---
[欺詐檢測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb)|[銷售預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb)|[CPU 性能預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)
|[行銷預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)|[需求預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)|
|[新聞群組資料分類](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb)|[飲料生產預測](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)|

## <a name="design-automated-ml-experiments"></a>設計自動化 ML 實驗

使用**Azure 機器學習**，可以使用以下步驟設計和運行自動 ML 培訓實驗：

1. **確定**要解決的 ML 問題：分類、預測或回歸

1. **指定標記的訓練資料的源和格式**：數位陣列或熊貓資料框

1. **配置模型培訓的計算目標**，例如[本地電腦、Azure 機器學習計算、遠端 VM 或 Azure 資料塊](how-to-set-up-training-targets.md)。  瞭解[遠端資源的](how-to-auto-train-remote.md)自動培訓。

1. **配置自動機器學習參數**，以確定不同模型、超參數設置、高級預處理/預處理次數化的反覆運算次數，以及確定最佳模型時要查看的指標。  您可以在[Azure 機器學習工作室](https://ml.azure.com)或 SDK 中配置自動訓練實驗[的](how-to-configure-auto-train.md)設置。 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]

1. **提交培訓運行。**

## <a name="how-automated-ml-works"></a>自動化 ML 的工作原理

在培訓期間，Azure 機器學習會創建許多並行管道，嘗試不同的演算法和參數。 該服務通過 ML 演算法與要素選擇配對進行反覆運算，其中每個反覆運算生成具有訓練分數的模型。 分數越高，模型就越能"適合"您的資料。  一旦達到實驗中定義的允出準則，它將停止。 下圖說明此程序。 

  ![自動機器學習](./media/concept-automated-ml/automl-concept-diagram2.png)


您還可以檢查記錄的運行資訊，其中包含在運行過程中收集的[指標](how-to-understand-automated-ml.md)。 訓練運行生成包含模型和資料預處理的`.pkl`Python 序列化物件（檔）。

雖然模型構建是自動化的，但您還可以瞭解生成的模型[的重要性或相關功能。](how-to-configure-auto-train.md#explain)

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>前置處理

在每個自動機器學習實驗中，您的資料都使用預設方法進行預處理，並選擇性地通過高級預處理進行。

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="automatic-preprocessing-standard"></a>自動預處理（標準）

在每個自動機器學習實驗中，您的資料都會自動縮放或正常化，以説明演算法執行良好性能。  在模型培訓期間，以下縮放或正常化技術之一將應用於每個模型。

|&nbsp; &縮放&nbsp;正常化| 描述 |
| ------------- | ------------- |
| [標準尺規包裝器](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | 通過刪除均值並將縮放為單位方差來標準化要素  |
| [明馬克斯斯卡拉爾](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | 通過縮放每個要素，按該列的最小和最大值來轉換要素  |
| [馬克斯卡斯比例尺](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |按其最大絕對值縮放每個要素 |
| [堅固的斯卡爾](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |此縮放器按其分位數範圍進行功能 |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |線性維數降低使用奇異值分解資料並將其投影到較低維度空間 |
| [截斷SVD包裝器](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |該變壓器通過截斷的單數值分解 （SVD） 實現線性維數降低。 與 PCA 相反，此估計器在計算奇異值分解之前不會將資料居中，這意味著它可以有效地使用 scipy.疏鬆陣列 |
| [稀疏正常化器](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | 每個樣本（即資料矩陣的每一行）至少有一個非零分量，獨立于其他樣本重新縮放，以便其規範 （l1 或 l2） 等於一個 |

### <a name="advanced-preprocessing-optional-featurization"></a>高級預處理：可選的工藝

還提供其他高級預處理和壯舉化，如資料護欄、編碼和轉換。 [詳細瞭解包含哪些壯舉](how-to-use-automated-ml-for-ml-models.md#featurization)。 使用：

+ Azure 機器學習工作室：[使用這些步驟](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)在 **"查看其他配置**"部分中啟用**自動任務化**。

+ Python SDK：`"feauturization": 'auto' / 'off' / 'FeaturizationConfig'`為[`AutoMLConfig`類](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)指定 。 

## <a name="classification--regression"></a>分類&回歸

分類和回歸是最常見的機器學習任務類型。 兩者都是監督學習的類型，其中模型使用培訓資料學習，並將這些學習應用於新資料。 Azure 機器學習專門針對這些任務提供壯舉，例如用於分類的深層神經網路文本函數。 瞭解有關[壯舉選項](how-to-use-automated-ml-for-ml-models.md#featurization)的詳細資訊。 

分類模型的主要目標是根據培訓資料中的學習來預測新資料將屬於哪些類別。 常見的分類示例包括欺詐檢測、手寫辨識和物件檢測。  瞭解更多資訊，並查看[自動機器學習的分類](tutorial-train-models-with-aml.md)示例。

回歸模型不同于預測值為分類的分類，回歸模型基於獨立預測變數預測數值。 在回歸中，目標是通過估計一個變數如何影響其他變數來説明建立這些獨立預測變數之間的關係。 例如，汽車價格基於功能，如汽油里程，安全評級等。 瞭解更多，並查看[自動機器學習的回歸](tutorial-auto-train-models.md)示例。

## <a name="time-series-forecasting"></a>時間序列預測

構建預測是任何業務不可或缺的一部分，無論是收入、庫存、銷售還是客戶需求。 您可以使用自動 ML 組合技術和方法，並獲得推薦的高品質時間序列預測。

自動時間序列實驗被視為多變數回歸問題。 過去的時間序列值被"透視"，成為回歸者與其他預測變數的其他維度。 與傳統的時間序列方法不同，這種方法具有在訓練期間自然地合併多個上下文變數及其相互關係的優點。 自動 ML 為資料集和預測視野中的所有項學習單個但通常是內部分支模型。 因此，有更多的資料可用於估計模型參數，並可以對看不見的系列進行概括。

瞭解更多，並查看[用於時間序列預測的自動機器學習](how-to-auto-train-forecast.md)示例。 或者，請參閱[能源需求筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)，瞭解高級預測配置的詳細代碼示例，包括：

* 假日檢測和壯舉
* 時間序列和 DNN 學員（自動 ARIMA、先知、預測TCN）
* 許多模型通過分組支援
* 滾動源交叉驗證
* 可配置延遲
* 滾動視窗聚合功能

## <a name="ensemble-models"></a><a name="ensemble"></a>組合模型

自動機器學習支援預設情況下啟用的合奏模型。 與使用單一模型相比，組合學習通過組合多個模型來提高機器學習結果和預測性能。 集合反覆運算顯示為運行的最後反覆運算。 自動機器學習使用投票和堆疊組合方法組合模型：

* **投票**：根據預測類概率（分類任務）或預測回歸目標的加權平均值（對於回歸任務）進行預測。
* **堆疊**：堆疊結合了異構模型，並根據各個模型的輸出訓練元模型。 當前預設元模型是分類任務的"邏輯回歸"和"回歸/預測任務彈性網"。

[Caruana 組合選擇演算法](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf)具有排序的集合初始化，用於決定在集合中使用哪些模型。 在高級別上，此演算法使用最多五個具有最佳個人分數的模型初始化了該集合，並驗證這些模型是否在最佳分數的 5% 閾值內，以避免初始集合不佳。 然後，對於每個集合反覆運算，將新模型添加到現有集合中，並計算結果分數。 如果新模型改進了現有的合奏分數，則將更新該集合以包括新模型。

請參閱自動機器學習中更改預設集合設置的[操作操作](how-to-configure-auto-train.md#ensemble)。

## <a name="use-with-onnx-in-c-apps"></a>在 C# 應用中與 ONNX 一起使用

使用 Azure 機器學習，可以使用自動 ML 構建 Python 模型並將其轉換為 ONNX 格式。 ONNX 運行時支援 C#，因此您可以使用在 C# 應用中自動構建的模型，而無需重新編碼或 REST 終結點引入的任何網路延遲。 試試[這個Jupyter筆記本中](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)這個流的例子。

## <a name="automated-ml-in-azure-machine-learning"></a>Azure 機器學習中的自動 ML

Azure 機器學習提供了兩種使用自動 ML 的體驗

* 對於代碼經驗豐富的客戶[，Azure 機器學習 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 

* 對於有限/無代碼體驗客戶，Azure 機器學習工作室[https://ml.azure.com](https://ml.azure.com/)  

下面總結了每種經驗中支援的高級別自動 ML 功能。

<a name="parity"></a>

### <a name="experiment-settings"></a>實驗設置 

以下設置允許您配置自動 ML 實驗。 

| | Python SDK| 演播室
----|:----:|:----:
將資料拆分為訓練/驗證集| ✓|✓
支援 ML 任務：分類、回歸和預測| ✓| ✓
基於主要指標進行優化| ✓| ✓
支援 AML 計算作為計算目標 | ✓|✓
配置預測範圍、目標滯後&滾動視窗|✓|✓
設置允出準則 |✓|✓ 
設置併發反覆運算| ✓|✓
刪除列| ✓|✓
塊演算法|✓|✓
交叉驗證 |✓|✓
支援 Azure 資料磚塊群集的培訓| ✓|
查看工程特徵名稱|✓|
技術化摘要| ✓|
假日壯舉|✓|
日誌檔的詳細程度級別| ✓|

### <a name="model-settings"></a>型號設置

這些設置可以應用於最佳模型，這是自動 ML 實驗的結果。

||Python SDK|演播室
----|:----:|:----:
最佳型號註冊| ✓|✓
最佳模型部署| ✓| ✓
最佳模型可解釋性| ✓|✓
啟用投票組&堆疊集合模型| ✓|✓
基於非主指標顯示最佳模型|✓|
啟用/禁用 ONNX 模型相容性|✓|
測試模型 | ✓| |

### <a name="run-control-settings"></a>運行控制設置

這些設置允許您查看和控制實驗運行及其子運行。 

||Python SDK| 演播室
----|:----:|:----:
運行摘要表| ✓|✓
取消運行| ✓|✓
取消子運行| ✓| ✓
獲取護欄| ✓|✓
暫停運行| ✓| 
恢復運行| ✓| 

## <a name="next-steps"></a>後續步驟

查看示例並瞭解如何使用自動機器學習構建模型：

+ 遵循[教程：使用 Azure 機器學習自動訓練回歸模型](tutorial-auto-train-models.md)

+ 配置自動訓練實驗的設置：
  + 在 Azure 機器學習工作室中，[使用這些步驟](how-to-use-automated-ml-for-ml-models.md)。
  + 使用 Python SDK，[請使用這些步驟](how-to-configure-auto-train.md)。

+ 瞭解如何使用時間序列資料自動訓練，[請使用這些步驟](how-to-auto-train-forecast.md)。

+ 嘗試[聚居筆記本示例，實現自動機器學習](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* 自動 ML 還可用於其他 Microsoft[SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)解決方案，如[ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview)[ML.NET、HDInsight、Power](../hdinsight/spark/apache-spark-run-machine-learning-automl.md) [BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated)和 SQL 伺服器
