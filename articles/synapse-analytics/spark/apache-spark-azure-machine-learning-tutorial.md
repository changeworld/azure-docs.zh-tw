---
title: 教學課程：使用自動化機器學習在 Python 中訓練模型
description: 有關如何使用 Apache Spark 和自動化機器學習，在 Python 中訓練機器學習模型的教學課程。
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: fc9909614a9d557c19a22e215b7513a038f88c33
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942344"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>教學課程：使用自動化機器學習在 Python 中訓練模型

Azure Machine Learning 是一個雲端式環境，可讓您定型、部署、自動化、管理和追蹤機器學習模型。 

在本教學課程中，您將使用 Azure Machine Learning 中的 [自動化機器學習](../../machine-learning/concept-automated-ml.md) 建立迴歸模型，以預測計程車車資價格。 此程序透過接受定型資料和組態設定，並自動逐一查看不同方法、模型及超參數設定的組合，以獲得最佳模型。

在本教學課程中，您會了解如何：
- 使用 Apache Spark 和 Azure 開放資料集下載資料。
- 使用 Apache Spark 資料框架來轉換和清除資料。
- 在自動化機器學習中定型回歸模型。
- 計算模型精確度。

## <a name="before-you-begin"></a>開始之前

- 遵循 [建立無伺服器的 Apache Spark 集](../quickstart-create-apache-spark-pool-studio.md) 區快速入門，建立無伺服器 Apache Spark 集區。
- 如果您沒有現有的 Azure Machine Learning 工作區，請完成 [Azure Machine Learning 工作區設定](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) 教學課程。 

## <a name="understand-regression-models"></a>了解迴歸模型

*迴歸模型* 會根據獨立的預測指標來預測數值輸出值。 在迴歸中，其目標是藉由評估一個變數如何影響其他變數，來協助您建立那些獨立預測變數之間的關聯性。  

### <a name="example-based-on-new-york-city-taxi-data"></a>以紐約市計程車資料為基礎的範例

在此範例中，您會使用 Spark 來對紐約城市的計程車行程提示資料執行一些分析 (NYC) 。 您可以透過 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得資料。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間和位置，以及成本。

> [!IMPORTANT]
> 從其儲存體位置提取此資料可能會產生額外費用。 在下列步驟中，您會開發模型，用以預測紐約市計程車車資價格。 

##  <a name="download-and-prepare-the-data"></a>下載並準備資料

其做法如下：

1. 使用 PySpark 核心建立筆記本。 如需相關指示，請參閱 [建立筆記本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
   
    > [!Note]
    > 由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 內容。
  
2. 由於原始資料採用 Parquet 格式，因此您可以使用 Spark 內容，將檔案直接以資料框架的形式提取至記憶體中。 藉由透過開放式資料集 API 來抓取資料來建立 Spark 資料框架。 在這裡，您會使用 Spark 資料框架 `schema on read` 屬性來推斷資料類型和架構。 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. 根據您的 Spark 集區大小，未經處理資料可能太大，或花費太多時間來操作。 您可以使用和篩選，將此資料篩選為較小的某個資料，例如一個月的資料 ```start_date``` ```end_date``` 。 篩選資料框架之後，您也可以 ```describe()``` 在新的資料框架上執行函數，以查看每個欄位的摘要統計資料。 

   根據摘要統計資料，您可以看到資料中有一些異常的情況。 例如，統計資料顯示最短車程距離小於 0。 您必須篩選掉這些異常的資料點。
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. 從資料集產生特徵，方法是選取一組資料行，然後從 [收取] 欄位建立各種以時間為基礎的功能 `datetime` 。 篩選出先前步驟中所識別的極端值，然後移除最後幾個資料行，因為它們不是定型的必要欄位。
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   如您所見，這將會建立一個新的資料框架，其中包含每月日、收取時數、工作日和總行程時間的額外資料行。 

   ![計程車資料框架的圖片。](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>產生測試和驗證資料集

有了最終的資料集後，您就可以使用 Spark 中的 ```random_ split ``` 函式將資料分割為定型集和測試集。 藉由使用提供的加權，此函式會將資料隨機分割成訓練資料集，以進行模型定型和驗證資料集來進行測試。

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
此步驟可確保會以資料點測試已完成、但尚未用來定型模型的模型。 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>連線至 Azure Machine Learning 工作區
在 Azure Machine Learning 中，工作區是會接受 Azure 訂用帳戶和資源資訊的類別。 它也會建立雲端資源來監視及追蹤您的模型執行。 在此步驟中，您從現有的 Azure Machine Learning 工作區建立工作區物件。
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>將資料框架轉換為 Azure Machine Learning 資料集
若要提交遠端實驗，請將您的資料集轉換成 Azure Machine Learning ```TabularDatset``` 實例。 [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py) 藉由剖析提供的檔案，以表格格式代表資料。

下列程式碼會取得現有的工作區和預設的 Azure Machine Learning 資料存放區。 然後，它會將資料存放區和檔案位置傳遞給 path 參數，以建立新的 ```TabularDataset``` 實例。 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![已上傳資料集的圖片。](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>提交自動化實驗

下列各節將逐步引導您完成提交自動化機器學習實驗流程。

### <a name="define-training-settings"></a>定義定型設定
1. 為了提交實驗，您需要定義用於定型的實驗參數與模型設定。 如需完整的設定清單，請參閱 [在 Python 中設定自動化機器學習實驗](../../machine-learning/how-to-configure-auto-train.md)。

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. 將定義的定型設定作為 `kwargs` 參數傳遞給 `AutoMLConfig` 物件。 由於您使用的是 Spark，因此也必須傳遞 Spark 內容，該內容可由 ```sc``` 變數自動存取。 此外，您會指定定型資料和模型類型，在此案例中為迴歸類型。

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>自動化機器學習前置處理步驟會成為基礎模型的一部分。 這些步驟包括功能正規化、處理遺漏的資料，以及將文字轉換成數值。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

### <a name="train-the-automatic-regression-model"></a>定型自動迴歸模型 
接著，在您的 Azure Machine Learning 工作區中建立實驗物件。 實驗會作為個別執行的容器。 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
當實驗完成時，輸出會傳回已完成反覆運算的詳細資料。 對於每次反覆運算，您都可以檢視模型類型、執行的持續時間，以及訓練精確度。 此 `BEST` 欄位會根據您的計量類型來追蹤最佳執行訓練分數。

![模型輸出的螢幕擷取畫面。](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> 在您提交自動化機器學習實驗之後，其會執行各種反覆運算和模型類型。 此執行通常需要60至90分鐘。 

### <a name="retrieve-the-best-model"></a>擷取最佳模型
為了從您的反覆運算中選取最佳模型，使用 ```get_output``` 函式傳回執行效果最佳的配適模型。 下列程式碼會針對任何記錄的計量或特定反覆運算，擷取執行效果最佳的配適模型。

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>測試模型精確度
1. 為了測試模型精確度，將使用最佳模型對測試資料集執行計程車車資預測。 ```predict```函數會使用最佳模型，並預測 `y` 從驗證資料集)  (費用金額的值。 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. 均方根誤差是一個常用的量值，表示模型預測的樣本值與觀察到的值之間的差異。 您可以藉由比較 `y_test` 資料框架與模型所預測的值來計算結果的 mean 平方誤差。 

   ```mean_squared_error``` 函式會採用兩個陣列，並計算這兩個陣列之間的均方誤差。 然後，您會採用結果的平方根。 此標準指出計程車費用預測與實際費用值的距離。

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   均方根誤差這個量值可正確呈現模型預測回應的精確度。 在結果中，您會看到模型從資料集的特徵預測計程車車資的表現相當不錯，大多在 2.00 美元以內。

1. 請執行下列程式碼，以計算平均絕對百分比誤差。 此計量會以誤差的百分比表示精確度。 其運作方式是計算每個預測值與實際值之間的絕對差異，然後加總所有差異。 然後，它會以實際值總計的百分比來表示該總和。

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
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

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   在兩個預測精確度計量中，您會看到模型從資料集的特徵預測計程車車資的表現相當不錯。 

1. 在調整線性迴歸模型後，您現在必須判斷模型對於資料的適用程度。 為此，您將繪製相對於預測輸出的實際車資值。 此外，您會計算 R 平方量值，以了解如何資料有多接近配適的迴歸線。

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![回歸圖的螢幕擷取畫面。](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   在結果中，您可以看到 R 平方量值涵蓋了 95% 的變異數。 實際繪圖與觀測繪圖的比較圖也可以驗證這一點。 回歸模型帳戶的變異數越多，資料點越接近適合的迴歸線。  

## <a name="register-the-model-to-azure-machine-learning"></a>註冊模型以 Azure Machine Learning
驗證最佳模型之後，您可以將模型註冊到 Azure Machine Learning。 然後，您就可以下載或部署已註冊的模型，並接收所有已註冊的檔案。

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>在 Azure Machine Learning 中檢視結果
您也可以前往 Azure Machine Learning 工作區中的實驗來存取反覆運算的結果。 在這裡，您可以取得有關執行、嘗試的模型和其他模型計量之狀態的其他詳細資料。 

![Azure Machine Learning 工作區的螢幕擷取畫面。](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>後續步驟
- [Azure Synapse Analytics](../index.yml)
- [教學課程：使用 Apache Spark MLlib 和 Azure Synapse Analytics 來建置機器學習應用程式](./apache-spark-machine-learning-mllib-notebook.md)