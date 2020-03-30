---
title: 從銷售人員服務雲複製資料
description: 瞭解如何使用資料工廠管道中的複製活動將資料從 Salesforce 服務雲複製到受支援的接收器資料存儲或受支援的來源資料存儲到 Salesforce 服務雲。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 4540b27a9241a14b3d1a153d11bf43900e8ae0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153842"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>使用 Azure 資料工廠從 Salesforce 服務雲複製資料

本文概述了如何使用 Azure 資料工廠中的複製活動從 Salesforce 服務雲複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

此 Salesforce 服務雲連接器支援用於以下活動：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 Salesforce 服務雲複製到任何受支援的接收器資料存儲。 您還可以將資料從任何受支援的來源資料存儲複製到 Salesforce 服務雲。 有關"複製"活動支援為源或接收器的資料存儲的清單，請參閱[支援資料存儲](copy-activity-overview.md#supported-data-stores-and-formats)表。

具體而言，此 Salesforce 服務雲連接器支援：

- Salesforce Developer、Professional、Enterprise 或 Unlimited 版本。
- 從 Salesforce 生產環境、沙箱、自訂網域複製資料，以及將資料複製到這些位置。

Salesforce 連接器構建在 Salesforce REST/Bulk API 之上。 預設情況下，連接器使用[v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm)從 Salesforce 複製資料，並使用[v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm)將資料複製到 Salesforce。 您還可以顯式設置用於通過[`apiVersion`](#linked-service-properties)連結服務中的屬性讀取/寫入資料的 API 版本。

## <a name="prerequisites"></a>Prerequisites

必須在 Salesforce 中啟用 API 權限。 如需詳細資訊，請參閱[在 Salesforce 中透過權限集啟用 API 存取權](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)。

## <a name="salesforce-request-limits"></a>Salesforce 要求限制

Salesforce 對於 API 要求總數和並行 API 要求均有限制。 請注意下列幾點：

- 如果並行要求數目超過限制，系統就會進行節流，您將會看到隨機失敗。
- 如果要求總數超過限制，將會封鎖 Salesforce 帳戶 24 小時。

在上述兩種情況中，您也可能會收到 "REQUEST_LIMIT_EXCEEDED" 錯誤。 如需詳細資訊，請參閱 [Salesforce 開發人員限制](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf)中的＜API 要求限制＞一節。

## <a name="get-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

以下各節提供有關用於定義特定于 Salesforce 服務雲連接器的資料工廠實體的屬性的詳細資訊。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Salesforce 連結服務支援的屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type |類型屬性必須設置為**SalesforceServiceCloud**。 |是 |
| environmentUrl | 指定 Salesforce 服務雲實例的 URL。 <br> - 預設為 `"https://login.salesforce.com"`. <br> - 若要從沙箱複製資料，請指定 `"https://test.salesforce.com"`。 <br> - 若要從自訂網域複製資料，舉例來說，請指定 `"https://[domain].my.salesforce.com"`。 |否 |
| username |指定使用者帳戶的使用者名稱。 |是 |
| 密碼 |指定使用者帳戶的密碼。<br/><br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |是 |
| securityToken |指定使用者帳戶的安全性權杖。 <br/><br/>若要整體了解安全性權杖，請參閱[安全性和 API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)。 僅當將集成運行時的 IP 添加到 Salesforce 上的[受信任 IP 位址清單](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm)時，才能跳過安全權杖。 使用 Azure IR 時，請參閱[Azure 集成運行時 IP 位址](azure-integration-runtime-ip-addresses.md)。<br/><br/>有關如何獲取和重置安全權杖的說明，請參閱[獲取安全權杖](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm)。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 |否 |
| apiVersion | 指定要使用的 Salesforce REST/Bulk API 版本，例如`48.0`。 預設情況下，連接器使用[v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm)從 Salesforce 複製資料，並使用[v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm)將資料複製到 Salesforce。 | 否 |
| connectVia | 用於連接到資料存儲的[集成運行時](concepts-integration-runtime.md)。 如果未指定，就會使用預設的 Azure Integration Runtime。 | 如果來源連結服務沒有整合執行階段，則對於來源而言為「否」；對於接收而言為「是」 |

>[!IMPORTANT]
>將資料複製到 Salesforce 服務雲時，預設的 Azure 集成運行時不能用於執行複製。 換句話說，如果源連結的服務沒有指定的集成運行時，請顯式創建具有 Salesforce 服務雲服務實例附近位置的[Azure 集成運行時](create-azure-integration-runtime.md#create-azure-ir)。 按以下示例關聯 Salesforce 服務雲連結服務。

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

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Salesforce 服務雲資料集支援的屬性清單。

要將資料從 Salesforce 服務雲複製，請支援以下屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 類型屬性必須設置為**SalesforceServiceCloudObject**。  | 是 |
| objectApiName | 要從其中擷取資料的 Salesforce 物件名稱。 | 否 (來源)；是 (接收) |

> [!IMPORTANT]
> 所有自訂物件的 [API 名稱]**** 都要有 "__c" 部分。

![Data Factory Salesforce 連線的 API 名稱](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**例子：**

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
| tableName | Salesforce 服務雲中的表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Salesforce 服務雲源和接收器支援的屬性清單。

### <a name="salesforce-service-cloud-as-a-source-type"></a>銷售隊伍服務雲作為源類型

要從 Salesforce 服務雲複製資料，複製活動**源**部分中支援以下屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動源的類型屬性必須設置為**SalesforceServiceCloudSource**。 | 是 |
| 查詢 |使用自訂查詢來讀取資料。 您可以使用 [Salesforce 物件查詢語言 (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) 查詢或 SQL-92 查詢。 請參閱[查詢秘訣](#query-tips)一節中的秘訣。 如果未指定查詢，將檢索資料集中"objectApiName"中指定的 Salesforce 服務雲服務物件的所有資料。 | 否 (如果在資料集中指定 "objectApiName") |
| readBehavior | 指出是要查詢現有記錄，還是要查詢包含已刪除記錄在內的所有記錄。 如果未指定，預設行為是前者。 <br>允許的值：**query** (預設值)、**queryAll**。  | 否 |

> [!IMPORTANT]
> 所有自訂物件的 [API 名稱]**** 都要有 "__c" 部分。

![Data Factory Salesforce 連線的 API 名稱清單](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**例子：**

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

### <a name="salesforce-service-cloud-as-a-sink-type"></a>銷售隊伍服務雲作為接收器類型

要將資料複製到 Salesforce 服務雲，複製活動**接收器**部分支援以下屬性。

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動接收器的類型屬性必須設置為**SalesforceServiceCloudSink**。 | 是 |
| writeBehavior | 作業的寫入行為。<br/>允許的值為 **Insert** 和 **Upsert**。 | 否 (預設為 Insert) |
| externalIdFieldName | upsert 作業的外部識別碼欄位名稱。 指定欄位必須在 Salesforce 服務雲物件中定義為"外部 ID 欄位"。 對應的輸入資料中不能有 NULL 值。 | 是 (用於 upsert) |
| writeBatchSize | 每個批次中寫入 Salesforce 服務雲的資料的行計數。 | 否 (預設值為 5,000) |
| ignoreNullValues | 指出在寫入作業期間是否要忽略輸入資料中的 NULL 值。<br/>允許的值為 **true** 和 **false**。<br>- **true**：執行 upsert 或更新作業時，讓目的地物件中的資料保持不變。 執行插入作業時，插入已定義的預設值。<br/>- **false**：執行 upsert 或更新作業時，將目的地物件中的資料更新為 NULL。 執行插入作業時，插入 NULL 值。 | 否 (預設值為 false) |

**例子：**

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

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>從 Salesforce 服務雲報表中檢索資料

通過將查詢指定為`{call "<report name>"}`，可以從 Salesforce 服務雲報表中檢索資料。 例如 `"query": "{call \"TestReport\"}"`。

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>從 Salesforce 服務雲回收站檢索已刪除的記錄

要從 Salesforce 服務雲回收站查詢已刪除的軟記錄，可以指定`readBehavior`為`queryAll`。 

### <a name="difference-between-soql-and-sql-query-syntax"></a>SOQL 和 SQL 查詢語法的差異

從 Salesforce 服務雲複製資料時，可以使用 SOQL 查詢或 SQL 查詢。 請注意，這兩個查詢具有不同的語法和功能支援，不可混用。 建議您使用 Salesforce 服務雲本機支援的 SOQL 查詢。 下表列出主要差異：

| 語法 | SOQL 模式 | SQL 模式 |
|:--- |:--- |:--- |
| 資料行選擇 | 需要枚舉要在查詢中複製的欄位，例如`SELECT field1, filed2 FROM objectname` | 支援 `SELECT *` (資料行選取除外)。 |
| 引號 | 欄位/物件名稱不能加上引號。 | 欄位/物件名稱可以加上引號，例如 `SELECT "id" FROM "Account"` |
| 日期時間格式 |  請參閱[這裡](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm)的詳細資料和下一節中的範例。 | 請參閱[這裡](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017)的詳細資料和下一節中的範例。 |
| 布林值 | 以 `False` 和 `True` 表示，例如 `SELECT … WHERE IsDeleted=True`。 | 以 0 或 1 表示，例如 `SELECT … WHERE IsDeleted=1`。 |
| 資料行重新命名 | 不支援。 | 支援，例如 `SELECT a AS b FROM …`。 |
| 關聯性 | 支援，例如 `Account_vod__r.nvs_Country__c`。 | 不支援。 |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>在 DateTime 資料行上使用 where 子句來擷取資料

指定 SOQL 或 SQL 查詢時，請注意 DateTime 格式差異。 例如：

* **SOQL 樣本**：`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL 示例**：`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>MALFORMED_QUERY錯誤：已截斷

如果遇到"MALFORMED_QUERY：截斷"的錯誤，通常是由於資料中具有 JunctionIdList 類型列，Salesforce 在支援大量行中支援此類資料有限制。 要緩解，請嘗試排除 JunctionIdList 列或限制要複製的行數（您可以將其分區為多個複製活動運行）。

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>銷售人員服務雲的資料類型映射

從 Salesforce 服務雲複製資料時，將使用以下映射從 Salesforce 服務雲資料類型到資料工廠臨時資料類型。 若要了解複製活動如何將來源結構描述和資料類型對應至接收，請參閱[結構描述和資料類型對應](copy-activity-schema-and-type-mapping.md)。

| 銷售隊伍服務雲資料類型 | Data Factory 過渡期資料類型 |
|:--- |:--- |
| 自動編號 |String |
| 核取方塊 |Boolean |
| 貨幣 |Decimal |
| Date |Datetime |
| 日期/時間 |Datetime |
| 電子郵件 |String |
| Id |String |
| 查閱關聯性 |String |
| 複選挑選清單 |String |
| Number |Decimal |
| 百分比 |Decimal |
| 電話 |String |
| 挑選清單 |String |
| Text |String |
| 文字區域 |String |
| 文字區域 (完整) |String |
| 文字區域 (豐富) |String |
| 文字 (加密) |String |
| URL |String |

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Data Factory 中的複製活動所支援作為來源和接收的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
