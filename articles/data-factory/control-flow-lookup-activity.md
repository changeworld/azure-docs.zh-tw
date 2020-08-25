---
title: Azure Data Factory 中的查閱活動
description: 了解如何使用查閱活動查閱外部來源的值。 此輸出可供後續活動進一步參考。
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 7a0b4e52d729c3f13d5ac425627970d67b87979e
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795876"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Azure Data Factory 中的查閱活動

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

查閱活動可從任何 Azure Data Factory 所支援的資料來源擷取資料集。 請在下列狀況使用查閱活動：
- 動態判斷在後續活動中要處理哪些物件，而不是將物件名稱寫入程式碼。 某些物件範例是檔案和資料表。

查閱活動會讀取並傳回組態檔或資料表的內容。 也會傳回執行查詢或預存程序的結果。 查閱活動的輸出可用於後續的複製或轉換活動中 (如果輸出是單一值)。 輸出也可用於 ForEach 活動中 (如果輸出是屬性陣列)。

## <a name="supported-capabilities"></a>支援的功能

查閱活動支援下列資料來源。 查閱活動能傳回的資料列數目上限是 5000，大小最多為 2 MB。 目前，查閱活動在逾時之前的最長持續時間為一小時。

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>語法

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>類型屬性

名稱 | 描述 | 類型 | 必要項？
---- | ----------- | ---- | --------
資料集 | 提供查閱的資料集參考。 如需詳細資料，請參閱每個對應連接器文章中的＜**資料集屬性**＞一節。 | 索引鍵/值組 | 是
source | 包含資料集特定的來源屬性，與複製活動來源相同。 如需詳細資料，請參閱每個對應連接器文章中的＜**複製活動屬性**＞一節。 | 索引鍵/值組 | 是
firstRowOnly | 指出是否只傳回第一個資料列或傳回所有資料列。 | Boolean | 否。 預設值為 `true`。

> [!NOTE]
> 
> * 不支援 **ByteArray** 類型的來源資料行。
> * 資料集定義不支援**結構**。 針對文字格式檔案，可以使用標頭資料列來提供資料行名稱。
> * 如果您的查閱來源是 JSON 檔案，不支援用於調整 JSON 物件的 `jsonPathDefinition` 設定。 將會擷取整個物件。

## <a name="use-the-lookup-activity-result"></a>使用查閱活動結果

查閱結果會在活動執行結果的 `output` 區段中傳回。

* **當 `firstRowOnly` 設為 `true` (預設值)** ，輸出格式如下列程式碼所示。 查閱結果就在固定的 `firstRow` 索引鍵底下。 若要在後續活動中使用結果，請使用的模式  `@{activity('LookupActivity').output.firstRow.table` 。

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **當 `firstRowOnly` 設為 `false`** ，輸出格式如下列程式碼所示。 `count` 欄位會指出傳回多少筆記錄。 詳細的值會顯示在固定 `value` 陣列下。 在這種情況下，查閱活動後面會接著 [Foreach 活動](control-flow-for-each-activity.md)。 您可以使用 `@activity('MyLookupActivity').output.value` 模式，將 `value` 陣列傳遞至 ForEach 活動的 `items` 欄位。 若要存取 `value` 陣列中的元素，請使用下列語法：`@{activity('lookupActivity').output.value[zero based index].propertyname}`。 例如 `@{activity('lookupActivity').output.value[0].schema}`。

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>範例

在此範例中，管線包含兩個活動： **查閱** 和 **複製**。 複製活動會將資料從您 Azure SQL Database 實例中的 SQL 資料表複製到 Azure Blob 儲存體。 SQL 資料表的名稱會儲存在 Blob 儲存體的 JSON 檔案中。 查閱活動在執行階段會查閱表格名稱。 使用這種方法可動態修改 JSON。 您不需要重新部署管線或資料集。 

此範例僅示範查閱第一個資料列。 若要查閱所有資料列並鏈結結果與 ForEach 活動，請參閱[使用 Azure Data Factory 大量複製多個資料表](tutorial-bulk-copy.md)中的範例。


### <a name="pipeline"></a>管線

- 查閱活動會設定為使用 **LookupDataset**，它會參考 Azure Blob 儲存體中的位置。 查閱活動會從這個位置的 JSON 檔案讀取 SQL 資料表的名稱。 
- 複製活動會使用查閱活動的輸出，也就是 SQL 資料表的名稱。 **SourceDataset** 中的 **tableName** 屬性會設定為使用查閱活動的輸出。 複製活動會將資料從 SQL 資料表複製到 Azure Blob 儲存體 中的位置。 此位置會由 **SinkDataset** 屬性指定。 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>查閱資料集

**查閱**資料集是**AzureBlobStorageLinkedService**類型所指定之 Azure 儲存體 lookup 資料夾中檔案的**sourcetable.js** 。 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>複製活動的**來源**資料集

**來源**資料集會使用查閱活動的輸出，亦即 SQL 資料表的名稱。 複製活動會將資料從此 SQL 資料表複製到 Azure Blob 儲存體 中的位置。 此位置會由**接收**資料集指定。 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>複製活動的**接收**資料集

複製活動會將資料從 SQL 資料表複製到 Azure 儲存體中 **csv** 資料夾內的 **filebylookup.csv** 檔案。 檔案是由 **AzureBlobStorageLinkedService** 屬性所指定。 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

您可以使用下列兩種格式來 **sourcetable.json** file。

#### <a name="set-of-objects"></a>物件集合

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>物件的陣列

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>限制和因應措施

以下是查閱活動和因應措施建議的一些限制。

| 限制 | 因應措施 |
|---|---|
| 查閱活動最多可有 5,000 個資料列，大小上限為 2 MB。 | 設計兩個層級的管線，其中外部管線會逐一查看內部管線，並擷取不超過最大資料列或大小的資料。 |
| | |

## <a name="next-steps"></a>後續步驟
請參閱 Data Factory 支援的其他控制流程活動： 

- [執行管線活動](control-flow-execute-pipeline-activity.md)
- [ForEach 活動](control-flow-for-each-activity.md)
- [GetMetadata 活動](control-flow-get-metadata-activity.md)
- [Web 活動](control-flow-web-activity.md)
