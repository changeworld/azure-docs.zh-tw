---
title: 快速入門：開始使用 Spark 進行分析
description: 在本教學課程中，您將了解如何使用 Apache Spark 來分析資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2feabda5ea3f0c0748b92de9fcb7ef05abbdcf4c
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209435"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 進行分析

在本教學課程中，您將了解使用適用於 Azure Synapse 的 Apache Spark 來載入和分析資料的基本步驟。

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>使用 Spark 分析 Blob 儲存體中的 NYC 計程車資料

1. 在 **資料** 中樞內，按一下 **+** 按鈕以新增 **資源**，然後按一下 [ >> 流覽資源 **庫]**。 
1. 尋找 **NYC 計程車和禮車委員會 - 黃色計程車路線記錄**，然後在其上方按一下。 
1. 按一下頁面底部的 [ **繼續**]，然後 **新增資料集**。 
1. 經過一段時間之後，在 [已 **連結**] 下的 **資料** 中樞內，以滑鼠右鍵按一下 **Azure Blob 儲存體 >> 範例資料集] >> nyc_tlc_yellow** 然後選取 [**新增筆記本**]，然後 **載入至資料框架**。
1. 這會使用下列程式碼建立新的 Notebook：
    ```

    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    # Display 10 rows
    display(data_df.limit(10))
    ```
1. 在筆記本的 [ **附加至** ] 功能表中，選擇我們稍早建立的 **Spark1** 無伺服器 Spark 集區。
1. 選取資料格上的 [執行]
1. 如果您只想要查看資料框架的結構描述，請使用下列程式碼來執行資料格：
    ```

    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>將 NYC 計程車資料載入 Spark nyctaxi 資料庫

**SQLPOOL1** 的資料表中已有可用資料。 將其載入名為 **nyctaxi** 的 Spark 資料庫。

1. 在 Synapse Studio 中，移至 [開發] 中樞。
1. 選取 [+] > [Notebook]。
1. 在筆記本頂端，將 [附加至] 的值設定為 **Spark1**。
1. 在新筆記本的第一個程式碼儲存格中，輸入下列程式碼：


    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```


1. 執行指令碼。 可能需要2-3 分鐘的時間。
1. 在 **資料** 中樞的 [ **工作區** ] 索引標籤中，以滑鼠右鍵按一下 [ **資料庫**]， **然後選取 [** 重新整理]。 您現在應該會在清單中看到資料庫 **nyctaxidb.nyctaxi (Spark)** 。


## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和筆記本來分析 NYC 計程車資料

1. 返回筆記本。
1. 建立新的程式碼資料格，然後輸入下列程式碼。 


   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 執行儲存格，以顯示我們載入 **Nyctaxidb.nyctaxi** Spark 資料庫的 NYC 計程車資料。
1. 建立新的程式碼資料格，然後輸入下列程式碼。 然後執行資料格，以執行與專用 SQL 集區 **SQLPOOL1** 稍早所做的相同分析。 這段程式碼會儲存分析結果，並將其顯示為名為 **nyctaxidb.nyctaxi. passengercountstats** 的資料表。


   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. 在資料格結果中，選取 [圖表] 可查看視覺化的資料。

## <a name="load-data-from-a-spark-table-into-a-dedicated-sql-pool-table"></a>將資料從 Spark 資料表載入專用 SQL 集區資料表

先前我們已將資料從專用 SQL 集區資料表 **SQLPOOL1.dbo.Trip** 複製到 Spark 資料表 **nyctaxi.trip**。 然後使用 Spark 將資料匯總到 Spark 資料表 **nyctaxi.passengercountstats** 中。 現在，您會將資料從 **nyctaxidb.nyctaxi** 複製到名為 SQLPOOL1 的專用 SQL 集區資料表。 **passengercountstats**。

1. 建立新的程式碼資料格，然後輸入下列程式碼。 在筆記本中執行儲存格。 其會將彙總的 Spark 資料表複製回專用 SQL 集區資料表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用無伺服器 SQL 集區來分析資料](get-started-analyze-sql-on-demand.md)
