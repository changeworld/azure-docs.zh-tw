---
title: 將特定實驗室原則的權限授與使用者 | Microsoft Docs
description: 了解如何根據每個使用者的需求將使用者權限授與研發/測試實驗室中的特定實驗室原則
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 1a0f46ed64bf360ceb990f1e91cb65ace76b5a9a
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534544"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>將特定實驗室原則的權限授與使用者
## <a name="overview"></a>概觀
本文說明如何使用 PowerShell 將特定實驗室原則的權限授與使用者。 這樣便可根據每個使用者的需求來套用權限。 例如，您可能想要將變更 VM 原則設定 (而非成本原則) 的能力授與特定的使用者。

## <a name="policies-as-resources"></a>原則即資源
如 [Azure 角色型存取控制](../role-based-access-control/role-assignments-portal.md) 中所討論，RBAC 可讓您對 Azure 的資源進行更細緻的存取管理。 您可以使用 RBAC 來區隔開發小組的職責，僅授與使用者作業所需的存取權。

在研發/測試實驗室中，原則是一種可啟用 RBAC 動作 **Microsoft.DevTestLab/labs/policySets/policies/** 的資源類型。 每個實驗室原則都是原則資源類型中的資源，可指派為 Azure 角色的範圍。

例如，為了將**允許的 VM 大小**原則的讀取/寫入權限授與使用者，您可以建立與**microsoft.devtestlab/labs/policySets/原則/** 動作搭配運作的自訂角色，然後將適當的使用者指派給**microsoft.devtestlab/labs/policySets/policy/AllowedVmSizesInLab**範圍內的這個自訂角色。

若要深入了解 RBAC 中的自訂角色，請參閱[自訂角色存取控制](../role-based-access-control/custom-roles.md)。

## <a name="creating-a-lab-custom-role-using-powershell"></a>使用 PowerShell 建立實驗室自訂角色
為了開始進行，您必須[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 

設定完 Azure PowerShell Cmdlet 之後，您便可執行下列工作：

* 列出資源提供者的所有作業/動作
* 列出特定角色中的動作：
* 建立自訂角色

下列 PowerShell 指令碼提供範例來說明如何執行這些工作：

```azurepowershell
# List all the operations/actions for a resource provider.
Get-AzProviderOperation -OperationSearchString "Microsoft.DevTestLab/*"

# List actions in a particular role.
(Get-AzRoleDefinition "DevTest Labs User").Actions

# Create custom role.
$policyRoleDef = (Get-AzRoleDefinition "DevTest Labs User")
$policyRoleDef.Id = $null
$policyRoleDef.Name = "Policy Contributor"
$policyRoleDef.IsCustom = $true
$policyRoleDef.AssignableScopes.Clear()
$policyRoleDef.AssignableScopes.Add("/subscriptions/<SubscriptionID> ")
$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/policySets/policies/*")
$policyRoleDef = (New-AzRoleDefinition -Role $policyRoleDef)
```

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>使用自訂角色將特定原則的權限指派給使用者
定義自訂角色之後，您便可以將它們指派給使用者。 為了將自訂角色指派給使用者，您必須先取得代表該使用者的 **ObjectId** 。 若要這麼做，請使用**AzADUser** Cmdlet。

在下列範例中， **SomeUser** 使用者的 *ObjectId* 是 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3。

```azurepowershell
PS C:\>Get-AzADUser -SearchString "SomeUser"

DisplayName                    Type                           ObjectId
-----------                    ----                           --------
someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3
```

一旦擁有使用者的**ObjectId**和自訂角色名稱之後，您就可以使用**new-azroleassignment**指令程式，將該角色指派給使用者：

```azurepowershell
PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab
```

在先前的範例中，使用的是 **AllowedVmSizesInLab** 原則。 您也可以使用下列任何原則：

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
將特定實驗室原則的權限授與使用者之後，以下是一些需要考量的後續步驟：

* [安全存取實驗室](devtest-lab-add-devtest-user.md)
* [設定實驗室原則](devtest-lab-set-lab-policy.md)
* [建立實驗室範本](devtest-lab-create-template.md)
* [為您的 Vm 建立自訂構件](devtest-lab-artifact-author.md)
* [將 VM 新增至實驗室](devtest-lab-add-vm.md)

