---
title: 複製活動中的結構描述對應
description: 深入了解 Azure Data Factory 中的複製活動在複製資料時，如何將來源資料中的結構描述和資料類型對應到接收資料。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: jingwang
ms.openlocfilehash: 9ae07e2a471cc417b467092a2616a5a0cdafb1fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260809"
---
# <a name="schema-mapping-in-copy-activity"></a>複製活動中的結構描述對應

本文介紹 Azure 資料工廠複製活動在執行資料副本時如何執行架構映射和從來源資料到接收器資料的資料類型映射。

## <a name="schema-mapping"></a>結構描述對應

將資料從源複製到接收器時，將應用列映射。 預設情況下，將活動**映射來源資料複製到按列名稱進行接收器**。 您可以指定[顯式映射](#explicit-mapping)，以便根據需要自訂列映射。 更具體來說，複製活動會：

1. 從來源讀取資料，並判斷來源結構描述
2. 使用預設列映射按名稱映射列，或者如果指定，則應用顯式列映射。
3. 撰寫要接收的資料

### <a name="explicit-mapping"></a>顯式映射

您可以在複製活動 ->`translator` -> `mappings`屬性中指定要映射的列。 下面的示例定義管道中的副本活動，將資料從分隔的文本複製到 Azure SQL 資料庫。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

`translator`  ->  `mappings`以下屬性受支援的 -> 物件`source`與`sink`和

| 屬性 | 描述                                                  | 必要 |
| -------- | ------------------------------------------------------------ | -------- |
| NAME     | 源列或接收器列的名稱。                           | 是      |
| 序數  | 列索引。 從 1 開始。 <br>使用沒有標題列的分隔文本時應用和需要。 | 否       |
| path     | 要提取或映射的每個欄位的 JSON 路徑運算式。 應用分層資料，例如 MongoDB/REST。<br>對於根物件下的欄位，JSON 路徑以根 $開頭;對於`collectionReference`屬性選擇的陣列內的欄位，JSON 路徑從陣列元素開始。 | 否       |
| type     | 源或接收器列的資料工廠臨時資料類型。 | 否       |
| culture  | 源或接收器列的區域性。 <br>當類型為`Datetime`或`Datetimeoffset`時應用。 預設值為 `en-us`。 | 否       |
| format   | 格式字串，當類型為`Datetime`或`Datetimeoffset`時使用。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否       |

除了`translator` -> `mappings`物件`source`和 外，下面支援以下屬性`sink`。

| 屬性            | 描述                                                  | 必要 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 僅當分層資料（例如 MongoDB/REST）是源時，才受支援。<br>如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 | 否       |

### <a name="alternative-column-mapping"></a>替代列映射

您可以指定複製活動 ->`translator` -> `columnMappings`以在表格圖形資料之間映射。 在這種情況下，輸入資料集和輸出資料集都需要"結構"部分。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行****。 以下是會導致發生例外狀況的錯誤狀況：

* 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
* 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
* 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
* 重複的對應。

在下面的示例中，輸入資料集具有一個結構，它指向本地 Oracle 資料庫中的表。

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

在此示例中，輸出資料集具有一個結構，它指向 Salesfoce 中的表。

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

下列 JSON 定義了管線中的複製活動。 使用 **"翻譯器** -> **"列映射**屬性將源映射到接收器中的列的列。

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

如果您正在使用 `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 的語法指定資料行對應，則仍會依原狀支援該對應。

### <a name="alternative-schema-mapping"></a>替代架構映射

您可以指定複製活動 - `translator`  ->  `schemaMapping` >在分層圖形資料和表格圖形資料之間進行映射，例如從 MongoDB/REST 複製到文字檔，然後從 Oracle 複製到 Azure Cosmos DB 的 MongoDB API。 複製活動的 `translator` 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動轉換器的類型屬性設置為：**表格翻譯器** | 是 |
| schemaMapping | 鍵值對的集合，表示**從源端到接收器端**的映射關係。<br/>- **鍵：** 表示源。 對於**表格源**，指定資料集結構中定義的列名稱;對於**分層源**，指定要提取和映射的每個欄位的 JSON 路徑運算式。<br>- **值：** 表示接收器。 對於**表格接收器**，指定資料集結構中定義的列名稱;對於**分層接收器**，為要提取和映射的每個欄位指定 JSON 路徑運算式。 <br>對於分層資料，對於根物件下的欄位，JSON 路徑以根 $開頭;對於`collectionReference`屬性選擇的陣列內的欄位，JSON 路徑從陣列元素開始。  | 是 |
| collectionReference | 如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | 否 |

**示例：從蒙戈DB複製到 Oracle：**

例如，如果您有具備下列內容的 MongoDB 文件：

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

您想要壓平合併陣列內的資料 (order_pd 和 order_price)**，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市)** 交叉聯結︰

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

如下列複製活動 JSON 範例所示，設定結構描述對應規則：

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>資料類型對應

複製活動會使用下列 2 個步驟的方法，將來源類型對應至接收類型：

1. 從原生來源類型轉換成 Azure Data Factory 過渡資料類型
2. 從 Azure Data Factory 過渡資料類型轉換成原生接收類型

您可以在每個連接器主題的＜資料類型對應＞一節中找到原生類型和過渡類型之間的對應。

### <a name="supported-data-types"></a>支援的資料類型

資料工廠支援以下臨時資料類型：在[資料集結構](concepts-datasets-linked-services.md#dataset-structure-or-schema)配置中配置類型資訊時，可以指定以下值：

* Byte[]
* Boolean
* Datetime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

## <a name="next-steps"></a>後續步驟
請參閱其他複製活動文章：

- [複製活動概述](copy-activity-overview.md)
