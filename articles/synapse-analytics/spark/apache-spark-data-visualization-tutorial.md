---
title: 使用 Apache Spark 將資料視覺化
description: 使用 Apache Spark 和 Azure Synapse Analytics 筆記本建立豐富的資料視覺效果
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: machine-learning
ms.date: 10/20/2020
ms.author: midesa
ms.openlocfilehash: 820dd8b607f5ec2fdc44d25063e0a941f76237ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919496"
---
# <a name="analyze-data-with-apache-spark"></a>使用 Apache Spark 分析資料

在本教學課程中，您將瞭解如何使用 Azure 開放資料集來執行探索式資料分析，然後 Apache Spark，然後在 Azure Synapse Studio 筆記本中將結果視覺化。

尤其是，我們會分析 [紐約城市 (NYC) 計程車](https://azure.microsoft.com/en-us/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/) 資料集。 您可以透過 Azure 開放資料集取得資料。 此資料集的子集包含與黃色計程車行程相關的資訊，包括每個行程的相關資訊、開始時間和結束時間和位置、成本和其他有趣的屬性。
  
## <a name="before-you-begin"></a>開始之前
- 遵循[建立 Apache Spark 集區教學](../articles/../quickstart-create-apache-spark-pool-studio.md)課程來建立 Apache Spark 集區 

## <a name="download-and-prepare-the-data"></a>下載並準備資料
1. 使用 PySpark 核心建立筆記本。 如需相關指示，請參閱 [建立筆記本](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook)。 
   
> [!Note]
> 
> 由於使用 PySpark 核心，因此不需要明確建立任何內容。 當您執行第一個程式碼儲存格時，系統會自動為您建立 Spark 內容。
>

2. 在本教學課程中，我們將使用數個不同的程式庫，以協助我們將資料集視覺化。 若要進行這項分析，我們必須匯入下列程式庫： 

```python
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
```

3. 因為未經處理資料採用 Parquet 格式，所以您可以使用 Spark 內容，直接將檔案當作資料框架提取至記憶體中。 透過開放資料集 API 擷取資料，以建立 Spark 資料框架。 在這裡，我們將 *在 read 屬性上* 使用 Spark 資料框架架構，以推斷資料類型和架構。

```python
from azureml.opendatasets import NycTlcYellow
from datetime import datetime
from dateutil import parser

end_date = parser.parse('2018-06-06')
start_date = parser.parse('2018-05-01')
nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
df = nyc_tlc.to_spark_dataframe()
```

4. 讀取資料之後，我們會想要進行一些初始篩選以清除資料集。 我們可能會移除不必要的資料行，並新增可解壓縮重要資訊的其他資料行。 此外，我們也會篩選出資料集內的異常。

```python
# Filter the dataset 
from pyspark.sql.functions import *

filtered_df = df.select('vendorID', 'passengerCount', 'tripDistance','paymentType', 'fareAmount', 'tipAmount'\
                                , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                , dayofweek('tpepPickupDateTime').alias('day_of_week')\
                                , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month'))\
                            .filter((df.passengerCount > 0)\
                                & (df.tipAmount >= 0)\
                                & (df.fareAmount >= 1) & (df.fareAmount <= 250)\
                                & (df.tripDistance > 0) & (df.tripDistance <= 200))

filtered_df.createOrReplaceTempView("taxi_dataset")
```

## <a name="analyze-data"></a>分析資料
 作為資料分析師，您有各式各樣的工具可協助您從資料中解壓縮見解。 在本教學課程的這個部分中，我們將逐步解說 Azure Synapse Analytics 筆記本內可用的一些有用工具。 在這項分析中，我們想要瞭解在所選期間內產生更高計程車提示的因素。

###  <a name="apache-spark-sql-magic"></a>Apache Spark SQL 魔術 
首先，我們將使用 Synapse 筆記本 Apache Spark SQL 和魔術命令來執行探索式資料分析。 一旦有了查詢之後，我們就會使用內建功能來將結果視覺化 ```chart options``` 。 

1. 在筆記本中建立新的儲存格，並複製下列程式碼。 使用此查詢時，我們想要瞭解平均提示數量在我們選取的期間內如何變更。 此查詢也可協助我們識別其他有用的見解，包括每天的最小/最大 tip 數量和平均費用金額。
   
```sql
%%sql
SELECT 
    day_of_month
    , MIN(tipAmount) AS minTipAmount
    , MAX(tipAmount) AS maxTipAmount
    , AVG(tipAmount) AS avgTipAmount
    , AVG(fareAmount) as fareAmount
FROM taxi_dataset 
GROUP BY day_of_month
ORDER BY day_of_month ASC
```

2. 當查詢完成執行之後，就可以藉由切換至 **圖表視圖** 來將結果視覺化。 在此範例中，我們會將 **line chart** ```day_of_month``` 欄位指定為索引 **鍵**，並將其指定 ```avgTipAmount``` 為 **值**，以建立折線圖。 完成選取後 **，按一下 [** 套用] 以重新整理圖表。 
   
## <a name="visualize-data"></a>顯現資料
除了內建的筆記本圖表選項以外，您也可以使用熱門的開放原始碼程式庫來建立自己的視覺效果。 在下列範例中，我們將使用以 seaborn 和 Matplotlib，這是常用於資料視覺效果的 Python 程式庫。 

> [!Note]
> 
> 根據預設，每個 Azure Synapse Analytics Apache Spark 集區都會包含一組常用和預設的程式庫。 您可以在 [Azure Synapse 運行](../spark/apache-spark-version-support.md)時間中查看程式庫的完整清單。 文件。 此外，若要讓您的應用程式可以使用協力廠商或本機建立的程式碼，您可以將連結 [庫安裝](../spark/apache-spark-azure-portal-add-libraries.md) 到您的其中一個 Spark 集區 (預覽) 。
>

1. 為了讓開發變得更容易且成本更低，我們會將資料集的取樣降至最低。 我們將使用內建的 Apache Spark 取樣功能。 此外，以 seaborn 和 Matplotlib 都需要 Pandas 資料框架或 Numpy 陣列。 為了取得 Pandas 資料框架，我們將使用 ```toPandas()``` 命令來轉換我們的資料框架。

```python
# To make development easier, faster and less expensive down sample for now
sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

# The charting package needs a Pandas dataframe or numpy array do the conversion
sampled_taxi_pd_df = sampled_taxi_df.toPandas()
```

1. 首先，我們想要瞭解資料集中的秘訣分佈。 我們將使用 Matplotlib 來建立長條圖，以顯示 tip 數量和計數的分佈。 根據分佈的不同，我們可以看到提示會扭曲至小於或等於 $10 的數量。

```python
# Look at tips by count histogram using Matplotlib

ax1 = sampled_taxi_pd_df['tipAmount'].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()
```

![秘訣的長條圖](./media/apache-spark-machine-learning-mllib-notebook/histogram.png)

1. 接下來，我們想要瞭解指定行程的秘訣與星期幾之間的關聯性。 我們將使用以 seaborn 來建立一個方塊圖，以摘要說明一周中每一天的趨勢。 

```python
# View the distribution of tips by day of week using Seaborn
ax = sns.boxplot(x="day_of_week", y="tipAmount",data=sampled_taxi_pd_df, showfliers = False)
ax.set_title('Tip amount distribution per day')
ax.set_xlabel('Day of Week')
ax.set_ylabel('Tip Amount ($)')
plt.show()

```
![每日的秘訣分佈](./media/apache-spark-data-viz/data-analyst-tutorial-per-day.png)

4. 現在還有另一個假設，那就是乘客數目與計程車提示總金額之間有正面的關聯性。 為了確認此關聯性，我們會執行下列程式碼來產生盒狀圖，以說明每個乘客計數的秘訣分佈。 
   
```python
# How many passengers tipped by various amounts 
ax2 = sampled_taxi_pd_df.boxplot(column=['tipAmount'], by=['passengerCount'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
ax2.set_ylim(0,30)
plt.suptitle('')
plt.show()
```
![盒狀圖](./media/apache-spark-machine-learning-mllib-notebook/box-whisker-plot.png)

1. 最後，我們想要瞭解費用量提示金額之間的關聯性。 根據結果，我們可以看到有些人沒有提示的觀察結果。 不過，我們也會看到整體費用和秘訣量之間有正面的關聯性。
   
```python
# Look at the relationship between fare and tip amounts

ax = sampled_taxi_pd_df.plot(kind='scatter', x= 'fareAmount', y = 'tipAmount', c='blue', alpha = 0.10, s=2.5*(sampled_taxi_pd_df['passengerCount']))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.suptitle('')
plt.show()
```
![散佈圖](./media/apache-spark-machine-learning-mllib-notebook/scatter.png)

## <a name="shut-down-the-spark-instance"></a>關閉 Spark 執行個體

當您完成執行應用程式之後，請藉由關閉索引標籤，或從筆記本底部的狀態面板中選取 [結束工作階段]，關閉筆記本以釋放資源。

## <a name="see-also"></a>另請參閱

- [概觀：Azure Synapse Analytics 上的 Apache Spark](apache-spark-overview.md)
- [使用 Apache SparkML 建立機器學習模型](../spark/apache-spark-machine-learning-mllib-notebook.md)

## <a name="next-steps"></a>後續步驟

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Apache Spark 官方文件](https://spark.apache.org/docs/latest/)