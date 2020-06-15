---
title: Azure 自動化中的安全資產加密
description: 本文提供將自動化帳戶設定為使用加密的概念。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 1cb70109657343f41a1b3a19f3426377d97e261e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830118"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Azure 自動化中的安全資產加密

Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會在 Azure 自動化中使用多層級加密進行保護。 根據用於加密的最上層金鑰，加密有兩種模型：

- 使用 Microsoft 管理的金鑰
- 使用您所管理的金鑰

## <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

根據預設，您的 Azure 自動化帳戶會使用 Microsoft 管理的金鑰。

每個安全資產都會經過加密，並使用為每個自動化帳戶產生的唯一金鑰 (資料加密金鑰) 儲存在 Azure 自動化中。 這些金鑰本身會加密並儲存在 Azure 自動化中，但用於加密的是針對每個帳戶產生的另一個唯一金鑰 (稱為帳戶加密金鑰 (AEK))。 這些帳戶加密金鑰會使用 Microsoft 管理的金鑰進行加密並儲存在 Azure 自動化中。 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>您使用 Key Vault (預覽) 管理的金鑰

您可使用自己的金鑰來管理自動化帳戶的安全資產加密。 當您在自動化帳戶層級指定客戶管理的金鑰時，該金鑰用於保護及控制自動化帳戶的帳戶加密金鑰存取權。 這接著用於加密和解密所有安全資產。 客戶管理的金鑰可提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可稽核用來保護安全資產的加密金鑰。

使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰並將其儲存在金鑰保存庫中，或是使用 Azure Key Vault API 來產生金鑰。  如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>針對自動化帳戶使用客戶管理的金鑰

當您針對自動化帳戶使用加密搭配客戶管理的金鑰時，Azure 自動化會使用相關聯的金鑰保存庫中客戶管理的金鑰，包裝帳戶加密金鑰。 啟用客戶管理的金鑰並不會影響效能，而帳戶會立即使用新金鑰進行加密，毫無延遲。

新的自動化帳戶一律使用 Microsoft 管理的金鑰進行加密。 建立帳戶時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須佈建存取原則，以將金鑰權限授與給與自動化帳戶相關聯的受控識別。 只有在建立儲存體帳戶之後，才可使用受控識別。

當您啟用或停用客戶管理的金鑰、更新金鑰版本，或指定不同的金鑰，藉此修改用於 Azure 自動化安全資產加密的金鑰時，帳戶加密金鑰的加密會變更，但是 Azure 自動化帳戶中的安全資產不需要重新加密。

> [!NOTE] 
> 若要啟用客戶管理的金鑰，您必須使用 API 版本 2020-01-13-preview 進行 Azure 自動化 REST API 呼叫

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自動化中使用客戶管理的金鑰的必要條件

為自動化帳戶啟用客戶管理的金鑰之前，您必須確保符合下列必要條件：

 - 客戶管理的金鑰會儲存在 Azure Key Vault 中。 
 - 在金鑰保存庫上同時啟用 [虛刪除] 和 [不會清除] 屬性。 需有這些功能，才能在意外刪除時允許復原金鑰。
 - 只有 Azure 自動化加密支援 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Key Vault 金鑰、秘密和憑證](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自動化帳戶和金鑰保存庫可位於不同的訂用帳戶中，但必須位於相同的 Azure Active Directory 租用戶中。

### <a name="assignment-of-an-identity-to-the-automation-account"></a>將身分識別指派給自動化帳戶

若要使用客戶管理的金鑰搭配自動化帳戶，您的自動化帳戶必須向儲存客戶管理金鑰的金鑰保存庫進行驗證。 Azure 自動化會使用系統指派的受控識別，向 Azure Key Vault 驗證帳戶。 如需受控識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

使用下列 REST API 呼叫，將系統指派的受控識別設定為自動化帳戶：

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

自動化帳戶的系統指派身分識別會在類似下列的回應中傳回：

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

### <a name="configuration-of-the-key-vault-access-policy"></a>設定 Key Vault 存取原則

一旦將受控識別指派給自動化帳戶，您就可以針對用於儲存客戶管理金鑰的金鑰保存庫設定其存取權。 Azure 自動化需要對客戶管理的金鑰進行 **get**、**recover**、**wrapKey**、**UnwrapKey**。

您可使用下列 REST API 呼叫來設定這類存取原則：

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
> 您必須在 **tenantId** 和 **objectId** 欄位中分別提供自動化帳戶的受控識別回應中的 **identity.tenantId** 和 **identity.principalId** 值。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>將自動化帳戶的設定變更為使用客戶管理的金鑰

最後，您可使用下列 REST API 呼叫，將您的自動化帳戶從 Microsoft 管理的金鑰切換成客戶管理的金鑰：

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

## <a name="rotation-of-a-customer-managed-key"></a>輪替客戶管理的金鑰

您可以根據您的合規性原則，在 Azure Key Vault 中輪替客戶管理的金鑰。 輪替金鑰後，您必須將自動化帳戶更新為使用新的金鑰 URI。

輪替金鑰並不會在自動化帳戶中觸發安全資產的重新加密。 不需要採取任何進一步動作。

## <a name="revocation-of-access-to-a-customer-managed-key"></a>撤銷客戶管理金鑰的存取權

若要撤銷客戶管理金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) 或 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對自動化帳戶中所有安全資產的存取，因為 Azure 自動化無法存取加密金鑰。

## <a name="next-steps"></a>後續步驟

- 若要了解 Azure Key Vault，請參閱[什麼是 Azure Key Vault？](../key-vault/general/overview.md)
- 若要使用憑證，請參閱[在 Azure 自動化中管理憑證](shared-resources/certificates.md)。
- 若要處理認證，請參閱[在 Azure 自動化中管理認證](shared-resources/credentials.md)。
- 若要使用自動化變數，請參閱[在 Azure 自動化中管理變數](shared-resources/variables.md)。
- 如需處理連線的說明，請參閱[在 Azure 自動化中管理連線](automation-connections.md)。
