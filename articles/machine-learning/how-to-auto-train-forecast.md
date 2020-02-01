---
title: 自動定型時間序列預測模型
titleSuffix: Azure Machine Learning
description: 瞭解如何使用 Azure Machine Learning，使用自動化機器學習來定型時間序列預測回歸模型。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f5bd6b741f85f35fe03c941ed09728354d6b3d2d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905711"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自動定型時間序列預測模型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將瞭解如何使用 Azure Machine Learning 中的自動化機器學習來定型時間序列預測回歸模型。 設定預測模型類似于使用自動化機器學習來設定標準回歸模型，但有一些特定的設定選項和前置處理步驟可用於處理時間序列資料。 下列範例示範如何：

* 準備資料以進行時間序列模型化
* 在[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)物件中設定特定的時間序列參數
* 使用時間序列資料執行預測

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

您可以使用自動化 ML 結合技術和方法，並取得建議的高品質時間序列預測。 自動化的時間序列實驗會被視為多變數回歸問題。 過去的時間序列值會「切換」為回歸輸入變數的其他維度與其他預測指標。

與傳統時間序列方法不同的是，這種方法的優點是在定型期間自然結合多個內容變數及其關聯性。 在真實世界的預測應用程式中，有多個因素會影響預測。 例如，當預測銷售時，歷程記錄趨勢的互動、匯率和價格全都會共同推動銷售結果。 另一個優點是回歸模型中所有最近的創新都適用于預測。

您可以[設定](#config)預測未來應延伸的時間（預測範圍），以及延遲和更多。 自動化 ML 會針對資料集和預測視野中的所有專案學習單一但通常會在內部分支的模型。 因此，有更多的資料可用於估計模型參數，而一般化的數列則會變成可行的。

從定型資料中解壓縮的功能扮演重要的角色。 而且，自動化 ML 會執行標準的前置處理步驟，並產生額外的時間序列功能來捕捉季節性效果並最大化預測準確度。

## <a name="time-series-and-deep-learning-models"></a>時間序列和深度學習模型


自動化 ML 會為使用者提供原生時間序列和深度學習模型，做為建議系統的一部分。 這些學習工具組括：
+ Prophet
+ 自動 ARIMA
+ ForecastTCN

自動化 ML 的深度學習可讓您預測單一變數和多變數時間序列資料。

深度學習模型有三種內建功能：
1. 他們可以從輸入到輸出的任意對應學習
1. 它們支援多個輸入和輸出
1. 它們可以自動將跨長序列的輸入資料中的模式解壓縮

針對較大的資料，深度學習模型（例如 Microsoft 的 ForecastTCN）可以改善產生之模型的分數。 

原生時間序列學習工具也會提供做為自動化 ML 的一部分。 Prophet 最適合用於具有強烈季節性效果的時間序列，以及數個季節的歷程記錄資料。 Prophet 是正確的 & 快速、健全的極端值、遺漏的資料，以及您的時間序列中的顯著變更。 

自動回歸整合式移動平均（ARIMA）是時間序列預測的熱門統計方法。 這項預測技術通常用於短期預測案例中，其中資料會顯示迴圈的辨識項，例如週期，這可能會造成無法預測，且難以建立模型或預測。 自動 ARIMA 會將您的資料轉換成固定資料，以接收一致且可靠的結果。

## <a name="prerequisites"></a>必要條件

* Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* 本文假設您對設定自動化機器學習實驗的基本概念十分熟悉。 依照[教學](tutorial-auto-train-models.md)課程或[如何](how-to-configure-auto-train.md)操作來查看基本的自動化機器學習實驗設計模式。

## <a name="preparing-data"></a>準備資料

自動化機器學習服務中的預測回歸工作類型和回歸工作類型之間最重要的差異，包括資料中代表有效時間序列的功能。 一般時間序列具有定義完善且一致的頻率，而且在連續時間範圍內的每個取樣點都有一個值。 請考慮下列檔案 `sample.csv`快照集。

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

此資料集是公司每日銷售資料的簡單範例，其中有兩個不同的商店 A 和 B。此外，還有一項 `week_of_year` 的功能，可讓模型偵測每週季節性。 欄位 `day_datetime` 代表具有每日頻率的清除時間序列，而欄位 `sales_quantity` 是執行預測的目標資料行。 將資料讀取至 Pandas 資料框架，然後使用 `to_datetime` 函數來確保時間序列是 `datetime` 類型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在此情況下，資料已經以 `day_datetime`的時間欄位昇冪排序。 不過，在設定實驗時，請確定所需的時間資料行是以遞增順序排序，以建立有效的時間序列。 假設資料包含1000記錄，並在資料中進行具決定性的分割，以建立定型和測試資料集。 識別 [標籤] 資料行名稱，並將它設定為 [標籤]。 在此範例中，標籤將會 `sales_quantity`。 然後，將 標籤 欄位與 `test_data` 分隔，以形成 `test_target` 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 定型模型以預測未來的值時，請確定在針對您想要的水準執行預測時，可以使用定型中使用的所有功能。 例如，建立需求預測時，包括目前股價的功能可能會大幅增加定型準確度。 不過，如果您想要長期預測，可能無法精確地預測未來時間序列點對應的未來股價，而且模型精確度可能會受到影響。

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>設定及執行實驗

針對預測工作，自動化機器學習會使用時間序列資料特有的前置處理和估計步驟。 將會執行下列前置處理步驟：

* 偵測時間序列取樣頻率（例如，每小時、每天、每週），並建立不存在時間點的新記錄，讓數列持續進行。
* 插補目標中的遺漏值（透過向前填滿）和特徵資料行（使用中位數的資料行值）
* 建立以細微性為基礎的功能，以啟用跨不同數列的固定效果
* 建立以時間為基礎的功能，以協助學習季節性模式
* 將類別變數編碼為數值數量

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)物件會定義自動化機器學習工作所需的設定和資料。 類似于回歸問題，您可以定義標準訓練參數，例如工作類型、反復專案數目、定型資料，以及交叉驗證的數目。 針對預測工作，還有一些必須設定的參數會影響實驗。 下表說明每個參數和其使用方式。

| Param | 說明 | 必要項 |
|-------|-------|-------|
|`time_column_name`|用來指定輸入資料中用來建立時間序列並推斷其頻率的日期時間資料行。|✓|
|`grain_column_names`|在輸入資料中定義個別數列群組的名稱。 如果未定義細微性，則會假設資料集為一個時間序列。||
|`max_horizon`|以時間序列頻率的單位，定義所需的預測範圍上限。 單位是以定型資料的時間間隔為基礎，例如，每月、每週 forecaster 應預測。|✓|
|`target_lags`|要根據資料頻率延後目標值的資料列數目。 Lag 會以清單或單一整數來表示。 當獨立變數與相依變數之間的關聯性預設不相符或相互關聯時，應該使用 Lag。 例如，當您嘗試預測產品的需求時，任何月份的需求可能取決於之前3個月的特定商品價格。 在此範例中，您可能會想要讓目標（需求）對3個月造成負面的延遲，讓模型在正確的關聯性上定型。||
|`target_rolling_window_size`|*n*要用來產生預測值的歷程記錄期間，< = 定型集大小。 如果省略，則*n*是完整的定型集大小。 當您只想要在定型模型時考慮特定數量的歷程記錄時，請指定此參數。||
|`enable_dnn`|啟用預測 Dnn。||

如需詳細資訊，請參閱[參考檔](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

將時間序列設定建立為 dictionary 物件。 將 [`time_column_name`] 設定為資料集內的 [`day_datetime`] 欄位。 定義 `grain_column_names` 參數，確保針對資料建立**兩個不同的時間序列群組**：一個用於儲存 A 和 B。最後，將 `max_horizon` 設定為50，以便預測整個測試集。 使用 `target_rolling_window_size`將 [預測] 視窗設定為10個週期，並在 [`target_lags`] 參數前面指定兩個週期的目標值的單一 lag。 建議將 `max_horizon`、`target_rolling_window_size` 和 `target_lags` 設定為 [自動]，這會自動為您偵測這些值。 在下列範例中，這些參數已使用「自動」設定。 

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

藉由定義上述程式碼片段中的 `grain_column_names`，AutoML 會建立兩個不同的時間序列群組，也稱為多個時間序列。 如果未定義任何細微性，則 AutoML 會假設資料集為單一時間序列。 若要深入瞭解單一時間序列，請參閱[energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

現在建立標準 `AutoMLConfig` 物件，指定 `forecasting` 工作類型，並提交實驗。 在模型完成之後，請抓取最佳的執行反復專案。

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

如需先進預測設定的詳細程式碼範例，請參閱[預測範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，包括：

* [假日偵測和特徵化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [復原原始的交叉驗證](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可設定延遲](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滾動視窗匯總功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>設定 DNN 啟用預測實驗

> [!NOTE]
> 自動化 Machine Learning 中預測的 DNN 支援目前處於預覽階段。

為了利用 Dnn 進行預測，您必須將 AutoMLConfig 中的 `enable_dnn` 參數設定為 true。 

若要使用 Dnn，我們建議搭配使用 AML 計算叢集與 GPU Sku，並使用至少兩個節點做為計算目標。 如需詳細資訊，請參閱[AML 計算檔](how-to-set-up-training-targets.md#amlcompute)。 如需包含 Gpu 之 VM 大小的詳細資訊，請參閱[GPU 優化虛擬機器大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

若要讓 DNN 訓練有足夠的時間完成，建議您將實驗時間設定為至少幾個小時。

### <a name="view-feature-engineering-summary"></a>視圖功能工程摘要

如需自動化機器學習服務中的時間序列工作類型，您可以從功能工程處理常式中查看詳細資料。 下列程式碼會顯示每個原始功能以及下列屬性：

* 原始功能名稱
* 從這個原始功能形成的工程功能數目
* 偵測到的類型
* 是否已卸載功能
* 原始功能的功能轉換清單

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>使用最佳模型進行預測

使用最佳模型反復專案來預測測試資料集的值。

```python
predict_labels = fitted_model.predict(test_data)
actual_labels = test_labels.flatten()
```

或者，您可以使用 `forecast()` 函式，而不是 `predict()`，這會允許預測應該啟動的規格。 在下列範例中，您會先將 `y_pred` 中的所有值取代為 `NaN`。 在此情況下，預測來源會在定型資料的結尾，這通常是使用 `predict()`時。 不過，如果您只以 `NaN`取代後半個 `y_pred`，則函式會將前半個未修改的數值保留，但在後半部中預測 `NaN` 的值。 函式會傳回預測的值和對齊的功能。

您也可以使用 `forecast()` 函數中的 `forecast_destination` 參數，在指定的日期之前預測值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

在 `actual_labels` 實際值和 `predict_labels`中的預測值之間計算 RMSE （根平均平方誤差）。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

現在已決定整體模型精確度，最實際的下一個步驟是使用模型來預測未知的未來值。 以與測試集相同的格式提供資料集 `test_data` 但未來日期時間，而產生的預測集則是每個時間序列步驟的預測值。 假設資料集內的最後一個時間序列記錄是12/31/2018。 若要預測下一天的需求（或需要預測的期間數，請 < = `max_horizon`），為每個商店建立單一時間序列記錄01/01/2019。

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

重複必要的步驟，將未來的資料載入至資料框架，然後執行 `best_run.predict(test_data)` 以預測未來的值。

> [!NOTE]
> 無法預測大於 `max_horizon`的期間數的值。 模型必須以較大的範圍重新定型，以預測目前範圍以外的未來值。

## <a name="next-steps"></a>後續步驟

* 請遵循[本教學](tutorial-auto-train-models.md)課程，以瞭解如何使用自動化機器學習來建立實驗。
* 查看[適用于 Python 的 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)參考檔。
