---
title: 自動訓練時間序列預測模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure 機器學習使用自動機器學習訓練時間序列預測回歸模型。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 9f80156f61ad82e5563f1c38764c81297f5979f2
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767310"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自動訓練時間序列預測模型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中,您將瞭解如何在 Azure 機器學習中使用自動機器學習訓練時間序列預測回歸模型。 配置預測模型類似於使用自動機器學習設置標準回歸模型,但存在某些配置選項和預處理步驟,用於使用時間序列數據。 以下範例簡報如何:

* 準備時間序列模型資料
* 在[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)物件中設定特定的時間序列參數
* 使用時間序列資料執行預測

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

您可以使用自動 ML 組合技術和方法,並獲得推薦的高品質時間序列預測。 自動時間序列實驗被視為多變數回歸問題。 過去的時間序列值被「透視」,成為回歸者與其他預測變數的其他維度。

與傳統的時間序列方法不同,這種方法具有在訓練期間自然地合併多個上下文變數及其相互關係的優點。 在實際預測應用中,多種因素會影響預測。 例如,在預測銷售時,歷史趨勢、匯率和價格的相互作用共同推動銷售結果。 另一個好處是,回歸模型的所有近期創新立即應用於預測。

您可以[配置](#config)預測在未來應擴展的有多遠(預測範圍)以及滯後等。 自動 ML 為資料集和預測視野中的所有項學習單個但通常是內部分支模型。 因此,有更多的數據可用於估計模型參數,並可以對看不見的系列進行概括。

從訓練數據中提取的功能起著至關重要的作用。 此外,自動 ML 執行標準預處理步驟並生成其他時間序列功能,以捕獲季節性影響並最大限度地提高預測準確性。

## <a name="time-series-and-deep-learning-models"></a>時間序列與深度學習模型


自動 ML 為使用者提供本機時間序列和深度學習模型,作為推薦系統的一部分。 這些學員包括:
+ 先知(預覽)
+ 自動 ARIMA(預覽版)
+ 預測TCN(預覽版)

自動 ML 的深入學習允許預測單變數和多變數時間序列數據。

深度學習模型具有三種內在功能:
1. 他們可以從從輸入到輸出的任意映射中學習
1. 它們支援多個輸入及輸出
1. 他們可以自動擷取跨越長序列的輸入資料中的模式

給定更大的數據,深度學習模型(如 Microsoft 的預測 TCN)可以提高結果模型的分數。 瞭解如何[為深度學習設定實驗](#configure-a-dnn-enable-forecasting-experiment)。

本機時間序列學員也作為自動 ML 的一部分提供。 先知最適合具有強烈季節性效果和幾個季節的歷史數據的時間序列。 先知是準確的&快速,強健的離群值,缺少的數據,和戲劇性的變化,在你的時間序列。 

自動回歸綜合移動平均線(ARIMA)是時間序列預測的常用統計方法。 這種預測技術通常用於短期預測方案,其中數據顯示了週期等趨勢的證據,這些趨勢可能是不可預測的,難以建模或預測。 自動 ARIMA 將數據轉換為固定數據,以接收一致、可靠的結果。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 要建立工作區,請參閱[建立 Azure 機器學習工作區](how-to-manage-workspace.md)。
* 本文假定對設置自動機器學習實驗基本熟悉。 請按照[教程](tutorial-auto-train-models.md)或[操作操作瞭解如何](how-to-configure-auto-train.md)查看基本的機器學習實驗設計模式。

## <a name="preparing-data"></a>準備資料

自動機器學習中預測回歸任務類型和回歸任務類型之間的最重要的區別是在數據中包含表示有效時間序列的功能。 常規時間序列具有定義良好且一致的頻率,並且具有連續時間跨度中每個採樣點的值。 請考慮檔的`sample.csv`以下快照。

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

此資料集是具有兩個不同商店 A 和 B 的公司每日銷售數據的簡單示例。 此外`week_of_year`,還有一項功能 ,允許模型檢測每周的季節性。 該欄位`day_datetime`表示具有每日頻率的乾淨時間序列,該欄`sales_quantity`位 是運行預測的目標列。 將資料讀取到 Pandas 資料框中,`to_datetime`然後使用 函數確保時間`datetime`序列是一種類型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在這種情況下,數據已按時間欄位`day_datetime`排序升序。 但是,在設置實驗時,請確保按升序對所需的時間列進行排序以生成有效的時間序列。 假設數據包含 1,000 條記錄,並在數據中進行確定性拆分以創建訓練和測試數據集。 標識標籤列名稱並將其設置為標籤。 這個選項, 這個標籤會為`sales_quantity`。 然後從中分離標籤欄位`test_data`以`test_target`形成 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 在訓練預測未來值的模型時,請確保在為預期視點運行預測時可以使用訓練中使用的所有功能。 例如,在創建需求預測時,包括當前股票價格的功能可能會大大提高培訓的準確性。 但是,如果您打算以長視距進行預測,則可能無法準確預測與未來時間序列點對應的未來股票價值,並且模型準確性可能會受到影響。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>設定與執行實驗

對於預測任務,自動化機器學習使用特定於時間序列數據的預處理和估計步驟。 將執行以下預處理步驟:

* 檢測時間序列採樣頻率(例如,每小時、每天、每周),併為缺勤時間點創建新記錄,以使序列連續。
* 在目標(透過前位填充)和要素列(使用中位列值)中,估計缺失值
* 建立基於顆粒的功能,以跨不同系列實現固定效果
* 建立基於時間的功能,以幫助學習季節性模式
* 將分類變數編碼為數值數量

該[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)物件定義自動機器學習任務所需的設置和數據。 與回歸問題類似,您可以定義標準訓練參數,如任務類型、反覆運算次數、訓練數據和交叉驗證數。 對於預測任務,必須設置影響實驗的其他參數。 下表解釋了每個參數及其用法。

| 參數&nbsp;名稱 | 描述 | 必要 |
|-------|-------|-------|
|`time_column_name`|用於在用於生成時間序列和推斷其頻率的輸入數據中指定日期時間列。|✓|
|`grain_column_names`|名稱)在輸入數據中定義單個系列組。 如果未定義顆粒,則假定數據集為一個時間序列。||
|`max_horizon`|以時間序列頻率單位定義所需的最大預測視點。 單位基於訓練數據的時間間隔,例如,預測員應預測的每月、每周。|✓|
|`target_lags`|根據數據頻率滯後目標值的行數。 滯後表示為清單或單個整數。 當獨立變數和因變數之間的關係在預設情況下不匹配或關聯時,應使用延遲。 例如,在嘗試預測產品需求時,任何月份的需求都可能取決於 3 個月前特定商品的價格。 在此示例中,您可能希望將目標(需求)負延遲 3 個月,以便模型正在訓練正確的關係。||
|`target_rolling_window_size`|*n*用於生成預測值的歷史記錄期間,<= 訓練集大小。 如果省略 *,n*是完整的訓練集大小。 在訓練模型時只想考慮一定數量的歷史記錄時,指定此參數。||
|`enable_dnn`|啟用預測 DNN。||

有關詳細資訊,請參閱[參考文件](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

將時間序列設置創建為字典物件。 將`time_column_name`設置為資料集`day_datetime`中的欄位。 定義`grain_column_names`參數以確保為資料建立**兩個單獨的時間序列群組**;一個用於儲存 A 和`max_horizon`B。 最後,將設定為 50 以預測整個測試集。 使用將預測視窗設置為 10`target_rolling_window_size`個期間`target_lags`,並在 參數之前的兩個期間指定目標值的單個延遲。 建議設置`max_horizon``target_rolling_window_size``target_lags`和 「自動」,這將自動檢測這些值。 在下面的示例中,這些參數使用了"自動"設置。 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

通過在上面的代碼`grain_column_names`段中定義,AutoML 將創建兩個單獨的時間序列組,也稱為多個時間序列。 如果未定義顆粒,AutoML 將假定數據集是單個時間序列。 要瞭解有關單一時間序列的更多詳細資訊,請參閱[energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

現在建立一個`AutoMLConfig`標準物件,`forecasting`指定任務類型,並提交實驗。 模型完成後,檢索最佳運行反覆運算。

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

有關高級預測配置的詳細代碼示例,請參閱[預測示例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning),包括:

* [假日檢測和壯舉](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [捲動源交叉驗證](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可設定延遲](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [捲動視窗集合功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [平台](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>設定 DNN 啟用預測實驗

> [!NOTE]
> 對自動機器學習中預測的 DNN 支援處於預覽狀態,不支援本地運行。

為了利用 DN 進行預測,您需要將自動MLConfig`enable_dnn`中的參數設定為 true。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
瞭解有關[自動MLConfig](#configure-and-run-experiment)的更多。

或者,您也可以在`Enable deep learning`工作室中選擇該選項。
![替代文字](./media/how-to-auto-train-forecast/enable_dnn.png)

我們建議使用具有 GPU SKU 和至少兩個節點的 AML 計算群集作為計算目標。 為了留出足夠的時間完成 DNN 培訓,我們建議將實驗超時設置為至少幾個小時。
有關包括 GPU 的 AML 計算和 VM 大小的詳細資訊,請參閱[AML 計算文件](how-to-set-up-training-targets.md#amlcompute)與[GPU 最佳化的虛擬機器大小文件](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

查看[飲料生產預測筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb),瞭解利用 DN 的詳細代碼示例。

### <a name="view-feature-engineering-summary"></a>檢視要素專案摘要

對於自動機器學習中的時間序列任務類型,您可以查看要素工程流程的詳細資訊。 以下代碼顯示每個原始特徵以及以下屬性:

* 原始要素名稱
* 由此原始功能形成的工程特徵數量
* 偵測為的類型
* 是否刪除功能
* 原始要素的功能轉換清單

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>使用最佳模型進行預測

使用最佳模型反覆運算預測測試數據集的值。

應`forecast()`使用函數而不是`predict()`,這將允許預測何時開始。 在下面的範例中,您要將中`y_pred`的值取代為`NaN`。 在這種情況下,預測源將在培訓數據結束時,就像使用`predict()`時一樣。 但是,如果僅替換`y_pred``NaN`的後半部分,函數將保留前半部分的數值未修改,但預測下半年`NaN`的值。 函數返回預測值和對齊的要素。

您還可以使用函數`forecast_destination``forecast()`中的 參數預測值,直到指定日期。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

計算`actual_labels`實際值和`predict_labels`中的預測值之間的 RMSE(根均方位誤差)。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

現在,總體模型精度已經確定,最現實的下一步是使用模型預測未知的未來值。 以與測試集`test_data`相同的格式提供數據集,但提供將來的日期時間,生成的預測集是每個時間序列步驟的預測值。 假設數據集中的最後一個時間序列記錄為 2018 年 12 月 31 日。 要預測第二天的需求(或需要預測的多個期間,<`max_horizon`= ),請為 2019 年 1 月 1 日為每個商店創建單個時間序列記錄。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重複必要的步驟,將將來的數據載入到資料幀中,然後運行`best_run.predict(test_data)`以預測未來值。

> [!NOTE]
> 對於大於的`max_horizon`期間數,無法預測值。 模型必須重新訓練,具有更大的視野,以預測當前地平線以外的未來值。

## <a name="next-steps"></a>後續步驟

* 請按照[本教程](tutorial-auto-train-models.md)瞭解如何使用自動機器學習創建實驗。
* 查看[Azure 機器學習 SDK 以查看 Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考文檔。
