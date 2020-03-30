---
title: 使用 Azure PowerShell 列出 Azure 資源的拒絕分配
description: 瞭解如何列出使用 Azure PowerShell 在特定作用域中被拒絕訪問特定 Azure 資源操作的使用者、組、服務主體和託管標識。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/12/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5ba18b89bd37dbd55350321c503e37ab0590ab87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137387"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>使用 Azure PowerShell 列出 Azure 資源的拒絕分配

[拒絕分配](deny-assignments.md)會阻止使用者執行特定的 Azure 資源操作，即使角色指派授予他們存取權限也是如此。 本文介紹如何使用 Azure PowerShell 列出拒絕分配。

> [!NOTE]
> 不能直接創建自己的拒絕分配。 有關如何創建拒絕分配的資訊，請參閱[拒絕分配](deny-assignments.md)。

## <a name="prerequisites"></a>Prerequisites

要獲取有關拒絕分配的資訊，您必須具有：

- `Microsoft.Authorization/denyAssignments/read`許可權，包含在[Azure 資源的大多數內置角色中](built-in-roles.md)
- [Azure 雲外殼](/azure/cloud-shell/overview)或[Azure PowerShell](/powershell/azure/install-az-ps)中的 PowerShell

## <a name="list-deny-assignments"></a>列出拒絕指派

### <a name="list-all-deny-assignments"></a>列出所有拒絕分配

要列出當前訂閱的所有拒絕分配，請使用[Get-AzDeny 分配](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment
```

```Example
PS C:\> Get-AzDenyAssignment

Id                      : 22222222-2222-2222-2222-222222222222
DenyAssignmentName      : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          ObjectType:   ServicePrincipal
                          }
IsSystemProtected       : True

Id                      : 33333333-3333-3333-3333-333333333333
DenyAssignmentName      : Deny assignment '33333333-3333-3333-3333-333333333333' created by Blueprint Assignment
                          '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Description             : Created by Blueprint Assignment '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Actions                 : {*}
NotActions              : {*/read}
DataActions             : {}
NotDataActions          : {}
Scope                   : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks/providers/Microsoft.Storage/storageAccounts/storep6vkuxmu4m4pq
DoNotApplyToChildScopes : True
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
ExcludePrincipals       : {
                          DisplayName:  assignment-locked-storageaccount-TestingBPLocks
                          ObjectType:   ServicePrincipal
                          ObjectId:     2311a0b7-657a-4ca2-af6f-d1c33f6d2fff
                          }
IsSystemProtected       : True
```

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>在資源組作用域中列出拒絕分配

要在資源組作用域中列出所有拒絕分配，請使用[Get-AzDeny 分配](/powershell/module/az.resources/get-azdenyassignment)。

```azurepowershell
Get-AzDenyAssignment -ResourceGroupName <resource_group_name>
```

```Example
PS C:\> Get-AzDenyAssignment -ResourceGroupName TestingBPLocks | FL DenyAssignmentName, Scope

DenyAssignmentName : Deny assignment '22222222-2222-2222-2222-222222222222' created by Blueprint Assignment
                     '/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Blueprint/blueprintAssignments/assignment-locked-storageaccount-TestingBPLocks'.
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/TestingBPLocks
Principals         : {
                     DisplayName:       All Principals
                     ObjectType:        SystemDefined
                     ObjectId:  00000000-0000-0000-0000-000000000000
                     }
```

### <a name="list-deny-assignments-at-a-subscription-scope"></a>在訂閱範圍內列出拒絕分配

要在訂閱範圍內列出所有拒絕分配，請使用[Get-AzDeny 分配](/powershell/module/az.resources/get-azdenyassignment)。 要獲取訂閱 ID，可以在 Azure 門戶中的 **"訂閱"** 邊欄選項卡上找到它，也可以使用[獲取訂閱](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>後續步驟

- [了解 Azure 資源的拒絕指派](deny-assignments.md)
- [使用 Azure 門戶列出 Azure 資源的拒絕分配](deny-assignments-portal.md)
- [使用 REST API 列出 Azure 資源的拒絕指派](deny-assignments-rest.md)