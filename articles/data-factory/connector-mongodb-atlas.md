---
title: 從 MongoDB 塔複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料從 MongoDB 的資料存放區複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 09/28/2020
ms.openlocfilehash: 34b0c053f4f0fea933a6e1f48d8f93e6352776b9
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946638"
---
# <a name="copy-data-from-mongodb-atlas-using-azure-data-factory"></a>使用 Azure Data Factory 從 MongoDB 塔複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 MongoDB 塔資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

您可以將資料從 MongoDB 的塔資料庫複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體來說，這個 MongoDB 塔連接器 **最多可支援4.2 版**。

## <a name="prerequisites"></a>必要條件

如果您使用 Azure Integration Runtime 進行複製，請務必將有效區域的 [Azure Integration Runtime](azure-integration-runtime-ip-addresses.md) ip 新增至 MONGODB 塔 IP 存取清單。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 MongoDB 塔連接器專屬 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 MongoDB 的已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type |Type 屬性必須設定為： **MongoDbAtlas** |是 |
| connectionString |指定 MongoDB 的塔連接字串，例如 `mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>` 。 <br/><br /> 您也可以將連接字串放在 Azure Key Vault 中。 如需詳細資訊，請參閱 [Azure Key Vault 中的儲存認證](store-credentials-in-key-vault.md) 。 |是 |
| [資料庫] | 您要存取的資料庫名稱。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 深入了解[必要條件](#prerequisites)一節。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

**範例︰**

```json
{
    "name": "MongoDbAtlasLinkedService",
    "properties": {
        "type": "MongoDbAtlas",
        "typeProperties": {
            "connectionString": "mongodb+srv://<username>:<password>@<clustername>.<randomString>.<hostName>/<dbname>?<otherProperties>",
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

如需定義資料集的區段和屬性完整清單，請參閱[資料集和連結服務](concepts-datasets-linked-services.md)。 以下是針對 MongoDB 塔資料集支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **MongoDbAtlasCollection** | 是 |
| collectionName |MongoDB 塔資料庫中集合的名稱。 |是 |

**範例︰**

```json
{
    "name": "MongoDbAtlasDataset",
    "properties": {
        "type": "MongoDbAtlasCollection",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MongoDB Atlas linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 MongoDB 塔來源所支援的屬性清單。

### <a name="mongodb-atlas-as-source"></a>MongoDB 塔作為來源

複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為： **MongoDbAtlasSource** | 是 |
| 篩選條件 | 使用查詢運算子指定選取範圍篩選。 若要傳回集合中的所有文件，請省略此參數，或傳遞空白文件 ({})。 | 否 |
| cursorMethods.project | 指定要在文件中傳回以便投影的欄位。 若要傳回比對文件中的所有欄位，請省略此參數。 | 否 |
| cursorMethods.sort | 指定查詢傳回比對文件的順序。 請參閱 [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)。 | 否 |
| cursorMethods.limit | 指定伺服器傳回的文件數目上限。 請參閱 [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)。  | 否 |
| cursorMethods.skip | 指定要略過的檔數目，以及從 MongoDB 塔開始傳回結果的位置。 請參閱 [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)。 | 否 |
| batchSize | 指定要在 MongoDB 的每個回應批次中傳回的檔數目。 在大部分情況下，修改批次大小不會影響使用者或應用程式。 Cosmos DB 限制每個批次的大小不能超過 40 MB，也就是文件大小的 batchSize 總數，因此如果您的文件大小很大，請降低此值。 | 否<br/> (預設值為 **100**)  |

>[!TIP]
>ADF 支援在 **Strict 模式**中取用 BSON 文件。 請確定您的篩選查詢處於 Strict 模式，而非 Shell 模式。 如需詳細說明，請參閱 [MongoDB manual](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html) (MongoDB 手冊)。

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromMongoDbAtlas",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB Atlas input dataset name>",
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
                "type": "MongoDbAtlasSource",
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

## <a name="export-json-documents-as-is"></a>依原樣匯出 JSON 文件

您可以使用此 MongoDB 的塔連接器，將 JSON 檔從 MongoDB 的檔案存放區依原樣匯出至各種檔案型存放區或 Azure Cosmos DB。 若要完成這種跨平台結構描述的複製，請跳過資料集中的「結構」(也稱為「結構描述」**) 區段，以及複製活動中的結構描述對應。

## <a name="schema-mapping"></a>結構描述對應

若要將資料從 MongoDB 塔複製到表格式接收器，請參閱 [架構對應](copy-activity-schema-and-type-mapping.md#schema-mapping)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
