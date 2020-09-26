---
title: 使用 Apache Spark MLlib 建立機器學習應用程式
description: 了解如何使用 Apache Spark MLlib 建立一個透過羅吉斯迴歸使用分類來分析資料集的機器學習應用程式。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 2b641075a45db29c07b96c1934d4540f4c3292dd
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259980"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>使用 Apache Spark MLlib 和 Azure Synapse Analytics 來建置機器學習應用程式

在本文中，您將瞭解如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/) 來建立機器學習應用程式，以對 Azure 開放資料集執行簡單的預測性分析。 Spark 提供內建的機器學習程式庫。 這個範例會透過羅吉斯迴歸使用「分類」。

MLlib 是核心 Spark 程式庫，提供許多可用於機器學習工作的公用程式，包括具有下列用途的公用程式：

- 分類
- 迴歸
- 叢集
- 主題模型化
- 奇異值分解 (SVD) 和主體元件分析 (PCA)
- 假設測試和計算範例統計資料

## <a name="understand-classification-and-logistic-regression"></a>了解分類和羅吉斯迴歸

分類是常見的機器學習工作，是指將輸入資料依類別排序的程序。 這是以分類演算法指出如何為您所提供的輸入資料指派「標籤」的作業。 例如，試想某個機器學習演算法以股市資訊作為輸入，並且將股票分成兩個類別：該賣的股票和該留的股票。

「羅吉斯迴歸」是您可以用於分類的演算法。 Spark 的羅吉斯迴歸 API 可用於*二元分類*，或用來將輸入資料歸類到兩個群組之一。 如需羅吉斯迴歸的詳細資訊，請參閱 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)。

總之，羅吉斯迴歸的程序會產生一個*羅吉斯函數* ，此函數可用來預測輸入向量可能屬於哪一個群組的機率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>紐約市計程車資料的預測分析範例

在此範例中，您會使用 Spark 對紐約的計程車行程小費資料執行一些預測分析。 您可以透過 [Azure 開放資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)取得資料。 此資料集的子集包含黃色計程車行程的相關資訊，包括每個行程的相關資訊、開始和結束時間及位置、成本和其他有趣的屬性。

> [!IMPORTANT]
> 從其儲存體位置提取此資料可能會產生額外費用。

在下列步驟中，您會開發模型來預測特定行程是否包含小費。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>建立 Apache Spark MLlib 機器學習應用程式

1. 使用 PySpark 核心建立筆記本。 如需相關指示，請參閱[建立筆記本](../quickstart-apache-spark-notebook.md#create-a-notebook)。
2. 匯入此應用程式所需的類型。 複製下列程式碼並貼到空白儲存格中，然後按 **SHIFT + ENTER**，或使用程式碼左邊的藍色播放圖示來執行儲存格。

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

## <a name="construct-the-input-dataframe"></a>建構輸入資料框架

因為未經處理資料採用 Parquet 格式，所以您可以使用 Spark 內容，直接將檔案當作資料框架提取至記憶體中。 雖然下列程式碼使用預設選項，但是您可以視需要強制對應資料類型和其他結構描述屬性。

1. 執行下列幾行，將程式碼貼至新的儲存格來建立 Spark 資料框架。 這會透過開放式資料集 API 來抓取資料。 提取所有資料會產生大約 15 億個資料列。 根據您的 Spark 集區 (預覽) 大小，未經處理資料可能太大，或花費太多時間來操作。 您可以將此資料篩選成較小的項目。 下列程式碼範例會使用 start_date 和 end_date 來套用會傳回單一月份資料的篩選準則。

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. 從統計觀點來看，簡單篩選的缺點是可能會導致資料偏差。 另一種方法是使用 Spark 內建的取樣。 如果在上述程式碼之後套用，下列程式碼會將資料集縮減為大約 2000 個資料列。 此取樣步驟可以用來取代簡單篩選，或與簡單篩選搭配使用。

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. 您現在可以查看資料，以查看已讀取的內容。 根據資料集的大小而定，通常最好是使用子集來檢閱資料，而不是使用完整集合。 下列程式碼提供兩種方式來檢視資料：前者是基本的，後者提供更豐富的方格體驗，以及以圖形方式將資料視覺化的功能。

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. 視所產生的資料集大小大小而定，您需要實驗或執行筆記本多次，建議您在工作區本機快取資料集。 有三種方式可以執行明確的快取：

   - 以檔案的形式將資料框架儲存在本機
   - 將資料框架儲存為暫存資料表或檢視
   - 將資料框架儲存為永久資料表

下列程式碼範例包含這些方法的前兩個。

建立暫存資料表或檢視會提供不同的資料存取路徑，但是只會持續到 Spark 執行個體工作階段的持續時間。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>了解資料

一般來說，此時您會經歷「探索資料分析」(EDA) 的階段，以深入了解資料。 下列程式碼針對與小費 (導致狀態和資料品質的結論) 相關的資料，顯示三種不同的視覺效果。

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

# How many passengers tipped by various amounts
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
![盒狀圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![散佈圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="prepare-the-data"></a>準備資料

未經處理格式的資料通常不適合直接傳遞至模型。 您必須對資料執行一系列的動作，才能使其進入模型可以取用的狀態。

在下列程式碼中，會執行四個類別的作業：

- 透過篩選移除極端值/不正確的值。
- 移除不需要的資料行。
- 建立衍生自未經處理資料的新資料行，讓模型運作更有效率，有時稱為特徵化。
- 加上標籤-因為您正在進行二元分類 (在指定的行程中將會有秘訣) 需要將 tip 數量轉換成0或1值。

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

接著會對資料進行第二次傳遞，以新增最終特性。

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

最後一項工作，是將加上標籤的資料轉換成可依羅吉斯迴歸進行分析的格式。 羅吉斯迴歸演算法的輸入必須是一組「標籤-特性向量配對」，其中「特性向量」是代表輸入點的數字向量。 因此，我們需要將類別資料行轉換成數字。 `trafficTimeBins`和資料 `weekdayString` 行需要轉換成整數表示。 有多種方法可以執行轉換，不過在此範例中採取的方法是「OneHotEncoding」，這是一種常見的方法。

```python
# Since the sample uses an algorithm that only works with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

此動作會產生一個新的資料框架，其中包含正確格式的所有資料行來定型模型。

## <a name="train-a-logistic-regression-model"></a>定型羅吉斯迴歸模型

第一個工作是將資料集分割成定型集和測試或驗證集。 這裡的分割是任意的，您應該使用不同的分割設定來查看是否會影響模型。

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

現在有兩個資料框架，下一個工作是建立模型公式並對定型資料框架執行，然後針對測試資料框架進行驗證。 您應該使用不同版本的模型公式來進行實驗，以查看不同組合的影響。

> [!Note]
> 若要儲存模型，您將需要儲存體 Blob 資料參與者 Azure 角色。 在您的儲存體帳戶底下，流覽至 (IAM) 的 [存取控制]，然後選取 [ **新增角色指派**]。 將儲存體 Blob 資料參與者 Azure 角色指派給您的 SQL Database 伺服器。 僅有具備「擁有者」權限的成員才能執行此步驟。 如需各種 Azure 內建角色，請參閱此[指南](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)。

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

此儲存格的輸出為：

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>建立預測的視覺表示法

您現在可以建構最終的視覺效果，以利研判此測試的結果。 [ROC 曲線](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是檢閱結果的一種方式。

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

![羅吉斯迴歸小費模型的 ROC 曲線](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "羅吉斯迴歸小費模型的 ROC 曲線")

## <a name="shut-down-the-spark-instance"></a>關閉 Spark 執行個體

當您完成執行應用程式之後，請藉由關閉索引標籤，或從筆記本底部的狀態面板中選取 [結束工作階段]，關閉筆記本以釋放資源。

## <a name="see-also"></a>另請參閱

- [概觀：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)

## <a name="next-steps"></a>後續步驟

- [適用於 Apache Spark 的 .NET 文件](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 官方文件](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 某些 Apache Spark 官方文件會依賴使用 Spark 主控台，但 Azure Synapse Spark 不提供這項功能。 請改用 [Notebook](../quickstart-apache-spark-notebook.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 或 [IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 體驗。
