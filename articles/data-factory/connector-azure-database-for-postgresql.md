---
title: 將資料複製到和從 Azure 資料庫複製 PostgreSQL
description: 瞭解如何通過使用 Azure 資料工廠管道中的複製活動將資料複製到 PostgreSQL 的 Azure 資料庫。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444381"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>通過使用 Azure 資料工廠將資料複製到 Azure 資料庫以用於 PostgreSQL

本文介紹如何使用 Azure 資料工廠中的複製活動功能從 PostgreSQL 的 Azure 資料庫複製資料。 它基於[Azure 資料工廠文章中的"複製"活動](copy-activity-overview.md)，其中概述了複製活動。

此連接器專用於用於[PostgreSQL 服務的 Azure 資料庫](../postgresql/overview.md)。 要從位於本地或雲中的通用 PostgreSQL 資料庫中複製資料，請使用[PostgreSQL 連接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支援的功能

支援以下活動使用此 Azure 資料庫用於 PostgreSQL 連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從適用於 PostgreSQL 的 Azure 資料庫複製到任何支援的接收資料存放區。 或者，您可以將資料從任何受支援的來源資料存儲複製到 Azure 資料庫，用於 PostgreSQL。 有關複製活動支援為源和接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 因此，您無需手動安裝任何驅動程式來使用此連接器。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Azure 資料庫的資料工廠實體的 PostgreSQL 連接器的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

對於 PostgreSQL 連結服務的 Azure 資料庫，支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設置為 **：AzurePostgreSql**。 | 是 |
| connectionString | ODBC 連接字串，用於連線到適用於 PostgreSQL 的 Azure 資料庫。<br/>您還可以在 Azure 金鑰保存庫中輸入密碼，`password`並將配置從連接字串中拔出。 有關詳細資訊，請參閱以下示例並在[Azure 金鑰保存庫中存儲憑據](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 此屬性工作表示用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是您可以根據案例設置的更多屬性：

| 屬性 | 描述 | 選項。 | 必要 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如，`EncryptionMethod=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 確定驅動程式是否驗證啟用 SSL 加密時資料庫伺服器發送的證書（加密方法=1）。 例如，`ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | 否 |

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

***在 Azure 金鑰保存庫中存儲密碼***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱 Azure[資料工廠 中的資料集](concepts-datasets-linked-services.md)。 本節提供資料集中 Azure 資料庫支援的屬性清單。

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將資料集的類型屬性設定為 **AzurePostgreSqlTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為**AzurePostgreSqlTable** | 是 |
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

有關可用於定義活動的節和屬性的完整清單，請參閱 Azure[資料工廠中的管道和活動](concepts-pipelines-activities.md)。 本節提供 Azure 資料庫支援的 PostgreSQL 源屬性的清單。

### <a name="azure-database-for-postgresql-as-source"></a>以適用於 PostgreSQL 的 Azure 資料庫作為來源

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將複製活動中的來源類型設定為 **AzurePostgreSqlSource**。 複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須將複製活動源的類型屬性設置為**AzurePostgreSqlSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` | 否（如果指定了資料集中的表Name 屬性） |

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
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>用於後格雷SQL 的 Azure 資料庫作為接收器

要將資料複製到 PostgreSQL 的 Azure 資料庫，複製活動**接收器**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設置為**AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 為每次運行中將資料寫入 Azure 資料庫以 PostgreSQL 之前要執行的複製活動指定 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入到 PostgreSQL 的 Azure 資料庫表中。<br>允許值是表示行數的整數。 | 否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br>允許的值是時間跨度字串。 範例是 00:30:00 (30 分鐘)。 | 否（預設值為 00：00：30） |

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

## <a name="lookup-activity-properties"></a>查找活動屬性

有關屬性的詳細資訊，請參閱 Azure[資料工廠中的查找活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
有關 Azure 資料工廠中複製活動支援為源和接收器的資料存儲清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)。
