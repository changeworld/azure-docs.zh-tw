---
title: 教學課程：使用 Apache Spark MLlib 建置機器學習應用程式
description: 本教學課程說明如何使用 Apache Spark MLlib 來建立機器學習應用程式，以透過羅吉斯回歸使用分類來分析資料集。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 39ba8a5884abb4be9fa0b8e32a292e06738e1550
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935645"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>教學課程：使用 Apache Spark MLlib 和 Azure Synapse Analytics 來建置機器學習應用程式

在本文中，您將了解如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/) 建立機器學習應用程式，在 Azure 開放資料集上進行簡單預測性分析。 Spark 提供內建的機器學習程式庫。 這個範例會透過羅吉斯迴歸使用「分類」。

SparkML 和 MLlib 是核心 Spark 程式庫，提供許多適用于機器學習工作的公用程式，包括適用于的公用程式：

- 分類
- 迴歸
- 叢集
- 主題模型化
- 奇異值分解 (SVD) 和主體元件分析 (PCA)
- 假設測試和計算範例統計資料

## <a name="understand-classification-and-logistic-regression"></a>了解分類和羅吉斯迴歸

分類是常見的機器學習工作，是指將輸入資料依類別排序的程序。 分類演算法的工作是要找出如何指派 *標籤* 給您所提供的輸入資料。 例如，您可以將機器學習演算法想像成可接受股票資訊做為輸入，並將股票分成兩個類別：您應銷售的股票和應該保留的股票。

「羅吉斯迴歸」是您可以用於分類的演算法。 Spark 的羅吉斯迴歸 API 可用於 *二元分類*，或用來將輸入資料歸類到兩個群組之一。 如需羅吉斯回歸的詳細資訊，請參閱 [維琪百科](https://en.wikipedia.org/wiki/Logistic_regression)。

總而言之，羅吉斯回歸的程式會產生 *羅吉斯* 函式，您可以用它來預測輸入向量屬於某個群組或另一個群組的機率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>NYC 計程車資料的預測性分析範例

在此範例中，您會使用 Spark 對紐約的計程車行程提示資料執行一些預測性分析。 您可以透過 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得資料。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間及位置、成本和其他有趣的屬性。

> [!IMPORTANT]
> 從其儲存體位置提取此資料可能會產生額外費用。

在下列步驟中，您會開發模型來預測特定行程是否包含小費。

## <a name="create-an-apache-spark-machine-learning-model"></a>建立 Apache Spark 機器學習模型

1. 使用 PySpark 核心建立筆記本。 如需相關指示，請參閱 [建立筆記本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
2. 匯入此應用程式所需的類型。 將下列程式碼複製並貼到空白儲存格中，然後按下 Shift + Enter。 或使用程式碼左邊的藍色播放圖示來執行儲存格。

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 內容。

## <a name="construct-the-input-dataframe"></a>建造輸入資料框架

由於原始資料採用的是 Parquet 格式，因此您可以使用 Spark 內容直接將檔案提取至記憶體中作為資料框架。 雖然下列步驟中的程式碼會使用預設選項，但您可以視需要強制執行資料類型和其他架構屬性的對應。

1. 藉由將程式碼貼入新的資料格中，執行下列幾行來建立 Spark 資料框架。 此步驟會透過開放資料集 API 來抓取資料。 提取所有資料會產生大約 15 億個資料列。 

   根據您的無伺服器 Apache Spark 集區大小而定，原始資料可能會太大或花費太多時間來操作。 您可以將此資料篩選成較小的項目。 下列程式碼範例會使用 `start_date` 和 `end_date` 來套用篩選，以傳回單一月份的資料。

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. 簡單篩選的缺點是，從統計觀點來看，它可能會對資料造成偏差。 另一種方法是使用 Spark 內建的取樣。 

   下列程式碼會將資料集縮減至大約2000個數據列（如果它在上述程式碼之後套用的話）。 您可以使用這個取樣步驟，而不是簡單的篩選準則，也可以搭配簡單的篩選準則。

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. 現在可以查看資料以查看已讀取的內容。 根據資料集的大小而定，通常最好是使用子集來檢查資料，而不是完整的資料集。 

   下列程式碼提供兩種方式來查看資料。 第一種方式是基本。 第二種方式提供更豐富的格線體驗，以及以圖形方式視覺化資料的功能。

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. 視所產生的資料集大小而定，您需要實驗或執行筆記本的次數很多，您可能會想要在本機工作區中快取資料集。 有三種方式可執行明確的快取：

   - 在本機將資料框架儲存為檔案。
   - 將資料框架儲存為臨時表或視圖。
   - 將資料框架儲存為永久資料表。

下列程式碼範例中包含這兩種方法的其中一種。

建立臨時表或視圖可提供不同的資料存取路徑，但它只會在 Spark 實例會話期間持續。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>準備資料

原始格式的資料通常不適合直接傳遞至模型。 您必須對資料執行一系列的動作，使其進入模型可以取用的狀態。

在下列程式碼中，您會執行四個類別的作業：

- 透過篩選移除極端值或不正確的值。
- 移除不需要的資料行。
- 建立衍生自原始資料的新資料行，讓模型的工作更有效率。 這項操作有時稱為特徵化。
- 標籤。 因為您正在執行二元分類 (在指定的行程) 上將會有秘訣，所以需要將秘訣數量轉換成0或1值。

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

然後再對資料進行第二次傳遞，以新增最終功能。

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

最後一個工作是將加上標籤的資料轉換成可透過羅吉斯回歸進行分析的格式。 羅吉斯回歸演算法的輸入必須是一組 *標籤/特性向量配對*，其中 *功能向量* 是代表輸入點的數位向量。 

因此，您需要將類別資料行轉換成數位。 具體而言，您需要將 `trafficTimeBins` 和資料 `weekdayString` 行轉換成整數標記法。 有多種方法可以執行轉換。 下列範例會採用 `OneHotEncoder` 常見的方法。

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

此動作會產生一個新的資料框架，其中包含正確格式的所有資料行來定型模型。

## <a name="train-a-logistic-regression-model"></a>定型羅吉斯迴歸模型

第一個工作是將資料集分割成定型集和測試或驗證集。 此處的分割是任意的。 使用不同的分割設定進行實驗，以查看它們是否會影響模型。

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

現在有兩個數據框架，下一項工作是建立模型公式，並針對定型資料框架執行。 然後，您可以針對測試資料框架進行驗證。 使用不同版本的模型公式進行實驗，以查看不同組合的影響。

> [!Note]
> 若要儲存模型，您需要 *儲存體 Blob 資料參與者* Azure 角色。 在您的儲存體帳戶底下，移至 [ **存取控制] (IAM)** ，然後選取 [ **新增角色指派**]。 將儲存體 Blob 資料參與者角色指派給您的 Azure SQL Database 伺服器。 只有具備擁有者許可權的成員可以執行此步驟。 
>
>如需各種 Azure 內建角色，請參閱 [本指南](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

此儲存格的輸出為：

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>建立預測的視覺表示法

您現在可以建構最終的視覺效果，以利研判此測試的結果。 [ROC 曲線](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是檢查結果的一種方式。

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![顯示 tip 模型中羅吉斯回歸之 ROC 曲線的圖形。](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>關閉 Spark 執行個體

當您完成執行應用程式之後，關閉此索引標籤來關閉筆記本以釋放資源。或者，從筆記本底部的 [狀態] 面板中選取 [ **結束會話** ]。

## <a name="see-also"></a>另請參閱

- [概觀：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark 官方文件](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> 某些官方 Apache Spark 檔相依于使用 Spark 主控台，在 Azure Synapse Analytics 的 Apache Spark 上無法使用。 請改用 [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 或 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 體驗。