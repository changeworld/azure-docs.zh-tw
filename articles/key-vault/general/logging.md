---
title: Azure Key Vault 記錄 | Microsoft Docs
description: 要了解如何監視金鑰保存庫存取，您可以啟用 Azure Key Vault 記錄，以在您提供的 Azure 儲存體帳戶中儲存這方面的資訊。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5423fc27ecc58bcd79b36a845e4b7569f342f712
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286709"
---
# <a name="azure-key-vault-logging"></a>Azure Key Vault 記錄

在建立一或多個金鑰保存庫之後，您可能會想要監視金鑰保存庫的存取方式、時間和存取者。 想要做到這點，您可以啟用 Azure Key Vault 記錄，以在您提供的 Azure 儲存體帳戶中儲存這方面的資訊。 如需設定這項操作的逐步指引，請參閱[如何啟用 Key Vault 記錄](howto-logging.md)。

在金鑰保存庫作業 10 分鐘 (最多) 後，您就可以存取記錄資訊。 但大多不用這麼久。  儲存體帳戶中的記錄由您全權管理：

* 請使用標準的 Azure 存取控制方法限制可存取記錄的人員，藉此來保護記錄。
* 刪除不想繼續保留在儲存體帳戶中的記錄。

如 Key Vault 的概觀資訊，請參閱[什麼是 Azure Key Vault？](overview.md) 如需 Key Vault 適用地區的詳細資訊，請參閱[定價頁面](https://azure.microsoft.com/pricing/details/key-vault/)。 如需使用[適用於 Key Vault 的 Azure 監視器](../../azure-monitor/insights/key-vault-insights-overview.md)相關資訊。

## <a name="interpret-your-key-vault-logs"></a>解譯金鑰保存庫記錄

當您啟用記錄時，系統會自動為您指定的儲存體帳戶建立稱為 **insights-logs-auditevent** 的新容器。 您可以使用此相同儲存體帳戶來收集多個金鑰保存庫的記錄。

各個 blob 皆會儲存為文字，並格式化為 JSON blob。 讓我們看看記錄項目範例。 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

下表列出各個欄位的名稱和其描述：

| 欄位名稱 | 描述 |
| --- | --- |
| **time** |日期和時間 (UTC)。 |
| **resourceId** |Azure Resource Manager 資源識別碼。 對於金鑰保存庫記錄來說，這一律是金鑰保存庫的資源識別碼。 |
| **operationName** |下一份表格所述作業的名稱。 |
| **operationVersion** |用戶端所要求的 REST API 版本。 |
| **類別** |結果的類型。 對於 Key Vault 記錄來說，`AuditEvent` 是其唯一可用的值。 |
| **resultType** |REST API 要求的結果。 |
| **resultSignature** |HTTP 狀態。 |
| **resultDescription** |結果的其他描述 (若有提供)。 |
| **durationMs** |服務 REST API 要求時所花費的時間，以毫秒為單位。 此時間不包括網路延遲，因此在用戶端所測量到的時間可能不符合此時間。 |
| **callerIpAddress** |提出要求用戶端的 IP 位址。 |
| **correlationId** |選擇性的 GUID，用戶端可傳遞此 GUID 來讓用戶端記錄與服務端 (金鑰保存庫) 記錄相互關聯。 |
| **身分識別** |權杖中的身分識別，會在 REST API 要求中提供。 就像從 Azure PowerShell Cmdlet 產生的要求一樣，這通常是「使用者」、「服務主體」或「使用者+appId」的組合。 |
| **properties** |根據作業 ( **operationName** ) 而有所不同的資訊。 在大部分情況下，此欄位會包含用戶端資訊 (用戶端傳遞的使用者代理程式字串)、完全符合的 REST API 要求 URI 和 HTTP 狀態碼。 此外，在因為提出要求 (例如， **KeyCreate** 或 **VaultGet** ) 而傳回物件時，此欄位也會包含金鑰 URI (形式為 `id`)、保存庫 URI 或祕密 URI。 |

**operationName** 欄位值的格式為 *ObjectVerb* 。 例如：

* 所有金鑰保存庫作業都有 `Vault<action>` 格式，例如 `VaultGet` 和 `VaultCreate`。
* 所有金鑰作業都有 `Key<action>` 格式，例如 `KeySign` 和 `KeyList`。
* 所有祕密作業都有 `Secret<action>` 格式，例如 `SecretGet` 和 `SecretListVersions`。

下表列出 **operationName** 值和相對應的 REST API 命令：

### <a name="operation-names-table"></a>作業名稱資料表

| operationName | REST API 命令 |
| --- | --- |
| **驗證** |透過 Azure Active Directory 端點驗證 |
| **VaultGet** |[取得金鑰保存庫的相關資訊](/rest/api/keyvault/vaults) |
| **VaultPut** |[建立或更新金鑰保存庫](/rest/api/keyvault/vaults) |
| **VaultDelete** |[刪除金鑰保存庫](/rest/api/keyvault/vaults) |
| **VaultPatch** |[更新金鑰保存庫](/rest/api/keyvault/vaults) |
| **VaultList** |[列出資源群組中的所有金鑰保存庫](/rest/api/keyvault/vaults) |
| **KeyCreate** |[建立金鑰](/rest/api/keyvault/createkey) |
| **KeyGet** |[取得金鑰的相關資訊](/rest/api/keyvault/getkey) |
| **KeyImport** |[將金鑰匯入保存庫](/rest/api/keyvault/vaults) |
| **KeyBackup** |[備份金鑰](/rest/api/keyvault/backupkey) |
| **KeyDelete** |[刪除金鑰](/rest/api/keyvault/deletekey) |
| **KeyRestore** |[還原金鑰](/rest/api/keyvault/restorekey) |
| **KeySign** |[使用金鑰簽署](/rest/api/keyvault/sign) |
| **KeyVerify** |[使用金鑰驗證](/rest/api/keyvault/vaults) |
| **KeyWrap** |[包裝金鑰](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[解除包裝金鑰](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[使用金鑰加密](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[使用金鑰解密](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[更新金鑰](/rest/api/keyvault/updatekey) |
| **KeyList** |[列出保存庫中的金鑰](/rest/api/keyvault/vaults) |
| **KeyListVersions** |[列出金鑰的版本](/rest/api/keyvault/getkeyversions) |
| **SecretSet** |[建立密碼](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[取得祕密](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[更新密碼](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[刪除秘密](/rest/api/keyvault/deletesecret) |
| **SecretList** |[列出保存庫中的密碼](/rest/api/keyvault/vaults) |
| **SecretListVersions** |[列出密碼的版本](/rest/api/keyvault/getsecretversions) |
| **VaultAccessPolicyChangedEventGridNotification** | 已發佈保存庫存取原則已變更事件 |
| **SecretNearExpiryEventGridNotification** |已發佈祕密即將過期事件 |
| **SecretExpiredEventGridNotification** |已發佈祕密已過期事件 |
| **KeyNearExpiryEventGridNotification** |已發佈金鑰即將過期事件 |
| **KeyExpiredEventGridNotification** |已發佈金鑰已過期事件 |
| **CertificateNearExpiryEventGridNotification** |已發佈憑證即將過期事件 |
| **CertificateExpiredEventGridNotification** |已發佈憑證已過期事件 |

## <a name="use-azure-monitor-logs"></a>使用 Azure 監視器記錄

您可以使用 Azure 監視器記錄中的 Key Vault 解決方案來檢閱 Key Vault 的 `AuditEvent` 記錄。 在 Azure 監視器記錄中，您可以使用記錄查詢來分析資料，並取得所需的資訊。 

如需詳細資訊 (包括如何進行此設定)，請參閱 [Azure 監視器中的 Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)。

## <a name="next-steps"></a>後續步驟

- [如何啟用 Key Vault 記錄](howto-logging.md)
- 如需在 .NET Web 應用程式中使用 Azure Key Vault 的教學課程，請參閱[從 Web 應用程式使用 Azure Key Vault](tutorial-net-create-vault-azure-web-app.md)。
- 如需程式設計參考，請參閱 [Azure 金鑰保存庫開發人員指南](developers-guide.md)。
- 如需 Azure 金鑰保存庫的 Azure PowerShell 1.0 Cmdlet 清單，請參閱 [Azure 金鑰保存庫 Cmdlet](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)。