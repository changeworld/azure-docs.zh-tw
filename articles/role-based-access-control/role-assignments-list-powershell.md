---
title: 使用 Azure PowerShell 列出 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure PowerShell 和 Azure 角色型存取控制 (Azure RBAC) ，判斷使用者、群組、服務主體或受控識別有哪些資源可以存取哪些資源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/28/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: e27fe0589498de13f5eb6e17f8869bb9d7352a09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87372071"
---
# <a name="list-azure-role-assignments-using-azure-powershell"></a>使用 Azure PowerShell 列出 Azure 角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] 本文說明如何使用 Azure PowerShell 列出角色指派。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> 如果您的組織具有外包管理功能給使用 [Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，此服務提供者所授權的角色指派將不會顯示在此處。

## <a name="prerequisites"></a>必要條件

- Azure Cloud Shell 或[Azure PowerShell](/powershell/azure/install-az-ps) [的 PowerShell](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-the-current-subscription"></a>列出目前訂用帳戶的角色指派

取得目前訂用帳戶中所有角色指派清單的最簡單方式 (包括從根和管理群組繼承的角色指派) 是在不使用任何參數的情況下使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment) 。

```azurepowershell
Get-AzRoleAssignment
```

```Example
PS C:\> Get-AzRoleAssignment

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Alain
SignInName         : alain@example.com
RoleDefinitionName : Storage Blob Data Reader
RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/33333333-3333-3333-3333-333333333333
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Marketing
SignInName         :
RoleDefinitionName : Contributor
RoleDefinitionId   : b24988ac-6180-42a0-ab88-20f7382dd24c
ObjectId           : 22222222-2222-2222-2222-222222222222
ObjectType         : Group
CanDelegate        : False

...
```

## <a name="list-role-assignments-for-a-subscription"></a>列出訂用帳戶的角色指派

若要列出訂用帳戶範圍的所有角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。 若要取得訂用帳戶識別碼，您 **可以在 Azure 入口網站的 [訂** 用帳戶] 分頁上找到它，也可以使用 [>select-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzRoleAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /subscriptions/00000000-0000-0000-0000-000000000000
```

## <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出指派給指定使用者的所有角色，使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname>
```

```Example
PS C:\> Get-AzRoleAssignment -SignInName isabella@example.com | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

若要列出指派給指定使用者的所有角色，以及指派給該使用者所屬群組的角色，使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -SignInName <email_or_userprincipalname> -ExpandPrincipalGroups
```

```Example
Get-AzRoleAssignment -SignInName isabella@example.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

若要列出資源群組範圍的所有角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzRoleAssignment -ResourceGroupName pharma-sales | FL DisplayName, RoleDefinitionName, Scope

DisplayName        : Alain Charon
RoleDefinitionName : Backup Operator
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Isabella Simonsen
RoleDefinitionName : BizTalk Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

DisplayName        : Alain Charon
RoleDefinitionName : Virtual Machine Contributor
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理群組的角色指派

若要列出管理群組範圍的所有角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。 若要取得管理群組識別碼，您可以在 Azure 入口網站的 [ **管理群組** ] 分頁上找到它，也可以使用 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-a-resource"></a>列出資源的角色指派

若要列出特定資源的角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment) 和 `-Scope` 參數。 範圍會依資源而有所不同。 若要取得範圍，您可以在 `Get-AzRoleAssignment` 沒有任何參數的情況下執行，以列出所有角色指派，然後尋找您要列出的範圍。

```azurepowershell
Get-AzRoleAssignment -Scope "/subscriptions/<subscription_id>/resourcegroups/<resource_group_name>/providers/<provider_name>/<resource_type>/<resource>
```

下列範例說明如何列出儲存體帳戶的角色指派。 請注意，此命令也會列出適用于此儲存體帳戶的較高範圍（例如資源群組和訂用帳戶）的角色指派。

```Example
PS C:\> Get-AzRoleAssignment -Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"
```

如果您只想列出直接指派給資源的角色指派，您可以使用 [Where 物件](/powershell/module/microsoft.powershell.core/where-object) 命令來篩選清單。

```Example
PS C:\> Get-AzRoleAssignment | Where-Object {$_.Scope -eq "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/storage-test-rg/providers/Microsoft.Storage/storageAccounts/storagetest0122"}
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>列出傳統服務管理員和共同管理員的角色指派

若要列出傳統訂用帳戶管理員和共同管理員的角色指派，請使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出受控識別的角色指派

1. 取得系統指派或使用者指派的受控識別的物件識別碼。 

    若要取得使用者指派的受控識別的物件識別碼，您可以使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal)。

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. 若要列出角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/get-azroleassignment)。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure PowerShell 新增或移除 Azure 角色指派](role-assignments-powershell.md)
