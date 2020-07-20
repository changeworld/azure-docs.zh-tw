---
title: 使用陣列和嵌套結構來分析架構
description: 如何使用 Apache Spark 和 SQL 分析陣列和嵌套結構
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84907899"
---
# <a name="analyze-complex-data-types-in-synapse"></a>分析 Synapse 中的複雜資料類型

本文適用于**Azure Cosmos DB 的 Azure Synapse 連結**中的 Parquet 檔案和容器。 它會說明使用者如何使用 Spark 或 SQL 來讀取或轉換具有複雜架構（例如陣列或嵌套結構）的資料。 下列範例是使用單一檔完成，但可以使用 Spark 或 SQL 輕鬆地調整為數十億份檔。 以下程式碼使用 PySpark （Python）。

## <a name="use-case"></a>使用案例

使用現代化資料類型時，複雜的資料類型通常是處理常見的，而且代表資料工程師的挑戰。 分析嵌套架構和陣列會帶來一些挑戰：
* 撰寫 SQL 查詢的複雜
* 不容易重新命名/轉換嵌套資料行的 datatype
* 遇到深度嵌套物件的效能問題

資料工程師必須瞭解如何有效率地處理這些資料類型，讓所有人都能輕鬆存取。

在下列範例中，Synapse Spark 將用來透過資料框架，將物件讀取和轉換成平面結構。 Synapse SQL 無伺服器是用來直接查詢這類物件，並將這些結果當做一般資料表來傳回。

## <a name="what-are-arrays-and-nested-structures"></a>什麼是陣列和嵌套結構？

下列物件來自應用程式深入解析。 在此物件中，有嵌套結構，但也有包含嵌套結構的陣列。

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>陣列和嵌套結構的架構範例
當您使用**printschema**命令來列印該物件之資料框架的架構（稱為**df**）時，我們會看到下列標記法：

* 黃色色彩代表 nested 結構
* 綠色色彩代表具有兩個元素的陣列

[![架構來源](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid、_ts 和 _etag 已在系統中新增，因為檔已內嵌到 Azure Cosmos DB 交易存放區中。

上述資料框架只會計算5個數據行和1個數據列。 在轉換之後，策劃資料框架會有13個數據行和2個以表格格式提供的資料列。

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>將嵌套的結構壓平合併，並使用 Apache Spark 來分解陣列

有了 Synapse Spark，將嵌套的結構轉換成資料行，並將陣列元素轉換成多個資料列，很容易。 每個人都可以使用下列步驟來執行自己的工作。

[![Spark 轉換步驟](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**步驟 1**：我們定義函式來壓平合併式架構。 此函式可在不變更的情況下使用。 在 Pyspark 筆記本中使用該函式建立資料格：

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**步驟 2**：使用函式將資料框架**df**的嵌套架構壓平合併為新的資料框架**df_flat**：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Display 函數應該會傳回10個數據行和1個數據列。 陣列及其嵌套元素仍然存在。

**步驟 3**：我們現在會將資料框架**df_flat**中的陣列**coNtext_custom_dimensions**轉換成新的資料框架**df_flat_explode**。 在下列程式碼中，我們也會定義我們選取的資料行：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Display 函數應會傳回下列結果：10個數據行和2個數據列。 下一個步驟是使用步驟1中定義的函式來將嵌套的架構壓平合併。

**步驟 4**：使用函式將資料框架**df_flat_explode**的嵌套架構壓平合併為新的資料框架**df_flat_explode_flat**：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Display 函數應該會顯示13個數據行和2個數據列：

資料框架 df_flat_explode_flat 的函數 printSchema 會傳回下列結果：

[![架構最終](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>直接使用 SQL 無伺服器讀取陣列和嵌套結構

使用 SQL 無伺服器，可以查詢、建立這類物件的視圖和資料表。

首先，視資料的儲存方式而定，使用者應該使用下列分類法。 大寫的所有內容都是您的使用案例所特有：

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' （ADLSg2）|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = REGIONTOQUERY，SECRET = ' YOURSECRET ' |' CosmosDB ' （Synapse 連結）|



**SQL 無伺服器**將支援適用于 AZURE COSMOS DB 和 AAD 傳遞之 Azure Synapse 連結的連結服務。 此功能目前在 [Synapse] 連結的 [閘道預覽] 之下。

取代下列內容：
* 由您所連接之資料來源的連接字串，' 您的上大量 '
* 依據您用來連線到來源的格式，' 上方的類型 '

```sql
select *
FROM
openrowset(
BULK 'YOUR BULK ABOVE',
            FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

已完成兩種不同類型的作業：
* 下列程式程式碼會定義名為 coNtextdataeventTime 的資料行，其參考了 nested 元素： eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

這一行會定義名為 coNtextdataeventTime 的資料行，其參考的是嵌套元素： CoNtext>Data>eventTime

* **cross apply**是用來為數組底下的每個元素建立新的資料列，然後使用定義每個嵌套物件，類似于第一個專案符號點： 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

如果陣列有5個具有4個嵌套結構的元素，SQL 無伺服器會傳回5個數據列和4個數據行。

SQL 無伺服器可以就地查詢，將陣列對應到2個數據列，並將所有的嵌套結構顯示在資料行中。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用 Spark 查詢適用于 Azure Cosmos DB 的 Azure Synapse 連結](./synapse-link/how-to-query-analytical-store-spark.md)
* [查詢 parquet 的巢狀型別](./sql/query-parquet-nested-types.md) 