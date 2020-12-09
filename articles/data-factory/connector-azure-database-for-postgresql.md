---
title: 在適用於 PostgreSQL 的 Azure 資料庫中複製和轉換資料
description: 瞭解如何使用 Azure Data Factory 在適用於 PostgreSQL 的 Azure 資料庫中複製和轉換資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 2537167783f3e68c52c665dafa9378193852acb4
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930376"
---
# <a name="copy-and-transform-data-in-azure-database-for-postgresql-by-using-azure-data-factory"></a>使用 Azure Data Factory 在適用於 PostgreSQL 的 Azure 資料庫中複製和轉換資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，將資料從複製和複製到適用於 PostgreSQL 的 Azure 資料庫，以及使用資料流程來轉換適用於 PostgreSQL 的 Azure 資料庫中的資料。 若要了解 Azure Data Factory，請閱讀[簡介文章](introduction.md)。

此連接器專門針對 [適用於 PostgreSQL 的 Azure 資料庫服務](../postgresql/overview.md)。 若要從位於內部部署或雲端的一般于 postgresql 資料庫複製資料，請使用 [于 postgresql 連接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此適用於 PostgreSQL 的 Azure 資料庫連接器：

- 使用[支援的來源/接收矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義適用於 PostgreSQL 的 Azure 資料庫連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對適用於 PostgreSQL 的 Azure 資料庫已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為： **AzurePostgreSql**。 | 是 |
| connectionString | ODBC 連接字串，用於連線到適用於 PostgreSQL 的 Azure 資料庫。<br/>您也可以將密碼放在 Azure Key Vault 中，並 `password` 從連接字串中提取設定。 如需詳細資訊，請參閱下列範例，並 [將認證儲存在 Azure Key Vault 中](store-credentials-in-key-vault.md) 。 | 是 |
| connectVia | 此屬性代表要用來連接到資料存放區的 [整合運行](concepts-integration-runtime.md) 時間。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是您可以根據您的案例設定的更多屬性：

| 屬性 | 描述 | 選項。 | 必要 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如，`EncryptionMethod=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 判斷當啟用 SSL 加密 (加密方法 = 1) 時，驅動程式是否會驗證資料庫伺服器所傳送的憑證。 例如，`ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | 否 |

**範例**：

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**範例**：

**_將密碼儲存在 Azure Key Vault_* _

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集之區段和屬性的完整清單，請參閱 [Azure Data Factory 中的資料集](concepts-datasets-linked-services.md)。 本節提供資料集中適用於 PostgreSQL 的 Azure 資料庫支援的屬性清單。

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，請將資料集的類型屬性設定為 _ * AzurePostgreSqlTable * *。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 **AzurePostgreSqlTable** | 是 |
| tableName | 資料表的名稱 | 否 (如果已指定活動來源中的「查詢」) |

**範例**：

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱 [Azure Data Factory 中的管線和活動](concepts-pipelines-activities.md)。 本節提供適用於 PostgreSQL 的 Azure 資料庫來源所支援的屬性清單。

### <a name="azure-database-for-postgresql-as-source"></a>以適用於 PostgreSQL 的 Azure 資料庫作為來源

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將複製活動中的來源類型設定為 **AzurePostgreSqlSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為 **>azurepostgresqlsource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如：`SELECT * FROM mytable` 或 `SELECT * FROM "MyTable"`。 請注意，在於 postgresql 中，如果未加上引號，則會將機構名稱視為不區分大小寫。 | 如果已指定資料集中的 tableName 屬性，則沒有 ()  |

**範例**：

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM mytable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>適用於 PostgreSQL 的 Azure 資料庫為接收

若要將資料複製到適用於 PostgreSQL 的 Azure 資料庫，「複製活動 **接收** 」區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設為 **AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 在每次執行時將資料寫入適用於 PostgreSQL 的 Azure 資料庫之前，請指定複製活動所要執行的 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入適用於 PostgreSQL 的 Azure 資料庫資料表中。<br>允許的值是表示資料列數目的整數。 | 否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br>允許的值為 Timespan 字串。 範例是 00:30:00 (30 分鐘)。 | 沒有 (預設值為 00:00:30)  |

**範例**：

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以從適用於 PostgreSQL 的 Azure 資料庫讀取和寫入資料表。 如需詳細資訊，請參閱對應資料流程中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。 您可以選擇使用適用於 PostgreSQL 的 Azure 資料庫資料集或 [內嵌資料集](data-flow-source.md#inline-datasets) 做為來源和接收類型。

### <a name="source-transformation"></a>來源轉換

下表列出適用於 PostgreSQL 的 Azure 資料庫來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 資料表 | 如果您選取資料表做為輸入，資料流程就會從資料集所指定的資料表中提取所有資料。 | 否 | - |*僅適用于內嵌資料集的 ()*<br>tableName |
| 查詢 | 如果您選取 [查詢為輸入]，請指定要從來源提取資料的 SQL 查詢，這會覆寫您在資料集中指定的任何資料表。 使用查詢是減少測試或查閱資料列的絕佳方法。<br><br>不支援 **Order By** 子句，但您可以設定完整的 SELECT FROM 語句。 您也可使用使用者定義的資料表函數。 **select * From udfGetData ( # B1** 是 SQL 中的 UDF，會傳回您可以在資料流程中使用的資料表。<br>查詢範例： `select * from mytable where customerId > 1000 and customerId < 2000` 或 `select * from "MyTable"` 。 請注意，在於 postgresql 中，如果未加上引號，則會將機構名稱視為不區分大小寫。| 否 | String | 查詢 |
| 批次大小 | 指定批次大小，以將大型資料區塊為批次。 | 否 | 整數 | batchSize |
| 隔離等級 | 選擇下列其中一個隔離等級：<br>-讀取認可<br>-讀取未認可的 (預設) <br>-可重複讀取<br>-Serializable<br>-無 (忽略隔離等級)  | 否 | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>沒有</small> |isolationLevel |

#### <a name="azure-database-for-postgresql-source-script-example"></a>適用於 PostgreSQL 的 Azure 資料庫來源腳本範例

當您使用適用於 PostgreSQL 的 Azure 資料庫作為來源類型時，相關聯的資料流程腳本為：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from mytable',
    format: 'query') ~> AzurePostgreSQLSource
```

### <a name="sink-transformation"></a>接收轉換

下表列出適用於 PostgreSQL 的 Azure 資料庫接收所支援的屬性。 您可以在 [ **接收選項** ] 索引標籤中編輯這些屬性。

| 名稱 | 描述 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定您的資料庫目的地允許哪些作業。 預設僅允許插入。<br>若要更新、upsert 或刪除資料列，則需要 [Alter row 轉換](data-flow-alter-row.md) 來標記這些動作的資料列。 | 是 | `true` 或 `false` | 刪除 <br/>插入 <br/>更新 <br/>upsertable |
| 索引鍵資料行 | 針對更新、upsert 和刪除，必須設定索引鍵資料行 (s) ，以決定要改變的資料列。<br>您選擇做為索引鍵的資料行名稱將會用來做為後續更新、upsert、刪除的一部分。 因此，您必須挑選存在於接收對應中的資料行。 | 否 | Array | 金鑰 |
| 略過寫入索引鍵資料行 | 如果您不想將值寫入至索引鍵資料行，請選取 [略過寫入索引鍵資料行]。 | 否 | `true` 或 `false` | skipKeyWrites |
| 資料表動作 |決定在寫入之前，是否要重新建立或移除目的地資料表中的所有資料列。<br>- **None**：不會對資料表執行任何動作。<br>- **重新** 建立：資料表將被捨棄並重新建立。 如果要動態建立新的資料表，則為必要。<br>- **截斷**：目標資料表中的所有資料列都會被移除。 | 否 | `true` 或 `false` | 重建<br/>truncate |
| 批次大小 | 指定每個批次中寫入的資料列數目。 較大的批次大小會改善壓縮和記憶體優化，但會導致在快取資料時發生記憶體例外狀況的風險。 | 否 | 整數 | batchSize |
| 前置和後置 SQL 腳本 | 指定將在 (前置) 處理之前執行的多行 SQL 腳本，以及 (後置處理) 資料寫入至您的接收資料庫。 | 否 | String | preSQLs<br>postSQLs |

#### <a name="azure-database-for-postgresql-sink-script-example"></a>適用於 PostgreSQL 的 Azure 資料庫接收器腳本範例

當您使用適用於 PostgreSQL 的 Azure 資料庫作為接收類型時，相關聯的資料流程腳本為：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> AzurePostgreSQLSink
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱 [Azure Data Factory 中的查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
