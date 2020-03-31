---
title: 從服務現在複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，將資料從 ServiceNow 複製到支援的接收資料存放區。
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: dabcc5afe4a092e4919c854071a698c6e6ebf0b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926160"
---
# <a name="copy-data-from-servicenow-using-azure-data-factory"></a>使用 Azure Data Factory 從 ServiceNow 複製資料

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 ServiceNow 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

以下活動支援此 ServiceNow 連接器：

- 使用[支援的源/接收器矩陣](copy-activity-overview.md)[複製活動](copy-activity-overview.md)
- [查找活動](control-flow-lookup-activity.md)

您可以將資料從 ServiceNow 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 ServiceNow 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 ServiceNow 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為：**ServiceNow** | 是 |
| 端點 | ServiceNow 伺服器的端點 (`http://<instance>.service-now.com`)。  | 是 |
| authenticationType | 要使用的驗證類型。 <br/>允許的值為：**Basic**、**OAuth2** | 是 |
| username | 用來連線到 ServiceNow 伺服器以進行 Basic 和 OAuth2 驗證的使用者名稱。  | 是 |
| 密碼 | 對應至用於進行 Basic 和 OAuth2 驗證之使用者名稱的密碼。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| clientId | 用於 OAuth2 驗證的用戶端識別碼。  | 否 |
| clientSecret | 用於 OAuth2 驗證的用戶端祕密。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 否 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 來加密資料來源端點。 預設值為 true。  | 否 |
| useHostVerification | 指定在透過 SSL 連線時，是否要求伺服器憑證中的主機名稱符合伺服器的主機名稱。 預設值為 true。  | 否 |
| usePeerVerification | 指定在透過 SSL 連線時，是否要確認伺服器的身分識別。 預設值為 true。  | 否 |

**例子：**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://<instance>.service-now.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

有關可用於定義資料集的節和屬性的完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 ServiceNow 資料集所支援的屬性清單。

若要從 ServiceNow 複製資料，請將資料集的 type 屬性設定為 **ServiceNowObject**。 以下是支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設置為：**服務現在物件** | 是 |
| tableName | 資料表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 ServiceNow 來源所支援的屬性清單。

### <a name="servicenow-as-source"></a>ServiceNow 作為來源

若要從 ServiceNow 複製資料，請將複製活動中的來源類型設定為 **ServiceNowSource**。 複製活動**源**部分支援以下屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為：**ServiceNowSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如：`"SELECT * FROM Actual.alm_asset"`。 | 否 (如果已指定資料集中的 "tableName") |

在查詢中指定 ServiceNow 的結構描述和資料行時，請注意下列內容，並**參考有關複製效能含意的[效能祕訣](#performance-tips)**。

- **結構描述：** 在 ServiceNow 查詢中，將結構描述指定為 `Actual` 或 `Display`，而在呼叫 [ServiceNow RESTful API](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET) 時，您可以將它視為 `sysparm_display_value` 的參數 (true 或 false)。 
- **資料行：**`Actual` 結構描述下實際值的資料行名稱為 `[column name]_value`，而 `Display` 結構描述下顯示值的資料行名稱為 `[column name]_display_value`。 請注意，資料行名稱必須對應至要在查詢中使用的結構描述。

**依例查詢：OR** 
 `SELECT col_value FROM Actual.alm_asset` 
`SELECT col_display_value FROM Display.alm_asset`

**例子：**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```
## <a name="performance-tips"></a>效能秘訣

### <a name="schema-to-use"></a>要使用的結構描述

ServiceNow 有 2 個不同的結構描述，一個是 **"Actual"** (傳回實際的資料)，另一個則是 **"Display"** (傳回資料的顯示值)。 

如果您的查詢中有一個篩選，請使用 "Actual" 結構描述，此結構描述具有較好的複製效能。 針對 "Actual" 結構描述進行查詢時，ServiceNow 在擷取資料以便只傳回已篩選的結果集時原本就支援篩選，而在查詢 "Display" 結構描述時，ADF 會擷取所有資料並在內部套用篩選。

### <a name="index"></a>索引

ServiceNow 資料表索引可協助改善查詢效能，請參閱[建立資料表索引](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/table_administration/task/t_CreateCustomIndex.html) \(英文\)。

## <a name="lookup-activity-properties"></a>查找活動屬性

要瞭解有關屬性的詳細資訊，請檢查[查找活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
