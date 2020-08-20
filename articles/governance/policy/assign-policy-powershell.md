---
title: 快速入門：使用 PowerShell 進行新原則指派
description: 在本快速入門中，使用 Azure PowerShell 建立 Azure 原則指派，以識別不符合規範的資源。
ms.date: 08/17/2020
ms.topic: quickstart
ms.openlocfilehash: e941b74101308af703f243197fb4043f8f32d233
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548407"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立 Azure 原則指派，以識別不相容資源

了解 Azure 中合規性的第一個步驟是識別您資源的狀態。 在本快速入門中，您會建立原則指派，以識別未使用受控磁碟的虛擬機器。 完成時，您會識別出「不符合規範」  的虛擬機器。

Azure PowerShell 模組可用來從命令列或在指令碼中管理 Azure 資源。
本指南說明如何使用 Az 模組建立原則指派。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 開始之前，請確定已安裝最新版 Azure PowerShell。 如需詳細資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

- 使用 Azure PowerShell 註冊 Azure 原則見解資源提供者。 註冊資源提供者，以確保您的訂用帳戶可搭配它使用。 若要註冊資源提供者，您必須有權註冊資源提供者作業。 這項作業包含在「參與者」和「擁有者」角色中。 執行下列命令以註冊資源提供者：

  ```azurepowershell-interactive
  # Register the resource provider if it's not already registered
  Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
  ```

  如需註冊及檢視資源提供者的詳細資訊，請參閱[資源提供者和類型](../../azure-resource-manager/management/resource-providers-and-types.md)。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-policy-assignment"></a>建立原則指派

在本快速入門中，您會為「稽核沒有受控磁碟的 VM」  定義建立原則指派。 此原則定義會識別未使用受控磁碟的虛擬機器。

執行下列命令以建立新的原則指派：

```azurepowershell-interactive
# Get a reference to the resource group that is the scope of the assignment
$rg = Get-AzResourceGroup -Name '<resourceGroupName>'

# Get a reference to the built-in policy definition to assign
$definition = Get-AzPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit VMs that do not use managed disks' }

# Create the policy assignment with the built-in definition against your resource group
New-AzPolicyAssignment -Name 'audit-vm-manageddisks' -DisplayName 'Audit VMs without managed disks Assignment' -Scope $rg.ResourceId -PolicyDefinition $definition
```

上述命令會使用下列資訊：

- **Name** - 指派的實際名稱。 例如，我們使用了 audit-vm-manageddisks  。
- **DisplayName** - 原則指派的顯示名稱。 在此案例中，您會使用_稽核沒有受控磁碟指派的虛擬機器_。
- **定義** – 原則定義，這是您用來建立指派的根基。 在此案例中，即為原則定義_稽核沒有受控磁碟的虛擬機器_的 ID。
- **範圍** – 範圍會決定在哪些資源或資源群組上強制執行原則指派。 範圍從訂用帳戶到資源群組。 請務必將 &lt;scope&gt; 取代為您的資源群組。

您現在可以識別不相容的資源，以了解環境的相容性狀態。

## <a name="identify-non-compliant-resources"></a>識別不相容的資源

使用下列資訊來識別不符合您所建立之原則指派的資源。 執行下列命令：

```azurepowershell-interactive
# Get the resources in your resource group that are non-compliant to the policy assignment
Get-AzPolicyState -ResourceGroupName $rg.ResourceGroupName -PolicyAssignmentName 'audit-vm-manageddisks' -Filter 'IsCompliant eq false'
```

如需關於取得原則狀態的詳細資訊，請參閱 [Get-AzPolicyState](/powershell/module/az.policyinsights/Get-AzPolicyState)。

您的結果類似下列範例：

```output
Timestamp                   : 3/9/19 9:21:29 PM
ResourceId                  : /subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmId}
PolicyAssignmentId          : /subscriptions/{subscriptionId}/providers/microsoft.authorization/policyassignments/audit-vm-manageddisks
PolicyDefinitionId          : /providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d
IsCompliant                 : False
SubscriptionId              : {subscriptionId}
ResourceType                : /Microsoft.Compute/virtualMachines
ResourceTags                : tbd
PolicyAssignmentName        : audit-vm-manageddisks
PolicyAssignmentOwner       : tbd
PolicyAssignmentScope       : /subscriptions/{subscriptionId}
PolicyDefinitionName        : 06a78e20-9358-41c9-923c-fb736d382a4d
PolicyDefinitionAction      : audit
PolicyDefinitionCategory    : Compute
ManagementGroupIds          : {managementGroupId}
```

結果會符合原則指派在 Azure 入口網站檢視中的 [資源合規性]  索引標籤所顯示的內容。

## <a name="clean-up-resources"></a>清除資源

若要移除建立的指派，請使用下列命令：

```azurepowershell-interactive
# Removes the policy assignment
Remove-AzPolicyAssignment -Name 'audit-vm-manageddisks' -Scope '/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>'
```

## <a name="next-steps"></a>後續步驟

在這個快速入門中，您指派原則定義以識別 Azure 環境中的不相容資源。

若要深入了解指派原則，以驗證新資源是相容的，請繼續進行以下的教學課程：

> [!div class="nextstepaction"]
> [建立及管理原則](./tutorials/create-and-manage.md)