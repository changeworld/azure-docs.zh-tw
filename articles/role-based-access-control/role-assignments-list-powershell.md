---
title: 使用 Azure RBAC 和 Azure PowerShell 列出角色指派
description: 瞭解如何確定使用者、組、服務主體或託管標識可以使用基於 Azure 角色的存取控制 （RBAC） 和 Azure PowerShell 訪問哪些資源。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 0ec3153e5b1bfbe04a079d1cfc44e8e8709784d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931142"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-powershell"></a>使用 Azure RBAC 和 Azure PowerShell 列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文介紹如何使用 Azure PowerShell 列出角色指派。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

> [!NOTE]
> 如果您的組織將管理功能外包給使用[Azure 委派資源的](../lighthouse/concepts/azure-delegated-resource-management.md)服務提供者，則該服務提供者授權的角色指派將不會在此處顯示。

## <a name="prerequisites"></a>Prerequisites

- [Azure 雲外殼](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)中的 PowerShell

## <a name="list-role-assignments-for-the-current-subscription"></a>列出當前訂閱的角色指派

獲取當前訂閱中所有角色指派的清單（包括來自根組和管理組的繼承角色指派）的最簡單方法是使用沒有任何參數的[Get-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)。

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

要在訂閱範圍內列出所有角色指派，請使用[獲取-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)。 要獲取訂閱 ID，可以在 Azure 門戶中的 **"訂閱"** 邊欄選項卡上找到它，也可以使用[獲取訂閱](/powershell/module/Az.Accounts/Get-AzSubscription)。

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

要列出資源組作用域中的所有角色指派，請使用[獲取-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)。

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

要列出管理組作用域中的所有角色指派，請使用[獲取-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)。 要獲取管理組 ID，可以在 Azure 門戶中的 **"管理組**"邊欄選項卡上找到它，也可以使用[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> Get-AzRoleAssignment -Scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="list-role-assignments-for-classic-service-administrator-and-co-administrators"></a>列出傳統服務管理員和共同管理員的角色指派

若要列出傳統訂用帳戶管理員和共同管理員的角色指派，請使用 [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment)。

```azurepowershell
Get-AzRoleAssignment -IncludeClassicAdministrators
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出託管標識的角色指派

1. 獲取系統分配或使用者分配的託管標識的物件識別碼。 

    要獲取使用者分配的託管標識的物件識別碼，可以使用[Get-AzADService 委託 。](/powershell/module/az.resources/get-azadserviceprincipal)

    ```azurepowershell
    Get-AzADServicePrincipal -DisplayNameBeginsWith "<name> or <vmname>"
    ```

1. 要列出角色指派，請使用[獲取-AzRole 分配](/powershell/module/az.resources/get-azroleassignment)。

    ```azurepowershell
    Get-AzRoleAssignment -ObjectId <objectid>
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure PowerShell 添加或刪除角色指派](role-assignments-powershell.md)
