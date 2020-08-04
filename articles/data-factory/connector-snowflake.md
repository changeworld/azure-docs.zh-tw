---
title: 將資料從和複製到雪花
description: 瞭解如何使用 Azure Data Factory 將資料從和複製到雪花。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/30/2020
ms.openlocfilehash: 48248b07b64278d5c8d4f297bf83df813aa486fe
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529495"
---
# <a name="copy-data-from-and-to-snowflake-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從和複製到雪花

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，將資料從和複製到雪花。 如需 Data Factory 的詳細資訊，請參閱[簡介一文](introduction.md)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此雪花式連接器：

- [複製活動](copy-activity-overview.md)與[支援的來源/接收矩陣](copy-activity-overview.md)資料表
- [查閱活動](control-flow-lookup-activity.md)

針對複製活動，此雪花式連接器支援下列功能：

- 從使用雪花的[複製到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html)命令的雪花複製資料，以達到最佳效能。
- 將資料複製到雪花，利用雪花的[複製到 [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html)命令以達到最佳效能。 它支援 Azure 上的雪花。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性會定義雪花式連接器的特定 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

下列屬性受到雪花連結服務的支援。

| 屬性         | 說明                                                  | 必要 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | Type 屬性必須設定為**雪花**。              | 是      |
| connectionString | 指定連接到雪花實例所需的資訊。 您可以選擇將密碼或整個連接字串放在 Azure Key Vault 中。 如需詳細資訊，請參閱表格下方的範例，以及[Azure Key Vault 文章中的 Store 認證](store-credentials-in-key-vault.md)。<br><br>一些一般設定：<br>- **帳戶名稱：** 雪花式帳戶的[完整帳戶名稱](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name)（包括可識別區域和雲端平臺的其他區段），例如 xy12345. 美國東部-2. azure。<br/>- **使用者名稱：** 連接之使用者的登入名稱。<br>- **密碼：** 使用者的密碼。<br>- **資料庫：** 連接之後要使用的預設資料庫。 它應該是指定的角色具有許可權的現有資料庫。<br>- **倉儲：** 連線後要使用的虛擬倉儲。 它應該是指定的角色具有許可權的現有倉儲。<br>- **角色：** 要在雪花式會話中使用的預設存取控制角色。 指定的角色應該是已指派給指定使用者的現有角色。 預設角色為 [公用]。 | 是      |
| connectVia       | 用來連接到資料存放區的[整合運行](concepts-integration-runtime.md)時間。 您可以使用 Azure integration runtime 或自我裝載整合執行時間（如果您的資料存放區位於私人網路中）。 如果未指定，則會使用預設的 Azure integration runtime。 | 否       |

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

雪花資料集支援下列屬性。

| 屬性  | 說明                                                  | 必要                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 資料集的類型屬性必須設定為**SnowflakeTable**。 | 是                         |
| 結構描述 | 結構描述的名稱。 |針對來源為否，針對接收則為 [是]  |
| 資料表 | 資料表/檢視的名稱。 |針對來源為否，針對接收則為 [是]  |

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

### <a name="snowflake-as-the-source"></a>雪花作為來源

雪花式連接器會利用雪花的[複製到 [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html)命令來達到最佳效能。

如果雪花式複製命令原本就支援接收資料存放區和格式，您可以使用複製活動，直接從雪花複製到接收。 如需詳細資訊，請參閱[雪花的直接複製](#direct-copy-from-snowflake)。 否則，請使用雪花的內建[分段複製](#staged-copy-from-snowflake)。

若要從雪花複製資料，複製活動的 [**來源**] 區段支援下列屬性。

| 屬性                     | 說明                                                  | 必要 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 複製活動來源的類型屬性必須設定為**SnowflakeSource**。 | 是      |
| 查詢          | 指定要從雪花讀取資料的 SQL 查詢。<br>不支援執行預存程式。 | 否       |
| exportSettings | 用來從雪花式抓取資料的先進設定。 您可以設定 [複製到] 命令所支援的範本，Data Factory 將會在您叫用語句時傳遞。 | 否       |
| ***在 `exportSettings` 下列底下：*** |  |  |
| 類型 | 匯出命令的類型，設定為**SnowflakeExportCopyCommand**。 | 是 |
| additionalCopyOptions | 提供做為索引鍵/值組字典的其他複製選項。 範例： MAX_FILE_SIZE、覆寫。 如需詳細資訊，請參閱[雪花複製選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供的其他檔案格式選項，可將命令複製為索引鍵/值組的字典。 範例： DATE_FORMAT、TIME_FORMAT TIMESTAMP_FORMAT。 如需詳細資訊，請參閱[雪花格式類型選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-from-snowflake"></a>從雪花直接複製

如果您的接收資料存放區和格式符合本節所述的準則，您可以使用複製活動，直接從雪花複製到接收。 Data Factory 檢查設定，如果不符合下列準則，則複製活動執行會失敗：

- **接收連結服務**是使用**共用存取**簽章驗證的[**Azure Blob 儲存體**](connector-azure-blob-storage.md)。

- **接收資料格式**為**Parquet**、**分隔文字**或具有下列設定的**JSON** ：

    - 針對**Parquet**格式，壓縮編解碼器為**None**、 **Snappy**或**Lzo**。
    - 針對**分隔的文字**格式：
        - `rowDelimiter`是**\r\n**，或任何單一字元。
        - `compression`不可以是**壓縮**、 **gzip**、 **bzip2**或**deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - `quoteChar`為**雙引號**、**單引號**或**空字串**（無引號字元）。
    - 若是**JSON**格式，直接複製僅支援來源雪花表或查詢結果只有單一資料行，而此資料行的資料類型是**VARIANT**、 **OBJECT**或**ARRAY**的情況。
        - `compression`不可以是**壓縮**、 **gzip**、 **bzip2**或**deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - `filePattern`在中，複製活動接收會保留為預設值，或設定為**setOfObjects**。

- 未指定複製活動來源中的 `additionalColumns` 。
- 未指定資料行對應。

**範例︰**

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
                "sqlReaderQuery": "SELECT * FROM MyTable",
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

#### <a name="staged-copy-from-snowflake"></a>雪花的分段複製

當您的接收資料存放區或格式與雪花複製命令原本不相容（如上一節所述）時，請使用暫時的 Azure Blob 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 會將資料從雪花匯出至暫存儲存體，然後將資料複製到接收，最後從臨時存放裝置清除暫時資料。 如需使用預備環境複製資料的詳細資訊，請參閱[分段複製](copy-activity-performance-features.md#staged-copy)。

若要使用這項功能，請建立[Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties)，其參考 azure 儲存體帳戶作為過渡暫存。 然後 `enableStaging` `stagingSettings` 在複製活動中指定和屬性。

> [!NOTE]
> 預備 Azure Blob 儲存體連結服務必須依照雪花複製命令的要求，使用共用存取簽章驗證。 

**範例︰**

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

### <a name="snowflake-as-sink"></a>雪花作為接收器

雪花式連接器會利用雪花的[複製到 [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html)命令來達到最佳效能。 它支援將資料寫入 Azure 上的雪花。

如果雪花式複製命令原本就支援來源資料存放區和格式，您可以使用複製活動直接從來源複製到雪花。 如需詳細資訊，請參閱[直接複製到雪花](#direct-copy-to-snowflake)。 否則，請使用內建的[分段複製來執行雪花](#staged-copy-to-snowflake)。

若要將資料複製到雪花，複製活動的 [**接收**] 區段支援下列屬性。

| 屬性          | 說明                                                  | 必要                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 複製活動接收的 type 屬性，設定為**SnowflakeSink**。 | 是                                           |
| preCopyScript     | 指定在每次執行中將資料寫入雪花之前，要執行複製活動的 SQL 查詢。 使用此屬性來清除預先載入的資料。 | 否                                            |
| importSettings | 用來將資料寫入雪花的先進設定。 您可以設定 [複製到] 命令所支援的範本，Data Factory 將會在您叫用語句時傳遞。 | 否 |
| ***在 `importSettings` 下列底下：*** |                                                              |  |
| 類型 | 匯入命令的類型，設定為**SnowflakeImportCopyCommand**。 | 是 |
| additionalCopyOptions | 提供做為索引鍵/值組字典的其他複製選項。 範例： ON_ERROR、FORCE、LOAD_UNCERTAIN_FILES。 如需詳細資訊，請參閱[雪花複製選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)。 | 否 |
| additionalFormatOptions | 提供給 COPY 命令的其他檔案格式選項，提供做為索引鍵/值組的字典。 範例： DATE_FORMAT、TIME_FORMAT TIMESTAMP_FORMAT。 如需詳細資訊，請參閱[雪花格式類型選項](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)。 | 否 |

#### <a name="direct-copy-to-snowflake"></a>直接複製到雪花

如果您的來源資料存放區和格式符合本節所述的準則，您可以使用複製活動直接從來源複製到雪花。 Azure Data Factory 檢查設定，如果不符合下列準則，則複製活動執行會失敗：

- **來源連結服務**是具有**共用存取**簽章驗證的[**Azure Blob 儲存體**](connector-azure-blob-storage.md)。

- **源資料格式**為**Parquet**、**分隔文字**或具有下列設定的**JSON** ：

    - 若為**Parquet**格式，壓縮編解碼器為**None**或**Snappy**。

    - 針對**分隔的文字**格式：
        - `rowDelimiter`是**\r\n**，或任何單一字元。 如果資料列分隔符號不是 "\r\n"，則必須為 `firstRowAsHeader` **false**，而且 `skipLineCount` 不會指定。
        - `compression`不可以是**壓縮**、 **gzip**、 **bzip2**或**deflate**。
        - `encodingName`會保留為預設值或設定為 "UTF-8"、"UTF-16"、"UTF-16BE"、"UTF-32"、"32BE"、"BIG5"、"EUC-JP"、"EUC-KR"、"GB18030"、".ISO-2022-JP"、"ISO-2022-KR"、"ISO-8859-1"，"ISO-8859-2"，"ISO-8859-5"，"ISO-8859-6"，"ISO-8859-7"，"ISO-8859-8"，"ISO-8859-9"，"WINDOWS-1250"，"windows-1251"，"windows-1252"，"windows-1253"。
        - `quoteChar`為**雙引號**、**單引號**或**空字串**（無引號字元）。
    - 若是**JSON**格式，直接複製只支援接收雪花表只有單一資料行，而此資料行的資料類型是**VARIANT**、 **OBJECT**或**ARRAY**。
        - `compression`不可以是**壓縮**、 **gzip**、 **bzip2**或**deflate**。
        - `encodingName` 會保留為預設值，或設定為 **utf-8**。
        - 未指定資料行對應。

- 在複製活動來源中： 

   -  `additionalColumns` 未指定。
   - 如果您的來源是資料夾， `recursive` 則會設定為 true。
   - `prefix``modifiedDateTimeStart` `modifiedDateTimeEnd` 未指定、、。

**範例︰**

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

#### <a name="staged-copy-to-snowflake"></a>分段複製到雪花

當您的接收資料存放區或格式與雪花複製命令原本不相容（如上一節所述）時，請使用暫時的 Azure Blob 儲存體實例來啟用內建的分段複製。 分段複製功能也能提供更好的輸送量。 Data Factory 會自動轉換資料，以符合雪花的資料格式需求。 接著，它會叫用 COPY 命令，將資料載入雪花。 最後，它會清除 Blob 儲存體中的暫存資料。 如需使用暫存複製資料的詳細資訊，請參閱[分段複製](copy-activity-performance-features.md#staged-copy)。

若要使用這項功能，請建立[Azure Blob 儲存體連結服務](connector-azure-blob-storage.md#linked-service-properties)，其參考 azure 儲存體帳戶作為過渡暫存。 然後 `enableStaging` `stagingSettings` 在複製活動中指定和屬性。

> [!NOTE]
> 預備 Azure Blob 儲存體連結服務必須依照雪花複製命令的要求，使用共用存取簽章驗證。

**範例︰**

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


## <a name="lookup-activity-properties"></a>查閱活動屬性

如需屬性的詳細資訊，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟

如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區和格式](copy-activity-overview.md#supported-data-stores-and-formats)。
