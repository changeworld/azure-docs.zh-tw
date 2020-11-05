---
title: 將時間序列預測模型自動定型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Machine Learning，以利用自動化機器學習來定型時間序列預測迴歸模型。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 3be1d404d0cac7f9e5c9b1c2f7350cf05c5fe794
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358111"
---
# <a name="auto-train-a-time-series-forecast-model"></a>將時間序列預測模型自動定型


在本文中，您將瞭解如何在 [Azure Machine Learning PYTHON SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py)中使用自動化機器學習服務（AutoML）來設定和定型時間序列預測回歸模型。 

若要這樣做，您可以： 

> [!div class="checklist"]
> * 準備資料以進行時間序列模型。
> * 在物件中設定特定的時間序列參數 [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 。
> * 使用時間序列資料執行預測。

如需低程式碼體驗，請參閱[教學課程：使用自動化機器學習來預測需求](tutorial-automated-ml-forecast.md)，以取得在 [Azure Machine Learning 工作室](https://ml.azure.com/)中使用自動化機器學習的時間序列預測範例。

不同于傳統的時間序列方法，在自動化 ML 中，過去的時間序列值會「轉換」為回歸輸入變數與其他預測指標的額外維度。 這個方法會在定型期間結合多個內容變數及其相互關聯性。 由於有多個因素可能會影響預測，因此此方法非常吻合真實世界的預測案例。 例如，當預測銷售時，歷程記錄趨勢、匯率和價格的相互作用全都會共同推動銷售結果。 

## <a name="prerequisites"></a>先決條件

針對本文，您需要 

* Azure Machine Learning 工作區。 若要建立工作區，請參閱[建立 Azure Machine Learning 工作區](how-to-manage-workspace.md)。

* 本文假設您已熟悉如何設定自動化機器學習實驗。 遵循 [教學](tutorial-auto-train-models.md) 課程或操作 [說明](how-to-configure-auto-train.md) ，查看主要的自動化機器學習實驗設計模式。

## <a name="preparing-data"></a>準備資料

在 AutoML 中，預測回歸工作類型與回歸工作類型之間最重要的差異，在於您資料中的一項功能，代表有效的時間序列。 一般時間序列具有妥善定義且一致的頻率，且在連續時間範圍內的每個取樣點都有一個值。 

請考慮 `sample.csv` 檔案的下列快照集。
此資料集是具有兩個不同商店 A 和 B 之公司的每日銷售資料。 

此外，還有一些功能

 *  `week_of_year`：可讓模型偵測每週季節性。
* `day_datetime`：表示具有每日頻率的清除時間序列。
* `sales_quantity`：執行預測的目標資料行。 

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


請將資料讀取至 Pandas 資料框架，然後使用 `to_datetime` 函式來確保時間序列是 `datetime` 類型。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在此情況下，資料已經依時間欄位 `day_datetime` 遞增排序。 不過，在設定實驗時，請確定所需的時間資料行是以遞增順序排序，以建置有效的時間序列。 

下列程式碼： 
* 假設資料包含1000記錄，並在資料中進行決定性分割，以建立定型和測試資料集。 
* 將標籤資料行識別為 `sales_quantity` 。
* 分隔標籤欄位 `test_data` 以形成 `test_target` 集合。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> 將模型定型以預測未來值時，請確定在針對想要的範圍執行預測時，可使用定型中使用的所有特徵。 <br> <br>例如，建立需求預測時，包括目前股價的特徵可能會大幅增加定型準確度。 不過，如果想要預測較長範圍的情況，則可能無法精確地預測與未來時間序列點對應的未來股價值，且模型精確度可能會受到影響。

<a name="config"></a>

## <a name="training-and-validation-data"></a>訓練和驗證資料

您可以直接在物件中指定個別的定型和驗證集 `AutoMLConfig` 。   深入了解 [AutoMLConfig](#configure-experiment)。

針對時間序列預測，根據預設，只有輪流 **來源交叉驗證 (ROCV)** 用於驗證。 將定型和驗證資料一起傳遞，並使用中的參數設定交叉驗證折迭數目 `n_cross_validations` `AutoMLConfig` 。 ROCV 會使用原始時間點來將序列分割成定型和驗證資料。 滑動時間原點即會產生交叉驗證摺疊。 此策略可保留時間序列資料的完整性，並消除資料洩漏的風險

![滾動原始來源交叉驗證](./media/how-to-auto-train-forecast/ROCV.svg)

您也可以攜帶自己的驗證資料，深入瞭解在 [AutoML 中設定資料分割和交叉驗證](how-to-configure-cross-validation-data-splits.md#provide-validation-data)。


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

深入瞭解 AutoML 如何套用交叉驗證，以 [防止過度調整的模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。

## <a name="configure-experiment"></a>設定實驗

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) 物件會定義自動化機器學習工作所需的設定和資料。 預測模型的設定類似于設定標準回歸模型，但特定的模型、設定選項和特徵化步驟則特別存在於時間序列資料中。 

### <a name="supported-models"></a>支援的模型
自動化機器學習會在建立和微調程式的過程中，自動嘗試不同的模型和演算法。 身為使用者，您不需要指定演算法。 針對預測實驗，原生時間序列和深度學習模型都屬於建議系統的一部分。 下表摘要說明此模型子集。 

>[!Tip]
> 傳統的回歸模型也會測試為建議系統的一部分，以進行預測實驗。 如需完整的模型清單，請參閱 [支援的模型資料表](how-to-configure-auto-train.md#supported-models) 。 

模型| 描述 | 優點
----|----|---
Prophet (預覽)|Prophet 最適合用於具有強烈季節性影響，且包含數個季節歷程記錄資料的時間序列。 若要利用此模型，請使用將它安裝在本機 `pip install fbprophet` 。 | 精確且快速，能夠應付時間序列中的極端值、遺失資料及重大變更。
自動 ARIMA (預覽)|自動回歸整合式移動平均 (ARIMA) 在資料為固定的情況下執行效果最佳。 這表示其統計屬性 (如平均值和變異數) 在整個集合上是常數。 例如，若投擲一枚硬幣，則不論您是今天、明天還是明年投擲，出現正面的機率都是 50%。| 由於過去值是用來預測未來值，因此非常適用於單一變量序列。
ForecastTCN (預覽)| ForecastTCN 是一種神經網路模型，其設計目的是要處理最嚴苛的預測工作，並擷取資料中的非線性本機和全球趨勢，以及時間序列之間的關聯性。|能夠運用資料中的複雜趨勢，並配合最大的資料集立即調整。

### <a name="configuration-settings"></a>組態設定

類似於迴歸問題，您可定義標準定型參數，例如工作類型、反覆項目數目、定型資料，以及交叉驗證的數目。 針對預測工作，還有一些必須設定的參數會影響實驗。 

下表摘要說明這些額外的參數。 如需語法設計模式的 [參考檔](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) ，請參閱。

| 參數名稱&nbsp; | 描述 | 必要 |
|-------|-------|-------|
|`time_column_name`|用來指定輸入資料中用來建置時間序列並推斷其頻率的日期時間資料行。|✓|
|`forecast_horizon`|定義您想要預測的期間數。 範圍是以時間序列頻率為單位。 單位是以預測器應預測出的定型資料時間間隔為基礎，例如，每月、每週。|✓|
|`enable_dnn`|[啟用預測 dnn]()。||
|`time_series_id_column_names`|資料行名稱 (s) 用來唯一識別資料中有多個資料列具有相同時間戳記的時間序列。 如果未定義時間序列識別碼，則會假設資料集為一個時間序列。 若要深入了解單一時間序列，請參閱 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。||
|`target_lags`|要根據資料頻率延隔目標值的資料列數目。 延隔會以清單或單一整數來表示。 當獨立變數與相依變數之間的關聯性預設不相符或相互關聯時，應該使用延隔。 ||
|`feature_lags`| 當 `target_lags` 設定且設定為時，自動化 ML 會自動決定延隔的功能 `feature_lags` `auto` 。 啟用功能延遲可能有助於改善精確度。 功能延遲預設為停用。 ||
|`target_rolling_window_size`|要用來產生預測值的 *n* 個歷程記錄週期，小於或等於定型集大小。 如果省略，則 *n* 就是完整的定型集大小。 若在將模型定型時只想考慮特定數量的歷程記錄，則請指定此參數。 深入瞭解 [目標滾動視窗匯總](#target-rolling-window-aggregation)。||
|`short_series_handling`| 啟用簡短的時間序列處理，以避免因為資料不足而在定型期間發生失敗。 預設會將 [短序列處理] 設定為 [True]。|


下列程式碼： 
* 利用 `ForecastingParameters` 類別來定義實驗訓練的預測參數
* 將設定 `time_column_name` 為 `day_datetime` 資料集中的欄位。 
* 定義的 `time_series_id_column_names` 參數 `"store"` 。 這可確保針對資料建立了 **兩個不同的時間序列群組** ;一個用於 store A 和 B。
* 將設定 `forecast_horizon` 為50，以便預測整個測試集。 
* 將預測視窗設定為10個週期，並使用 `target_rolling_window_size`
* 以參數預先指定兩個句點的目標值單一延隔時間 `target_lags` 。 
* 設定 `target_lags` 為建議的 [自動] 設定，這會為您自動偵測此值。

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(
    time_column_name='day_datetime', 
    forecast_horizon=50,
    time_series_id_column_names=["store"],
    target_lags='auto',
    target_rolling_window_size=10
)
```

這些 `forecasting_parameters` 會接著傳遞至您 `AutoMLConfig` 的標準物件，以及工作 `forecasting` 類型、主要度量、結束準則和定型資料。 

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
```

### <a name="featurization-steps"></a>特徵化步驟

在每個自動化機器學習實驗中，預設會將自動調整和正規化技術套用至您的資料。 這些技術是 **特徵化** 的類型，可協助 *特定* 的演算法，而這些演算法對不同規模的功能很敏感。 深入瞭解[AutoML 中特徵化的](how-to-configure-auto-features.md#automatic-featurization)預設特徵化步驟

但是，下列步驟只會針對工作 `forecasting` 類型執行：

* 偵測時間序列取樣頻率 (例如，每小時、每天、每週)，並為不存在的時間點建立新記錄，使序列連續不斷。
* 插補目標中的遺漏值 (透過向前填滿)，以及插補特徵資料行中的遺漏值 (使用中位數的資料行值)
* 建立以時間序列識別碼為基礎的功能，以啟用跨不同系列的固定效果
* 建立時間型特徵，以協助學習季節性模式
* 將類別變數編碼為數值數量

若要取得這些步驟的結果所建立功能的摘要，請參閱 [特徵化透明度](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> 自動化機器學習特徵化步驟 (功能標準化、處理遺漏的資料、將文字轉換為數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同特徵化步驟會自動套用至您的輸入資料。

#### <a name="customize-featurization"></a>自訂特徵化

您也可以選擇自訂您的特徵化設定，以確保用來定型 ML 模型的資料和功能會產生相關的預測。 

支援的工作自訂 `forecasting` 包括：

|自訂|定義|
|--|--|
|**資料行用途更新**|針對指定的資料行覆寫自動偵測到的功能類型。|
|**轉換器參數更新** |更新指定轉換器的參數。 目前支援 *Imputer* (fill_value 和中位數) 。|
|**卸除資料行** |指定要從特徵化中捨棄的資料行。|

若要使用 SDK 自訂 featurizations，請 `"featurization": FeaturizationConfig` 在您的物件中指定 `AutoMLConfig` 。 深入瞭解 [自訂 featurizations](how-to-configure-auto-features.md#customize-featurization)。

```python
featurization_config = FeaturizationConfig()
# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']
# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')
# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})
# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

如果您在實驗中使用 Azure Machine Learning studio，請參閱 [如何在 studio 中自訂特徵化](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

## <a name="optional-configurations"></a>選擇性設定

其他選擇性設定適用于預測工作，例如啟用深度學習和指定目標滾動視窗匯總。 

### <a name="enable-deep-learning"></a>啟用深度學習

> [!NOTE]
> 在自動化 Machine Learning 中預測的 DNN 支援處於 **預覽階段** ，不支援本機執行。

您也可以運用深度學習與深度類神經網路（Dnn）來改善模型的分數。 自動化 ML 的深度學習可供預測單一變量和多變量時間序列資料。

深度學習模型有三種內建功能：
1. 可從輸入到輸出的任意對應中學習
1. 支援多個輸入和輸出
1. 它們可以自動將輸入資料中的模式解壓縮到長序列。 

若要啟用深度學習，請 `enable_dnn=True` 在物件中設定 `AutoMLConfig` 。

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
> [!Warning]
> 當您針對以 SDK 建立的實驗啟用 DNN 時，會停用 [最佳模型說明](how-to-machine-learning-interpretability-automl.md) 。

若要啟用在 Azure Machine Learning studio 中建立之 AutoML 實驗的 DNN，請參閱 [studio 操作說明中的工作類型設定](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)。

如需運用 DNN 的詳細程式碼範例，請參閱[飲料生產預測筆記本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)。

### <a name="target-rolling-window-aggregation"></a>目標移動時段彙總
預測器可擁有的最佳資訊通常是目標其最新值。  目標輪流視窗匯總可讓您將資料值的滾動匯總新增為特徵。 產生和使用這些額外特徵作為額外的內容資料，可協助提高定型模型的精確度。

例如，假設您想要預測能源需求。 您可能會想要新增三天的滾動視窗功能，以考慮熱空間的熱變更。 在此範例中，請在函式中設定來建立此視窗 `target_rolling_window_size= 3` `AutoMLConfig` 。 

資料表會顯示套用視窗匯總時所產生的特徵工程。 **最小值、最大值** 和 **總和** 的資料行會根據所定義的設定，在三個滑動視窗上產生。 每個資料列都有新的計算功能，在2017年9月8日的時間戳記案例中，上午10:00 最大值、最小值和總和值的計算方式是使用 2017 1 9 月8日的 **需求值** ：上午 10:00-3：上午10:00。 這三個時段會移位以在剩餘的資料列中填入資料。

![目標滾動視窗](./media/how-to-auto-train-forecast/target-roll.svg)

請檢視運用[目標移動時段彙總特徵](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 程式碼範例。

## <a name="run-the-experiment"></a>執行實驗 

當您的 `AutoMLConfig` 物件就緒時，您可以提交實驗。 在模型完成之後，請擷取最佳的執行反覆項目。

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>使用最佳模型進行預測

使用最佳模型反覆項目來預測測試資料集的值。

`forecast()`函數可讓您在預測開始時使用的規格，與 `predict()` 通常用於分類和回歸工作的不同。

在下列範例中，您會先將 `y_pred` 中的所有值取代為 `NaN`。 在此情況下，預測來源會在定型資料的結尾。 不過，如果只以 `NaN` 取代 `y_pred` 的後半部分，則此函式不會修改前半部分的數值，但會預測後半部分的 `NaN` 值。 此函式會傳回預測的值和調整後的特徵。

您也可以使用 `forecast()` 函式中的 `forecast_destination` 參數，以預測直到指定日期之前的值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

計算 `actual_labels` 實際值與中預測值之間的根本 mean 平方誤差 (RMSE) `predict_labels` 。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

現在已決定整體模型精確度，接著下一個最實際步驟是使用模型來預測未知的未來值。 

如果使用與測試集 `test_data` 相同的格式來提供資料集，但具有未來的日期時間，則所產生預測集是每個時間序列步驟的預測值。 假設資料集內的最後一個時間序列記錄是 2018 年 12 月 31 日。 若要預測隔天的需求 (或需要預測的週期數，小於或等於 `forecast_horizon`)，請為每間商店建立 2019 年 1 月 1 日的單一時間序列記錄。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

重複必要的步驟，將此未來資料載入至資料框架，然後執行 `best_run.predict(test_data)` 來預測未來值。

> [!NOTE]
> 週期數若大於 `forecast_horizon`，則無法預測值。 若要預測目前範圍以外的未來值，模型必須以較大的範圍來重新定型。


## <a name="example-notebooks"></a>Notebook 範例
如需進階預測設定的詳細程式碼範例，請參閱[預測範例筆記本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，包括：

* [假日偵測和特徵化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [移動原點交叉驗證](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可設定延隔](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [移動時段彙總功能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>後續步驟

* 深入了解[模型部署的方式和位置](how-to-deploy-and-where.md)。
* 深入瞭解 [可解譯性：自動化機器學習中的模型說明 (預覽) ](how-to-machine-learning-interpretability-automl.md)。 
* 瞭解如何在 [許多模型解決方案加速器](https://aka.ms/many-models)中使用 AutoML 來訓練多個模型。
* 遵循 [教學](tutorial-auto-train-models.md) 課程，以取得使用自動化機器學習來建立實驗的端對端範例。
