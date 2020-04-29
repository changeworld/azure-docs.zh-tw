---
title: 從 Azure Cosmos DB 的適用于 MongoDB 的 API 複製資料
description: 了解如何使用 Data Factory 將資料從支援的來源資料存放區複製到 Azure Cosmos DB 的 MongoDB 版 API，或從 Azure Cosmos DB 的 MongoDB 版 API 複製到支援的接收存放區。
services: data-factory, cosmosdb
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/20/2019
ms.openlocfilehash: 9b23f46a418f2663531cc121f00b83d00d84e48d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415452"
---
# <a name="copy-data-to-or-from-azure-cosmos-dbs-api-for-mongodb-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到 Azure Cosmos DB 的 MongoDB 版 API，或從中複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動將資料複製到 Azure Cosmos DB 的 MongoDB 版 API，或從中複製資料。 本文是以 [Azure Data Factory 中的複製活動](copy-activity-overview.md)為基礎，該文提供複製活動的一般概觀。

>[!NOTE]
>此連接器僅支援將資料複製到 Azure Cosmos DB 的 MongoDB 版 API，或從中複製資料。 針對 SQL API，請參閱 [Cosmos DB SQL API 連接器](connector-azure-cosmos-db.md)。 目前不支援其他 API 類型。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 Azure Cosmos DB 的 MongoDB 版 API 複製到任何支援的接收資料存放區，或將資料從任何支援的來源資料存放區複製到 Azure Cosmos DB 的 MongoDB 版 API。 如需複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。

您可以使用 Azure Cosmos DB 的 MongoDB 版 API 連接器執行下列作業：

- 從 [Azure Cosmos DB 的 MongoDB 版 API](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction) 複製資料，或將資料複製到其中。
- 寫入 Azure Cosmos DB 作為 **insert** 或 **upsert**。
- 依原樣匯入和匯出 JSON 文件，或從表格式資料集複製資料或將資料複製到其中。 範例包括 SQL 資料庫和 CSV 檔案。 若要依原樣將文件複製到 JSON 檔案或另一個 Azure Cosmos DB 集合或複製其中的文件，請參閱「匯入或匯出 JSON 文件」。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下幾節將針對可用來為 Azure Cosmos DB 的 MongoDB 版 API 定義專屬 Data Factory 實體的屬性，提供其相關的詳細資料。

## <a name="linked-service-properties"></a>連結服務屬性

以下是 Azure Cosmos DB 的 MongoDB 版 API 連結服務所支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | **type** 屬性必須設定為 **CosmosDbMongoDbApi**。 | 是 |
| connectionString |為 Azure Cosmos DB 的 MongoDB 版 API 指定連接字串。 其提供在 Azure 入口網站 -> [Cosmos DB] 刀鋒視窗 -> 主要或次要連接字串中，模式為 `mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb`。 <br/><br />您也可以將密碼放在 Azure Key Vault 中，並 `password` 從連接字串中提取設定。如需詳細資訊，請參閱  [在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)。|是 |
| [資料庫] | 您要存取的資料庫名稱。 | 是 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或自我裝載整合執行階段 (如果您的資料存放區位於私人網路中)。 如果未指定此屬性，則會使用預設的 Azure Integration Runtime。 |否 |

**範例**

```json
{
    "name": "CosmosDbMongoDBAPILinkedService",
    "properties": {
        "type": "CosmosDbMongoDbApi",
        "typeProperties": {
            "connectionString": "mongodb://<cosmosdb-name>:<password>@<cosmosdb-name>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb",
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 以下是 Azure Cosmos DB 的 MongoDB 版 API 資料集所支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 **type** 屬性必須設定為 **CosmosDbMongoDbApiCollection**。 |是 |
| collectionName |Azure Cosmos DB 集合的名稱。 |是 |

**範例**

```json
{
    "name": "CosmosDbMongoDBAPIDataset",
    "properties": {
        "type": "CosmosDbMongoDbApiCollection",
        "typeProperties": {
            "collectionName": "<collection name>"
        },
        "schema": [],
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB's API for MongoDB linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

本節提供 Azure Cosmos DB 的 MongoDB 版 API 來源和接收支援的屬性清單。

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)。

### <a name="azure-cosmos-dbs-api-for-mongodb-as-source"></a>以 Azure Cosmos DB 的 Mongo 版 API 作為來源

複製活動的 [**來源**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 **type** 屬性必須設定為 **CosmosDbMongoDbApiSource**。 |是 |
| filter | 使用查詢運算子指定選取範圍篩選。 若要傳回集合中的所有文件，請省略此參數，或傳遞空白文件 ({})。 | 否 |
| cursorMethods.project | 指定要在文件中傳回以便投影的欄位。 若要傳回比對文件中的所有欄位，請省略此參數。 | 否 |
| cursorMethods.sort | 指定查詢傳回比對文件的順序。 請參閱 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定伺服器傳回的文件數目上限。 請參閱 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 | 
| cursorMethods.skip | 指定要跳過的文件數，以及 MongoDB 開始傳回結果的位置。 請參閱 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定要在回應的每個批次中從 MongoDB 執行個體傳回的文件數目。 在大部分情況下，修改批次大小不會影響使用者或應用程式。 Cosmos DB 限制每個批次的大小不能超過 40 MB，也就是文件大小的 batchSize 總數，因此如果您的文件大小很大，請降低此值。 | 否<br/>（預設值為**100**） |

>[!TIP]
>ADF 支援在 **Strict 模式**中取用 BSON 文件。 請確定您的篩選查詢處於 Strict 模式，而非 Shell 模式。 如需詳細說明，請參閱 [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html) (MongoDB 手冊)。

**範例**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Cosmos DB's API for MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "CosmosDbMongoDbApiSource",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-dbs-api-for-mongodb-as-sink"></a>以 Azure Cosmos DB 的 Mongo 版 API 作為接收

複製活動的 [**接收**] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 **type** 屬性必須設定為 **CosmosDbMongoDbApiSink**。 |是 |
| writeBehavior |描述如何將資料寫入至 Azure Cosmos DB。 允許的值：**insert** 和 **upsert**。<br/><br/>如果已有**upsert**相同`_id`的檔存在，則 upsert 的行為是取代檔;否則，請插入檔。<br /><br />**注意**：如果`_id`未在原始`_id`檔中或透過資料行對應指定，Data Factory 會自動為檔產生。 這表示您必須確定，為了讓 **upsert** 如預期般運作，您的文件具有識別碼。 |否<br />(預設值為 **insert**) |
| writeBatchSize | **writeBatchSize** 屬性可控制在每個批次中寫入的文件大小。 您可以嘗試增加 **writeBatchSize** 的值來改善效能，如果您的文件大小很大，則可嘗試降低此值。 |否<br />（預設值為**10000**） |
| writeBatchTimeout | 批次插入作業在超時之前完成的等候時間。允許的值為 timespan。 | 否<br/>(預設為 **00:30:00** - 30 分鐘) |

>[!TIP]
>若要以現況匯入 JSON 文件，請參閱[匯入或匯出 JSON 文件](#import-and-export-json-documents)一節；若要從表格式資料複製，請參閱[結構描述對應](#schema-mapping)。

**範例**

```json
"activities":[
    {
        "name": "CopyToCosmosDBMongoDBAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "CosmosDbMongoDbApiSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-and-export-json-documents"></a>匯入和匯出 JSON 檔

您可以使用 Azure Cosmos DB 連接器輕鬆地：

* 在兩個 Azure Cosmos DB 集合之間依原樣複製文件。
* 將 JSON 檔從各種來源匯入到 Azure Cosmos DB，包括 MongoDB、Azure Blob 儲存體、Azure Data Lake 存放區，以及 Azure Data Factory 支援的其他檔案架構存放區。
* 將 JSON 文件從 Azure Cosmos DB 集合匯出至各種檔案型存放區。

若要達成無從驗證結構描述的複製：

* 當您使用複製資料工具時，請選取 [依原樣匯出到 JSON 檔案或 Cosmos DB 集合]**** 選項。
* 當您使用活動撰寫時，請選擇 JSON 格式與來源或接收的對應檔案存放區。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 Azure Cosmos DB 的 MongoDB 版 API 複製到表格式接收，或執行反向複製，請參閱[結構描述對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

特別是針對寫入 Cosmos DB，請確認您從來源資料使用正確的物件識別碼填入 Cosmos DB，例如您在 SQL 資料庫資料表中有個「識別碼」資料行，並想要使用其值作為 MongoDB 中的文件識別碼，以插入/upsert，則必須根據 MongoDB strict 模式定義 (`_id.$oid`) 設定適當的結構描述對應，如下所示：

![MongoDB 接收中的對應識別碼](./media/connector-azure-cosmos-db-mongodb-api/map-id-in-mongodb-sink.png)

執行複製活動後，系統會在接收中產生以下 BSON ObjectId：

```json
{
    "_id": ObjectId("592e07800000000000000000")
}
``` 

## <a name="next-steps"></a>後續步驟

如需 Azure Data Factory 中複製活動作為來源和接收端支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
