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
ms.date: 07/20/2020
ms.openlocfilehash: 3b5f5d64498922e9fc35942ff4570d801aa6c516
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98118875"
---
# <a name="analyze-with-apache-spark"></a>使用 Apache Spark 進行分析

在本教學課程中，您將了解使用適用於 Azure Synapse 的 Apache Spark 來載入和分析資料的基本步驟。

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-spark"></a>使用 Spark 分析 Blob 儲存體中的 NYC 計程車資料

1. 在 [資料] 中樞內，選取 [新增資源] (**已連結** 上方的加號按鈕) > [瀏覽資源庫]。
1. 選取 [NYC 計程車和禮車委員會 - 黃色計程車路線記錄]。
1. 選取頁面底部的 [繼續] > [新增資料集]。
1. 在 [連結] 下的 [資料] 中樞中，以滑鼠右鍵按一下 [Azure Blob 儲存體] 並選取 [範例資料集] > **nyc_tlc_yellow**。
1. 選取 [新增筆記本] 以使用下列程式碼建立新的筆記本：

    ```py
    from azureml.opendatasets import NycTlcYellow

    data = NycTlcYellow()
    data_df = data.to_spark_dataframe()
    display(data_df.limit(10))
    ```

1. 在筆記本中的 [連結至] 功能表中，選取無伺服器 Spark 集區。
1. 選取資料格上的 [執行]。
1. 如果您只要查看資料框架的結構描述，請使用下列程式碼來執行資料格：

    ```py
    data_df.printSchema()
    ```

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>將 NYC 計程車資料載入 Spark nyctaxi 資料庫

**SQLPOOL1** 的資料表中已有可用資料。 將其載入名為 **nyctaxi** 的 Spark 資料庫。

1. 在 Synapse Studio 中，移至 [開發] 中樞。
1. 選取 [+] > [Notebook]。
1. 在筆記本頂端，將 [附加至] 的值設定為 **Spark1**。
1. 選取 [新增程式碼] 以新增筆記本程式碼儲存格，然後輸入下列文字：

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLPOOL1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. 選取資料格上的 [執行]。
1. 在 [資料] 中樞，以滑鼠右鍵按一下 [資料庫]，然後選取 [重新整理]。 您應該會看到這些資料庫：

    - **SQLPOOL1 (SQL)**
    - **nyctaxi (Spark)**

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>使用 Spark 和筆記本來分析 NYC 計程車資料

1. 返回筆記本。
1. 建立新的程式碼儲存格，並輸入下列文字。

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. 執行儲存格，以顯示載入到 **nyctaxi** Spark 資料庫中的 NYC 計程車資料。
1. 執行下列程式碼，以執行先前對專用 SQL 集區 **SQLPOOL1** 執行的相同分析。 此程式碼會將分析的結果儲存到名為 **nyctaxi.passengercountstats** 的資料表中，並加以顯示。

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

先前您已將資料從專用 SQL 集區資料表 **SQLPOOL1.dbo.Trip** 複製到 Spark 資料表 **nyctaxi.trip**。 然後使用 Spark 將資料匯總到 Spark 資料表 **nyctaxi.passengercountstats** 中。 現在將資料從 **nyctaxi.passengercountstats** 複製到名為 **SQLPOOL1.dbo.PassengerCountStats** 的專用 SQL 集區資料表。

在您的筆記本中執行下列儲存格。 其會將彙總的 Spark 資料表複製回專用 SQL 集區資料表。

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLPOOL1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用無伺服器 SQL 集區來分析資料](get-started-analyze-sql-on-demand.md)
