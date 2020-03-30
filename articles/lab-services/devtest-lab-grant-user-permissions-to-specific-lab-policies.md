---
title: 將特定實驗室原則的權限授與使用者 | Microsoft Docs
description: 了解如何根據每個使用者的需求將使用者權限授與研發/測試實驗室中的特定實驗室原則
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 5ca829f0-eb69-40a1-ae26-03a629db1d7e
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: spelluru
ms.openlocfilehash: 9b31f3e68fbabc32f301fdcd8066a3bfbf1c2dbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284209"
---
# <a name="grant-user-permissions-to-specific-lab-policies"></a>將特定實驗室原則的權限授與使用者
## <a name="overview"></a>總覽
本文說明如何使用 PowerShell 將特定實驗室原則的權限授與使用者。 這樣便可根據每個使用者的需求來套用權限。 例如，您可能想要將變更 VM 原則設定 (而非成本原則) 的能力授與特定的使用者。

## <a name="policies-as-resources"></a>原則即資源
如 [Azure 角色型存取控制](../role-based-access-control/role-assignments-portal.md) 中所討論，RBAC 可讓您對 Azure 的資源進行更細緻的存取管理。 您可以使用 RBAC 來區隔開發小組的職責，僅授與使用者作業所需的存取權。

在研發/測試實驗室中，原則是一種可啟用 RBAC 動作 **Microsoft.DevTestLab/labs/policySets/policies/** 的資源類型。 每個實驗室原則都是「原則」資源類型中的資源，並且可被指派成某個 RBAC 角色的範圍。

例如，為了向使用者授予**允許的 VM 大小策略**的讀取/寫入權限，您可以創建一個適用于 Microsoft 的自訂角色 **。DevTestLab/labs/策略集/策略/** 操作，然後在**Microsoft.DevTestLab/labs/策略/策略/允許VmSizesInLab**的範圍內將適當的使用者分配給此自訂角色。

若要深入了解 RBAC 中的自訂角色，請參閱[自訂角色存取控制](../role-based-access-control/custom-roles.md)。

## <a name="creating-a-lab-custom-role-using-powershell"></a>使用 PowerShell 建立實驗室自訂角色
要開始，您需要[安裝 Azure PowerShell](/powershell/azure/install-az-ps)。 

設定完 Azure PowerShell Cmdlet 之後，您便可執行下列工作：

* 列出資源提供者的所有作業/動作
* 列出特定角色中的動作：
* 建立自訂角色

下列 PowerShell 指令碼提供範例來說明如何執行這些工作：

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

## <a name="assigning-permissions-to-a-user-for-a-specific-policy-using-custom-roles"></a>使用自訂角色將特定原則的權限指派給使用者
定義自訂角色之後，您便可以將它們指派給使用者。 為了將自訂角色指派給使用者，您必須先取得代表該使用者的 **ObjectId** 。 為此，請使用**獲取-阿達德User** Cmdlet。

在下列範例中， **SomeUser** 使用者的 *ObjectId* 是 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3。

    PS C:\>Get-AzADUser -SearchString "SomeUser"

    DisplayName                    Type                           ObjectId
    -----------                    ----                           --------
    someuser@hotmail.com                                          05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3

獲得使用者的**ObjectId**和自訂角色名稱後，可以使用**New-AzRoleAssign** Cmdlet 將該角色指派給使用者：

    PS C:\>New-AzRoleAssignment -ObjectId 05DEFF7B-0AC3-4ABF-B74D-6A72CD5BF3F3 -RoleDefinitionName "Policy Contributor" -Scope /subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DevTestLab/labs/<LabName>/policySets/default/policies/AllowedVmSizesInLab

在先前的範例中，使用的是 **AllowedVmSizesInLab** 原則。 您也可以使用下列任何原則：

* MaxVmsAllowedPerUser
* MaxVmsAllowedPerLab
* AllowedVmSizesInLab
* LabVmsShutdown

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>後續步驟
將特定實驗室原則的權限授與使用者之後，以下是一些需要考量的後續步驟：

* [安全存取實驗室](devtest-lab-add-devtest-user.md)
* [設置實驗室策略](devtest-lab-set-lab-policy.md)
* [創建實驗室範本](devtest-lab-create-template.md)
* [為 VM 創建自訂專案](devtest-lab-artifact-author.md)
* [將 VM 新增至實驗室](devtest-lab-add-vm.md)

