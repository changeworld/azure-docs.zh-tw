---
title: 做為事件方格來源 Azure Key Vault
description: 說明使用 Azure 事件方格為 Azure Key Vault 事件提供的屬性和架構
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 40bff9585e64163039a8847ff868c982ffb20414
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81458244"
---
# <a name="azure-key-vault-as-event-grid-source"></a>做為事件方格來源 Azure Key Vault

本文提供[Azure Key Vault](../key-vault/index.yml)中事件的屬性和架構，目前為預覽狀態。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure Key Vault 帳戶會產生下列事件種類：

| 事件完整名稱 | 事件顯示名稱 | 描述 |
| ---------- | ----------- |---|
| KeyVault. CertificateNewVersionCreated | 已建立憑證新版本 | 在建立新憑證或新憑證版本時觸發。 |
| KeyVault. CertificateNearExpiry | 憑證即將到期 | 當憑證的目前版本即將到期時觸發。 （事件會在到期日的30天前觸發）。 |
| KeyVault. CertificateExpired | 憑證到期 | 當憑證過期時觸發。 |
| KeyVault. KeyNewVersionCreated | 已建立金鑰新版本 | 在建立新的金鑰或新的金鑰版本時觸發。 |
| KeyVault. KeyNearExpiry | 金鑰即將到期 | 當目前的金鑰版本即將到期時觸發。 （事件會在到期日的30天前觸發）。 |
| KeyVault. KeyExpired | 金鑰已過期 | 在金鑰過期時觸發。 |
| KeyVault. SecretNewVersionCreated | 已建立秘密新版本 | 在建立新的秘密或新的秘密版本時觸發。 |
| KeyVault. SecretNearExpiry | 密碼即將到期 | 當目前的秘密版本即將到期時觸發。 （事件會在到期日的30天前觸發）。 |
| KeyVault. SecretExpired | 密碼已過期 | 當秘密過期時觸發。 |

### <a name="event-examples"></a>事件範例

下列範例顯示**KeyVault. SecretNewVersionCreated**的架構：

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
| id | 字串 | 觸發此事件之物件的識別碼 |
| vaultName | 字串 | 觸發此事件的物件金鑰保存庫名稱 |
| objectType | 字串 | 觸發此事件之物件的類型 |
| 下方 | 字串 | 觸發此事件的物件名稱 |
| version | 字串 | 觸發此事件的物件版本 |
| nbf | number | 觸發此事件的物件自 1970-01-01T00：00：00Z 之後的非之前日期（以秒為單位） |
| exp | number | 觸發此事件的物件自 1970-01-01T00：00：00Z 之後的到期日（以秒為單位） |

## <a name="tutorials-and-how-tos"></a>教學課程和操作說明
|Title  |描述  |
|---------|---------|
| [使用 Azure 事件方格監視 Key Vault 事件](../key-vault/general/event-grid-overview.md) | 整合 Key Vault 與事件方格的總覽。 |
| [教學課程：使用事件方格建立和監視 Key Vault 事件](../key-vault/general/event-grid-tutorial.md) | 瞭解如何設定 Key Vault 的事件方格通知。 |


## <a name="next-steps"></a>後續步驟

* 如需 Azure Event Grid 的簡介，請參閱[什麼是事件方格？](overview.md)。
* 如需有關如何建立 Azure 事件方格訂用帳戶的詳細資訊，請參閱[Event grid 訂](subscription-creation-schema.md)用帳戶架構。
* 若要深入瞭解 Key Vault 與 Event Grid 整合，請參閱[使用 Azure 事件方格監視 Key Vault （預覽）](../key-vault/general/event-grid-overview.md)。
* 如需 Key Vault 與 Event Grid 整合的教學課程，請參閱[使用 Azure 事件方格接收和回應金鑰保存庫通知（預覽）](../key-vault/general/event-grid-tutorial.md)。
* 若要取得 Key Vault 和 Azure 自動化的其他指引，請參閱：
    - [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)
    - [使用 Azure 事件方格監視 Key Vault (預覽)](../key-vault/general/event-grid-overview.md)
    - [使用 Azure 事件方格來接收和回應金鑰保存庫通知 (預覽)](../key-vault/general/event-grid-tutorial.md)
    - [Azure 自動化概觀](../automation/index.yml)
