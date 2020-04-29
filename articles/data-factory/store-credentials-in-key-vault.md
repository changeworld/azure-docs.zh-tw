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
ms.openlocfilehash: 22ab4433d84db926733fd0b18035875e63322dda
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81451681"
---
# <a name="store-credential-in-azure-key-vault"></a>在 Azure Key Vault 中儲存認證

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

您可以在 [Azure Key Vault](../key-vault/general/overview.md) 中儲存供資料存放區和計算使用的認證。 執行活動 (該活動使用資料存放區/計算) 時，Azure Data Factory 會擷取認證。

目前，自訂活動以外的所有活動類型都支援這項功能。 特別針對連接器設定，查看[每個連接器主題](copy-activity-overview.md#supported-data-stores-and-formats)中的＜連結服務屬性＞一節以取得詳細資訊。

## <a name="prerequisites"></a>先決條件

此功能依賴 data factory 受控識別。 瞭解其如何從[data factory 的受控識別](data-factory-service-identity.md)運作，並確定您的 data factory 具有相關聯的資料處理站。

## <a name="steps"></a>步驟

若要參考儲存在 Azure Key Vault 中的認證，您需要：

1. 藉由複製與您的處理站一起產生的「受控識別物件識別碼」值，來**取得 data factory 受控識別**。 如果您使用 ADF 撰寫 UI，受控識別物件識別碼會顯示在 [Azure Key Vault 連結的服務建立] 視窗上;您也可以從 Azure 入口網站取出它，請參閱[取得 data factory 受控識別](data-factory-service-identity.md#retrieve-managed-identity)。
2. **將存取權授與 Azure Key Vault 的受控識別。** 在您的金鑰保存庫中 > 存取原則-> 新增存取原則]，搜尋此受控識別以授與 [秘密許可權] 下拉式清單中的 [**取得**] 許可權。 這樣可以讓這個指定的處理站存取金鑰保存庫中的密碼。
3. **建立指向您 Azure Key Vault 的連結服務。** 請參閱 [Azure Key Vault 已連結服務](#azure-key-vault-linked-service)。
4. **建立資料存放區連結服務，在其中參考儲存在金鑰保存庫中的對應密碼。** 請參閱[參考儲存在金鑰保存庫中的祕密](#reference-secret-stored-in-key-vault)。

## <a name="azure-key-vault-linked-service"></a>Azure Key Vault 已連結服務

以下是針對 Azure Key Vault 已連結服務支援的屬性：

| 屬性 | 描述 | 必要 |
|:--- |:--- |:--- |
| type | type 屬性必須設為：**AzureKeyVault**。 | 是 |
| baseUrl | 指定 Azure Key Vault URL。 | 是 |

**使用編寫 UI：**

選取 [**連接** -> ] [**連結服務** -> ] [**新增**]。 在 [新增連結服務] 中，搜尋並選取 [Azure Key Vault]：

![搜尋 Azure Key Vault](media/store-credentials-in-key-vault/search-akv.png)

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
| secretName | Azure Key Vault 中的密碼名稱。 | 是 |
| secretVersion | Azure Key Vault 中的密碼版本。<br/>如果未指定，它會一律使用最新版本的密碼。<br/>如果指定，則它會遵循指定的版本。| 否 |
| store | 代表您用來儲存認證的 Azure Key Vault 已連結服務。 | 是 |

**使用編寫 UI：**

在建立資料存放區/計算的連線時，請針對祕密欄位選取 [Azure Key Vault]****。 選取已佈建的 Azure Key Vault 連結服務，並提供 [祕密名稱]****。 您也可以選擇性地提供祕密版本。 

>[!TIP]
>針對使用連結服務（例如 SQL Server、Blob 儲存體等）中連接字串的連接器，您可以選擇只在 AKV 中儲存秘密欄位（例如密碼），或將整個連接字串儲存在 AKV 中。 您可以在 UI 上找到這兩個選項。

![設定 Azure Key Vault 秘密](media/store-credentials-in-key-vault/configure-akv-secret.png)

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
