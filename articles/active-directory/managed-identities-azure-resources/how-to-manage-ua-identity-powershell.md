---
title: 使用 Azure PowerShell Azure AD 建立、列出 & 刪除使用者指派的受控識別
description: 如何使用 Azure PowerShell 建立、列出和刪除使用者指派的受控識別的逐步指示。
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9861b257ee487824db26288925a786a9a6aeee34
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/27/2020
ms.locfileid: "89014212"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-azure-powershell"></a>使用 Azure PowerShell 建立、列出或刪除使用者指派的受控識別

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

適用于 Azure 資源的受控識別會在 Azure Active Directory 中提供具有受控識別的 Azure 服務。 您可以使用此身分識別來向支援 Azure AD 驗證的服務進行驗證，而不需要您程式碼中的認證。 

在本文中，您將瞭解如何使用 Azure PowerShell 來建立、列出和刪除使用者指派的受控識別。

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

- 如果您不熟悉 Azure 資源的受控識別，請參閱[概觀一節](overview.md)。 **請務必檢閱[系統指派和使用者指派受控識別之間的差異](overview.md#managed-identity-types)**。
- 如果您還沒有 Azure 帳戶，請先[註冊免費帳戶](https://azure.microsoft.com/free/)，再繼續進行。
- 如果您尚未安裝[最新版的 Azure PowerShell](/powershell/azure/install-az-ps)，請先安裝。
- 如果您在本機執行 PowerShell，您也需要： 
    - 執行 `Connect-AzAccount` 來建立與 Azure 的連線。
    - 安裝[最新版的 PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget)。
    - 執行 `Install-Module -Name PowerShellGet -AllowPrerelease` 以取得 `PowerShellGet` 模組的搶鮮版 (執行此命令以安裝 `Az.ManagedServiceIdentity` 模組後，您可能需要以 `Exit` 退出目前的 PowerShell 工作階段)。
    - 執行 `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` 安裝 `Az.ManagedServiceIdentity` 模組的搶鮮版，以執行本文中由使用者指派的受控識別作業。

## <a name="create-a-user-assigned-managed-identity"></a>建立使用者指派的受控識別

若要建立使用者指派的受控識別，您的帳戶需要[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

若要建立使用者指派的受控識別，請使用 `New-AzUserAssignedIdentity` 命令。 `ResourceGroupName` 參數會指定要建立使用者指派之受控識別的資源群組，而 `-Name` 參數則指定其名稱。 將 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 參數取代為您自己的值：

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

 ```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>列出使用者指派的受控識別

若要列出/讀取使用者指派的受控識別，您的帳戶需要[受控識別操作者](/azure/role-based-access-control/built-in-roles#managed-identity-operator)或[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

若要列出使用者指派的受控識別，請使用 [Get-AzUserAssigned] 命令。  `-ResourceGroupName` 參數會指定建立使用者指派的受控識別所在的資源群組。 將 `<RESOURCE GROUP>` 取代為您自己的值：

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
在回應中，使用者指派的受控識別具備`"Microsoft.ManagedIdentity/userAssignedIdentities"`為索引鍵傳回的值`Type`。

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>刪除使用者指派的受控識別

若要刪除使用者指派的受控識別，您的帳戶需要[受控識別參與者](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)角色指派。

若要刪除使用者指派的受控識別，請使用 `Remove-AzUserAssignedIdentity` 命令。  `-ResourceGroupName` 參數會指定建立使用者指派之身分識別所在的資源群組，而 `-Name` 參數會指定其名稱。 將 `<RESOURCE GROUP>` 和 `<USER ASSIGNED IDENTITY NAME>` 參數取代為您自己的值：

 ```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> 將使用者指派的受控識別從受指派的任何資源中刪除，並不會移除參考。 必須個別移除身分識別指派。

## <a name="next-steps"></a>後續步驟

如需 Azure PowerShell Azure 資源受控識別命令的完整清單和更多詳細資料，請參閱 [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity)。
