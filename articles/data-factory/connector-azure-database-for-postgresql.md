---
title: 將資料複製到適用於 PostgreSQL 的 Azure 資料庫或從中複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到適用於 PostgreSQL 的 Azure 資料庫或從中複製資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2020
ms.openlocfilehash: 11e0d3336f085ccae9a7fb83ed050d69a15ce42b
ms.sourcegitcommit: 192f9233ba42e3cdda2794f4307e6620adba3ff2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/26/2020
ms.locfileid: "96296500"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到適用於 PostgreSQL 的 Azure 資料庫或從中複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文說明如何使用 Azure Data Factory 中的「複製活動」功能，從適用於 PostgreSQL 的 Azure 資料庫複製資料。 它是 [以 Azure Data Factory 文章中的「複製活動](copy-activity-overview.md) 」為基礎，其中提供「複製活動」的一般總覽。

此連接器專門針對 [適用於 PostgreSQL 的 Azure 資料庫服務](../postgresql/overview.md)。 若要從位於內部部署或雲端的一般于 postgresql 資料庫複製資料，請使用 [于 postgresql 連接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此適用於 PostgreSQL 的 Azure 資料庫連接器：

- 使用[支援的來源/接收矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從適用於 PostgreSQL 的 Azure 資料庫複製到任何支援的接收資料存放區。 或者，您可以將資料從任何支援的來源資料存放區複製到適用於 PostgreSQL 的 Azure 資料庫。 如需複製活動作為來源和接收所支援的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats) 資料表。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 因此，您不需要手動安裝任何驅動程式，就能使用此連接器。

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
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如，`EncryptionMethod=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | No |
| ValidateServerCertificate (VSC) | 判斷當啟用 SSL 加密 (加密方法 = 1) 時，驅動程式是否會驗證資料庫伺服器所傳送的憑證。 例如，`ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | No |

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
| type | 複製活動接收的 type 屬性必須設為 **AzurePostgreSQLSink**。 | Yes |
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

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱 [Azure Data Factory 中的查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>下一步
如需 Azure Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
