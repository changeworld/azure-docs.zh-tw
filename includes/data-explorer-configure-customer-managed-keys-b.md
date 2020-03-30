---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 03/25/2020
ms.author: orspodek
ms.openlocfilehash: 081ba777f6ab19be774f127383e359ff761e7f0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297897"
---
## <a name="create-a-new-key-vault"></a>創建新金鑰保存庫

要使用 PowerShell 創建新金鑰保存庫，請致電[New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault)。 用於存儲 Azure 資料資源管理器加密的客戶管理金鑰的金鑰保存庫必須啟用兩個金鑰保護設置，**即"虛刪除****"和"不清除**"。 在下面的示例中，將括弧中的預留位置值替換為您自己的值。

```azurepowershell-interactive
$keyVault = New-AzKeyVault -Name <key-vault> `
    -ResourceGroupName <resource_group> `
    -Location <location> `
    -EnableSoftDelete `
    -EnablePurgeProtection
```

## <a name="configure-the-key-vault-access-policy"></a>配置金鑰保存庫訪問策略

接下來，為金鑰保存庫配置訪問策略，以便群集具有訪問它的許可權。 在此步驟中，您將使用以前分配給群集的系統分配的託管標識。 要設置金鑰保存庫的訪問策略，請調用[Set-AzKeyVault 訪問策略](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy)。 將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy `
    -VaultName $keyVault.VaultName `
    -ObjectId $cluster.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get,recover
```

## <a name="create-a-new-key"></a>建立新的金鑰

接下來，在金鑰保存庫中創建一個新金鑰。 要創建新金鑰，請調用[Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey)。 將括弧中的預留位置值替換為您自己的值，並使用前面示例中定義的變數。

```azurepowershell-interactive
$key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName -Name <key> -Destination 'Software'
```
