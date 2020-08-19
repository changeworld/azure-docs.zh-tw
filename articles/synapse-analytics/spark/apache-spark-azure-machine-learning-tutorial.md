---
title: 使用 Azure 自動化 ML 執行實驗
description: 使用 Apache Spark 和 Azure 自動化 ML 來執行機器學習實驗
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590595"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>使用 Azure 自動化 ML 和 Apache Spark 來執行實驗

Azure Machine Learning 是以雲端為基礎的環境，可讓您定型、部署、自動化、管理及追蹤機器學習模型。 

在本教學課程中，您將在 Azure Machine Learning 中使用 [自動化機器學習](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) 來建立回歸模型，以預測 NYC 計程車費用價格。 此程式會接受定型資料和設定，並自動逐一查看不同功能正規化/標準化方法、模型和超參數設定的組合，以達到最佳模型。

在此教學課程中，您將了解下列工作：
- 使用 Apache Spark 和 Azure 開放資料集下載資料
- 使用 Apache Spark 資料框架轉換和清除資料
- 為自動化機器學習迴歸模型定型
- 計算模型精確度

### <a name="before-you-begin"></a>開始之前
- 遵循 [建立 Apache Spark 集區教學](../quickstart-create-apache-spark-pool-studio.md)課程，建立 Apache Spark 集區。
- 如果您沒有現有的 Azure Machine Learning 工作區，請完成 [Azure Machine Learning 工作區設定教學](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) 課程。 

### <a name="understand-regression-models"></a>瞭解回歸模型
*回歸模型* 會根據獨立的預測指標來預測數值輸出值。 在迴歸中，目標是藉由評估一個變數如何影響其他變數，來協助您建立那些獨立預測變數之間的關聯性。  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>NYC 計程車資料的迴歸分析範例
在此範例中，您將使用 Spark 對紐約的計程車行程提示資料執行一些分析。 您可以透過 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得資料。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間及位置、成本和其他有趣的屬性。

> [!IMPORTANT]
> 
> 從其儲存體位置提取此資料可能會產生額外費用。 在下列步驟中，您將開發一個模型來預測 NYC 計程車費用價格。 
> 

##  <a name="download-and-prepare-the-data"></a>下載並準備資料

1. 使用 PySpark 核心建立筆記本。 如需相關指示，請參閱 [建立筆記本](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > 由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 內容。
   >

2. 因為未經處理資料採用 Parquet 格式，所以您可以使用 Spark 內容，直接將檔案當作資料框架提取至記憶體中。 藉由透過開放式資料集 API 來抓取資料來建立 Spark 資料框架。 在這裡，我們將 *在 read 屬性上* 使用 Spark 資料框架架構，以推斷資料類型和架構。 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. 根據您的 Spark 集區 (預覽) 大小，未經處理資料可能太大，或花費太多時間來操作。 您可以使用和篩選，將此資料向下篩選為較小的內容 ```start_date``` ```end_date``` 。 這會套用傳回月份資料的篩選準則。 一旦有篩選過的資料框架，我們也會 ```describe()``` 在新的資料框架上執行函數，以查看每個欄位的摘要統計資料。 

   根據摘要統計資料，我們可以看到資料中有一些異常和極端值。 例如，統計資料顯示最小的行程距離小於0。 我們需要篩選掉這些不規則的資料點。
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. 現在，我們將從資料集產生特徵，方法是選取一組資料行，並從 [收取日期時間] 欄位建立各種以時間為基礎的功能。 我們也會篩選出先前步驟中所識別的極端值，然後移除不需要定型的最後幾個資料行。
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
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

![計程車資料框架的圖片。](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>產生測試和驗證資料集

當我們擁有最後一個資料集之後，就可以使用 Spark 函數將資料分割成定型集和測試集 ```random_ split ``` 。 此函數會使用提供的權數，將資料隨機分割成訓練資料集，以進行模型定型和驗證資料集以進行測試。

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

此步驟可確保資料點會測試完成的模型，但尚未用來定型模型。 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>連接到 Azure Machine Learning 工作區
在 Azure Machine Learning 中，  **工作區** 是可接受您的 Azure 訂用帳戶和資源資訊的類別。 它也會建立雲端資源來監視及追蹤您的模型執行。 在此步驟中，我們將從現有的 Azure Machine Learning 工作區建立工作區物件。
   
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
若要提交遠端實驗，我們必須將資料集轉換成 Azure Machine Learning ```TabularDatset``` 。 [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)藉由剖析提供的檔案，以表格格式代表資料。

下列程式碼會取得現有的工作區和預設的 Azure Machine Learning 預設資料存放區。 然後，它會將資料存放區和檔案位置傳遞給 path 參數，以建立新的 ```TabularDataset``` 。 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![上傳資料集的圖片。](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>提交自動 ML 實驗

#### <a name="define-training-settings"></a>定義定型設定

1. 若要提交實驗，我們必須定義實驗參數和定型的模型設定。 您可以在 [這裡](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train)查看完整的設定清單。

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

2. 現在，我們會將定義的定型設定當作 \* \* kwargs 參數傳遞給 AutoMLConfig 物件。 因為我們要在 Spark 中定型，所以也必須傳遞可由變數自動存取的 Spark 內容 ```sc``` 。 此外，我們會指定定型資料和模型的類型，這是在此案例中的回歸。

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
> 自動化機器學習前置處理步驟 (功能正規化、處理遺漏的資料、將文字轉換成數值等等) 會成為基礎模型的一部分。 使用模型進行預測時，定型期間所套用的相同前置處理步驟會自動套用至您的輸入資料。

#### <a name="train-the-automatic-regression-model"></a>定型自動迴歸模型 
現在，我們將在您的 Azure Machine Learning 工作區中建立實驗物件。 實驗會作為個別執行的容器。 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

一旦實驗完成，輸出就會傳回已完成反覆運算的詳細資料。 對於每次反覆運算，您都可以檢視模型類型、執行的持續時間，以及訓練精確度。 此欄位會根據您的度量類型，最適合追蹤最佳的執行訓練分數。

![模型輸出的螢幕擷取畫面。](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> 提交之後，AutoML 實驗將會執行各種反覆運算和模型類型。 這種執行通常需要1到1.5 小時的時間。 

#### <a name="retrieve-the-best-model"></a>擷取最佳模型
若要從您的反復專案中選取最佳的模型，我們將使用函式 ```get_output``` 來傳回最佳執行和調整的模型。 下列程式碼將會針對任何已記錄的計量或特定的反復專案，取得最佳的執行和合適模型。

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>測試模型精確度

1. 為了測試模型的精確度，我們將使用最佳模型在測試資料集上執行計程車費用預測。 ```predict```函數會使用最佳模型，並預測從驗證資料集) 的 y (費用數量的值。 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2.  (RMSE 的根本平方誤差) 是由模型預測的範例值與觀察到的值之間的差異，經常使用的量值。 我們會藉由比較 y_test 資料框架與模型所預測的值，來計算結果的 mean 平方誤差。 

   此函數 ```mean_squared_error``` 會採用兩個數組，並計算兩者之間的平均平方誤差。 接著，我們會取得結果的平方根。 此標準指出計程車費用預測與實際車資值的距離。

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   「根本 mean」誤差是模型預測回應的精確度良好的衡量標準。 從結果中，您會看到模型相當適合用來預測資料集功能中的計程車車資，通常是在 +-$2.00

3. 執行下列程式碼，以計算平均絕對百分比誤差 (MAPE) 。 此計量會將精確度表示為錯誤的百分比。 它是藉由計算每個預測值與實際值之間的絕對差異，然後加總所有差異來完成這項工作。 然後，它會以實際值總計的百分比來表示該總和。

   ```python
   # Calculate MAPE and Model Accuracy 
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
   從兩個預測精確度度量，您會看到模型相當適合用來預測資料集功能中的計程車車資。 

4. 在調整線性回歸模型之後，我們現在需要判斷模型適合資料的程度。 為了達到此目的，我們會根據預測的輸出繪製實際的費用值。 此外，我們也會計算 R 平方值，以瞭解資料如何與擬合的迴歸線接近。

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![回歸圖的螢幕擷取畫面。](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   從結果中，我們可以看到 R 平方值帳戶的變異數為95%。 這也會由觀察到的實際圖來驗證。 回歸模型所占的變異數越多，資料點就會落在擬合的迴歸線上。  

## <a name="register-model-to-azure-machine-learning"></a>註冊模型以 Azure Machine Learning
一旦我們驗證了最佳模型之後，就可以註冊模型以 Azure Machine Learning。 註冊模型之後，您可以下載或部署已註冊的模型，並接收您註冊的所有檔案。

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>在 Azure Machine Learning 中查看結果
最後，您也可以流覽至 Azure Machine Learning 工作區中的實驗，以存取反覆運算的結果。 在這裡，您將能深入瞭解您的執行、已嘗試的模型和其他模型計量的狀態的其他詳細資料。 

![AML 工作區的螢幕擷取畫面。](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>後續步驟
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark MLlib 教學課程](./apache-spark-machine-learning-mllib-notebook.md)
