---
title: 使用 Azure PowerShell 列出 Azure 資源的拒絕指派
description: 瞭解如何使用 Azure PowerShell，來列出已拒絕存取特定範圍的特定 Azure 資源動作的使用者、群組、服務主體和受控識別。
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77137387"
---
# <a name="list-deny-assignments-for-azure-resources-using-azure-powershell"></a>使用 Azure PowerShell 列出 Azure 資源的拒絕指派

[拒絕指派](deny-assignments.md)會封鎖使用者執行特定的 Azure 資源動作，即使角色指派授與他們存取權也一樣。 本文說明如何使用 Azure PowerShell 列出拒絕指派。

> [!NOTE]
> 您無法直接建立自己的拒絕指派。 如需如何建立拒絕指派的相關資訊，請參閱[拒絕指派](deny-assignments.md)。

## <a name="prerequisites"></a>先決條件

若要取得拒絕指派的相關資訊，您必須具有：

- `Microsoft.Authorization/denyAssignments/read`許可權，包含在[Azure 資源的大部分內建角色](built-in-roles.md)中
- Azure Cloud Shell 或[Azure PowerShell](/powershell/azure/install-az-ps) [中的 PowerShell](/azure/cloud-shell/overview)

## <a name="list-deny-assignments"></a>列出拒絕指派

### <a name="list-all-deny-assignments"></a>列出所有拒絕指派

若要列出目前訂用帳戶的所有拒絕指派，請使用[AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

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

### <a name="list-deny-assignments-at-a-resource-group-scope"></a>列出資源群組範圍的拒絕指派

若要列出資源群組範圍中的所有拒絕指派，請使用[AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。

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

### <a name="list-deny-assignments-at-a-subscription-scope"></a>列出訂用帳戶範圍的拒絕指派

若要列出訂用帳戶範圍中的所有拒絕指派，請使用[AzDenyAssignment](/powershell/module/az.resources/get-azdenyassignment)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[get-azsubscription](/powershell/module/Az.Accounts/Get-AzSubscription)。

```azurepowershell
Get-AzDenyAssignment -Scope /subscriptions/<subscription_id>
```

```Example
PS C:\> Get-AzDenyAssignment -Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="next-steps"></a>後續步驟

- [了解 Azure 資源的拒絕指派](deny-assignments.md)
- [使用 Azure 入口網站列出 Azure 資源的拒絕指派](deny-assignments-portal.md)
- [使用 REST API 列出 Azure 資源的拒絕指派](deny-assignments-rest.md)