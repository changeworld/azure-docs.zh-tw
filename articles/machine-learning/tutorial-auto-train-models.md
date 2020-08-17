---
title: 迴歸教學課程：自動化 ML
titleSuffix: Azure Machine Learning
description: 在此教學課程中，您會了解如何使用自動化機器學習來產生機器學習模型。 Azure Machine Learning 可以用自動化方式，為您執行資料前處理、演算法選擇及超參數選擇。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aniththa
ms.author: anumamah
ms.reviewer: nibaccam
ms.date: 08/14/2020
ms.custom: devx-track-python
ms.openlocfilehash: 884e97815a048d3e37dba57d362d71e72ef5e103
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/11/2020
ms.locfileid: "88120842"
---
# <a name="tutorial-use-automated-machine-learning-to-predict-taxi-fares"></a>教學課程：使用自動化機器學習預測計程車車資
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本教學課程中，您將使用 Azure Machine Learning 中的自動化機器學習建立迴歸模型，以預測 NYC 計程車車資價格。 此程序接受定型資料和組態設定，並自動逐一查看不同功能正規化/標準化方法、模型及超參數設定的組合，以獲得最佳模型。

![流程圖](./media/tutorial-auto-train-models/flow2.png)

在此教學課程中，您將了解下列工作：

> [!div class="checklist"]
> * 使用 Azure 開放資料集來下載、轉換及清除資料
> * 為自動化機器學習迴歸模型定型
> * 計算模型精確度

如果您沒有 Azure 訂用帳戶，請在開始前先建立一個免費帳戶。 立即試用[免費或付費版本的 Azure Machine Learning](https://aka.ms/AMLFree)。

## <a name="prerequisites"></a>必要條件

* 如果您還沒有 Azure Machine Learning 工作區或 Notebook 虛擬機器，請完成[設定教學課程](tutorial-1st-experiment-sdk-setup.md)。
* 完成設定教學課程之後，請使用相同的筆記本伺服器開啟 tutorials/regression-automl-nyc-taxi-data/regression-automated-ml.ipynb 筆記本。

如果您想要在自己的[本機環境](how-to-configure-environment.md#local)中執行此教學課程，也可以在 [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) 上取得。 執行 `pip install azureml-sdk[automl] azureml-opendatasets azureml-widgets` 以取得必要套件。

## <a name="download-and-prepare-data"></a>下載並準備資料

匯入必要的套件。 開放資料集套件包含一個代表每個資料來源 (例如 `NycTlcGreen`) 的類別，以在下載之前輕鬆篩選日期參數。

```python
from azureml.opendatasets import NycTlcGreen
import pandas as pd
from datetime import datetime
from dateutil.relativedelta import relativedelta
```

從建立用來保存計程車資料的資料架構開始。 在非 Spark 環境中運作時，開放資料集僅允許一次下載一個月含特定類別的資料，以避免使用大型資料集時發生發生 `MemoryError`。

若要下載計程車資料，請反覆地逐一擷取一個月的資料，並且在將該資料附加到 `green_taxi_df` 之前，從每個月中隨機取樣 2,000 筆記錄，以避免使資料架構膨脹。 接著預覽資料。


```python
green_taxi_df = pd.DataFrame([])
start = datetime.strptime("1/1/2015","%m/%d/%Y")
end = datetime.strptime("1/31/2015","%m/%d/%Y")

for sample_month in range(12):
    temp_df_green = NycTlcGreen(start + relativedelta(months=sample_month), end + relativedelta(months=sample_month)) \
        .to_pandas_dataframe()
    green_taxi_df = green_taxi_df.append(temp_df_green.sample(2000))

green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType |fareAmount |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|    tripType|
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|None|None|-73.88|40.84|-73.94|...|2|15.00|0.50|0.50|0.3|0.00|0.00|nan|16.30|1.00
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|None|None|-73.96|40.81|-73.96|...|2|4.50|1.00|0.50|0.3|0.00|0.00|nan|6.30|1.00
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0.45|None|None|-73.92|40.76|-73.91|...|2|4.00|0.00|0.50|0.3|0.00|0.00|nan|4.80|1.00
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|None|None|-73.81|40.70|-73.82|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1.00
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|None|None|-73.92|40.76|-73.92|...|2|4.00|0.50|0.50|0|0.00|0.00|nan|5.00|1.00
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|None|None|-73.96|40.72|-73.95|...|2|6.50|0.50|0.50|0.3|0.00|0.00|nan|7.80|1.00
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|None|None|-73.88|40.76|-73.87|...|2|6.00|0.00|0.50|0.3|0.00|0.00|nan|6.80|1.00
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|None|None|-73.96|40.72|-73.91|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1.00
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|None|None|-73.94|40.71|-73.95|...|1|7.00|0.00|0.50|0.3|1.75|0.00|nan|9.55|1.00
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0.65|None|None|-73.94|40.71|-73.94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nan|6.30|1.00

既然已載入初始資料，請定義一個函式，從搭車日期時間欄位建立各種以時間為基礎的功能。 這將會建立適用於月份數字、當月某日、週中的日及當天特定時間的新欄位，並且可讓模型將以時間為基礎的季節性當作考量因素。 在資料架構上使用 `apply()` 函式，以將 `build_time_features()` 函式反覆地套用至計程車資料中的每個資料列。

```python
def build_time_features(vector):
    pickup_datetime = vector[0]
    month_num = pickup_datetime.month
    day_of_month = pickup_datetime.day
    day_of_week = pickup_datetime.weekday()
    hour_of_day = pickup_datetime.hour

    return pd.Series((month_num, day_of_month, day_of_week, hour_of_day))

green_taxi_df[["month_num", "day_of_month","day_of_week", "hour_of_day"]] = green_taxi_df[["lpepPickupDatetime"]].apply(build_time_features, axis=1)
green_taxi_df.head(10)
```

|vendorID| lpepPickupDatetime|  lpepDropoffDatetime|    passengerCount| tripDistance|   puLocationId|   doLocationId|   pickupLongitude|    pickupLatitude| dropoffLongitude    |...|   paymentType|fareAmount  |extra| mtaTax| improvementSurcharge|   tipAmount|  tollsAmount|    ehailFee|   totalAmount|tripType|month_num|day_of_month|day_of_week|hour_of_day
|----|----|----|----|----|----|---|--|---|---|---|----|----|----|--|---|----|-----|----|----|----|----|---|----|----|----
|131969|2|2015-01-11 05:34:44|2015-01-11 05:45:03|3|4.84|None|None|-73.88|40.84|-73.94|...|2|15.00|0.50|0.50|0.3|0.00|0.00|nan|16.30|1.00|1|11|6|5
|1129817|2|2015-01-20 16:26:29|2015-01-20 16:30:26|1|0.69|None|None|-73.96|40.81|-73.96|...|2|4.50|1.00|0.50|0.3|0.00|0.00|nan|6.30|1.00|1|20|1|16
|1278620|2|2015-01-01 05:58:10|2015-01-01 06:00:55|1|0.45|None|None|-73.92|40.76|-73.91|...|2|4.00|0.00|0.50|0.3|0.00|0.00|nan|4.80|1.00|1|1|3|5
|348430|2|2015-01-17 02:20:50|2015-01-17 02:41:38|1|0.00|None|None|-73.81|40.70|-73.82|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1.00|1|17|5|2
1269627|1|2015-01-01 05:04:10|2015-01-01 05:06:23|1|0.50|None|None|-73.92|40.76|-73.92|...|2|4.00|0.50|0.50|0|0.00|0.00|nan|5.00|1.00|1|1|3|5
|811755|1|2015-01-04 19:57:51|2015-01-04 20:05:45|2|1.10|None|None|-73.96|40.72|-73.95|...|2|6.50|0.50|0.50|0.3|0.00|0.00|nan|7.80|1.00|1|4|6|19
|737281|1|2015-01-03 12:27:31|2015-01-03 12:33:52|1|0.90|None|None|-73.88|40.76|-73.87|...|2|6.00|0.00|0.50|0.3|0.00|0.00|nan|6.80|1.00|1|3|5|12
|113951|1|2015-01-09 23:25:51|2015-01-09 23:39:52|1|3.30|None|None|-73.96|40.72|-73.91|...|2|12.50|0.50|0.50|0.3|0.00|0.00|nan|13.80|1.00|1|9|4|23
|150436|2|2015-01-11 17:15:14|2015-01-11 17:22:57|1|1.19|None|None|-73.94|40.71|-73.95|...|1|7.00|0.00|0.50|0.3|1.75|0.00|nan|9.55|1.00|1|11|6|17
|432136|2|2015-01-22 23:16:33   2015-01-22 23:20:13 1   0.65|None|None|-73.94|40.71|-73.94|...|2|5.00|0.50|0.50|0.3|0.00|0.00|nan|6.30|1.00|1|22|3|23

移除一些您在定型或建置其他功能時不需用到的資料行。

```python
columns_to_remove = ["lpepPickupDatetime", "lpepDropoffDatetime", "puLocationId", "doLocationId", "extra", "mtaTax",
                     "improvementSurcharge", "tollsAmount", "ehailFee", "tripType", "rateCodeID",
                     "storeAndFwdFlag", "paymentType", "fareAmount", "tipAmount"
                    ]
for col in columns_to_remove:
    green_taxi_df.pop(col)

green_taxi_df.head(5)
```

### <a name="cleanse-data"></a>清理資料

在新的資料架構上執行 `describe()` 函式，來查看每個欄位的摘要統計資料。

```python
green_taxi_df.describe()
```

|vendorID|passengerCount|tripDistance|pickupLongitude|pickupLatitude|dropoffLongitude|dropoffLatitude|  totalAmount|month_num   day_of_month|day_of_week|hour_of_day
|----|----|---|---|----|---|---|---|---|---|---|---
|count|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00|48000.00
|平均值|1.78|1.37|2.87|-73.83|40.69|-73.84|40.70|14.75|6.50|15.13|3.27|13.52
|std|0.41|1.04|2.93|2.76|1.52|2.61|1.44|12.08|3.45|8.45|1.95|6.83
|Min|1.00|0.00|0.00|-74.66|0.00|-74.66|0.00|-300.00|1.00|1.00|0.00|0.00
|25%|2.00|1.00|1.06|-73.96|40.70|-73.97|40.70|7.80|3.75|8.00|2.00|9.00
|50%|2.00|1.00|1.90|-73.94|40.75|-73.94|40.75|11.30|6.50|15.00|3.00|15.00
|75%|2.00|1.00|3.60|-73.92|40.80|-73.91|40.79|17.80|9.25|22.00|5.00|19.00
|max|2.00|9.00|97.57|0.00|41.93|0.00|41.94|450.00|12.00|30.00|6.00|23.00


從摘要統計資料中，您會看到有數個欄位包含將降低模型精確度的極端值或值。 首先，將經/緯度欄位篩選為曼哈頓區域的範圍內。 這會篩選掉較長途的計程車車程，或與其他特性的關聯性方面屬於極端值的車程。

此外，請將 `tripDistance` 欄位篩選為大於零、但小於 31 英哩 (兩個經/緯度組之間的半正矢距離)。 這可以消除車程成本不一致的長途極端值車程。

最後，`totalAmount` 欄位的計程車車資含有負值 (這在我們的模型內容中不具意義)，且 `passengerCount` 欄位含有最小值為零的無效資料。

使用查詢函式篩選出這些異常狀況，然後移除前幾個在定型時不需用到的資料行。


```python
final_df = green_taxi_df.query("pickupLatitude>=40.53 and pickupLatitude<=40.88")
final_df = final_df.query("pickupLongitude>=-74.09 and pickupLongitude<=-73.72")
final_df = final_df.query("tripDistance>=0.25 and tripDistance<31")
final_df = final_df.query("passengerCount>0 and totalAmount>0")

columns_to_remove_for_training = ["pickupLongitude", "pickupLatitude", "dropoffLongitude", "dropoffLatitude"]
for col in columns_to_remove_for_training:
    final_df.pop(col)
```

再次於資料上呼叫 `describe()`，以確保清理會如預期般運作。 您現在具有一組已備妥且已清理的計程車、假日及氣象資料，可用於機器學習服務模型定型。

```python
final_df.describe()
```

## <a name="configure-workspace"></a>設定工作區

從現有的工作區建立工作區物件。 [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) \(英文\) 是會接受您 Azure 訂用帳戶和資源資訊的類別。 它也會建立雲端資源來監視及追蹤您的模型執行。 `Workspace.from_config()` 會讀取 **config.json** 檔案，並將驗證詳細資料載入名為 `ws` 的物件中。 `ws` 用於本教學課程的其餘程式碼。

```python
from azureml.core.workspace import Workspace
ws = Workspace.from_config()
```

## <a name="split-the-data-into-train-and-test-sets"></a>將資料分成定型集和測試集

您可以使用 `scikit-learn` 程式庫中的 `train_test_split` 函式，將資料分割成定型集和測試集。 此函式會將資料分成用於模型定型的 x (**特性**) 資料集，以及用於測試的 y (**要預測的值**) 資料集。

`test_size` 參數會決定要配置給測試的資料百分比。 `random_state` 參數會設定隨機產生器的種子，讓您的「定型-測試」分割具有確定性。

```python
from sklearn.model_selection import train_test_split

x_train, x_test = train_test_split(final_df, test_size=0.2, random_state=223)
```

此步驟的目的是要以資料點測試已完成、但尚未用來訓練模型的模型，以評估實際的精確度。

換句話說，訓練完善的模型應該能夠準確地從資料預測它尚未觀察到的部分。 您現在已備妥資料，可自動定型機器學習服務模型。

## <a name="automatically-train-a-model"></a>自動為模型定型

若要自動將模型定型，請執行下列步驟：
1. 定義用於實驗執行的設定。 將訓練資料附加至組態，並修改用來控制訓練程序的設定。
1. 提交實驗來調整模型。 提交實驗之後，程序會根據您定義的條件約束，反覆運算不同的機器學習演算法和超參數設定。 它會將精確度計量最佳化，以選擇最適化模型。

### <a name="define-training-settings"></a>定義定型設定

定義用於定型的實驗參數與模型設定。 檢視[設定](how-to-configure-auto-train.md)的完整清單。 提交使用這些預設設定的實驗大約需要 5 至 20 分鐘的時間，但如果您想要縮短執行時間，請降低 `experiment_timeout_hours` 參數。

|屬性| 本教學課程中的值 |描述|
|----|----|---|
|**iteration_timeout_minutes**|2|每次反覆運算的時間限制 (分鐘)。 降低此值以減少總執行時間。|
|**experiment_timeout_hours**|0.3|在實驗終止之前，所有反覆運算合在一起所花費的時間量上限 (以小時為單位)。|
|**enable_early_stopping**|True|此旗標可在分數未在短期內改善時啟用提早終止。|
|**primary_metric**| spearman_correlation | 您想要最佳化的度量。 最適化模型將根據此計量來選擇。|
|**特徵化**| 自動 | 使用 **auto** 時，實驗可以預先處理輸入資料 (處理遺漏的資料、將文字轉換成數值等等)。|
|**verbosity**| logging.INFO | 控制記錄層級。|
|**n_cross_validations**|5|未指定驗證資料時所要執行的交叉驗證分割數目。|

```python
import logging

automl_settings = {
    "iteration_timeout_minutes": 2,
    "experiment_timeout_hours": 0.3,
    "enable_early_stopping": True,
    "primary_metric": 'spearman_correlation',
    "featurization": 'auto',
    "verbosity": logging.INFO,
    "n_cross_validations": 5
}
```

請使用您定義的定型設定作為 `AutoMLConfig` 物件的 `**kwargs` 參數。 此外，請指定您的訓練資料和模型類型 (在此案例中為 `regression`)。

```python
from azureml.train.automl import AutoMLConfig

automl_config = AutoMLConfig(task='regression',
                             debug_log='automated_ml_errors.log',
                             training_data=x_train,
                             label_column_name="totalAmount",
                             **automl_settings)
```

> [!NOTE]
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="train-the-automatic-regression-model"></a>定型自動迴歸模型

在您的工作區中建立實驗物件。 實驗會作為個別執行的容器。 將已定義的 `automl_config` 物件傳至實驗，並將輸出設定為 `True` 以檢視執行期間的進度。

實驗開始後，顯示的輸出會隨著實驗的執行即時更新。 對於每次反覆運算，您都可以檢視模型類型、執行的持續時間，以及訓練精確度。 欄位 `BEST` 會根據您的計量類型追蹤最佳訓練分數。

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, "taxi-experiment")
local_run = experiment.submit(automl_config, show_output=True)
```

```output
Running on local machine
Parent Run ID: AutoML_1766cdf7-56cf-4b28-a340-c4aeee15b12b
Current status: DatasetFeaturization. Beginning to featurize the dataset.
Current status: DatasetEvaluation. Gathering dataset statistics.
Current status: FeaturesGeneration. Generating features for the dataset.
Current status: DatasetFeaturizationCompleted. Completed featurizing the dataset.
Current status: DatasetCrossValidationSplit. Generating individually featurized CV splits.
Current status: ModelSelection. Beginning model selection.

****************************************************************************************************
ITERATION: The iteration being evaluated.
PIPELINE: A summary description of the pipeline being evaluated.
DURATION: Time taken for the current iteration.
METRIC: The result of computing score on the fitted pipeline.
BEST: The best observed score thus far.
****************************************************************************************************

 ITERATION   PIPELINE                                       DURATION      METRIC      BEST
         0   StandardScalerWrapper RandomForest             0:00:16       0.8746    0.8746
         1   MinMaxScaler RandomForest                      0:00:15       0.9468    0.9468
         2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.9303    0.9468
         3   StandardScalerWrapper LightGBM                 0:00:10       0.9424    0.9468
         4   RobustScaler DecisionTree                      0:00:09       0.9449    0.9468
         5   StandardScalerWrapper LassoLars                0:00:09       0.9440    0.9468
         6   StandardScalerWrapper LightGBM                 0:00:10       0.9282    0.9468
         7   StandardScalerWrapper RandomForest             0:00:12       0.8946    0.9468
         8   StandardScalerWrapper LassoLars                0:00:16       0.9439    0.9468
         9   MinMaxScaler ExtremeRandomTrees                0:00:35       0.9199    0.9468
        10   RobustScaler ExtremeRandomTrees                0:00:19       0.9411    0.9468
        11   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9077    0.9468
        12   StandardScalerWrapper LassoLars                0:00:15       0.9433    0.9468
        13   MinMaxScaler ExtremeRandomTrees                0:00:14       0.9186    0.9468
        14   RobustScaler RandomForest                      0:00:10       0.8810    0.9468
        15   StandardScalerWrapper LassoLars                0:00:55       0.9433    0.9468
        16   StandardScalerWrapper ExtremeRandomTrees       0:00:13       0.9026    0.9468
        17   StandardScalerWrapper RandomForest             0:00:13       0.9140    0.9468
        18   VotingEnsemble                                 0:00:23       0.9471    0.9471
        19   StackEnsemble                                  0:00:27       0.9463    0.9471
```

## <a name="explore-the-results"></a>探索結果

使用 [Jupyter 小工具](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)，探索自動定型的結果。 這項小工具可讓您查看所有個別執行反覆項目的圖表和資料表，以及定型精確度計量和中繼資料。 此外，您也可以使用下拉式清單選取器，來篩選主要計量以外的不同精確度計量。

```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Jupyter 小工具執行詳細資料](./media/tutorial-auto-train-models/automl-dash-output.png)
![Jupyter 小工具繪圖](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="retrieve-the-best-model"></a>擷取最佳模型

從您的反覆項目中選取最佳的模型。 `get_output` 函式會傳回最佳執行和上一個配適引動過程的配適模型。 藉由在 `get_output` 上使用多載，您便可以針對任何已記錄的計量或特定的反覆項目，擷取最佳執行和配適模型。

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

### <a name="test-the-best-model-accuracy"></a>測試最佳模型的精確度

使用最佳模型在測試資料集上執行預測，以預測計程車車資。 函式 `predict` 會使用最佳模型，並從 `x_test` 資料集預測 y 值 (**車程成本**)。 從 `y_predict` 列印前 10 個預測成本值。

```python
y_test = x_test.pop("totalAmount")

y_predict = fitted_model.predict(x_test)
print(y_predict[:10])
```

計算結果的 `root mean squared error`。 將 `y_test` 資料架構轉換為清單，以便與預測值進行比較。 `mean_squared_error` 函式會採用兩個值陣列，並計算這兩個陣列之間的均方誤差。 取結果的平方根會產生與 y 變數 (**成本**) 相同單位的誤差。 這大致上可表示計程車車資預測與實際車資的差距。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

y_actual = y_test.values.flatten().tolist()
rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

執行下列程式碼，以使用完整的 `y_actual` 和 `y_predict` 資料集來計算平均絕對百分比誤差 (MAPE)。 此計量會計算每個預測值與實際值之間的絕對差異，並加總所有差異。 然後它會以實際值總計的百分比來表示該總和。

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

```output
Model MAPE:
0.14353867606052823

Model Accuracy:
0.8564613239394718
```


從這兩個預測精確度計量中，您會看到模型從資料集的特性預測計程車車資的表現相當不錯，大多在 +- 4.00 美元以內，誤差約為 15%。

傳統的機器學習模型開發程序會耗費大量資源，而且需要投入大量的網域知識和時間來執行並比較數十個模型的結果。 使用自動化機器學習，是您針對個人案例快速測試許多不同模型的絕佳方式。

## <a name="clean-up-resources"></a>清除資源

如果您打算執行其他 Azure Machine Learning 教學課程，請不要完成本節。

### <a name="stop-the-compute-instance"></a>停止計算執行個體

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>刪除所有內容

如果您不打算使用您建立的資源，請刪除它們，以免產生任何費用。

1. 在 Azure 入口網站中，選取最左邊的 [資源群組]。
1. 在清單中，選取您所建立的資源群組。
1. 選取 [刪除資源群組]。
1. 輸入資源群組名稱。 然後選取 [刪除]。

您也可以保留資源群組，但刪除單一工作區。 顯示工作區屬性，然後選取 [刪除]。

## <a name="next-steps"></a>後續步驟

在此自動化機器學習教學課程中，您已執行下列工作：

> [!div class="checklist"]
> * 設定工作區和備妥用於實驗的資料。
> * 搭配自訂參數在本機使用自動化迴歸模型來進行定型。
> * 瀏覽及檢閱定型結果。

使用 Azure Machine Learning [部署模型](tutorial-deploy-models-with-aml.md)。
