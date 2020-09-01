---
title: 在雪花式複製和轉換資料
description: 瞭解如何使用 Data Factory 在雪花中複製和轉換資料。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: fa8bb310d6a088db92b3dfd8eb6d2f584e9ffab7
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181879"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>使用 Azure Data Factory 在雪花中複製和轉換資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從和複製到雪花，以及使用資料流程來轉換雪花中的資料。 如需 Data Factory 的詳細資訊，請參閱 [簡介文章](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此雪花式連接器：

- 使用[支援的來源/接收矩陣](copy-activity-overview.md)資料表[複製活動](copy-activity-overview.md)
- [對應資料流程](concepts-data-flow-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

針對複製活動，此雪花式連接器支援下列功能：

- 從利用雪花式 [複製到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 命令的雪花式複製資料，以達到最佳效能。
- 將資料複製到雪花式，以利用雪花式的 [複製到 [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 命令來達到最佳效能。 它支援 Azure 上的雪花式。 

當您使用 Azure Synapse Analytics 工作區時，不支援雪花式作為接收器。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性會定義雪花式連接器專屬 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是支援雪花連結服務的屬性。

| 屬性         | 說明                                                  | 必要 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | Type 屬性必須設定為 **雪花**式。              | 是      |
| connectionString | 指定連接到雪花式實例所需的資訊。 您可以選擇在 Azure Key Vault 中放置密碼或整個連接字串。 如需詳細資訊，請參閱下表中的範例，以及 [Azure Key Vault 文章中的商店認證](store-credentials-in-key-vault.md) 。<br><br>一些典型設定：<br>- **帳戶名稱：** 雪花式帳戶的  [完整帳戶名稱](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (包括識別區域和雲端平臺) 的其他區段，例如 xy12345 東部-美國-2. azure。<br/>- **使用者名稱：** 連接的使用者登入名稱。<br>- **密碼：** 使用者的密碼。<br>- **資料庫：** 連接後要使用的預設資料庫。 它應該是指定的角色具有許可權的現有資料庫。<br>- **倉儲：** 連接後要使用的虛擬倉儲。 它應該是指定的角色具有許可權的現有倉儲。<br>- **角色：** 雪花式會話中要使用的預設存取控制角色。 指定的角色應該是已指派給指定使用者的現有角色。 預設角色為 PUBLIC。 | 是      |
| connectVia       | 用來連接到資料存放區的 [整合運行](concepts-integration-runtime.md) 時間。 如果您的資料存放區位於私人網路) ，您可以使用 Azure integration runtime 或自我裝載整合執行時間 (。 如果未指定，則會使用預設的 Azure integration runtime。 | 否       |

**範例︰**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault 中的密碼：**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 

雪花式資料集支援下列屬性。

| 屬性  | 說明                                                  | 必要                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 資料集的 type 屬性必須設為 **SnowflakeTable**。 | 是                         |
| 結構描述 | 結構描述的名稱。 請注意，在 ADF 中，架構名稱會區分大小寫。 |否，來源，是接收的  |
| 資料表 | 資料表/檢視的名稱。 請注意，在 ADF 中，資料表名稱會區分大小寫。 |否，來源，是接收的  |

**範例︰**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供雪花來源和接收所支援的屬性清單。

### <a name="snowflake-as-the-source"></a>雪花式作為來源

雪花式連接器會利用雪花式的 [複製到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 命令來達到最佳效能。

如果雪花式複製命令原生支援接收資料存放區和格式，您可以使用複製活動，直接從雪花複製到接收。 如需詳細資訊，請參閱 [雪花的直接複製](#direct-copy-from-snowflake)。 否則，請從雪花使用內建的 [分段複製](#staged-copy-from-snowflake)。

若要從雪花式複製資料，[複製活動 **來源** ] 區段支援下列屬性。

| 屬性                     | 說明                                                  | 必要 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 複製活動來源的 type 屬性必須設為 **SnowflakeSource**。 | 是      |
| 查詢          | 指定從雪花讀取資料的 SQL 查詢。 如果架構、資料表和資料行的名稱包含小寫，請在查詢中以引號括住物件識別碼，例如 `select * from "schema"."myTable"` 。<br>不支援執行預存程式。 | 否       |
| exportSettings | 用來從雪花式取出資料的 Advanced 設定。 您可以設定「複製到」命令所支援的專案，當您叫用語句時，Data Factory 將會通過此命令。 | 否       |
| ***在 `exportSettings` 下列情況下：*** |  |  |
| 類型 | 匯出命令的類型，設定為 **SnowflakeExportCopyCommand**。 | 是 |
| additionalCopyOptions | 額外的複製選項，以索引鍵/值組的字典形式提供。 範例： MAX_FILE_SIZE、覆寫。 如需詳細資訊，請參閱 [雪花式複製選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供用來複製命令做為機碼值組字典的其他檔案格式選項。 範例： DATE_FORMAT、TIME_FORMAT TIMESTAMP_FORMAT。 如需詳細資訊，請參閱 [雪花式格式類型選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-from-snowflake"></a>從雪花式直接複製

如果您的接收資料存放區和格式符合本節所述的準則，您可以使用複製活動，直接從雪花複製到接收。 Data Factory 會檢查設定，並在不符合下列準則時失敗複製活動執行：

- **接收連結服務**是具有**共用存取**簽章驗證的[**Azure Blob 儲存體**](connector-azure-blob-storage.md)。

- **接收資料格式**是**Parquet**、分隔的**文字**，或是具有下列設定的**JSON** ：

    - 針對 **Parquet** 格式，壓縮編解碼器為 **None**、 **Snappy**或 **Lzo**。
    - 若為 **分隔的文字** 格式：
        - `rowDelimiter` 為 **\r\n**或任何單一字元。
        - `compression` 不可以是 **壓縮**、 **gzip**、 **bzip2**或 **deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - `quoteChar` 是 **雙引號**、 **單引號**或 **空字串** (沒有引號字元) 。
    - 針對 **JSON** 格式，直接複製只支援來源雪花式資料表或查詢結果只有單一資料行，而且此資料行的資料類型為 **VARIANT**、 **OBJECT**或 **ARRAY**的情況。
        - `compression` 不可以是 **壓縮**、 **gzip**、 **bzip2**或 **deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - `filePattern` 在 [複製活動接收] 中，保留預設值或設定為 [ **setOfObjects**]。

- 在複製活動來源中， `additionalColumns` 未指定。
- 未指定資料行對應。

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>從雪花進行分段複製

當您的接收資料存放區或格式與雪花式複製命令原生相容時（如最後一節所述），請使用暫時的 Azure Blob 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 會將資料從雪花匯出至暫存儲存體，然後將資料複製到接收，最後清除暫存儲存體中的暫存資料。 如需使用暫存複製資料的詳細資訊，請參閱 [分段複製](copy-activity-performance-features.md#staged-copy) 。

若要使用這項功能，請建立 [Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties) ，將 azure 儲存體帳戶稱為過渡暫存。 然後指定 `enableStaging` `stagingSettings` 複製活動中的和屬性。

> [!NOTE]
> 暫存 Azure Blob 儲存體連結服務必須依照雪花式複製命令的要求，使用共用存取簽章驗證。 

**範例：**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>雪花式作為接收器

雪花式連接器會利用雪花式的 [複製到 [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 命令來達到最佳效能。 它支援將資料寫入 Azure 上的雪花式。

如果雪花式複製命令原生支援來源資料存放區和格式，您可以使用複製活動，直接從來源複製到雪花。 如需詳細資訊，請參閱 [直接複製到雪花](#direct-copy-to-snowflake)式。 否則，請使用內建的 [分段複製到雪花](#staged-copy-to-snowflake)式。

若要將資料複製到雪花，複製活動 **接收** 區段支援下列屬性。

| 屬性          | 說明                                                  | 必要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 複製活動接收的 type 屬性，設定為 **SnowflakeSink**。 | 是                                           |
| preCopyScript     | 針對複製活動指定一個 SQL 查詢，在每次執行時將資料寫入雪花之前執行。 使用此屬性來清除預先載入的資料。 | 否                                            |
| importSettings | 用來將資料寫入雪花的 Advanced 設定。 您可以設定「複製到」命令所支援的專案，當您叫用語句時，Data Factory 將會通過此命令。 | 否 |
| ***在 `importSettings` 下列情況下：*** |                                                              |  |
| 類型 | 匯入命令的類型，設定為 **SnowflakeImportCopyCommand**。 | 是 |
| additionalCopyOptions | 額外的複製選項，以索引鍵/值組的字典形式提供。 範例： ON_ERROR、FORCE、LOAD_UNCERTAIN_FILES。 如需詳細資訊，請參閱 [雪花式複製選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供給 COPY 命令的其他檔案格式選項，提供做為索引鍵/值組的字典。 範例： DATE_FORMAT、TIME_FORMAT TIMESTAMP_FORMAT。 如需詳細資訊，請參閱 [雪花式格式類型選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-to-snowflake"></a>直接複製到雪花

如果您的來源資料存放區和格式符合本節所述的準則，您可以使用複製活動，直接從來源複製到雪花。 Azure Data Factory 會檢查設定，並在不符合下列準則時失敗複製活動執行：

- **來源連結服務**是具有**共用存取**簽章驗證的[**Azure Blob 儲存體**](connector-azure-blob-storage.md)。

- **源資料格式**是**Parquet**、**分隔的文字**，或是具有下列設定的**JSON** ：

    - 若為 **Parquet** 格式，壓縮編解碼器為 **None**或 **Snappy**。

    - 若為 **分隔的文字** 格式：
        - `rowDelimiter` 為 **\r\n**或任何單一字元。 如果資料列分隔符號不是 "\r\n"，則必須為 `firstRowAsHeader` **false**，且 `skipLineCount` 不會指定。
        - `compression` 不可以是 **壓縮**、 **gzip**、 **bzip2**或 **deflate**。
        - `encodingName` 會保留為預設值，或設定為 "UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-7-32"、"32BE"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB18030"、"ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"、"ISO-8859-2"、"ISO-8859-5"、"ISO-8859-6"、"ISO-8859-7"、"ISO-8859-8"、"ISO-8859-9"、"WINDOWS-1250"、"WINDOWS-1251"、"windows-1252"、"windows-1253"、"windows-1254"、"windows-1255"。
        - `quoteChar` 是 **雙引號**、 **單引號**或 **空字串** (沒有引號字元) 。
    - 針對 **JSON** 格式，直接複製只支援接收雪花式資料表只有單一資料行，而此資料行的資料類型為 **VARIANT**、 **OBJECT**或 **ARRAY**的情況。
        - `compression` 不可以是 **壓縮**、 **gzip**、 **bzip2**或 **deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - 未指定資料行對應。

- 在複製活動來源中： 

   -  `additionalColumns` 未指定。
   - 如果您的來源是資料夾， `recursive` 則會設定為 true。
   - 未指定 `prefix`、`modifiedDateTimeStart`、`modifiedDateTimeEnd` 和 `enablePartitionDiscovery`。

**範例：**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>分階段複製到雪花

當您的接收資料存放區或格式與雪花式複製命令原生相容時（如最後一節所述），請使用暫時的 Azure Blob 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 會自動轉換資料以符合雪花的資料格式需求。 然後，它會叫用複製命令以將資料載入雪花。 最後，它會清除 Blob 儲存體中的暫存資料。 如需使用暫存複製資料的詳細資訊，請參閱 [分段複製](copy-activity-performance-features.md#staged-copy) 。

若要使用這項功能，請建立 [Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties) ，將 azure 儲存體帳戶稱為過渡暫存。 然後指定 `enableStaging` `stagingSettings` 複製活動中的和屬性。

> [!NOTE]
> 暫存 Azure Blob 儲存體連結服務需要使用雪花式複製命令所需的共用存取簽章驗證。

**範例：**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>對應資料流程屬性

在對應資料流程中轉換資料時，您可以讀取和寫入雪花中的資料表。 如需詳細資訊，請參閱對應資料流程中的[來源轉換](data-flow-source.md)和[接收轉換](data-flow-sink.md)。 您可以選擇使用雪花式資料集或 [內嵌資料集](data-flow-source.md#inline-datasets) 做為來源和接收類型。

### <a name="source-transformation"></a>來源轉換

下表列出雪花來源所支援的屬性。 您可以在 [ **來源選項** ] 索引標籤中編輯這些屬性。連接器會利用雪花式 [內部資料傳輸](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 資料表 | 如果您選取 [資料表] 做為輸入，則在使用內嵌資料集時，資料流程將會從雪花式資料集或來源選項中指定的資料表提取所有資料。 | 否 | String | * 僅適用于內嵌資料集的 () *<br>tableName<br>schemaName |
| 查詢 | 如果您選取 [查詢為輸入]，請輸入查詢以從雪花提取資料。 這項設定會覆寫您在資料集中選擇的任何資料表。<br>如果架構、資料表和資料行的名稱包含小寫，請在查詢中以引號括住物件識別碼，例如 `select * from "schema"."myTable"` 。 | 否 | String | 查詢 |

#### <a name="snowflake-source-script-examples"></a>雪花式來源腳本範例

當您使用雪花資料集做為來源類型時，相關聯的資料流程腳本為：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

如果您使用內嵌資料集，相關聯的資料流程腳本為：

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>接收轉換

下表列出雪花式接收所支援的屬性。 您可以在 [ **設定** ] 索引標籤中編輯這些屬性。使用內嵌資料集時，您將會看到其他設定，這與 [ [資料集屬性](#dataset-properties) ] 區段中所述的屬性相同。 連接器會利用雪花式 [內部資料傳輸](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)。

| Name | 說明 | 必要 | 允許的值 | 資料流程腳本屬性 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 方法 | 指定雪花式目的地上允許的作業。<br>若要更新、upsert 或刪除資料列，則需要 [Alter row 轉換](data-flow-alter-row.md) 來標記這些動作的資料列。 | 是 | `true` 或 `false` | 刪除 <br/>插入 <br/>更新 <br/>upsertable |
| 索引鍵資料行 | 對於更新、更新插入和刪除，必須設定索引鍵資料行，以決定要改變哪一個資料列。 | 否 | Array | 金鑰 |
| 資料表動作 | 決定在寫入之前，是否要重新建立或移除目的地資料表中的所有資料列。<br>- **None**：不會對資料表執行任何動作。<br>- **重新**建立：資料表將被捨棄並重新建立。 如果要動態建立新的資料表，則為必要。<br>- **截斷**：目標資料表中的所有資料列都會被移除。 | 否 | `true` 或 `false` | 重建<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>雪花式接收器腳本範例

當您使用雪花式資料集做為接收類型時，相關聯的資料流程腳本為：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

如果您使用內嵌資料集，相關聯的資料流程腳本為：

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱 [查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中複製活動所支援作為來源和接收器的資料存放區清單，請參閱 [支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
