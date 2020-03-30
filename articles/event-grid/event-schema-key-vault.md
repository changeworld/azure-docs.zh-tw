---
title: Azure 金鑰保存庫的 Azure 事件網格事件架構
description: 使用 Azure 事件網格描述為 Azure 金鑰保存庫事件提供的屬性和架構
services: event-grid
author: msmbaldwin
ms.service: event-grid
ms.topic: reference
ms.date: 10/25/2019
ms.author: mbaldwin
ms.openlocfilehash: 17404388b2b6c3fee1c6ab666f7233a66817f642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082874"
---
# <a name="azure-event-grid-event-schema-for-azure-key-vault-preview"></a>Azure Key Vault 的 Azure 事件方格事件結構描述 (預覽)

本文提供[Azure 金鑰保存庫中](../key-vault/index.yml)的事件的屬性和架構，當前處於預覽狀態。 如需事件結構描述的簡介，請參閱 [Azure Event Grid 事件結構描述](event-schema.md)。

## <a name="available-event-types"></a>可用的事件類型

Azure 金鑰保存庫帳戶生成以下事件種類：

| 事件全名 | 事件顯示名稱 | 描述 |
| ---------- | ----------- |---|
| 微軟.KeyVault.證書新版本創建 | 已創建證書新版本 | 創建新證書或新證書版本時觸發。 |
| 微軟.KeyVault.證書近過期 | 證書接近過期 | 當前版本的證書即將過期時觸發。 （預設值為到期日期前 30 天。 |
| 微軟.KeyVault.證書過期 | 憑證到期 | 證書過期時觸發。 |
| 微軟.KeyVault.Key Newversion創建 | 已創建金鑰新版本 | 創建新金鑰或新金鑰版本時觸發。 |
| 微軟.KeyVault.KeyNear過期 | 金鑰接近過期 | 當金鑰的當前版本即將過期時觸發。 （預設值為到期日期前 30 天。 |
| 微軟.KeyVault.金鑰過期 | 金鑰已過期 | 金鑰過期時觸發。 |
| 微軟.KeyVault.秘密新版本創建 | 已創建秘密新版本 | 創建新機密或新機密版本時觸發。 |
| 微軟.KeyVault.秘密近視 | 秘密接近過期 | 當前版本的機密即將過期時觸發。 （預設值為到期日期前 30 天。 |
| 微軟.KeyVault.秘密過期 | 秘密過期 | 當機密過期時觸發。 |

## <a name="event-examples"></a>事件範例

下面的示例顯示**Microsoft.KeyVault.秘密新版本創建的**架構 ：

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

## <a name="event-properties"></a>事件屬性

事件具有下列的最高層級資料：

| 屬性 | 類型 | 描述 |
| ---------- | ----------- |---|
| id | 字串 | 觸發此事件的物件的 ID |
| vaultName | 字串 | 觸發此事件的物件的金鑰保存庫名稱 |
| objectType | 字串 | 觸發此事件的物件的類型 |
| 物件名稱 | 字串 | 觸發此事件的物件的名稱 |
| version | 字串 | 觸發此事件的物件的版本 |
| nbf | number | 觸發此事件的物件的未早于日期（以秒為單位） |
| exp | number | 觸發此事件的物件的到期日期（以秒為單位） |


## <a name="next-steps"></a>後續步驟

* 有關 Azure 事件網格的簡介，請參閱[什麼是事件網格？](overview.md)
* 有關如何創建 Azure 事件網格訂閱的詳細資訊，請參閱[事件網格訂閱架構](subscription-creation-schema.md)。
* 要瞭解有關金鑰保存庫與事件網格集成的更多資訊，請參閱[使用 Azure 事件網格監視金鑰保存庫（預覽）。](../key-vault/event-grid-overview.md)
* 有關金鑰保存庫與事件網格集成的教程，請參閱[使用 Azure 事件網格接收和回應金鑰保存庫通知（預覽）。](../key-vault/event-grid-tutorial.md)
* 要獲取有關金鑰保存庫和 Azure 自動化的其他指南，請參閱：
    - [何謂 Azure Key Vault？](../key-vault/key-vault-overview.md)
    - [使用 Azure 事件方格監視 Key Vault (預覽)](../key-vault/event-grid-overview.md)
    - [使用 Azure 事件方格來接收和回應金鑰保存庫通知 (預覽)](../key-vault/event-grid-tutorial.md)
    - [Azure 自動化概述](../automation/index.yml)
