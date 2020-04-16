---
title: 將資料複製到與 Azure 資料庫複製 PostgreSQL
description: 瞭解如何透過使用 Azure 資料工廠管道中的複製活動將資料複製到 PostgreSQL 的 Azure 資料庫。
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
ms.openlocfilehash: b85e72ae6698cd9fa018c940e158bfcf25279ed5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410471"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>使用 Azure 資料工廠將資料複製到 Azure 資料庫以用於 PostgreSQL

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文介紹如何使用 Azure 數據工廠中的複製活動功能從 PostgreSQL 的 Azure 資料庫複製數據。 它基於[Azure 資料工廠文章中的"複製"活動](copy-activity-overview.md),其中概述了複製活動。

此連線器專用於用於[PostgreSQL 服務的 Azure 資料庫](../postgresql/overview.md)。 要從位於本地或雲端中的一般 PostgreSQL 資料庫複製資料,請使用[PostgreSQL 連接器](connector-postgresql.md)。

## <a name="supported-capabilities"></a>支援的功能

支援以下活動使用此 Azure 資料庫用於 PostgreSQL 連接器:

- 使用[支援的來源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [尋找活動](control-flow-lookup-activity.md)

您可以將資料從適用於 PostgreSQL 的 Azure 資料庫複製到任何支援的接收資料存放區。 或者,您可以將數據從任何受支援的源資料儲存複製到 Azure 資料庫,用於 PostgreSQL。 有關複製活動支援為源和接收體的清單,請參閱[支援資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)表。

Azure Data Factory 會提供內建的驅動程式來啟用連線。 因此,您無需手動安裝任何驅動程式來使用此連接器。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定於 Azure 資料庫的資料工廠實體的 PostgreSQL 連接器的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

對於 PostgreSQL 連結服務的 Azure 資料庫,支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設定為 **:AzurePostgreSql**。 | 是 |
| connectionString | ODBC 連接字串，用於連線到適用於 PostgreSQL 的 Azure 資料庫。<br/>您還可以在 Azure 密鑰保管庫中輸入`password`密碼, 並將配置從連接字串中拔出。 有關詳細資訊,請參閱以下範例並在[Azure 金鑰保管庫中儲存認證](store-credentials-in-key-vault.md)。 | 是 |
| connectVia | 這個屬性表示用於連線到資料儲存的[整合時](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`。 以下是您可以根據案例設定的更多屬性:

| 屬性 | 描述 | 選項。 | 必要 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod (EM)| 驅動程式用來加密在驅動程式和資料庫伺服器之間傳送之資料的方法。 例如，`EncryptionMethod=<0/1/6>;`| 0 (無加密) **(預設)** / 1 (SSL) / 6 (RequestSSL) | 否 |
| ValidateServerCertificate (VSC) | 確定驅動程式是否驗證啟用 SSL 加密時資料庫伺服器發送的證書(加密方法=1)。 例如，`ValidateServerCertificate=<0/1>;`| 0 (停用) **(預設)** / 1 (啟用) | 否 |

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

***在 Azure 金鑰保存的密碼***

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

有關可用於定義資料集的節和屬性的完整清單,請參閱 Azure[資料工廠 中的資料集](concepts-datasets-linked-services.md)。 本節提供數據集中 Azure 資料庫支援的屬性清單。

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將資料集的類型屬性設定為 **AzurePostgreSqlTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為**AzurePostgreSqlTable** | 是 |
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

有關可用於定義活動的節和屬性的完整清單,請參閱 Azure[資料工廠中的導管和活動](concepts-pipelines-activities.md)。 本節提供 Azure 資料庫支援的 PostgreSQL 源屬性的清單。

### <a name="azure-database-for-postgresql-as-source"></a>以適用於 PostgreSQL 的 Azure 資料庫作為來源

若要從適用於 PostgreSQL 的 Azure 資料庫複製資料，將複製活動中的來源類型設定為 **AzurePostgreSqlSource**。 複製活動**來源**部份支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 必須複製活動來源的類型屬性設定為**AzurePostgreSqlSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` | 否(如果指定了資料集中的表格Name 屬性) |

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

### <a name="azure-database-for-postgresql-as-sink"></a>以後格雷SQL 的 Azure 資料庫作為接收器

要將資料複製到 PostgreSQL 的 Azure 資料庫,複製活動**接收器**部分支援以下屬性:

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設定為**AzurePostgreSQLSink**。 | 是 |
| preCopyScript | 為每次運行中將資料寫入 Azure 資料庫以 PostgreSQL 之前要執行的複製活動指定 SQL 查詢。 您可以使用此屬性來清除預先載入的資料。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時,將數據插入到 PostgreSQL 的 Azure 資料庫表中。<br>允許值是表示行數的整數。 | 否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br>允許的值是時間跨度字串。 範例是 00:30:00 (30 分鐘)。 | 否(預設值為 00:00:30) |

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

## <a name="lookup-activity-properties"></a>尋找活動屬性

有關屬性的詳細資訊,請參閱 Azure[資料工廠中的尋找活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
有關 Azure 資料工廠複製活動支援為來源及接收器的資料儲存清單,請參考[資料儲存](copy-activity-overview.md#supported-data-stores-and-formats)。
