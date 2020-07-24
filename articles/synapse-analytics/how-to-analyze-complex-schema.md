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
ms.openlocfilehash: ad6761466cc958235557609e929e641a0311ee43
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999157"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>分析 Azure Synapse 分析中的複雜資料類型

本文與 Parquet 檔案和[Synapse 連結中的](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)容器有關 Azure Cosmos DB。 它會說明使用者如何使用 Spark 或 SQL 來讀取或轉換具有複雜架構（例如陣列或嵌套結構）的資料。 下列範例是以單一檔完成，但可以使用 Spark 或 SQL 輕鬆地調整成數十億份檔。 本文中所包含的程式碼會使用 PySpark （Python）。

## <a name="use-case"></a>使用案例

複雜的資料型別日益普遍，而且對資料工程師而言，分析嵌套架構和陣列的挑戰通常包含耗時且複雜的 SQL 查詢。 此外，也很難以重新命名或轉換已嵌套的資料行資料類型。 此外，使用深度嵌套的物件時，也會發生效能問題。

資料工程師必須瞭解如何有效率地處理複雜的資料類型，讓所有人都能輕鬆存取。

在下列範例中，Synapse Spark 是用來透過資料框架，將物件讀取和轉換成平面結構。 Synapse SQL 無伺服器是用來直接查詢這類物件，並將這些結果當做一般資料表傳回。

## <a name="what-are-arrays-and-nested-structures"></a>什麼是陣列和嵌套結構？

下列物件來自[應用程式深入](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)解析。 在此物件中，有包含嵌套結構的嵌套結構和陣列。

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
使用命令列印物件的資料框架（稱為**df**）的架構時 `df.printschema` ，我們會看到下列標記法：

* 黃色色彩代表嵌套結構
* 綠色色彩代表具有兩個元素的陣列

[![架構來源](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**、 **_ts**和 **_etag**已新增至系統，因為檔已內嵌到 Azure Cosmos DB 交易存放區中。

上述資料框架只會計算5個數據行和1個數據列。 在轉換之後，策劃資料框架會有13個數據行和2個以表格格式提供的資料列。

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>將嵌套的結構壓平合併，並使用 Apache Spark 來分解陣列

有了 Synapse Spark，您可以輕鬆地將嵌套結構轉換成資料行，並將陣列元素轉換成多個資料列。 下列步驟可用於執行。

[![Spark 轉換步驟](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**步驟 1**：我們定義函式來壓平合併式架構。 此函式可在不變更的情況下使用。 使用下列功能在[PySpark 筆記本](quickstart-apache-spark-notebook.md)中建立資料格：

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

**步驟 2**：使用函式將資料框架（**df**）的嵌套架構壓平合併為新的資料框架 `df_flat` ：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

Display 函數應該會傳回10個數據行和1個數據列。 陣列及其嵌套元素仍然存在。

**步驟 3**：將 `context_custom_dimensions` 資料框架中的陣列轉換 `df_flat` 成新的資料框架 `df_flat_explode` 。 在下列程式碼中，我們也會定義要選取的資料行：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

Display 函數應該會傳回10個數據行和2個數據列。 下一個步驟是使用步驟1中定義的函式來將嵌套的架構壓平合併。

**步驟 4**：使用函式將資料框架的嵌套架構壓平 `df_flat_explode` 合併到新的資料框架中 `df_flat_explode_flat` ：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

Display 函數應該會顯示13個數據行和2個數據列。

資料框架的功能會傳回 `printSchema` `df_flat_explode_flat` 下列結果：

[![架構最終](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>直接使用 SQL 無伺服器讀取陣列和嵌套結構

使用 SQL 無伺服器，可以查詢和建立這類物件的視圖和資料表。

首先，視資料的儲存方式而定，使用者應該使用下列分類法。 以大寫顯示的所有內容都是您的使用案例所特有：

| BULK              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' （ADLSg2）|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = REGIONTOQUERY，SECRET = ' YOURSECRET ' |' CosmosDB ' （Synapse 連結）|


> [!NOTE]
> SQL 無伺服器將支援適用于 Azure Cosmos 和 AAD 傳遞之 Synapse 連結的連結服務。 此功能目前在 [Synapse] 連結的 [閘道預覽] 之下。

取代每個欄位，如下所示：
* ' 您的 BULK 以上 ' = 您所連接之資料來源的連接字串
* [您的類型高於] = 用來連線到來源的格式

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

有兩種不同類型的作業：

第一種作業類型會在下列程式程式碼中指出，這會定義名 `contextdataeventTime` 為的資料行，其參考的是 nested 元素： eventTime。 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

這一行會定義名為 coNtextdataeventTime 的資料行，其參考的是嵌套元素： CoNtext>Data>eventTime

第二種作業類型會使用 `cross apply` ，針對陣列下的每個元素建立新的資料列，然後使用來定義每個嵌套物件，類似于第一個專案符號點： 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

如果陣列有5個具有4個嵌套結構的元素，SQL 無伺服器會傳回5個數據列和4個數據行。 SQL 無伺服器可以就地查詢，將陣列對應到2個數據列，並將所有的嵌套結構顯示在資料行中。

## <a name="next-steps"></a>接下來的步驟

* [瞭解如何使用 Spark 查詢 Azure Cosmos DB 的 Synapse 連結](./synapse-link/how-to-query-analytical-store-spark.md)
* [查詢 parquet 的巢狀型別](./sql/query-parquet-nested-types.md) 