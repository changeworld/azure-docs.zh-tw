---
title: 將資料從和複製到 Salesforce 服務雲端
description: 瞭解如何使用 data factory 管線中的複製活動，將資料從 Salesforce 服務雲端複製到支援的接收資料存放區，或從支援的來源資料存放區複製到 Salesforce 服務雲端。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/13/2020
ms.openlocfilehash: d83dcc5c86f2dfed5f588738e7799dd708333da1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076790"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>使用 Azure Data Factory 將資料從和複製到 Salesforce 服務雲端
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的複製活動，將資料從和複製到 Salesforce 服務雲端。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Salesforce 服務雲端連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Salesforce 服務雲端複製到任何支援的接收資料存放區。 您也可以將資料從任何支援的來源資料存放區複製到 Salesforce 服務雲端。 如需複製活動所支援作為來源或接收器的資料存放區清單，請參閱支援的[資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Salesforce 服務雲端連接器支援：

- Salesforce Developer、Professional、Enterprise 或 Unlimited 版本。
- 從 Salesforce 生產環境、沙箱、自訂網域複製資料，以及將資料複製到這些位置。

Salesforce 連接器建置於 Salesforce REST/Bulk API 之上。 根據預設，連接器會使用[v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm)從 salesforce 複製資料，並使用[v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm)將資料複製到 salesforce。 您也可以透過連結服務中的[ `apiVersion` 屬性](#linked-service-properties)，明確設定用來讀取/寫入資料的 API 版本。

## <a name="prerequisites"></a>先決條件

必須在 Salesforce 中啟用 API 權限。 如需詳細資訊，請參閱[在 Salesforce 中透過權限集啟用 API 存取權](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)。

## <a name="salesforce-request-limits"></a>Salesforce 要求限制

Salesforce 對於 API 要求總數和並行 API 要求均有限制。 請注意下列幾點：

- 如果並行要求數目超過限制，系統就會進行節流，您將會看到隨機失敗。
- 如果要求總數超過限制，將會封鎖 Salesforce 帳戶 24 小時。

在上述兩種情況中，您也可能會收到 "REQUEST_LIMIT_EXCEEDED" 錯誤。 如需詳細資訊，請參閱 [Salesforce 開發人員限制](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm)中的＜API 要求限制＞一節。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Salesforce 服務雲端連接器的特定 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Salesforce 連結服務支援的屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type |Type 屬性必須設定為**SalesforceServiceCloud**。 |是 |
| environmentUrl | 指定 Salesforce 服務雲端實例的 URL。 <br> - 預設為 `"https://login.salesforce.com"`. <br> - 若要從沙箱複製資料，請指定 `"https://test.salesforce.com"`。 <br> - 若要從自訂網域複製資料，舉例來說，請指定 `"https://[domain].my.salesforce.com"`。 |否 |
| 使用者名稱 |指定使用者帳戶的使用者名稱。 |是 |
| 密碼 |指定使用者帳戶的密碼。<br/><br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| securityToken |指定使用者帳戶的安全性權杖。 <br/><br/>若要整體了解安全性權杖，請參閱[安全性和 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)。 只有當您將 Integration Runtime 的 IP 新增至 Salesforce 上的[信任 ip 地址清單](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm)時，才能略過安全性權杖。 使用 Azure IR 時，請參閱[Azure Integration Runtime 的 IP 位址](azure-integration-runtime-ip-addresses.md)。<br/><br/>如需如何取得和重設安全性權杖的指示，請參閱[取得安全性權杖](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |否 |
| apiVersion | 指定要使用的 Salesforce REST/Bulk API 版本，例如 `48.0` 。 根據預設，連接器會使用[v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm)從 salesforce 複製資料，並使用[v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm)將資料複製到 salesforce。 | 否 |
| connectVia | 用來連線到資料存放區的[整合執行階段](concepts-integration-runtime.md)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 如果來源連結服務沒有整合執行階段，則對於來源而言為「否」；對於接收而言為「是」 |

>[!IMPORTANT]
>當您將資料複製到 Salesforce 服務雲端時，預設 Azure Integration Runtime 無法用來執行複製。 換句話說，如果您的來源連結服務沒有指定的整合執行時間，請使用您的 Salesforce 服務雲端實例附近的位置明確[建立 Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) 。 將 Salesforce 服務雲端連結服務建立關聯，如下列範例所示。

**範例：在資料處理站中儲存認證**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**範例：在 Key Vault 中儲存認證**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Salesforce 服務雲端資料集所支援的屬性清單。

若要將資料從和複製到 Salesforce 服務雲端，支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為**SalesforceServiceCloudObject**。  | 是 |
| objectApiName | 要從其中擷取資料的 Salesforce 物件名稱。 | 否 (來源)；是 (接收) |

> [!IMPORTANT]
> 所有自訂物件的 [API 名稱]**** 都要有 "__c" 部分。

![Data Factory Salesforce 連線的 API 名稱](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**範例︰**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的 type 屬性必須設定為 **RelationalTable**。 | 是 |
| tableName | Salesforce 服務雲端中的資料表名稱。 | 否 (如果已指定活動來源中的「查詢」) |

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Salesforce 服務雲端來源和接收所支援的屬性清單。

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce 服務雲端作為來源類型

若要從 Salesforce 服務雲端複製資料，複製活動的 [**來源**] 區段支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的類型屬性必須設定為**SalesforceServiceCloudSource**。 | 是 |
| 查詢 |使用自訂查詢來讀取資料。 您可以使用 [Salesforce 物件查詢語言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 查詢或 SQL-92 查詢。 請參閱[查詢秘訣](#query-tips)一節中的秘訣。 如果未指定 query，將會抓取資料集的 "objectApiName" 中指定的 Salesforce 服務雲端物件的所有資料。 | 否 (如果在資料集中指定 "objectApiName") |
| readBehavior | 指出是要查詢現有記錄，還是要查詢包含已刪除記錄在內的所有記錄。 如果未指定，預設行為是前者。 <br>允許的值：**query** (預設值)、**queryAll**。  | 否 |

> [!IMPORTANT]
> 所有自訂物件的 [API 名稱]**** 都要有 "__c" 部分。

![Data Factory Salesforce 連線的 API 名稱清單](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce 服務雲端作為接收類型

若要將資料複製到 Salesforce 服務雲端，複製活動**接收**區段中支援下列屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的 type 屬性必須設定為**SalesforceServiceCloudSink**。 | 是 |
| writeBehavior | 作業的寫入行為。<br/>允許的值為 **Insert** 和 **Upsert**。 | 否 (預設為 Insert) |
| externalIdFieldName | upsert 作業的外部識別碼欄位名稱。 指定的欄位必須在 Salesforce 服務雲端物件中定義為「外部識別碼欄位」。 對應的輸入資料中不能有 NULL 值。 | 是 (用於 upsert) |
| writeBatchSize | 每個批次中寫入 Salesforce 服務雲端的資料列計數。 | 否 (預設值為 5,000) |
| ignoreNullValues | 指出在寫入作業期間是否要忽略輸入資料中的 NULL 值。<br/>允許的值為 **true** 和 **false**。<br>- **true**：執行 upsert 或更新作業時，讓目的地物件中的資料保持不變。 執行插入作業時，插入已定義的預設值。<br/>- **false**：執行 upsert 或更新作業時，將目的地物件中的資料更新為 NULL。 執行插入作業時，插入 NULL 值。 | 否 (預設值為 false) |

**範例︰**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>查詢秘訣

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>從 Salesforce 服務雲端報表取出資料

您可以藉由將查詢指定為，從 Salesforce 服務雲端報表抓取資料 `{call "<report name>"}` 。 例如 `"query": "{call \"TestReport\"}"`。

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>從 Salesforce 服務雲端回收站取出已刪除的記錄

若要從 Salesforce 服務雲端回收站查詢虛刪除的記錄，您可以將指定 `readBehavior` 為 `queryAll` 。 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL 和 SQL 查詢語法的差異

從 Salesforce 服務雲端複製資料時，您可以使用 [SOQL 查詢] 或 [SQL 查詢]。 請注意，這兩個查詢具有不同的語法和功能支援，不可混用。 建議使用 SOQL 查詢，這是 Salesforce 服務雲端的原生支援。 下表列出主要差異：

| Syntax | SOQL 模式 | SQL 模式 |
|:--- |:--- |:--- |
| 資料行選擇 | 需要列舉要在查詢中複製的欄位，例如`SELECT field1, filed2 FROM objectname` | 支援 `SELECT *` (資料行選取除外)。 |
| 引號 | 欄位/物件名稱不能加上引號。 | 欄位/物件名稱可以加上引號，例如 `SELECT "id" FROM "Account"` |
| 日期時間格式 |  請參閱[這裡](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)的詳細資料和下一節中的範例。 | 請參閱[這裡](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017)的詳細資料和下一節中的範例。 |
| 布林值 | 以 `False` 和 `True` 表示，例如 `SELECT … WHERE IsDeleted=True`。 | 以 0 或 1 表示，例如 `SELECT … WHERE IsDeleted=1`。 |
| 資料行重新命名 | 不支援。 | 支援，例如 `SELECT a AS b FROM …`。 |
| 關聯性 | 支援，例如 `Account_vod__r.nvs_Country__c`。 | 不支援。 |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>在 DateTime 資料行上使用 where 子句來擷取資料

指定 SOQL 或 SQL 查詢時，請注意 DateTime 格式差異。 例如:

* **SOQL 範例**：`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 範例**：`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>MALFORMED_QUERY 錯誤：已截斷

如果您遇到「MALFORMED_QUERY：已截斷」的錯誤，通常是因為您在資料中有 JunctionIdList 類型資料行，而 Salesforce 有大量資料列支援這類資料的限制。 若要減輕此問題，請嘗試排除 JunctionIdList 資料行，或限制要複製的資料列數目（您可以分割成多個複製活動執行）。

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Salesforce 服務雲端的資料類型對應

當您從 Salesforce 服務雲端複製資料時，會使用下列從 Salesforce 服務雲端資料類型對應到 Data Factory 過渡資料類型的對應。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| Salesforce 服務雲端資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| 自動編號 |字串 |
| 核取方塊 |布林值 |
| 貨幣 |Decimal |
| 日期 |Datetime |
| 日期/時間 |Datetime |
| 電子郵件 |字串 |
| ID |字串 |
| 查閱關聯性 |字串 |
| 複選挑選清單 |字串 |
| Number |Decimal |
| 百分比 |Decimal |
| 電話 |字串 |
| 挑選清單 |字串 |
| Text |字串 |
| 文字區域 |字串 |
| 文字區域 (完整) |字串 |
| 文字區域 (豐富) |字串 |
| 文字 (加密) |字串 |
| URL |字串 |

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
