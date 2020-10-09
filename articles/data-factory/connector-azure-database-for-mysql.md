---
title: 將資料複製到適用於 MySQL 的 Azure 資料庫或從中複製資料
description: 瞭解如何使用 Azure Data Factory 管線中的複製活動，將資料複製到適用於 MySQL 的 Azure 資料庫或從中複製資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/25/2019
ms.openlocfilehash: bbb4aed8ca10fcf7c15e7442ee7067b2e3f8087d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81410695"
---
# <a name="copy-data-to-and-from-azure-database-for-mysql-using-azure-data-factory"></a>使用 Azure Data Factory 將資料複製到適用於 MySQL 的 Azure 資料庫或從中複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從適用於 MySQL 的 Azure 資料庫複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

此連接器專門用於 [適用於 MySQL 的 Azure 資料庫服務](../mysql/overview.md)。 若要從位於內部部署或雲端的一般 MySQL 資料庫複製資料，請使用 [MySQL 連接器](connector-mysql.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此適用於 MySQL 的 Azure 資料庫連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從適用於 MySQL 的 Azure 資料庫複製到任何支援的接收資料存放區。 或者，您可以將資料從任何支援的來源資料存放區複製到適用於 MySQL 的 Azure 資料庫。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義適用於 MySQL 的 Azure 資料庫連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對適用於 MySQL 的 Azure 資料庫已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設為：**AzureMySql** | 是 |
| connectionString | 指定連線到適用於 MySQL 的 Azure 資料庫執行個體所需的資訊。 <br/> 您也可以將密碼放在 Azure Key Vault 中，並從連接字串中提取 `password` 組態。 請參閱下列範例和[在 Azure Key Vault 中儲存認證](store-credentials-in-key-vault.md)一文中的更多詳細資料。 | 是 |
| connectVia | 用來連線到資料存放區的 [Integration Runtime](concepts-integration-runtime.md)。 您可以使用 Azure Integration Runtime 或「自我裝載 Integration Runtime」(如果您的資料存放區位於私人網路中)。 如果未指定，就會使用預設的 Azure Integration Runtime。 |否 |

一般的連接字串為 `Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`。 您可以根據您的案例設定更多屬性：

| 屬性 | 描述 | 選項。 | 必要 |
|:--- |:--- |:--- |:--- |
| SSLMode | 此選項指定驅動程式在連線到 MySQL 時，是否使用 TLS 加密和驗證。 例如 `SSLMode=<0/1/2/3/4>`| DISABLED (0) / PREFERRED (1) **(預設)** / REQUIRED (2) / VERIFY_CA (3) / VERIFY_IDENTITY (4) | 否 |
| UseSystemTrustStore | 此選項指定是否使用來自系統信任存放區或來自指定 PEM 檔案的 CA 憑證。 例如 `UseSystemTrustStore=<0/1>;`| 啟用 (1) / 停用 (0) **(預設)** | 否 |

**範例︰**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Azure Key Vault 中儲存密碼**

```json
{
    "name": "AzureDatabaseForMySQLLinkedService",
    "properties": {
        "type": "AzureMySql",
        "typeProperties": {
            "connectionString": "Server=<server>.mysql.database.azure.com;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供適用於 MySQL 的 Azure 資料庫資料集所支援的屬性清單。

若要從適用於 MySQL 的 Azure 資料庫複製資料，將資料集的 type 屬性設定為 **AzureMySqlTable**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為：**AzureMySqlTable** | 是 |
| tableName | MySQL 資料庫中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "AzureMySQLDataset",
    "properties": {
        "type": "AzureMySqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure MySQL linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供適用於 MySQL 的 Azure 資料庫來源和接收所支援的屬性清單。

### <a name="azure-database-for-mysql-as-source"></a>適用於 MySQL 的 Azure 資料庫作為來源

若要從適用於 MySQL 的 Azure 資料庫複製資料，[複製活動 **來源** ] 區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為：**AzureMyTableSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM MyTable"` 。 | 否 (如果已指定資料集中的 "tableName") |
| queryCommandTimeout | 查詢要求超時之前的等候時間。預設值為120分鐘 (02:00:00)  | 否 |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure MySQL input dataset name>",
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
                "type": "AzureMySqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-mysql-as-sink"></a>適用於 MySQL 的 Azure 資料庫為接收

若要將資料複製到適用於 MySQL 的 Azure 資料庫，「複製活動 **接收** 」區段支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收的 type 屬性必須設定為： **AzureMySqlSink** | 是 |
| preCopyScript | 針對複製活動指定一個 SQL 查詢，在每次執行將資料寫入適用於 MySQL 的 Azure 資料庫之前執行。 您可以使用此屬性來清除預先載入的資料。 | 否 |
| writeBatchSize | 當緩衝區大小達到 writeBatchSize 時，將資料插入適用於 MySQL 的 Azure 資料庫資料表中。<br>允許的值為代表資料列數的整數。 | 否 (預設值為 10000) |
| writeBatchTimeout | 在逾時前等待批次插入作業完成的時間。<br>允許的值為時間範圍。 範例是 00:30:00 (30 分鐘)。 | 沒有 (預設值為 00:00:30)  |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure MySQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureMySqlSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="data-type-mapping-for-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫的資料類型對應

從適用於 MySQL 的 Azure 資料庫複製資料時，會使用下列對應從 MySQL 資料類型對應到 Azure Data Factory 過渡期資料類型。 請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)，以了解複製活動如何將來源結構描述和資料類型對應至接收器。

| 適用於 MySQL 的 Azure 資料庫的資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int32` |

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
