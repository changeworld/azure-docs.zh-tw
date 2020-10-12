---
title: 使用 Azure Data Factory 從 Zoho 複製資料 (預覽)
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Zoho 將資料複製到支援的接收資料存放區。
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: jingwang
ms.openlocfilehash: 78e7fc6b2a4c9804fbba60aa9946cc612b494461
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87531280"
---
# <a name="copy-data-from-zoho-using-azure-data-factory-preview"></a>使用 Azure Data Factory 從 Zoho 複製資料 (預覽)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Zoho 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

> [!IMPORTANT]
> 此連接器目前為預覽版。 您可以親身體驗並提供意見反應。 如果您需要依賴解決方案中的預覽連接器，請連絡 [Azure 支援](https://azure.microsoft.com/support/)。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Zoho 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)


您可以將資料從 Zoho 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

此連接器支援 Xero 存取權杖驗證和 OAuth 2.0 驗證。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Zoho 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Zoho 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設定為：**Zoho** | 是 |
| connectionProperties | 定義如何連接到 Zoho 的一組屬性。 | 是 |
| ***在 `connectionProperties` 下列情況下：*** | | |
| 端點 | Zoho 伺服器的端點 (`crm.zoho.com/crm/private`)。 | 是 |
| authenticationType | 允許的值為 `OAuth_2.0` 和 `Access Token` 。 | 是 |
| clientId | 與您的 Zoho 應用程式相關聯的用戶端識別碼。 | Yes 表示 OAuth 2.0 驗證 | 
| clientSecrect | 與您的 Zoho 應用程式相關聯的 clientsecret。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | Yes 表示 OAuth 2.0 驗證 | 
| refreshToken | 與您的 Zoho 應用程式相關聯的 OAuth 2.0 重新整理權杖，用來在存取權杖過期時重新整理。 重新整理權杖永遠不會過期。 若要取得重新整理權杖，您必須要求 `offline` access_type 的詳細資訊，請參閱 [這篇文章](https://www.zoho.com/crm/developer/docs/api/auth-request.html)。 <br>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。| Yes 表示 OAuth 2.0 驗證 |
| accessToken | 用於 Zoho 驗證的存取權杖。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 來加密資料來源端點。 預設值為 true。  | 否 |
| useHostVerification | 指定在透過 TLS 連線時，是否要求伺服器憑證中的主機名稱符合伺服器的主機名稱。 預設值為 true。  | 否 |
| usePeerVerification | 指定是否要在透過 TLS 連接時驗證服務器的身分識別。 預設值為 true。  | 否 |

**範例： OAuth 2.0 驗證**

```json
{
    "name": "ZohoLinkedService",
    "properties": {
        "type": "Zoho",
        "typeProperties": {
            "connectionProperties": { 
                "authenticationType":"OAuth_2.0", 
                "endpoint": "crm.zoho.com/crm/private", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<client secret>"
                },
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true, 
                "usePeerVerification": true
            }
        }
    }
}
```

**範例：存取權杖驗證**

```json
{
    "name": "ZohoLinkedService",
    "properties": {
        "type": "Zoho",
        "typeProperties": {
            "connectionProperties": { 
                "authenticationType":"Access Token", 
                "endpoint": "crm.zoho.com/crm/private", 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true
            }
        }
    }
}
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Zoho 資料集所支援的屬性清單。

若要從 Zoho 複製資料，請將資料集的 type 屬性設定為 **ZohoObject**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **ZohoObject** | 是 |
| tableName | 資料表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "ZohoDataset",
    "properties": {
        "type": "ZohoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Zoho linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Zoho 來源所支援的屬性清單。

### <a name="zoho-as-source"></a>Zoho 作為來源

若要從 Zoho 複製資料，請將複製活動中的來源類型設定為 **ZohoSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為：**ZohoSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM Accounts"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromZoho",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Zoho input dataset name>",
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
                "type": "ZohoSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
