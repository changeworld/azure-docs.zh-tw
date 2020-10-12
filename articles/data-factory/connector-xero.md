---
title: 使用 Azure Data Factory 從 Xero 複製資料
description: 了解如何使用 Azure Data Factory 管線中的複製活動，從 Xero 將資料複製到支援的接收資料存放區。
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
ms.openlocfilehash: 14b3857211eca39ebe09a3a0752ca1d8eee17bc0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87529988"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>使用 Azure Data Factory 從 Xero 複製資料

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文概述如何使用 Azure Data Factory 中的「複製活動」，從 Xero 複製資料。 本文是根據[複製活動概觀](copy-activity-overview.md)一文，該文提供複製活動的一般概觀。

## <a name="supported-capabilities"></a>支援的功能

下列活動支援此 Xero 連接器：

- 含[支援來源/接收器矩陣](copy-activity-overview.md)的[複製活動](copy-activity-overview.md)
- [查閱活動](control-flow-lookup-activity.md)

您可以將資料從 Xero 複製到任何支援的接收資料存放區。 如需複製活動所支援作為來源/接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)表格。

具體而言，這個 Xero 連接器支援：

- Xero [私人應用程式](https://developer.xero.com/documentation/getting-started/getting-started-guide)，但不支援公用應用程式。
- 所有 Xero 資料表 (API 端點)，但「報告」除外。
- OAuth 1.0 和 OAuth 2.0 驗證。

Azure Data Factory 提供的內建驅動程式可啟用連線，因此使用此連接器您不需要手動安裝任何驅動程式。

## <a name="getting-started"></a>開始使用

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

下列各節提供屬性的相關詳細資料，這些屬性是用來定義 Xero 連接器專屬的 Data Factory 實體。

## <a name="linked-service-properties"></a>連結服務屬性

以下是針對 Xero 已連結服務支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | Type 屬性必須設定為：**Xero** | 是 |
| connectionProperties | 定義如何連接到 Xero 的一組屬性。 | 是 |
| ***在 `connectionProperties` 下列情況下：*** | | |
| 主機 | Xero 伺服器的端點 (`api.xero.com`)。  | 是 |
| authenticationType | 允許的值為 `OAuth_2.0` 和 `OAuth_1.0` 。 | 是 |
| consumerKey | 與 Xero 應用程式相關聯的取用者金鑰。 將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| privateKey | 從您 Xero 私人應用程式產生之 .pem 檔案的私密金鑰，請參閱[建立公開/私密金鑰組](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key)。 請注意，若要 **產生使用 numbits 512 的 privatekey** `openssl genrsa -out privatekey.pem 512` ，則不支援1024。 包含 .pem 檔案的所有文字，包括 Unix 行尾結束符號 (\n)，請參閱以下範例。<br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | 是 |
| tenantId | 與您的 Xero 應用程式相關聯的租使用者識別碼。 適用于 OAuth 2.0 驗證。<br>瞭解如何從 [檢查您已獲授權存取的租使用者區段](https://developer.xero.com/documentation/oauth2/auth-flow)中取得租使用者識別碼。 | Yes 表示 OAuth 2.0 驗證 |
| refreshToken | 與 Xero 應用程式相關聯的 OAuth 2.0 重新整理權杖，用來在存取權杖過期時重新整理存取權杖。 適用于 OAuth 2.0 驗證。 瞭解 [如何從本文](https://developer.xero.com/documentation/oauth2/auth-flow)取得重新整理權杖。<br>重新整理權杖永遠不會過期。 若要取得重新整理權杖，您必須要求 [offline_access 範圍](https://developer.xero.com/documentation/oauth2/scopes)。<br/>將此欄位標記為 SecureString，將它安全地儲存在 Data Factory 中，或[參考 Azure Key Vault 中儲存的祕密](store-credentials-in-key-vault.md)。 | Yes 表示 OAuth 2.0 驗證 |
| useEncryptedEndpoints | 指定是否使用 HTTPS 來加密資料來源端點。 預設值為 true。  | 否 |
| useHostVerification | 指定在透過 TLS 連線時，伺服器的憑證中是否需要主機名稱，以符合伺服器的主機名稱。 預設值為 true。  | 否 |
| usePeerVerification | 指定是否要在透過 TLS 連接時驗證服務器的身分識別。 預設值為 true。  | 否 |

**範例： OAuth 2.0 驗證**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                },
                "tenantId": "<tenant ID>", 
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

**範例： OAuth 1.0 驗證**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**範例私密金鑰值：**

包含 .pem 檔案的所有文字，包括 Unix 行尾結束符號 (\n)。

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>資料集屬性

如需可用來定義資料集的區段和屬性完整清單，請參閱[資料集](concepts-datasets-linked-services.md)一文。 本節提供 Xero 資料集所支援的屬性清單。

若要從 Xero 複製資料，請將資料集的 type 屬性設定為 **XeroObject**。 以下是支援的屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 資料集的類型屬性必須設定為： **XeroObject** | 是 |
| tableName | 資料表的名稱。 | 否 (如果已指定活動來源中的「查詢」) |

**範例**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>複製活動屬性

如需可用來定義活動的區段和屬性完整清單，請參閱[管線](concepts-pipelines-activities.md)一文。 本節提供 Xero 來源所支援的屬性清單。

### <a name="xero-as-source"></a>Xero 作為來源

若要從 Xero 複製資料，請將複製活動中的來源類型設定為 **XeroSource**。 複製活動的 **source** 區段支援下列屬性：

| 屬性 | 說明 | 必要 |
|:--- |:--- |:--- |
| type | 複製活動來源的 type 屬性必須設定為：**XeroSource** | 是 |
| 查詢 | 使用自訂 SQL 查詢來讀取資料。 例如： `"SELECT * FROM Contacts"` 。 | 否 (如果已指定資料集中的 "tableName") |

**範例︰**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

指定 Xero 查詢時，請注意下列事項：

- 具有複雜項目的資料表將會分割成多個資料表。 例如，銀行交易有複雜的資料結構 "LineItems"，所以銀行交易的資料會對應至資料表 `Bank_Transaction` 和 `Bank_Transaction_Line_Items`，並以 `Bank_Transaction_ID` 作為外部索引鍵而連結在一起。

- Xero 資料可透過兩個結構描述取得：`Minimal` (預設值) 和 `Complete`。 Complet 結構描述包含必要呼叫資料表，其在進行所要的查詢之前需有額外的資料 (例如識別碼資料行)。

下表中的 Minimal 和 Complete 結構描述有相同的資訊。 若要減少 API 呼叫數目，請使用 Minimal 結構描述 (預設值)。

- Bank_Transactions
- Contact_Groups 
- 連絡人 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- 發票 
- Invoices_Credit_Notes
- Invoices_Prepayments 
- Invoices_Overpayments 
- Manual_Journals 
- Overpayments 
- Overpayments_Allocations 
- Prepayments 
- Prepayments_Allocations 
- Receipts 
- Receipt_Validation_Errors 
- Tracking_Categories

只有使用 Complete 結構描述時才會查詢下表：

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_Persons 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_Payments 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>查閱活動屬性

若要了解關於屬性的詳細資料，請參閱[查閱活動](control-flow-lookup-activity.md)。


## <a name="next-steps"></a>後續步驟
如需複製活動所支援的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
