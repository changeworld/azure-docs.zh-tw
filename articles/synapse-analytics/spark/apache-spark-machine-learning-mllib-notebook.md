---
title: 使用 Apache Spark MLlib 和 Azure 突觸分析建構機器學習應用
description: 瞭解如何使用 Apache Spark MLlib 創建機器學習應用,該應用程式使用邏輯回歸的分類分析數據集。
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick, carlrab
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 9dc4047b9e95b088bb614858091f43286cefe361
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430002"
---
# <a name="build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>使用 Apache Spark MLlib 和 Azure 突觸分析建構機器學習應用

在本文中,您將瞭解如何使用 Apache Spark [MLlib](https://spark.apache.org/mllib/)創建機器學習應用程式,該應用程式在 Azure 打開的數據集上執行簡單的預測分析。 Spark 提供內建機器學習庫。 此範例使用邏輯來歸*類別*。

MLlib 是一個核心 Spark 庫,它提供了許多可用於機器學習任務的實用程式,包括適用於以下功能的實用程式:

- 分類
- 迴歸
- 叢集
- 主題模型化
- 奇異值分解 (SVD) 和主體元件分析 (PCA)
- 假設測試和計算範例統計資料

## <a name="understand-classification-and-logistic-regression"></a>了解分類和羅吉斯迴歸

分類** 是常見的機器學習工作，是指將輸入資料依類別排序的程序。 分類演演算法的工作是找出如何為提供的輸入資料分配*標籤*。 例如,您可以想到機器學習演演演算法,該演演演算法接受股票資訊作為輸入,並將股票分為兩類:您應該出售的股票和應保留的股票。

*邏輯回歸*是可用於分類的演演演算法。 Spark 的羅吉斯迴歸 API 可用於*二元分類*，或用來將輸入資料歸類到兩個群組之一。 如需羅吉斯迴歸的詳細資訊，請參閱 [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression)。

總之，羅吉斯迴歸的程序會產生一個*羅吉斯函數* ，此函數可用來預測輸入向量可能屬於哪一個群組的機率。

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>紐約市計程車數據預測分析示例

在此示例中,您可以使用 Spark 對來自紐約的計程車行程提示數據執行一些預測性分析。 資料可透過 Azure[開放資料集 。](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 數據集的此子集包含有關黃色計程車行程的資訊,包括有關每次行程、開始和結束時間和地點、成本和其他有趣屬性的資訊。

> [!IMPORTANT]
> 從存儲位置提取此數據可能會收取額外費用。

在以下步驟中,您將開發一個模型來預測特定行程是否包含提示。

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>建立 Apache Spark MLlib 機器學習應用程式

1. 使用 PySpark 內核創建筆記本。 有關說明,請參閱[創建筆記本](./apache-spark-notebook-create-spark-use-sql.md#create-a-notebook)。
2. 匯入此應用程式所需的類型。 複製以下代碼並將其貼上到空單元格中,然後按**SHIFT + ENTER**,或使用代碼左側的藍色播放圖示運行單元格。

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

    由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您運行第一個代碼單元時,將自動為您創建 Spark 上下文。

## <a name="construct-the-input-dataframe"></a>建構輸入資料框架

由於原始數據採用 Parquet 格式,因此可以使用 Spark 上下文將檔直接作為數據幀放入記憶體。 雖然下面的代碼使用默認選項,但如果需要,可以強制映射數據類型和其他架構屬性。

1. 通過將代碼粘貼到新單元格中,運行以下行以創建 Spark 數據幀。 第一節將 Azure 儲存存取資訊分配給變數。 第二部分允許 Spark 從 Blob 存儲遠端讀取。 最後一行代碼讀取鑲木地板,但此時未載入任何數據。

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

2. 拉動所有這些數據會產生大約 15 億行。 根據 Spark 池(預覽)的大小,原始數據可能太大或操作時間過長。 您可以將此資料篩選為更小的數據。 如果需要,添加以下行以將數據篩選到大約 200 萬行,以便獲得回應更靈敏的體驗。 使用這些參數提取一周的數據。

    ```python
    # Create an ingestion filter
    start_date = '2018-05-01 00:00:00'
    end_date = '2018-05-08 00:00:00'

    filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')
    ```

3. 簡單篩選的缺點是,從統計角度來看,它可能會引入數據偏差。 另一種方法是使用Spark中內置的採樣。 如果在上面的代碼之後應用,以下代碼可將數據集減少到大約 2000 行。 此採樣步驟可以代替簡單篩選器使用,也可以與簡單篩選器結合使用。

    ```python
    # To make development easier, faster and less expensive down sample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

4. 現在可以查看數據以查看已讀取的內容。 通常最好使用子集而不是完整集查看數據,具體取決於數據集的大小。 以下代碼提供了兩種查看數據的方法:前者是基本數據,後者是基本代碼,後者提供了更豐富的網格體驗,以及以圖形方式可視化數據的能力。

    ```python
    sampled_taxi_df.show(5)
    display(sampled_taxi_df.show(5))
    ```

5. 根據生成的數據集大小的大小以及多次試驗或運行筆記本的需要,最好在工作區中本地緩存數據集。 執行顯式緩存有三種方法:

   - 儲存資料盒儲存為檔案
   - 將資料盒另存為暫存表或檢視
   - 將資料盒另存為永久表

這些方法的前 2 個包含在以下代碼示例中。

創建臨時表或視圖提供對數據的不同訪問路徑,但僅在Spark實例會話的持續期間持續。

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="understand-the-data"></a>了解資料

通常,此時您將經歷*一個探索性數據分析*(EDA) 階段,以了解數據。 以下代碼顯示了與提示相關的三種不同的數據可視化效果,這些提示導致對數據的狀態和品質下結論。

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

![直方圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-histogram.png)
![框 Whisker](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-box-whisker.png)
![繪圖散射圖](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-eda-scatter.png)

## <a name="preparing-the-data"></a>準備資料

原始形式的數據通常不適合直接傳遞到模型。 必須對數據執行一系列操作,才能使其進入模型可以使用它的狀態。

在下面的代碼中,將執行四類操作:

- 通過篩選刪除異常值/不正確的值。
- 刪除不需要的列。
- 創建從原始數據派生的新列,使模型更有效地工作,有時稱為"壯舉"。
- 標籤,因為您正在進行二進位分類(是否會在給定行程中有提示或沒有),因此需要將小費金額轉換為 0 或 1 值。

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

然後通過數據進行第二次傳遞以添加最終要素。

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

最後一項工作，是將加上標籤的資料轉換成可依羅吉斯迴歸進行分析的格式。 邏輯回歸演演演算法的輸入需要是一組*標籤特徵向量對*,其中*特徵向量*是表示輸入點的數位向量。 因此,我們需要將分類列轉換為數位。 和`trafficTimeBins``weekdayString`列需要轉換為整數表示形式。 執行轉換的方法有多種,但本示例中採用的方法是*OneHotEncode,* 這是一種常見的方法。

```python
# The sample uses an algorithm that only works with numeric features convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new dataframe that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

這將導致一個新的數據框,所有列都採用正確的格式來訓練模型。

## <a name="train-a-logistic-regression-model"></a>訓練邏輯回歸模型

第一個任務是將數據集拆分為訓練集和測試或驗證集。 此處的拆分是任意的,您應該使用不同的拆分設置來四處播放,以查看它們是否影響模型。

```python
#Decide on the split between training and testing data from the dataframe
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the dataframe into test and training dataframes
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

現在有兩個 DataFrame,下一個任務是創建模型公式,並針對訓練數據幀運行它,然後根據測試 DataFrame 進行驗證。 您應該嘗試模型公式的不同版本,以查看不同組合的影響。

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

從此儲存格的輸出是

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>建立預測的視覺表示法

您現在可以建構最終的視覺效果，以利研判此測試的結果。 [ROC 曲線](https://en.wikipedia.org/wiki/Receiver_operating_characteristic)是查看結果的一種方式。

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

![邏輯回歸提示模型的 ROC 曲線](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-mllib-nyctaxi-roc.png "邏輯回歸提示模型的 ROC 曲線")

## <a name="shut-down-the-spark-instance"></a>關閉 Spark 實體

運行完應用程式後,關閉筆記本以釋放資源,關閉選項卡或從筆記本底部的狀態面板中選擇 **「結束會話**」。。

## <a name="see-also"></a>另請參閱

- [概述:Azure 突觸分析上的 Apache 火花](apache-spark-overview.md)

## <a name="next-steps"></a>後續步驟

- [阿帕奇火花文檔的 .NET](/dotnet/spark?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [阿帕契火花官方檔](https://spark.apache.org/docs/latest/)

>[!NOTE]
> 某些官方的 Apache Spark 文件依賴於使用 Spark 控制台,這在 Azure 同步 Spark 上不可用。 改用[筆記本](../spark/apache-spark-notebook-create-spark-use-sql.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)或[IntelliJ](../spark/intellij-tool-synapse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)體驗。
