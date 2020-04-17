---
title: 將 Azure 自動化中的安全資產加密
description: Azure 自動化使用多級加密保護安全資產。 默認情況下,加密使用Microsoft管理的金鑰完成。 客戶可以將其自動化帳戶配置為使用客戶管理的密鑰進行加密。 本文介紹了兩種加密模式的詳細資訊以及如何在兩種模式之間切換。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457479"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>將 Azure 自動化中的安全資產加密

Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產在 Azure 自動化中使用多級別的加密進行保護。 基於加密的頂級金鑰,有兩種加密模型:
-    使用 Microsoft 管理的金鑰
-    使用客戶管理的金鑰

## <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

默認情況下,Azure 自動化帳戶使用Microsoft管理的密鑰。

每個安全資產都使用為每個自動化帳戶生成的唯一密鑰(數據加密密鑰)加密並存儲在 Azure 自動化中。 這些密鑰本身使用為每個帳戶生成稱為帳戶加密密鑰 (AEK) 的唯一密鑰在 Azure 自動化中加密並存儲。 這些帳戶加密金鑰使用Microsoft管理的密鑰加密並存儲在Azure自動化中。 

## <a name="customer-managed-keys-with-key-vault-preview"></a>帶金鑰保管庫的客戶管理金鑰(預覽)

您可以使用自己的密鑰管理自動化帳戶的安全資產加密。 在自動化帳戶級別指定客戶管理的密鑰時,該密鑰用於保護和控制對自動化帳戶帳戶帳戶帳戶加密金鑰的訪問。 這反過來又用於加密和解密所有安全資產。 客戶管理的密鑰提供了創建、旋轉、禁用和撤銷存取控制的更大靈活性。 您還可以審核用於保護安全資產的加密密鑰。

使用 Azure 金鑰保管庫儲存客戶管理的密鑰。 您可以建立自己的密鑰並將其存儲在密鑰保管庫中,也可以使用 Azure 金鑰保管庫 API 產生金鑰。  有關 Azure 金鑰保管庫的詳細資訊,請參閱[什麼是 Azure 密鑰保管庫?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>為自動化帳戶啟用客戶管理的金鑰

使用自動化帳戶的客戶託管金鑰啟用加密時,Azure自動化使用關聯的密鑰保管庫中的客戶託管金鑰包裝帳戶加密密鑰。 啟用客戶管理的密鑰不會影響性能,並且帳戶將立即使用新密鑰加密,不會有任何延遲。

新的自動化帳戶始終使用Microsoft管理的密鑰進行加密。 在創建帳戶時無法啟用客戶管理的密鑰。 客戶管理的金鑰存儲在 Azure 密鑰保管庫中,密鑰保管庫必須預配存取策略,這些策略向與自動化帳戶關聯的託管標識授予密鑰許可權。 託管標識僅在創建存儲帳戶後可用。

修改用於 Azure 自動化安全資產加密的密鑰時,透過啟用或禁用客戶管理的金鑰、更新金鑰版本或指定其他金鑰,帳戶加密金鑰的加密將更改,但 Azure 自動化帳戶中的安全資產無需重新加密。

以下三節介紹了為自動化帳戶啟用客戶管理密鑰的機制。 

> [!NOTE] 
> 要啟用客戶管理的金鑰,您需要使用 api 版本 2020-01-13 預覽進行 Azure 自動化 REST API 呼叫

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自動化使用客戶管理金鑰的先決條件

在為自動化帳戶啟用客戶管理的金鑰之前,必須確保滿足以下先決條件:

 - 客戶管理密鑰存儲在 Azure 金鑰保管庫中。 
 - 在金鑰保存式庫中啟用 **「軟刪除****」和「不清除」** 屬性。 這些功能是必需的,以便在意外刪除時恢復密鑰。
 - Azure 自動化加密僅支援 RSA 金鑰。 有關金鑰的詳細資訊,請參閱有關[Azure 金鑰保管庫金鑰、機密和憑證](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自動化帳戶和密鑰保管庫可以位於不同的訂閱中,但需要位於同一 Azure 活動目錄租戶中。

### <a name="assign-an-identity-to-the-automation-account"></a>將識別配置給自動化帳戶

要將客戶管理的密鑰與自動化帳戶一起使用,自動化帳戶需要針對存儲客戶管理的密鑰的密鑰保管庫進行身份驗證。 Azure 自動化使用系統分配的託管標識使用 Azure 密鑰保管庫對帳戶進行身份驗證。 如需受控識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

使用以下 REST API 呼叫將託管識別分配給自動化帳戶的系統:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

要求本文：

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

自動化帳戶的系統分配識別在回應中傳回,類似於以下內容:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configure-the-key-vault-access-policy"></a>設定金鑰保存庫存取原則

將託管標識分配給自動化帳戶後,將配置對存儲客戶託管密鑰的密鑰保管庫的訪問。 Azure 自動化要求在客戶管理的金鑰上 **,** 取得,**回復**,**包裝鍵**,**請解包金鑰**。

此存取原則可以使用以下 REST API 呼叫進行設定:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

要求本文：

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> **租戶 Id**與**objectId**欄位必須分別從自動化帳戶的託管識別回應中提供**識別.租戶Id**和**識別.theid 的值**。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>變更自動化帳戶的設定以使用客戶管理的金鑰

最後,您可以使用以下 REST API 呼叫將自動化帳戶從 Microsft 管理的金鑰切換到客戶管理的金鑰:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

要求本文：

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

範例回應

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="manage-customer-managed-keys-lifecycle"></a>管理客戶管理金鑰生命週期

### <a name="rotate-customer-managed-keys"></a>旋轉客戶管理的金鑰

您可以根據合規性策略在 Azure 密鑰保管庫中輪換客戶管理的密鑰。 旋轉金鑰時,必須更新自動化帳戶才能使用新的密鑰 URI。

旋轉金鑰不會觸發對自動化帳戶中的安全資產進行重新加密。 無需採取進一步行動。

### <a name="revoke-access-to-customer-managed-keys"></a>復原對客戶管理金鑰的存取

要復原對客戶管理金鑰的存取許可權,請使用 PowerShell 或 Azure CLI。 有關詳細資訊,請參閱[Azure 金鑰保管庫 PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/)或[Azure 金鑰保管庫 CLI](https://docs.microsoft.com/cli/azure/keyvault)。 由於 Azure 自動化無法訪問加密密鑰,因此撤銷訪問可有效地阻止對自動化帳戶中的所有安全資產的訪問。

## <a name="next-steps"></a>後續步驟

- [何謂 Azure Key Vault？](../key-vault/general/overview.md)

- [Azure 自動化中的憑證資產](shared-resources/certificates.md)

- [Azure 自動化中的認證資產](shared-resources/credentials.md)

- [Azure 自動化中的變數資產](shared-resources/variables.md)
