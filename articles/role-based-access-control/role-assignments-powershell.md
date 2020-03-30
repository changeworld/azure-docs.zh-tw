---
title: 使用 RBAC 和 Azure PowerShell 添加或刪除角色指派
description: 瞭解如何使用基於 Azure 角色的存取控制 （RBAC） 和 Azure PowerShell 為使用者、組、服務主體或託管標識授予對 Azure 資源的訪問。
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
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 68a73f622dc69b70870ddc1db16edcf406b63800
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283208"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-powershell"></a>使用 Azure RBAC 和 Azure PowerShell 添加或刪除角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]本文介紹如何使用 Azure PowerShell 分配角色。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

要添加或刪除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)
- [Azure 雲外殼](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)中的 PowerShell

## <a name="get-object-ids"></a>獲取物件指示

要添加或刪除角色指派，可能需要指定物件的唯一 ID。 ID 的格式為： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 門戶或 Azure PowerShell 獲取 ID。

### <a name="user"></a>User

要獲取 Azure AD 使用者的物件識別碼，可以使用[Get-AzADUser](/powershell/module/az.resources/get-azaduser)。

```azurepowershell
Get-AzADUser -StartsWith <string_in_quotes>
(Get-AzADUser -DisplayName <name_in_quotes>).id
```

### <a name="group"></a>群組

要獲取 Azure AD 組的物件識別碼，可以使用[Get-AzADGroup](/powershell/module/az.resources/get-azadgroup)。

```azurepowershell
Get-AzADGroup -SearchString <group_name_in_quotes>
(Get-AzADGroup -DisplayName <group_name_in_quotes>).id
```

### <a name="application"></a>Application

要獲取 Azure AD 服務主體的物件識別碼（應用程式使用的身份），可以使用[Get-AzADService 委託書](/powershell/module/az.resources/get-azadserviceprincipal)。 對於服務主體，請使用物件識別碼**而不是應用程式**ID。

```azurepowershell
Get-AzADServicePrincipal -SearchString <service_name_in_quotes>
(Get-AzADServicePrincipal -DisplayName <service_name_in_quotes>).id
```

## <a name="add-a-role-assignment"></a>新增角色指派

在 RBAC 中，要授予存取權限，您需要添加角色指派。

### <a name="user-at-a-resource-group-scope"></a>資源組作用域的使用者

要為資源組作用域中的使用者添加角色指派，請使用[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales


RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="using-the-unique-role-id"></a>使用唯一角色 ID

有時角色名稱可能會更改，例如：

- 您正在使用自己的自訂角色，並決定更改名稱。
- 您使用的是名稱中具有 **（預覽）** 的預覽角色。 角色釋放時，角色將重命名。

> [!IMPORTANT]
> 預覽版在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使重命名角色，角色 ID 也不會更改。 如果使用腳本或自動化來創建角色指派，則最佳做法是使用唯一角色 ID 而不是角色名稱。 因此，如果角色重命名，腳本更有可能工作。

要使用唯一角色 ID 而不是角色名稱添加角色指派，請使用[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionId <role_id> -ResourceGroupName <resource_group_name>
```

下面的示例將[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色指派給*製藥銷售*資源組範圍內的*\@aain example.com*使用者。 要獲取唯一角色 ID，可以使用[Get-AzRole定義](/powershell/module/az.resources/get-azroledefinition)或查看[Azure 資源的內置角色](built-in-roles.md)。

```Example
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 -RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c -Scope /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

### <a name="group-at-a-resource-scope"></a>資源作用域的組

要在資源作用域中為組添加角色指派，請使用[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 有關如何獲取組的物件識別碼 的資訊，請參閱[獲取物件識別碼](#get-object-ids)。

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -ResourceName <resource_name> -ResourceType <resource_type> -ParentResource <parent resource> -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzADGroup -SearchString "Pharma"

SecurityEnabled DisplayName         Id                                   Type
--------------- -----------         --                                   ----
           True Pharma Sales Admins aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa Group

PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa -RoleDefinitionName "Virtual Machine Contributor" -ResourceName RobertVirtualNetwork -ResourceType Microsoft.Network/virtualNetworks -ResourceGroupName RobertVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/RobertVirtualNetwork
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

### <a name="application-at-a-subscription-scope"></a>訂閱範圍內的應用程式

要在訂閱範圍內為應用程式添加角色指派，請使用[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 有關如何獲取應用程式的物件識別碼 的資訊，請參閱[獲取物件識別碼。](#get-object-ids)

```azurepowershell
New-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 -RoleDefinitionName "Reader" -Scope /subscriptions/00000000-0000-0000-0000-000000000000

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

### <a name="user-at-a-management-group-scope"></a>管理組作用域的使用者

要為管理組作用域中的使用者添加角色指派，請使用[New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)。 要獲取管理組 ID，可以在 Azure 門戶中的 **"管理組**"邊欄選項卡上找到它，也可以使用[Get-AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
New-AzRoleAssignment -SignInName <email_or_userprincipalname> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

```Example
PS C:\> New-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Billing Reader" -Scope /providers/Microsoft.Management/managementGroups/marketing-group

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 RBAC 中，若要移除存取權，您需使用 [Remove-AzRoleAssignment](/powershell/module/az.resources/remove-azroleassignment) 來移除角色指派。

以下示例從*製藥銷售*資源組中的*alain\@example.com*使用者中刪除*虛擬機器參與者*角色指派：

```Example
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com -RoleDefinitionName "Virtual Machine Contributor" -ResourceGroupName pharma-sales
```

以下示例從訂閱作用域的<object_id>中刪除<role_name>角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /subscriptions/<subscription_id>
```

下面的示例從管理組作用域的<object_id>中刪除<role_name>角色。

```azurepowershell
Remove-AzRoleAssignment -ObjectId <object_id> -RoleDefinitionName <role_name> -Scope /providers/Microsoft.Management/managementGroups/<group_id>
```

如果收到錯誤訊息："提供的資訊不映射到角色指派"，請確保也指定 或`-Scope``-ResourceGroupName`參數。 有關詳細資訊，請參閱對[Azure 資源的 RBAC 進行故障排除](troubleshooting.md#role-assignments-with-unknown-security-principal)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure PowerShell 列出角色指派](role-assignments-list-powershell.md)
- [教程：使用 RBAC 和 Azure PowerShell 授予對 Azure 資源的組存取權限](tutorial-role-assignments-group-powershell.md)
- [教程：使用 Azure PowerShell 為 Azure 資源創建自訂角色](tutorial-custom-role-powershell.md)
- [使用 Azure PowerShell 管理資源](../azure-resource-manager/management/manage-resources-powershell.md)
