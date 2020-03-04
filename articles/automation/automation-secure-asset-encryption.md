---
title: 自動化中的安全資產加密
description: Azure 自動化會使用多個加密層級來保護安全資產。 根據預設，會使用 Microsoft 管理的金鑰來完成加密。 客戶可以將其自動化帳戶設定為使用客戶管理的金鑰進行加密。 本文將說明這兩種加密模式的詳細資料，以及如何在兩者之間進行切換。
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 3c21e2fcdde9bffac91af56d49dfa0bf336e8c0c
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246239"
---
# <a name="secure-assets-in-azure-automation"></a>保護 Azure 自動化中的資產

Azure 自動化中的安全資產包括認證、憑證、連接和加密的變數。 這些資產會在使用多個加密層級的 Azure 自動化中受到保護。 根據用於加密的最上層金鑰，加密有兩種模型：
-   使用 Microsoft 管理的金鑰
-   使用客戶管理的金鑰

## <a name="microsoft-managed-keys"></a>Microsoft 管理的金鑰

根據預設，您的 Azure 自動化帳戶會使用 Microsoft 管理的金鑰。

每個安全資產都會使用為每個自動化帳戶產生的唯一金鑰（資料加密金鑰）來加密並儲存在 Azure 自動化中。 這些金鑰本身會加密並儲存在 Azure 自動化中，但會使用針對每個稱為帳戶加密金鑰（AEK）的帳戶所產生的另一個唯一金鑰。 這些帳戶加密金鑰會使用 Microsoft 管理的金鑰，加密並儲存在 Azure 自動化中。 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Key Vault 的客戶管理金鑰（預覽）

您可以使用自己的金鑰來管理自動化帳戶的安全資產加密。 當您在自動化帳戶層級指定客戶管理的金鑰時，會使用該金鑰來保護及控制自動化帳戶的帳戶加密金鑰存取權。 這又用來加密和解密所有安全資產。 客戶管理的金鑰提供更大的彈性來建立、輪替、停用及撤銷存取控制。 您也可以審核用來保護安全資產的加密金鑰。

使用 Azure Key Vault 來儲存客戶管理的金鑰。 您可以建立自己的金鑰，並將其儲存在金鑰保存庫中，或者您可以使用 Azure Key Vault Api 來產生金鑰。  如需 Azure 金鑰保存庫的詳細資訊，請參閱 [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>為自動化帳戶啟用客戶管理的金鑰

當您針對自動化帳戶使用客戶管理的金鑰來啟用加密時，Azure 自動化會在相關聯的金鑰保存庫中，將帳戶加密金鑰與客戶管理的金鑰包裝在一起。 啟用客戶管理的金鑰並不會影響效能，而且帳戶會立即以新的金鑰加密，而不會有任何延遲。

新的自動化帳戶一律使用 Microsoft 管理的金鑰進行加密。 建立帳戶時，不可能啟用客戶管理的金鑰。 客戶管理的金鑰會儲存在 Azure Key Vault 中，而金鑰保存庫必須布建存取原則，以將金鑰許可權授與與自動化帳戶相關聯的受控識別。 只有在建立儲存體帳戶之後，才可以使用受控識別。

當您修改用於 Azure 自動化安全資產加密的金鑰時，藉由啟用或停用客戶管理的金鑰、更新金鑰版本或指定不同的金鑰，帳戶加密金鑰的加密會變更，但中的安全資產您的 Azure 自動化帳戶不需要重新加密。

下列三節說明為自動化帳戶啟用客戶管理的金鑰的機制。 

> [!NOTE] 
> 若要啟用客戶管理的金鑰，您必須使用 API 版本 2020-01-13-preview 進行 Azure 自動化 REST API 呼叫

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>在 Azure 自動化中使用客戶管理金鑰的必要條件

為自動化帳戶啟用客戶管理的金鑰之前，您必須確定符合下列先決條件：

 - 客戶受控金鑰會儲存在 Azure Key Vault 中。 
 - 同時啟用「虛**刪除**」和「不要**清除**」金鑰保存庫上的屬性。 需要這些功能，才能在意外刪除時允許金鑰的復原。
 - Azure 自動化加密僅支援 RSA 金鑰。 如需金鑰的詳細資訊，請參閱[關於 Azure Key Vault 金鑰、秘密和憑證](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)。
- 自動化帳戶和金鑰保存庫可以位於不同的訂用帳戶中，但必須位於相同的 Azure Active Directory 租使用者中。

### <a name="assign-an-identity-to-the-automation-account"></a>將身分識別指派給自動化帳戶

若要使用客戶管理的金鑰搭配自動化帳戶，您的自動化帳戶必須針對儲存客戶管理金鑰的金鑰保存庫進行驗證。 Azure 自動化會使用系統指派的受控識別，透過 Azure Key Vault 來驗證帳戶。 如需受控識別的詳細資訊，請參閱[什麼是適用於 Azure 資源的受控識別？](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

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

### <a name="configure-the-key-vault-access-policy"></a>設定 Key Vault 存取原則

一旦將受控識別指派給自動化帳戶，您就可以設定金鑰保存庫的存取權，以儲存客戶管理的金鑰。 Azure 自動化需要在客戶管理的金鑰上進行**get**、 **recover**、 **wrapKey**、 **UnwrapKey** 。

您可以使用下列 REST API 呼叫來設定這類存取原則：

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
> **Tenantid**和**objectId**欄位必須分別提供身分**識別**的值和**principalId** ，以及來自自動化帳戶的受控識別回應。

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>將自動化帳戶的設定變更為使用客戶管理的金鑰

最後，您可以使用下列 REST API 呼叫，將您的自動化帳戶從 Microsft 管理的金鑰切換到客戶管理的金鑰：

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

## <a name="manage-customer-managed-keys-lifecycle"></a>管理客戶管理的金鑰生命週期

### <a name="rotate-customer-managed-keys"></a>輪替客戶管理的金鑰

您可以根據您的相容性原則，在 Azure Key Vault 中旋轉客戶管理的金鑰。 輪替金鑰時，您必須更新自動化帳戶以使用新的金鑰 URI。

輪替金鑰並不會在自動化帳戶中觸發安全資產的重新加密。 不需要採取進一步的動作。

### <a name="revoke-access-to-customer-managed-keys"></a>撤銷對客戶管理的金鑰的存取權

若要撤銷客戶管理金鑰的存取權，請使用 PowerShell 或 Azure CLI。 如需詳細資訊，請參閱[Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/)或[Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)。 撤銷存取權可有效封鎖對自動化帳戶中所有安全資產的存取，因為 Azure 自動化無法存取加密金鑰。

## <a name="next-steps"></a>後續步驟

- [什麼是 Azure 金鑰保存庫？](../key-vault/key-vault-overview.md)

- [Azure 自動化中的憑證資產](shared-resources/certificates.md)

- [Azure 自動化中的認證資產](shared-resources/credentials.md)

- [Azure 自動化中的變數資產](shared-resources/variables.md)
