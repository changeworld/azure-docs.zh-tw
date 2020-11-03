---
title: 指派 Azure Key Vault 存取原則
description: 如何使用 Azure 入口網站、Azure CLI 或 Azure PowerShell，將 Key Vault 存取原則指派給服務主體或應用程式身分識別。
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: fb28b4b678b37f69331b2ecff6272fd7aa64d191
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2020
ms.locfileid: "93287618"
---
# <a name="assign-a-key-vault-access-policy-using-azure-powershell"></a>使用 Azure PowerShell 指派 Key Vault 存取原則

Key Vault 存取原則會決定指定的服務主體（也就是應用程式或使用者群組）是否可以對 Key Vault 的 [秘密](../secrets/index.yml)、 [金鑰](../keys/index.yml)和 [憑證](../certificates/index.yml)執行不同的作業。 您可以使用 [Azure 入口網站](assign-access-policy-portal.md)、 [Azure CLI](assign-access-policy-cli.md)或 Azure PowerShell (此文章) 來指派存取原則。

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

如需有關使用 Azure PowerShell 在 Azure Active Directory 中建立群組的詳細資訊，請參閱 [get-azureadgroup 和新增](/powershell/module/azuread/new-azureadgroup) [-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember)。

## <a name="configure-powershell-and-sign-in"></a>設定 PowerShell 和登入

1. 若要在本機執行命令，請安裝 [Azure PowerShell](/powershell/azure/) （如果尚未安裝）。

    若要直接在雲端中執行命令，請使用 [Azure Cloud Shell](../../cloud-shell/overview.md)。

1. 僅限本機 PowerShell：

    1. 安裝 [Azure Active Directory PowerShell 模組](https://www.powershellgallery.com/packages/AzureAD)。

    1. 登入 Azure：

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>取得物件識別碼

決定您要指派存取原則的應用程式、群組或使用者的物件識別碼：

- 應用程式和其他服務主體：使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal) 指令程式搭配 `-SearchString` 參數，將結果篩選為所需的服務主體名稱：

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- 群組：使用 [remove-azadgroup 指令程式](/powershell/module/az.resources/get-azadgroup?view=azps-2.7.0) 搭配 `-SearchString` 參數，將結果篩選為所需群組的名稱：

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    在輸出中，物件識別碼會列為 `Id` 。

- 使用者：使用 [>get-azaduser 指令程式](/powershell/module/az.resources/get-azaduser) ，將使用者的電子郵件地址傳遞給 `-UserPrincipalName` 參數。

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    在輸出中，物件識別碼會列為 `Id` 。

## <a name="assign-the-access-policy"></a>指派存取原則

使用 [>set-azkeyvaultaccesspolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) 指令 Cmdlet 來指派存取原則：

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

`-PermissionsToSecrets` `-PermissionsToKeys` `-PermissionsToCertificates` 當您將許可權指派給這些特定類型時，只需要包含、和。 、和的允許 `<secret-permissions>` 值 `<key-permissions>` `<certificate-permissions>` 會在 [>set-azkeyvaultaccesspolicy 參數](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters) 檔中提供。

## <a name="next-steps"></a>後續步驟

- [Azure Key Vault 安全性：身分識別和存取管理](overview-security.md#identity-and-access-management)
- [保護您的金鑰保存庫](secure-your-key-vault.md)。
- [Azure Key Vault 開發人員指南](developers-guide.md)
- [Azure Key Vault 最佳做法](best-practices.md)