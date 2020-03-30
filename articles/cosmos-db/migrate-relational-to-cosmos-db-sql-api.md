---
title: 將一對幾個關係資料移轉到 Azure Cosmos DB SQL API
description: 瞭解如何處理 SQL API 中一對一關聯性的複雜資料移轉
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: thvankra
ms.openlocfilehash: 467e9627a2623779bd808ca5aebdf76d8a5eda42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75896632"
---
# <a name="migrate-one-to-few-relational-data-into-azure-cosmos-db-sql-api-account"></a>將一對少數關係資料移轉到 Azure Cosmos DB SQL API 帳戶

為了從關係資料庫移轉到 Azure Cosmos DB SQL API，可能需要對資料模型進行更改以進行優化。

一個常見的轉換是通過在一個 JSON 文檔中嵌入相關的子項來使資料非正常化。 在這裡，我們來查看一些選項，用於使用 Azure 資料工廠或 Azure 資料塊。 有關 Cosmos DB 資料建模的一般指南，請查看[Azure Cosmos DB 中的資料建模](modeling-data.md)。  

## <a name="example-scenario"></a>範例案例

假設我們的 SQL 資料庫中有以下兩個表，即訂單和訂單詳細資訊。


![Order Details](./media/migrate-relational-to-cosmos-sql-api/orders.png)

我們希望在遷移期間將這種一對一的關係合併到一個 JSON 文檔中。 為此，我們可以使用下面的"FOR JSON"創建 T-SQL 查詢：

```sql
SELECT
  o.OrderID,
  o.OrderDate,
  o.FirstName,
  o.LastName,
  o.Address,
  o.City,
  o.State,
  o.PostalCode,
  o.Country,
  o.Phone,
  o.Total,
  (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
FROM Orders o;
```

此查詢的結果如下所示： 

![Order Details](./media/migrate-relational-to-cosmos-sql-api/for-json-query-result.png#lightbox)


理想情況下，您希望使用單個 Azure 資料工廠 （ADF） 複製活動將 SQL 資料作為源進行查詢，並將輸出直接寫入 Azure Cosmos DB 接收器作為適當的 JSON 物件。 目前，無法在一個複製活動中執行所需的 JSON 轉換。 如果我們嘗試將上述查詢的結果複製到 Azure Cosmos DB SQL API 容器中，我們將將 OrderDetails 欄位視為文檔的字串屬性，而不是預期的 JSON 陣列。

我們可以通過以下方式之一解決當前限制：

* **將 Azure 資料工廠與兩個複製活動一起：** 
  1. 從 SQL 獲取 JSON 格式的資料到中間 blob 存儲位置中的文字檔，以及 
  2. 將資料從 JSON 文字檔載入到 Azure Cosmos DB 中的容器。

* **使用 Azure 資料塊從 SQL 讀取並寫入 Azure Cosmos DB** - 我們將在此處提供兩個選項。


讓我們更詳細地瞭解這些方法：

## <a name="azure-data-factory"></a>Azure Data Factory

儘管我們不能在目標 Cosmos DB 文檔中將 OrderDetails 嵌入為 JSON 陣列，但我們可以使用兩個單獨的複製活動來解決此問題。

### <a name="copy-activity-1-sqljsontoblobtext"></a>複製活動#1：SqlJson 到 Blob文本

對於來源資料，我們使用 SQL 查詢將結果集作為單個列，每行使用 SQL Server OPENJSON 和 FOR JSON PATH 功能每行包含一個 JSON 物件（表示順序）：

```sql
SELECT [value] FROM OPENJSON(
  (SELECT
    id = o.OrderID,
    o.OrderDate,
    o.FirstName,
    o.LastName,
    o.Address,
    o.City,
    o.State,
    o.PostalCode,
    o.Country,
    o.Phone,
    o.Total,
    (select OrderDetailId, ProductId, UnitPrice, Quantity from OrderDetails od where od.OrderId = o.OrderId for json auto) as OrderDetails
   FROM Orders o FOR JSON PATH)
)
```

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf1.png)


對於 SqlJsonToBlobText 複製活動的接收器，我們選擇"分隔文本"並將其指向 Azure Blob 存儲中具有動態生成的唯一檔案名的特定資料夾（例如，'（@concat管道）。RunId，'.json'）。
由於我們的文字檔不是真正的"分隔"，我們不希望使用逗號將其解析為單獨的列，並且希望保留雙引號（），我們將"列分隔符號"設置為 Tab （"\t"） - 或資料中未出現的另一個字元 - 和"報價字元"到"無引號字元"。

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf2.png)

### <a name="copy-activity-2-blobjsontocosmos"></a>複製活動#2：BlobJson 到宇宙

接下來，我們通過添加第二個複製活動來修改我們的 ADF 管道，該活動在 Azure Blob 存儲中查找由第一個活動創建的文字檔。 它處理它作為"JSON"源，以在文字檔中找到的每個 JSON 行作為一個文檔插入 Cosmos DB 接收器。

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf3.png)

或者，我們還向管道添加"刪除"活動，以便它刪除每次運行之前 /Orders/ 資料夾中剩餘的所有以前檔。 我們的 ADF 管道現在如下所示：

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf4.png)

在觸發上面的管道後，我們看到在我們的中間 Azure Blob 存儲位置創建的檔，每行包含一個 JSON 物件：

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf5.png)

我們還看到訂單文檔，其中插入了正確的訂單詳細資訊，並插入到我們的 Cosmos 資料庫集合中：

![ADF 副本](./media/migrate-relational-to-cosmos-sql-api/adf6.png)


## <a name="azure-databricks"></a>Azure Databricks

我們還可以使用[Azure 資料塊](https://azure.microsoft.com/services/databricks/)中的 Spark 將資料從 SQL 資料庫源複製到 Azure Cosmos DB 目標，而無需在 Azure Blob 存儲中創建中間文本/JSON 檔。 

> [!NOTE]
> 為清楚和簡單起見，下面的程式碼片段明確內聯包含虛擬資料庫密碼，但應始終使用 Azure 資料磚塊機密。
>

首先，我們創建所需的 SQL[連接器](https://docs.databricks.com/data/data-sources/sql-databases-azure.html)和[Azure Cosmos DB 連接器](https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html)庫到 Azure 資料塊群集。 重新開機群集以確保載入庫。

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks1.png)

接下來，我們介紹兩個示例，用於 Scala 和 Python。 

### <a name="scala"></a>Scala
在這裡，我們將 SQL 查詢的結果與"FOR JSON"輸出到資料幀：

```scala
// Connect to Azure SQL https://docs.databricks.com/data/data-sources/sql-databases-azure.html
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
val configSql = Config(Map(
  "url"          -> "xxxx.database.windows.net",
  "databaseName" -> "xxxx",
  "queryCustom"  -> "SELECT o.OrderID, o.OrderDate, o.FirstName, o.LastName, o.Address, o.City, o.State, o.PostalCode, o.Country, o.Phone, o.Total, (SELECT OrderDetailId, ProductId, UnitPrice, Quantity FROM OrderDetails od WHERE od.OrderId = o.OrderId FOR JSON AUTO) as OrderDetails FROM Orders o",
  "user"         -> "xxxx", 
  "password"     -> "xxxx" // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
))
// Create DataFrame from Azure SQL query
val orders = sqlContext.read.sqlDB(configSql)
display(orders)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks2.png)

接下來，我們將連接到 Cosmos 資料庫和集合：

```scala
// Connect to Cosmos DB https://docs.databricks.com/data/data-sources/azure/cosmosdb-connector.html
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
import org.joda.time._
import org.joda.time.format._
import com.microsoft.azure.cosmosdb.spark.schema._
import com.microsoft.azure.cosmosdb.spark.CosmosDBSpark
import com.microsoft.azure.cosmosdb.spark.config.Config
import org.apache.spark.sql.functions._
val configMap = Map(
  "Endpoint" -> "https://xxxx.documents.azure.com:443/",
  // NOTE: For clarity and simplicity, this example includes secrets explicitely as a string, but you should always use Databricks secrets
  "Masterkey" -> "xxxx",
  "Database" -> "StoreDatabase",
  "Collection" -> "Orders")
val configCosmos = Config(configMap)
```

最後，我們定義架構並使用from_json在將資料幀保存到 CosmosDB 集合之前應用資料幀。

```scala
// Convert DataFrame to proper nested schema
import org.apache.spark.sql.types._
val orderDetailsSchema = ArrayType(StructType(Array(
    StructField("OrderDetailId",IntegerType,true),
    StructField("ProductId",IntegerType,true),
    StructField("UnitPrice",DoubleType,true),
    StructField("Quantity",IntegerType,true)
  )))
val ordersWithSchema = orders.select(
  col("OrderId").cast("string").as("id"),
  col("OrderDate").cast("string"),
  col("FirstName").cast("string"),
  col("LastName").cast("string"),
  col("Address").cast("string"),
  col("City").cast("string"),
  col("State").cast("string"),
  col("PostalCode").cast("string"),
  col("Country").cast("string"),
  col("Phone").cast("string"),
  col("Total").cast("double"),
  from_json(col("OrderDetails"), orderDetailsSchema).as("OrderDetails")
)
display(ordersWithSchema)
// Save nested data to Cosmos DB
CosmosDBSpark.save(ordersWithSchema, configCosmos)
```

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks3.png)


### <a name="python"></a>Python

作為替代方法，您可能需要在 Spark 中執行 JSON 轉換（如果源資料庫不支援"FOR JSON"或類似操作），或者您可能希望對非常大的資料集使用平行作業。 在這裡，我們提出了一個PySpark示例。 首先在第一個單元中配置源資料庫和目標資料庫連接：

```python
import uuid
import pyspark.sql.functions as F
from pyspark.sql.functions import col
from pyspark.sql.types import StringType,DateType,LongType,IntegerType,TimestampType
 
#JDBC connect details for SQL Server database
jdbcHostname = "jdbcHostname"
jdbcDatabase = "OrdersDB"
jdbcUsername = "jdbcUsername"
jdbcPassword = "jdbcPassword"
jdbcPort = "1433"
 
connectionProperties = {
  "user" : jdbcUsername,
  "password" : jdbcPassword,
  "driver" : "com.microsoft.sqlserver.jdbc.SQLServerDriver"
}
jdbcUrl = "jdbc:sqlserver://{0}:{1};database={2};user={3};password={4}".format(jdbcHostname, jdbcPort, jdbcDatabase, jdbcUsername, jdbcPassword)
 
#Connect details for Target Azure Cosmos DB SQL API account
writeConfig = {
    "Endpoint": "Endpoint",
    "Masterkey": "Masterkey",
    "Database": "OrdersDB",
    "Collection": "Orders",
    "Upsert": "true"
}
```

然後，我們將查詢源資料庫（在本例中為 SQL Server）的訂單和訂單詳細資訊記錄，將結果放入 Spark 資料幀中。 我們還將創建一個清單，其中包含所有訂單 ID，以及用於平行作業的執行緒池：

```python
import json
import ast
import pyspark.sql.functions as F
import uuid
import numpy as np
import pandas as pd
from functools import reduce
from pyspark.sql import SQLContext
from pyspark.sql.types import *
from pyspark.sql import *
from pyspark.sql.functions import exp
from pyspark.sql.functions import col
from pyspark.sql.functions import lit
from pyspark.sql.functions import array
from pyspark.sql.types import *
from multiprocessing.pool import ThreadPool
 
#get all orders
orders = sqlContext.read.jdbc(url=jdbcUrl, table="orders", properties=connectionProperties)
 
#get all order details
orderdetails = sqlContext.read.jdbc(url=jdbcUrl, table="orderdetails", properties=connectionProperties)
 
#get all OrderId values to pass to map function 
orderids = orders.select('OrderId').collect()
 
#create thread pool big enough to process merge of details to orders in parallel
pool = ThreadPool(10)
```

然後，創建一個函數，用於將訂單寫入目標 SQL API 集合。 此函數將篩選給定訂單 ID 的所有訂單詳細資訊，將它們轉換為 JSON 陣列，並將陣列插入到 JSON 文檔中，我們將該文檔寫入該訂單的目標 SQL API 集合中：

```python
def writeOrder(orderid):
  #filter the order on current value passed from map function
  order = orders.filter(orders['OrderId'] == orderid[0])
  
  #set id to be a uuid
  order = order.withColumn("id", lit(str(uuid.uuid1())))
  
  #add details field to order dataframe
  order = order.withColumn("details", lit(''))
  
  #filter order details dataframe to get details we want to merge into the order document
  orderdetailsgroup = orderdetails.filter(orderdetails['OrderId'] == orderid[0])
  
  #convert dataframe to pandas
  orderpandas = order.toPandas()
  
  #convert the order dataframe to json and remove enclosing brackets
  orderjson = orderpandas.to_json(orient='records', force_ascii=False)
  orderjson = orderjson[1:-1] 
  
  #convert orderjson to a dictionaory so we can set the details element with order details later
  orderjsondata = json.loads(orderjson)
  
  
  #convert orderdetailsgroup dataframe to json, but only if details were returned from the earlier filter
  if (orderdetailsgroup.count() !=0):
    #convert orderdetailsgroup to pandas dataframe to work better with json
    orderdetailsgroup = orderdetailsgroup.toPandas()
    
    #convert orderdetailsgroup to json string
    jsonstring = orderdetailsgroup.to_json(orient='records', force_ascii=False)
    
    #convert jsonstring to dictionary to ensure correct encoding and no corrupt records
    jsonstring = json.loads(jsonstring)
    
    #set details json element in orderjsondata to jsonstring which contains orderdetailsgroup - this merges order details into the order 
    orderjsondata['details'] = jsonstring
 
  #convert dictionary to json
  orderjsondata = json.dumps(orderjsondata)
 
  #read the json into spark dataframe
  df = spark.read.json(sc.parallelize([orderjsondata]))
  
  #write the dataframe (this will be a single order record with merged many-to-one order details) to cosmos db using spark the connector
  #https://docs.microsoft.com/azure/cosmos-db/spark-connector
  df.write.format("com.microsoft.azure.cosmosdb.spark").mode("append").options(**writeConfig).save()
```

最後，我們將使用執行緒池上的映射函式呼叫上述函數，以並存執行，在之前創建的順序 ID 清單中傳遞：

```python
#map order details to orders in parallel using the above function
pool.map(writeOrder, orderids)
```
在這兩種方法中，在結束時，我們都應該在 Cosmos DB 集合中的每個訂單文檔中正確保存嵌入的訂單詳細資訊：

![Databricks](./media/migrate-relational-to-cosmos-sql-api/databricks4.png)

## <a name="next-steps"></a>後續步驟
* 瞭解[Azure Cosmos DB 中的資料建模](https://docs.microsoft.com/azure/cosmos-db/modeling-data)
* [瞭解如何在 Azure Cosmos DB 上建模和分區資料](https://docs.microsoft.com/azure/cosmos-db/how-to-model-partition-example)
