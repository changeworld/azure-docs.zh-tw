---
title: Azure 金鑰保管庫作為事件網格源
description: 使用 Azure 事件格格描述為 Azure 金鑰保管庫事件提供的屬性和架構
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 36b7b81a18c8725929ab5676b844e1ee319e287f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393303"
---
# <a name="azure-key-vault-as-event-grid-source"></a>Azure 金鑰保管庫作為事件網格源

本文提供[Azure 金鑰保管庫中](../key-vault/index.yml)的事件的屬性和架構,當前處於預覽狀態。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="event-grid-event-schema"></a>Event Grid 事件結構描述

### <a name="available-event-types"></a>可用的事件類型

Azure 金鑰保管庫帳戶產生以下事件類型:

| 事件全名 | 事件顯示名稱 | 描述 |
| ---------- | ----------- |---|
| 微軟.KeyVault.憑證新版本建立 | 已建立憑證新版本 | 創建新證書或新證書版本時觸發。 |
| 微軟.KeyVault.證書近過期 | 憑證接近過期 | 當前版本的證書即將過期時觸發。 (事件在到期日期前 30 天觸發。 |
| 微軟.KeyVault.憑證過期 | 憑證到期 | 證書過期時觸發。 |
| 微軟.KeyVault.Key Newversion建立 | 已建立金鑰新版本 | 創建新密鑰或新密鑰版本時觸發。 |
| 微軟.KeyVault.KeyNear過期 | 金鑰接近過期 | 當金鑰的當前版本即將過期時觸發。 (事件在到期日期前 30 天觸發。 |
| 微軟.KeyVault.金鑰過期 | 金鑰已過期 | 密鑰過期時觸發。 |
| 微軟.KeyVault.秘密新版本建立 | 已建立秘密新版本 | 創建新機密或新機密版本時觸發。 |
| 微軟.KeyVault.秘密近視 | 秘密接近過期 | 當前版本的機密即將過期時觸發。 (事件在到期日期前 30 天觸發。 |
| 微軟.KeyVault.秘密過期 | 秘密過期 | 當機密過期時觸發。 |

### <a name="event-examples"></a>事件範例

下面的範例顯示**Microsoft.KeyVault.秘密新版本建立的**架構 :

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
| id | 字串 | 觸發此事件的物件的識別碼 |
| vaultName | 字串 | 觸發此事件的物件的金鑰保管庫名稱 |
| objectType | 字串 | 觸發此事件的物件的類型 |
| 物件名稱 | 字串 | 觸發此事件的物件名稱 |
| version | 字串 | 觸發此事件的物件的版本 |
| nbf | number | 觸發此事件的物件的未早於日期(以秒為單位) |
| exp | number | 觸發此事件的物件的到期日期(以秒為單位) |

## <a name="tutorials-and-how-tos"></a>教學和如何
|Title  |描述  |
|---------|---------|
| [使用 Azure 事件網格監視金鑰保管庫事件](../key-vault/event-grid-overview.md) | 將密鑰保管庫與事件網格集成的概述。 |
| [教學:使用事件網格建立及監視金鑰保管庫事件](../key-vault/event-grid-tutorial.md) | 瞭解如何為金鑰保管庫設置事件網格通知。 |


## <a name="next-steps"></a>後續步驟

* 有關 Azure 事件網格的簡介,請參閱[什麼是事件網格?](overview.md)
* 有關如何創建 Azure 事件網格訂閱的詳細資訊,請參閱[事件網格訂閱架構](subscription-creation-schema.md)。
* 要瞭解有關密鑰保管庫與事件網格集成的更多資訊,請參閱[使用 Azure 事件網格監視金鑰保管庫(預覽)。](../key-vault/event-grid-overview.md)
* 有關金鑰保管庫與事件網格集成的教程,請參閱[使用 Azure 事件網格接收和回應金鑰保管庫通知(預覽)。](../key-vault/event-grid-tutorial.md)
* 要取得有關金鑰保管庫和 Azure 自動化的其他指南,請參閱:
    - [何謂 Azure Key Vault？](../key-vault/key-vault-overview.md)
    - [使用 Azure 事件方格監視 Key Vault (預覽)](../key-vault/event-grid-overview.md)
    - [使用 Azure 事件方格來接收和回應金鑰保存庫通知 (預覽)](../key-vault/event-grid-tutorial.md)
    - [Azure 自動化概述](../automation/index.yml)
