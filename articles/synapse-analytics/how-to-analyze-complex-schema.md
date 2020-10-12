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
ms.openlocfilehash: 51422bd47b5bd2d7d5103c154e90eaa910396024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89661018"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>分析 Azure Synapse Analytics 中的複雜資料類型

本文適用于 [Azure Cosmos DB 的 Azure Synapse 連結](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)中的 Parquet 檔案和容器。 您可以使用 Spark 或 SQL 來讀取或轉換具有複雜架構（例如陣列或嵌套結構）的資料。 下列範例是使用單一檔完成的，但可以輕鬆地使用 Spark 或 SQL 擴充至數十億份檔。 本文所含的程式碼會使用 PySpark (Python) 。

## <a name="use-case"></a>使用案例

複雜的資料類型逐漸普及，並代表資料工程師所面臨的挑戰。 分析嵌套架構和陣列可能牽涉到耗時且複雜的 SQL 查詢。 此外，重新命名或轉換嵌套資料行資料類型可能很困難。 此外，當您使用深層的嵌套物件時，可能會遇到效能問題。

資料工程師必須瞭解如何有效率地處理複雜的資料類型，並讓每個人都能輕鬆存取這些資料類型。 在下列範例中，您會使用 Azure Synapse Analytics 中的 Spark，透過資料框架將物件讀取和轉換成平面結構。 您可以在 Azure Synapse Analytics 中使用 SQL 的無伺服器模型，直接查詢這類物件，並以一般資料表的形式傳回這些結果。

## <a name="what-are-arrays-and-nested-structures"></a>什麼是陣列和嵌套結構？

以下是來自 [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)的物件。 在此物件中，有包含嵌套結構的嵌套結構和陣列。

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
當您使用命令列印物件資料框架的架構 (稱為 **df**) 時 `df.printschema` ，您會看到下列標記法：

* 黃色表示嵌套結構。
* 綠色代表具有兩個元素的陣列。

[![以黃色和綠色醒目提示顯示的程式碼，顯示架構原點](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

`_rid``_ts` `_etag` 當檔已內嵌至 Azure Cosmos DB 的交易式存放區時，已將、和新增至系統。

先前的資料框架會計算5個數據行和1個數據列的計數。 轉換之後，策劃資料框架會有13個數據行和2個數據列，採用表格格式。

## <a name="flatten-nested-structures-and-explode-arrays"></a>簡維嵌套結構和分裂陣列

使用 Azure Synapse Analytics 中的 Spark 時，很容易就能將嵌套結構轉換成資料行，並將陣列元素轉換成多個資料列。 使用下列步驟來執行。

[![顯示 Spark 轉換步驟的流程圖](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>定義函式來壓平合併架構

您可以在不變更的情況下使用此函數。 在 [PySpark 筆記本](quickstart-apache-spark-notebook.md) 中使用下列函式建立資料格：

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

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用函式來壓平合併式架構

在此步驟中，您會將資料框架的嵌套架構 (**df**) 壓平合併成新的資料框架 (`df_flat`) ：

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

顯示函數應該會傳回10個數據行和1個數據列。 陣列與其嵌套的元素仍然存在。

### <a name="transform-the-array"></a>轉換陣列

在這裡，您會將 `context_custom_dimensions` 資料框架中的陣列轉換 `df_flat` 成新的資料框架 `df_flat_explode` 。 在下列程式碼中，您也可以定義要選取的資料行：

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

顯示函數應該會傳回10個數據行和2個數據列。 下一步是使用步驟1中定義的函式，將嵌套的架構壓平合併。

### <a name="use-the-function-to-flatten-the-nested-schema"></a>使用函式來壓平合併式架構

最後，您會使用函式，將資料框架的嵌套架構壓平 `df_flat_explode` 合併到新的資料框架中 `df_flat_explode_flat` ：
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

顯示功能應該會顯示13個數據行和2個數據列。

資料框架的函數會傳回 `printSchema` `df_flat_explode_flat` 下列結果：

[![顯示最終架構的程式碼](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>直接讀取陣列和嵌套結構

使用 SQL 的無伺服器模型，您可以查詢和建立此類物件的視圖和資料表。

首先，視資料的儲存方式而定，使用者應該使用下列分類法。 大寫顯示的所有專案都是您的使用案例專用：

| 大量 | [格式] |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2) |
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = ACCOUNTNAME; database = DATABASENAME; collection = COLLECTIONNAME; region = REGIONTOQUERY '，SECRET = ' YOURSECRET ' |' CosmosDB ' (Azure Synapse 連結) |


取代每個欄位，如下所示：
* 「您的大量更新」是您所連接之資料來源的連接字串。
* 「您上面的型別」是您用來連接到來源的格式。

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

有兩種不同的作業類型：

- 第一個作業類型會在下列程式碼中指出，此程式碼會定義名 `contextdataeventTime` 為的資料行，該資料行會參考嵌套的元素 `Context.Data.eventTime` 。 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  這一行會定義名為的資料行，該資料行會 `contextdataeventTime` 參考嵌套的元素 `Context>Data>eventTime` 。

- 第二個作業類型會使用 `cross apply` 來為數組下的每個元素建立新的資料列。 然後，它會定義每個嵌套的物件。 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  如果陣列有5個具有4個嵌套結構的元素，則 SQL 的無伺服器模型會傳回5個數據列和4個數據行。 SQL 的無伺服器模型可以就地查詢、對應2個數據列中的陣列，並將所有的嵌套結構都顯示在資料行中。

## <a name="next-steps"></a>後續步驟

* [瞭解如何使用 Spark 查詢 Azure Cosmos DB 的 Synapse 連結](./synapse-link/how-to-query-analytical-store-spark.md)
* [查詢 Parquet 巢狀型別](./sql/query-parquet-nested-types.md) 
