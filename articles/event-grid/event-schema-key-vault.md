---
title: Azure Key Vault 作為事件方格來源
description: 說明使用 Azure 事件方格為 Azure Key Vault 事件提供的屬性和架構
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: b3d961228429c1f84e45c1df9147fa1687ab5074
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91324071"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure Key Vault 作為事件方格來源

本文提供 [Azure Key Vault](../key-vault/index.yml)中事件的屬性和架構。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure Key Vault 帳戶會產生下列事件種類：

| 事件的完整名稱 | 事件顯示名稱 | 說明 |
| ---------- | ----------- |---|
| KeyVault. CertificateNewVersionCreated | 已建立憑證新版本 | 當新憑證或新憑證版本建立時觸發。 |
| KeyVault. CertificateNearExpiry | 憑證即將到期 | 當目前版本的憑證即將到期時觸發。  (在到期日30天前觸發事件。 )  |
| KeyVault. CertificateExpired | 憑證到期 | 憑證到期時觸發。 |
| KeyVault. KeyNewVersionCreated | 已建立金鑰新版本 | 當新的金鑰或新的金鑰版本建立時觸發。 |
| KeyVault. KeyNearExpiry | 金鑰即將過期 | 當目前的金鑰版本即將到期時觸發。  (在到期日30天前觸發事件。 )  |
| KeyVault. KeyExpired | 金鑰已過期 | 當金鑰過期時觸發。 |
| KeyVault. SecretNewVersionCreated | 已建立秘密新版本 | 當新的秘密或新的秘密版本建立時觸發。 |
| KeyVault. 是 microsoft.keyvault.secretnearexpiry | 秘密即將過期 | 當目前的秘密版本即將到期時觸發。  (在到期日30天前觸發事件。 )  |
| KeyVault. SecretExpired | 秘密已過期 | 當秘密到期時觸發。 |
| KeyVault. VaultAccessPolicyChanged | 保存庫存取原則已變更 | 當 Key Vault 存取原則變更時觸發。 其中包含當 Key Vault 的許可權模型變更為 Azure RBAC 或從 Azure RBAC 變更時的案例  |

### <a name="event-examples"></a>事件範例

下列範例會顯示 KeyVault 的架構 **。 SecretNewVersionCreated**：

```JSON
[
   {
      "id":"00eccf70-95a7-4e7c-8299-2eb17ee9ad64",
      "topic":"/subscriptions/{subscription-id}/resourceGroups/sample-rg/providers/Microsoft.KeyVault/vaults/sample-kv",
      "subject":"newsecret",
      "eventType":"Microsoft.KeyVault.SecretNewVersionCreated",
      "eventTime":"2019-07-25T01:08:33.1036736Z",
      "data":{
         "Id":"https://sample-kv.vault.azure.net/secrets/newsecret/ee059b2bb5bc48398a53b168c6cdcb10",
         "vaultName":"sample-kv",
         "objectType":"Secret",
         "objectName ":"newsecret",
         "version":" ee059b2bb5bc48398a53b168c6cdcb10",
         "nbf":"1559081980",
         "exp":"1559082102"
      },
      "dataVersion":"1",
      "metadataVersion":"1"
   }
]
```

### <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| ---------- | ----------- |---|
| id | 字串 | 觸發這個事件之物件的識別碼。 |
| vaultName | 字串 | 觸發此事件之物件的金鑰保存庫名稱 |
| objectType | 字串 | 觸發這個事件之物件的類型。 |
| objectName | 字串 | 觸發這個事件之物件的名稱。 |
| version | 字串 | 觸發這個事件之物件的版本 |
| nbf | number | 觸發這個事件之物件的從 1970-01-01T00：00：00Z 起的不早日期（以秒為單位） |
| exp | number | 觸發這個事件之物件的 1970-01-01T00：00：00Z 之後的到期日（以秒為單位） |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|標題  |說明  |
|---------|---------|
| [使用 Azure 事件方格監視 Key Vault 事件](../key-vault/general/event-grid-overview.md) | 整合 Key Vault 與事件方格的總覽。 |
| [教學課程：使用事件方格建立和監視 Key Vault 事件](../key-vault/general/event-grid-logicapps.md) | 瞭解如何設定 Key Vault 的事件方格通知。 |


## <a name="next-steps"></a>後續步驟

* 如需 Azure 事件方格的簡介，請參閱 [什麼是事件方格？](overview.md)。
* 如需如何建立 Azure 事件方格訂用帳戶的詳細資訊，請參閱 [事件方格訂](subscription-creation-schema.md)用帳戶架構。
* 如需金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure Key Vault？](../key-vault/general/overview.md)

