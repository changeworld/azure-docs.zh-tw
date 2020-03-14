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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260809"
---
# <a name="schema-mapping-in-copy-activity"></a>複製活動中的結構描述對應

本文說明在執行資料複製時，Azure Data Factory 複製活動如何從來源資料與接收資料進行架構對應和資料類型對應。

## <a name="schema-mapping"></a>結構描述對應

將資料從來源複製到接收時，會套用資料行對應。 根據預設，複製活動會將**來源資料對應至依資料行名稱的接收**。 您可以指定[明確對應](#explicit-mapping)，依據您的需求自訂資料行對應。 更具體來說，複製活動會：

1. 從來源讀取資料，並判斷來源結構描述
2. 使用預設資料行對應依名稱對應資料行，或在指定的情況下套用明確的資料行對應。
3. 撰寫要接收的資料

### <a name="explicit-mapping"></a>明確對應

您可以在 [複製活動-> `translator` -> `mappings`] 屬性中指定要對應的資料行。 下列範例會在管線中定義複製活動，以將資料從分隔的文字複製到 Azure SQL Database。

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

使用 `source` 和 `sink`的 `translator` -> `mappings` > 物件下支援下列屬性：

| 屬性 | 描述                                                  | 必要 |
| -------- | ------------------------------------------------------------ | -------- |
| NAME     | 來源或接收資料行的名稱。                           | 是      |
| 序數  | 資料行索引。 從1開始。 <br>使用不含標頭行的分隔文字時，請套用和必要。 | 否       |
| path     | 要解壓縮或對應之每個欄位的 JSON 路徑運算式。 適用于階層式資料，例如 MongoDB/REST。<br>對於根物件下的欄位，JSON 路徑的開頭為 root $;針對 `collectionReference` 屬性所選擇之陣列內的欄位，JSON 路徑會從陣列元素開始。 | 否       |
| type     | 來源或接收資料行的 Data Factory 過渡資料類型。 | 否       |
| culture  | 來源或接收資料行的文化特性。 <br>當類型為 `Datetime` 或 `Datetimeoffset`時套用。 預設值為 `en-us`。 | 否       |
| format   | 當類型為 `Datetime` 或 `Datetimeoffset`時，所要使用的格式字串。 有關如何格式化日期時間的資訊，請參閱[自訂日期和時間格式字串](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)。 | 否       |

除了具有 `source` 和 `sink`的物件之外，`translator` -> `mappings` 下也支援下列屬性：

| 屬性            | 描述                                                  | 必要 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 只有在階層式資料（例如 MongoDB/REST）為來源時才支援。<br>如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 | 否       |

### <a name="alternative-column-mapping"></a>替代資料行對應

您可以指定複製活動-> `translator` -> `columnMappings`，以便在表格式資料之間進行對應。 在此情況下，輸入和輸出資料集都必須要有 "structure" 區段。 資料行對應支援將來源資料集「結構」中的所有或一部分資料行，對應至接收資料集「結構」中的所有資料行。 以下是會導致發生例外狀況的錯誤狀況：

* 來源資料存放區查詢結果在輸入資料集的「結構」區段中並未指定資料行名稱。
* 接收資料存放區 (如果含有預先定義的結構描述) 在輸出資料集的「結構」區段中並未指定資料行名稱。
* 接收資料集「結構」中的資料行數量多於或少於對應中所指定的數量。
* 重複的對應。

在下列範例中，輸入資料集具有結構，且指向內部部署 Oracle 資料庫中的資料表。

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

在此範例中，輸出資料集具有結構，並指向 Salesfoce 中的資料表。

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

下列 JSON 定義了管線中的複製活動。 來源中的資料行，會使用**translator** -> **columnMappings**屬性，對應至接收器中的資料行。

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

### <a name="alternative-schema-mapping"></a>替代架構對應

您可以指定複製活動-> `translator` -> `schemaMapping` 在階層式資料和表格式資料之間進行對應，例如從 MongoDB/REST 複製到文字檔，然後從 Oracle 複製到 Azure Cosmos DB 適用于 MongoDB 的 API。 複製活動的 `translator` 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動 translator 的 type 屬性必須設定為： **TabularTranslator** | 是 |
| schemaMapping | 索引鍵/值組的集合，表示**從來源端到接收端**的對應關聯性。<br/>- 機**碼：** 代表來源。 針對 [**表格式來源**]，指定資料集結構中所定義的資料行名稱;針對 [**階層式來源**]，為每個要解壓縮和對應的欄位指定 JSON 路徑運算式。<br>- **值：** 表示接收。 若為**表格式接收**，請指定資料集結構中所定義的資料行名稱;針對**階層式接收**，為每個要解壓縮和對應的欄位指定 JSON 路徑運算式。 <br>在階層式資料的情況下，針對根物件下的欄位，JSON 路徑會以根 $ 開頭;針對 `collectionReference` 屬性所選擇之陣列內的欄位，JSON 路徑會從陣列元素開始。  | 是 |
| collectionReference | 如果您想要逐一查看**陣列欄位內**相同模式的物件並擷取資料，然後轉換為每個物件一個資料列，則請指定該陣列的 JSON 路徑，以執行交叉套用。 只有在階層式資料是來源時，才支援這個屬性。 | 否 |

**範例：從 MongoDB 複製到 Oracle：**

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

您想要壓平合併陣列內的資料 (order_pd 和 order_price)，將內容複製到下列格式的 Azure SQL 資料表，並與一般根資訊 (編號、日期和城市) 交叉聯結︰

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

Data Factory 支援下列過渡資料類型：在[資料集結構](concepts-datasets-linked-services.md#dataset-structure-or-schema)設定中設定類型資訊時，您可以指定下列值：

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

- [複製活動概觀](copy-activity-overview.md)
