---
title: 在 Azure Key Vault 中儲存認證
description: 了解如何為 Azure Key Vault 中使用的資料存放區儲存認證，Azure Data Factory 可以在執行階段自動擷取。
services: data-factory
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: jingwang
ms.openlocfilehash: b5a181625488a57de4b878d13c01a8c90bf8785a
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414668"
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中儲存認證

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

您可以在 [Azure Key Vault](../key-vault/key-vault-overview.md) 中儲存供資料存放區和計算使用的認證。 執行活動 (該活動使用資料存放區/計算) 時，Azure Data Factory 會擷取認證。

目前，自訂活動以外的所有活動類型都支援這項功能。 特別針對連接器設定，查看[每個連接器主題](copy-activity-overview.md#supported-data-stores-and-formats)中的＜連結服務屬性＞一節以取得詳細資訊。

## <a name="prerequisites"></a>Prerequisites

此功能依賴於數據工廠託管標識。 瞭解數據[工廠的託管標識](data-factory-service-identity.md)的工作原理,並確保數據工廠具有關聯的標識。

## <a name="steps"></a>步驟

若要參考儲存在 Azure Key Vault 中的認證，您需要：

1. 以複製與工廠一起產生的「託管識別物件 ID」 的值來**檢索資料出廠管理標識**。 如果使用 ADF 創作 UI,託管識別物件 ID 將顯示在 Azure 密鑰保管庫連結服務創建視窗中;如果使用 ADF 創作 UI,則託管標識物件 ID 將顯示在 Azure 密鑰保管庫連結的服務創建視窗中。還可以從 Azure 門戶檢索它,請參閱[檢索資料工廠託管標識](data-factory-service-identity.md#retrieve-managed-identity)。
2. **授予 Azure 金鑰保管庫的託管標識訪問許可權。** 在密鑰保管庫 -> 訪問策略 ->添加访问策略中,搜索此託管標識以在「機密許可權下拉」中授予**獲取**許可權。 這樣可以讓這個指定的處理站存取金鑰保存庫中的密碼。
3. **創建指向 Azure 金鑰保管庫的連結服務。** 請參閱 [Azure Key Vault 已連結服務](#azure-key-vault-linked-service)。
4. **創建數據存儲連結服務,其中引用存儲在密鑰保管庫中的相應機密。** 請參閱[參考儲存在金鑰保存庫中的祕密](#reference-secret-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 已連結服務

以下是針對 Azure Key Vault 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設為：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL。 | 是 |

**使用編寫 UI：**

選擇**連線** -> **連結服務** -> **New**。 在「新建連結服務」中,搜尋並選擇「Azure 密鑰保管庫」:

![搜尋 Azure 金鑰保存庫](media/store-credentials-in-key-vault/search-akv.png)

選取儲存認證的已佈建 Azure Key Vault。 您可以 [測試連線]****，確保您的 AKV 連線有效。 

![設定 Azure Key Vault](media/store-credentials-in-key-vault/configure-akv.png)

**JSON 範例：**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
            "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-secret-stored-in-key-vault"></a>參考儲存在金鑰保存庫中的祕密

當您在參考金鑰保存庫密碼的已連結服務中設定欄位時，支援下列屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | 欄位的 type 屬性必須設定為：**AzureKeyVaultSecret**。 | 是 |
| secretName | Azure 密鑰保管庫中的機密名稱。 | 是 |
| secretVersion | Azure 密鑰保管庫中的機密版本。<br/>如果未指定，它會一律使用最新版本的密碼。<br/>如果指定，則它會遵循指定的版本。| 否 |
| store | 代表您用來儲存認證的 Azure Key Vault 已連結服務。 | 是 |

**使用編寫 UI：**

在建立資料存放區/計算的連線時，請針對祕密欄位選取 [Azure Key Vault]****。 選取已佈建的 Azure Key Vault 連結服務，並提供 [祕密名稱]****。 您也可以選擇性地提供祕密版本。 

>[!TIP]
>對於在連結服務(如 SQL Server、Blob 儲存等)中使用連接字串的連接器,您可以選擇僅儲存密碼欄位(例如 AKV 中的密碼),或者將整個連接字串儲存在 AKV 中。 您可以在 UI 上找到這兩個選項。

![設定 Azure 金鑰保存庫金鑰](media/store-credentials-in-key-vault/configure-akv-secret.png)

**JSON 範例：(請參閱「密碼」一節)**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>後續步驟
如需 Azure Data Factory 中的複製活動所支援作為來源和接收器的資料存放區清單，請參閱[支援的資料存放區](copy-activity-overview.md#supported-data-stores-and-formats)。
