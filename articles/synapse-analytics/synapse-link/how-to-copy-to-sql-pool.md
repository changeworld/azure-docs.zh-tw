---
title: 使用 Apache Spark 將適用於 Azure Cosmos DB 的 Synapse Link 資料複製到專用 SQL 集區
description: 將資料載入 Spark 資料框架中、策展資料，並將其載入專用 SQL 集區資料表
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: synapse-link
ms.date: 08/10/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 13891f9614e658be39adbb69fed1503a0c66d5e4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309210"
---
# <a name="copy-data-from-azure-cosmos-db-into-a-dedicated-sql-pool-using-apache-spark"></a>使用 Apache Spark 將資料從 Azure Cosmos DB 複製到專用 SQL 集區

適用於 Azure Cosmos DB 的 Synapse Link 可讓使用者對 Azure Cosmos DB 中的操作資料執行近乎即時的分析。 不過，有時候有些資料需要進行彙總和擴充，才能服務資料倉儲使用者。 只需透過筆記本的幾個資料格即可策展和匯出 Synapse Link 資料。

## <a name="prerequisites"></a>先決條件
* 使用下列項目[佈建 Synapse 工作區](../quickstart-create-workspace.md)：
    * [無伺服器 Apache Spark 集區](../quickstart-create-apache-spark-pool-studio.md)
    * [專用 SQL 集區](../quickstart-create-sql-pool-studio.md)
* [以具有資料的 HTAP 容器佈建 Cosmos DB 帳戶](../../cosmos-db/configure-synapse-link.md)
* [將 Azure Cosmos DB HTAP 容器連線到工作區](./how-to-connect-synapse-link-cosmos-db.md)
* [進行正確設定以將資料從 Spark 匯入到專用 SQL 集區](../spark/synapse-spark-sql-pool-import-export.md)

## <a name="steps"></a>步驟
在本教學課程中，您將連線到分析存放區，因此不會影響交易存放區 (其不會取用任何要求單位)。 我們會進行下列步驟：
1. 將 Cosmos DB HTAP 容器讀入 Spark 資料框架中
2. 在新的資料框架中彙總結果
3. 將資料內嵌至專用 SQL 集區

[![Spark 至 SQL 的步驟 1](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png)](../media/synapse-link-spark-to-sql/synapse-spark-to-sql.png#lightbox)

## <a name="data"></a>資料
在該範例中，我們使用名為 **RetailSales** 的 HTAP 容器。 此容器是名為 **ConnectedData** 的連結服務一部分，而且具有下列結構描述：
* _rid: string (nullable = true)
* _ts: long (nullable = true)
* logQuantity: double (nullable = true)
* productCode: string (nullable = true)
* quantity: long (nullable = true)
* price: long (nullable = true)
* id: string (nullable = true)
* advertising: long (nullable = true)
* storeId: long (nullable = true)
* weekStarting: long (nullable = true)
* _etag: string (nullable = true)

我們會依據 *productCode* 和 *weekStarting* 來彙總銷售額 ( *數量* 、 *收益* (價格 x 數量)) 以供報告之用。 最後，我們會將該資料匯出到名為 **dbo.productsales** 的專用 SQL 集區資料表。

## <a name="configure-a-spark-notebook"></a>設定 Spark 筆記本
使用 Scala as Spark (Scala) 作為主要語言來建立 Spark 筆記本。 我們會針對工作階段使用筆記本的預設設定。

## <a name="read-the-data-in-spark"></a>讀取 Spark 中的資料
使用 Spark 將 Cosmos DB HTAP 容器讀入第一個資料格中的資料框架內。

```java
val df_olap = spark.read.format("cosmos.olap").
    option("spark.synapse.linkedService", "ConnectedData").
    option("spark.cosmos.container", "RetailSales").
    load()
```

## <a name="aggregate-the-results-in-a-new-dataframe"></a>在新的資料框架中彙總結果

在第二個資料格中，我們會先執行新資料框架所需的轉換和彙總，然後再將其載入至專用 SQL 集區資料庫。

```java
// Select relevant columns and create revenue
val df_olap_step1 = df_olap.select("productCode","weekStarting","quantity","price").withColumn("revenue",col("quantity")*col("price"))
//Aggregate revenue, quantity sold and avg. price by week and product ID
val df_olap_aggr = df_olap_step1.groupBy("productCode","weekStarting").agg(sum("quantity") as "Sum_quantity",sum("revenue") as "Sum_revenue").
    withColumn("AvgPrice",col("Sum_revenue")/col("Sum_quantity"))
```

## <a name="load-the-results-into-a-dedicated-sql-pool"></a>將結果載入到專用 SQL 集區

在第三個資料格中，我們會將資料載入到專用 SQL 集區。 其會自動建立暫存的外部資料表、外部資料來源和外部檔案格式，並於作業完成後立即刪除。

```java
df_olap_aggr.write.sqlanalytics("userpool.dbo.productsales", Constants.INTERNAL)
```

## <a name="query-the-results-with-sql"></a>使用 SQL 查詢結果

您可以使用簡單的 SQL 查詢 (如下列 SQL 指令碼) 來查詢結果：
```sql
SELECT  [productCode]
,[weekStarting]
,[Sum_quantity]
,[Sum_revenue]
,[AvgPrice]
 FROM [dbo].[productsales]
```

您的查詢將會以圖表模式呈現下列結果：[![Spark 至 SQL 的步驟 2](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png)](../media/synapse-link-spark-to-sql/sql-script-spark-sql.png#lightbox)

## <a name="next-steps"></a>後續步驟
* [使用 Apache Spark 查詢 Azure Cosmos DB 分析存放區](./how-to-query-analytical-store-spark.md)