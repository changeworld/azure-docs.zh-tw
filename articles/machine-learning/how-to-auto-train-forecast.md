---
title: 將時間序列預測模型自動定型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Machine Learning，以利用自動化機器學習來定型時間序列預測迴歸模型。
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: 72b0a3074bfdfb6b6038f6c63eb01a7b33d45ea6
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.contentlocale: zh-TW
ms.lasthandoff: 07/05/2020
ms.locfileid: "85959121"
---
# <a name="auto-train-a-time-series-forecast-model"></a>將時間序列預測模型自動定型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，您將了解如何使用 [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py) 中的自動化機器學習，以設定時間序列預測迴歸模型並將其定型。 

如需低程式碼體驗，請參閱[教學課程：使用自動化機器學習來預測需求](tutorial-automated-ml-forecast.md)，以取得在 [Azure Machine Learning 工作室](https://ml.azure.com/)中使用自動化機器學習的時間序列預測範例。

設定預測模型類似於使用自動化機器學習來設定標準迴歸模型，但有一些特定的設定選項和前置處理步驟可用於處理時間序列資料。 

例如，您可[設定](#config)預測應擴展到未來何時 (預測範圍)，以及延隔時間等。 自動化 ML 會針對資料集和預測範圍中的所有項目，學習單一但通常是內部分支的模型。 因此，可使用更多資料來估計模型參數，且可推廣到看不見的序列。

下列範例會示範如何操作：

* 準備資料以進行時間序列模型化
* 在 [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 物件中設定特定的時間序列參數
* 使用時間序列資料執行預測

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

不同於傳統的時間序列方法，在自動化 ML 中，過去的時間序列值會進行「樞紐處理」，以與其他預測變量一起成為迴歸輸入變數的附加維度。 這個方法會在定型期間結合多個內容變數及其相互關聯性。 由於有多個因素可能會影響預測，因此此方法非常吻合真實世界的預測案例。 例如，當預測銷售時，歷程記錄趨勢、匯率和價格的相互作用全都會共同推動銷售結果。 

從定型資料中擷取的特徵扮演重要角色。 而且，自動化 ML 會執行標準的前置處理步驟並產生額外時間序列特徵，以取得季節性影響並最大程度地提高預測精確度

## <a name="time-series-and-deep-learning-models"></a>時間序列和深度學習模型

自動化 ML 的深度學習可供預測單一變量和多變量時間序列資料。

深度學習模型有三種內建功能：
1. 可從輸入到輸出的任意對應中學習
1. 支援多個輸入和輸出
1. 可從跨越較長序列的輸入資料中自動擷取模式

針對較大資料，深度學習模型 (例如 Microsoft 的 ForecastTCN) 可提高所產生模型的分數。 了解如何[設定實驗以進行深度學習](#configure-a-dnn-enable-forecasting-experiment)。

自動化 ML 會為使用者提供原生時間序列和深度學習模型來作為建議系統的一部分。 

模型| 描述 | 優點
----|----|---
Prophet (預覽)|Prophet 最適合用於具有強烈季節性影響，且包含數個季節歷程記錄資料的時間序列。 若要利用此模型，請使用將它安裝在本機 `pip install fbprophet` 。 | 精確且快速，能夠應付時間序列中的極端值、遺失資料及重大變更。
自動 ARIMA (預覽)|自動迴歸整合式移動平均 (ARIMA) 在資料為靜態時的執行效果最佳。 這表示其統計屬性 (如平均值和變異數) 在整個集合上是常數。 例如，若投擲一枚硬幣，則不論您是今天、明天還是明年投擲，出現正面的機率都是 50%。| 由於過去值是用來預測未來值，因此非常適用於單一變量序列。
ForecastTCN (預覽)| ForecastTCN 是一種神經網路模型，其設計目的是要處理最嚴苛的預測工作，並擷取資料中的非線性本機和全球趨勢，以及時間序列之間的關聯性。|能夠運用資料中的複雜趨勢，並配合最大的資料集立即調整。

## <a name="prerequisites"></a>Prerequisites

* Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。
* 本文假設基本上已熟悉如何設定自動化機器學習實驗。 請遵循[教學課程](tutorial-auto-train-models.md)或[操作說明](how-to-configure-auto-train.md)，以查看基本的自動化機器學習實驗設計模式。

## <a name="preparing-data"></a>準備資料

自動化機器學習中預測迴歸工作類型與迴歸工作類型之間最重要的差異在於，資料中包括代表有效時間序列的特徵。 一般時間序列具有妥善定義且一致的頻率，且在連續時間範圍內的每個取樣點都有一個值。 請考慮 `sample.csv` 檔案的下列快照集。

```output
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
```

此資料集是某家公司每日銷售資料的簡單範例，該公司擁有 A 和 B 兩間不同的商店。此外，還有一項 `week_of_year` 的特徵，可讓模型偵測每週的季節性。 欄位 `day_datetime` 代表具有每日頻率的乾淨時間序列，而欄位 `sales_quantity` 則是執行預測的目標資料行。 請將資料讀取至 Pandas 資料框架，然後使用 `to_datetime` 函式來確保時間序列是 `datetime` 類型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在此情況下，資料已經依時間欄位 `day_datetime` 遞增排序。 不過，在設定實驗時，請確定所需的時間資料行是以遞增順序排序，以建置有效的時間序列。 假設資料包含 1,000 筆記錄，並在資料中進行具決定性的分割，以建立定型和測試資料集。 請識別標籤資料行的名稱，並將其設定為標籤。 在此範例中，標籤將會是 `sales_quantity`。 然後，將標籤欄位從 `test_data` 區隔出來，以形成 `test_target` 集合。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> 將模型定型以預測未來值時，請確定在針對想要的範圍執行預測時，可使用定型中使用的所有特徵。 例如，建立需求預測時，包括目前股價的特徵可能會大幅增加定型準確度。 不過，如果想要預測較長範圍的情況，則可能無法精確地預測與未來時間序列點對應的未來股價值，且模型精確度可能會受到影響。

<a name="config"></a>

## <a name="train-and-validation-data"></a>訓練和驗證資料
您可直接在 `AutoMLConfig` 建構函式中指定個別的定型和驗證集。

### <a name="rolling-origin-cross-validation"></a>移動原點交叉驗證
針對時間序列預測，會使用移動原點交叉驗證 (ROCV)，以暫時一致的方式分割時間序列。 ROCV 會使用原始時間點來將序列分割成定型和驗證資料。 滑動時間原點即會產生交叉驗證摺疊。  

![替代文字](./media/how-to-auto-train-forecast/ROCV.svg)

此策略會保留時間序列資料完整性，並消除資料外洩的風險。 藉由將定型和驗證資料一起傳遞，並使用 `n_cross_validations` 設定交叉驗證摺疊數目，ROCV 就會自動用於預測工作。 深入了解自動 ML 如何套用交叉驗證以[防止過度調整模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
深入了解 [AutoMLConfig](#configure-and-run-experiment)。

## <a name="configure-and-run-experiment"></a>設定及執行實驗

針對預測工作，自動化機器學習會使用時間序列資料特定的前置處理和估計步驟。 將會執行下列前置處理步驟：

* 偵測時間序列取樣頻率 (例如，每小時、每天、每週)，並為不存在的時間點建立新記錄，使序列連續不斷。
* 插補目標中的遺漏值 (透過向前填滿)，以及插補特徵資料行中的遺漏值 (使用中位數的資料行值)
* 建立粒紋型特徵，以啟用跨不同序列的固定效果
* 建立時間型特徵，以協助學習季節性模式
* 將類別變數編碼為數值數量

[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 物件會定義自動化機器學習工作所需的設定和資料。 類似於迴歸問題，您可定義標準定型參數，例如工作類型、反覆項目數目、定型資料，以及交叉驗證的數目。 針對預測工作，還有一些必須設定的參數會影響實驗。 下表說明每個參數及其使用方式。

| 參數名稱&nbsp; | 描述 | 必要 |
|-------|-------|-------|
|`time_column_name`|用來指定輸入資料中用來建置時間序列並推斷其頻率的日期時間資料行。|✓|
|`grain_column_names`|在輸入資料中定義個別序列群組的名稱。 如果未定義粒紋，則會假設資料集為一個時間序列。||
|`max_horizon`|以時間序列頻率的單位，定義所需的預測範圍上限。 單位是以預測器應預測出的定型資料時間間隔為基礎，例如，每月、每週。|✓|
|`target_lags`|要根據資料頻率延隔目標值的資料列數目。 延隔會以清單或單一整數來表示。 當獨立變數與相依變數之間的關聯性預設不相符或相互關聯時，應該使用延隔。 例如，當嘗試預測產品的需求時，任何月份中需求可能取決於之前 3 個月的特定商品價格。 在此範例中，建議讓目標 (需求) 延隔 3 個月，以供模型以正確的關聯性來定型。||
|`target_rolling_window_size`|要用來產生預測值的 *n* 個歷程記錄週期，小於或等於定型集大小。 如果省略，則 *n* 就是完整的定型集大小。 若在將模型定型時只想考慮特定數量的歷程記錄，則請指定此參數。||
|`enable_dnn`|啟用預測 DNN 功能。||

如需詳細資訊，請參閱[參考文件](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)。

將這些時間序列設定建立為字典物件。 將 `time_column_name` 設定為資料集內的 `day_datetime` 欄位。 定義 `grain_column_names` 參數，以確保為資料建立**兩個個別的時間序列群組**；一個用於儲存 A 和 B。最後，將 `max_horizon` 設定為 50，以針對整個測試集進行預測。 使用 `target_rolling_window_size` 將預測時段設定為 10 個週期，並使用 `target_lags` 參數對前面兩個週期的目標值指定單一延隔。 建議將 `max_horizon`、`target_rolling_window_size` 和 `target_lags` 設定為 "auto"，如此其即會自動偵測這些值。 在下列範例中，這些參數已使用 "auto" 設定。 

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

藉由在上述程式碼片段中定義 `grain_column_names`，AutoML 會建立兩個不同的時間序列群組，也稱為多個時間序列。 如果未定義任何粒紋，則 AutoML 會假設資料集為單一時間序列。 若要深入了解單一時間序列，請參閱 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。

現在，請建立標準 `AutoMLConfig` 物件，並指定 `forecasting` 工作類型，然後提交實驗。 在模型完成之後，請擷取最佳的執行反覆項目。

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

如需進階預測設定的詳細程式碼範例，請參閱[預測範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，包括：

* [假日偵測和特徵化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [移動原點交叉驗證](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可設定延隔](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [移動時段彙總功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>設定 DNN 啟用預測實驗

> [!NOTE]
> 自動化機器學習中對預測的 DNN 支援處於預覽階段，且不支援本機執行。

為了利用 DNN 進行預測，您必須將 AutoMLConfig 中的 `enable_dnn` 參數設定為 true。 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
深入了解 [AutoMLConfig](#configure-and-run-experiment)。

或者，您也可以在 Studio 中選取 `Enable deep learning` 選項。
![替代文字](./media/how-to-auto-train-forecast/enable_dnn.png)

建議使用 AML 計算叢集搭配 GPU SKU，並使用至少兩個節點作為計算目標。 若要讓 DNN 定型有足夠的時間完成，建議將實驗逾時設定為至少幾個小時。
如需包含 GPU 的 AML 電腦及 VM 大小詳細資訊，請參閱 [AML 計算文件](how-to-set-up-training-targets.md#amlcompute)和 [GPU 最佳化虛擬機器大小文件](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。

如需運用 DNN 的詳細程式碼範例，請參閱[飲料生產預測筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

### <a name="target-rolling-window-aggregation"></a>目標移動時段彙總
預測器可擁有的最佳資訊通常是目標其最新值。 建立目標的累計統計資料可能會提高預測精確度。 目標移動時段彙總可供將資料值的移動彙總作為特徵新增。 若要啟用目標移動時段，請將 `target_rolling_window_size` 設為想要的整數時段大小。 

當預測能源需求時，可看到這種情況的範例。 您可新增三天的移動時段特徵，將加熱空間的熱變化納入考量。 在下列範例中，我們會藉由在 `AutoMLConfig` 建構函式中設定 `target_rolling_window_size=3`，以建立大小為三的這個時段。 此資料表會顯示套用時段彙總時所發生的特徵工程。 最小值、最大值和總和資料行是根據在三個滑動時段上產生的已定義設定。 每個資料列都有新的計算特徵，如果是 2017 年 9 月 8 日上午 4:00 的時間戳記，則會使用 2017 年 9 月 8 日上午 1:00 到上午 3:00 的需求值來計算最大值、最小值和總和。 這三個時段會移位以在剩餘的資料列中填入資料。

![替代文字](./media/how-to-auto-train-forecast/target-roll.svg)

產生和使用這些額外特徵作為額外的內容資料，可協助提高定型模型的精確度。

請檢視運用[目標移動時段彙總特徵](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 程式碼範例。

### <a name="view-feature-engineering-summary"></a>檢視特徵工程摘要

如需自動化機器學習中的時間序列工作類型，您可從特徵工程處理序中檢視詳細資料。 下列程式碼會顯示每個原始特徵以及下列屬性：

* 原始特徵名稱
* 從這個原始特徵形成的工程特徵數目
* 偵測到的類型
* 是否已捨棄特徵
* 原始特徵的特徵轉換清單

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>使用最佳模型進行預測

使用最佳模型反覆項目來預測測試資料集的值。

應該使用 `forecast()` 函式而不是 `predict()`，這會允許指定應於何時開始預測。 在下列範例中，您會先將 `y_pred` 中的所有值取代為 `NaN`。 在此情況下，預測原點會在定型資料的結尾，這通常與使用 `predict()` 時一樣。 不過，如果只以 `NaN` 取代 `y_pred` 的後半部分，則此函式不會修改前半部分的數值，但會預測後半部分的 `NaN` 值。 此函式會傳回預測的值和調整後的特徵。

您也可以使用 `forecast()` 函式中的 `forecast_destination` 參數，以預測直到指定日期之前的值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

計算 `actual_labels` 實際值與 `predict_labels` 中預測值之間的 RMSE (均方根誤差)。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

現在已決定整體模型精確度，接著下一個最實際步驟是使用模型來預測未知的未來值。 如果使用與測試集 `test_data` 相同的格式來提供資料集，但具有未來的日期時間，則所產生預測集是每個時間序列步驟的預測值。 假設資料集內的最後一個時間序列記錄是 2018 年 12 月 31 日。 若要預測隔天的需求 (或需要預測的週期數，小於或等於 `max_horizon`)，請為每間商店建立 2019 年 1 月 1 日的單一時間序列記錄。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

重複必要的步驟，將此未來資料載入至資料框架，然後執行 `best_run.predict(test_data)` 來預測未來值。

> [!NOTE]
> 週期數若大於 `max_horizon`，則無法預測值。 若要預測目前範圍以外的未來值，模型必須以較大的範圍來重新定型。

## <a name="next-steps"></a>後續步驟

* 遵循[教學課程](tutorial-auto-train-models.md)以了解如何使用自動化機器學習來建立實驗。
* 檢視[適用於 Python 的 Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 參考文件。
