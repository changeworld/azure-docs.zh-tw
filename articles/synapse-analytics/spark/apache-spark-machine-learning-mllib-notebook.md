---
title: 使用 Apache Spark MLlib 和 Azure Synapse 分析來建立機器學習應用程式
description: 瞭解如何使用 Apache Spark MLlib 來建立機器學習應用程式，以透過羅吉斯回歸使用分類來分析資料集。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 25d11d2cf41f8653c5a54007f121c1251bb24b1f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/23/2020
ms.locfileid: "82096294"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>使用 Apache Spark MLlib 和 Azure Synapse 分析來建立機器學習應用程式

在本文中，您將瞭解如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/)來建立機器學習應用程式，以對 Azure 開放資料集執行簡單的預測性分析。 Spark 提供內建的機器學習程式庫。 這個範例會透過羅吉斯回歸來使用*分類*。

MLlib 是核心 Spark 程式庫，提供許多適用于機器學習工作的公用程式，包括適用于的公用程式：

- 分類
- 迴歸
- 叢集
- 主題模型化
- 奇異值分解 (SVD) 和主體元件分析 (PCA)
- 假設測試和計算範例統計資料

## <a name="understand-classification-and-logistic-regression"></a>了解分類和羅吉斯迴歸

分類** 是常見的機器學習工作，是指將輸入資料依類別排序的程序。 分類演算法的工作是要找出如何將*標籤*指派給您所提供的輸入資料。 例如，您可以將機器學習演算法視為接受庫存資訊做為輸入，並將股票分成兩個類別：您應該銷售的股票和您應該保留的股票。

*羅吉斯回歸*是一種可用於分類的演算法。 Spark 的羅吉斯迴歸 API 可用於*二元分類*，或用來將輸入資料歸類到兩個群組之一。 如需羅吉斯迴歸的詳細資訊，請參閱 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)。

總之，羅吉斯迴歸的程序會產生一個*羅吉斯函數* ，此函數可用來預測輸入向量可能屬於哪一個群組的機率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 計程車資料的預測性分析範例

在此範例中，您會使用 Spark 對紐約的計程車行程秘訣資料執行一些預測性分析。 資料可透過[Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間和位置、成本和其他有趣的屬性。

> [!IMPORTANT]
> 從儲存位置提取此資料可能會產生額外費用。

在下列步驟中，您會開發模型來預測特定旅程是否包含提示。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>建立 Apache Spark MLlib 機器學習應用程式

1. 使用 PySpark 核心建立筆記本。 如需指示，請參閱[建立筆記本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
2. 匯入此應用程式所需的類型。 複製下列程式碼並貼到空白儲存格中，然後按**SHIFT + enter**鍵，或使用程式碼左邊的藍色播放圖示來執行儲存格。

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 內容。

## <a name="construct-the-input-dataframe"></a>建構輸入資料框架

因為原始資料採用 Parquet 格式，所以您可以使用 Spark 內容直接將檔案以資料框架的形式提取至記憶體中。 雖然下列程式碼使用預設選項，但您可以視需要強制對應資料類型和其他架構屬性。

1. 執行下列幾行，將程式碼貼入新的儲存格來建立 Spark 資料框架。 第一個區段會將 Azure 儲存體存取訊號指派給變數。 第二個區段允許 Spark 從遠端讀取 blob 儲存體。 最後一行程式碼會讀取 parquet，但此時不會載入任何資料。

    ```python
    # Azure storage access info
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow SPARK to read from Blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # SPARK read parquet, note that it won't load any data yet by now
    df = spark.read.parquet(wasbs_path)
    ```

2. 提取所有這項資料會產生大約1500000000個數據列。 根據您的 Spark 集區大小（預覽），原始資料可能太大，或花費太多時間來操作。 您可以將此資料篩選成較小的專案。 如有需要，請新增下列幾行，將資料篩選到大約2000000個數據列，以獲得更快的回應體驗。 使用這些參數來提取一周的資料。

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 簡單篩選的缺點是，從統計觀點來看，它可能會導致資料偏差。 另一種方法是使用 Spark 內建的取樣。 如果在上述程式碼之後套用，下列程式碼會將資料集縮減為大約2000個數據列。 此取樣步驟可以用來取代簡單的篩選器，或與簡單的篩選準則搭配使用。

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 您現在可以查看資料，以查看已讀取的內容。 根據資料集的大小而定，通常最好是使用子集來檢查資料，而不是完整的集合。 下列程式碼提供兩種方式來查看資料：前者是基本的，後者提供更豐富的方格體驗，以及以圖形方式視覺化資料的功能。

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 根據所產生的資料集大小，以及您需要實驗或執行筆記本的需求而定，建議您在工作區中的本機快取資料集。 有三種方式可執行明確的快取：

   - 以檔案的形式將資料框架儲存在本機
   - 將資料框架儲存為臨時表或視圖
   - 將資料框架儲存為永久資料表

下列程式碼範例包含這些方法的前兩個。

建立臨時表或 view 會提供不同的資料存取路徑，但只會持續在 Spark 實例會話的持續時間內。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>了解資料

一般來說，您會經歷*探索資料分析*（EDA）的階段，以瞭解資料。 下列程式碼顯示的三種不同的資料視覺效果，會導致資料狀態和品質的相關結論。

```python
# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()

# Look at tips by amount count histogram
ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# How many passengers tip'd by various amounts
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# Look at the relationship between fare and tip amounts
ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```

![長條圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![方塊框](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![圖散佈圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>準備資料

原始格式的資料通常不適合直接傳遞至模型。 您必須對資料執行一系列的動作，才能使其進入模型可以取用它的狀態。

下列四個作業類別的程式碼會執行：

- 透過篩選移除極端值/不正確的值。
- 移除不需要的資料行。
- 建立衍生自原始資料的新資料行，讓模型更有效率地運作，有時又稱為特徵化。
- 加上標籤，因為您正在進行二元分類（在指定的旅程上將會有提示，而不會有任何秘訣），因此需要將 tip 量轉換成0或1的值。

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

接著會對資料進行第二次傳遞，以加入最終功能。

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>建立羅吉斯迴歸模型

最後一項工作，是將加上標籤的資料轉換成可依羅吉斯迴歸進行分析的格式。 羅吉斯回歸演算法的輸入必須是一組*標籤-特性向量配對*，其中*特徵向量*是代表輸入點的數位向量。 因此，我們需要將類別資料行轉換成數位。 `trafficTimeBins`和`weekdayString`資料行必須轉換成整數表示。 有多種方法可以執行轉換，不過在此範例中採取的方法是*OneHotEncoding*，這是一種常見的方法。

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

這會產生新的資料框架，其中包含正確格式的所有資料行來定型模型。

## <a name="train-a-logistic-regression-model"></a>定型羅吉斯回歸模型

第一個工作是將資料集分割成定型集和測試或驗證集。 這裡的分割是任意的，您應該使用不同的分割設定來查看它們是否會影響模型。

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

現在有兩個數據框架，下一個工作是建立模型公式並對定型資料框架執行，然後針對 [測試] 資料框架進行驗證。 您應該使用不同的模型公式版本來進行實驗，以查看不同組合的影響。

```python
## Create a new LR object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create an LR model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional but its another form of inter session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset, evaluation using AUROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

此資料格的輸出為

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>建立預測的視覺表示法

您現在可以建構最終的視覺效果，以利研判此測試的結果。 [ROC 曲線](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是檢查結果的一種方式。

```python
## Plot the ROC curve, no need for pandas as this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![羅吉斯回歸秘訣模型的 ROC 曲線](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "羅吉斯回歸秘訣模型的 ROC 曲線")

## <a name="shut-down-the-spark-instance"></a>關閉 Spark 實例

當您完成執行應用程式後，請關閉此筆記本以釋放資源，方法是關閉索引標籤，或從筆記本底部的狀態面板中選取 [**結束會話**]。

## <a name="see-also"></a>另請參閱

- [總覽： Azure Synapse 分析的 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 官方文件](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 某些 Apache Spark 官方文件會依賴使用 Spark 主控台，但 Azure Synapse Spark 不提供這項功能。 請改用 [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 或 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 體驗。
