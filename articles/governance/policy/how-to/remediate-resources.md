---
title: 補救不相容的資源
description: 本指南會逐步引導您補救不符合「Azure 原則」中原則規範的資源。
ms.date: 06/09/2020
ms.topic: how-to
ms.openlocfilehash: be55f16734a94acfcc89d632f4cb79f550fa74d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636303"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>補救不符合 Azure 原則規範的資源

您可以透過**補救**讓不符合 **deployIfNotExists** 或 **modify** 原則規範的資源變成符合規範狀態。 藉由指示「Azure 原則」在現有資源上執行所指派原則的 **deployIfNotExists** 效果或標籤**作業** (不論該指派屬於管理群組、訂用帳戶、資源群組或個別資源)，即可完成補救。 本文說明了解和完成使用「Azure 原則」來進行補救所需的步驟。

## <a name="how-remediation-security-works"></a>補救安全性的運作方式

當「Azure 原則」執行 **deployIfNotExists** 原則定義中的範本時，會使用[受控識別](../../../active-directory/managed-identities-azure-resources/overview.md)來執行。
「Azure 原則」會為每個指派項目建立受控識別，但您必須提供有關要將哪些角色授與受控識別的詳細資料。 如果受控識別缺少角色，在指派原則或方案時，就會顯示此錯誤。 使用入口網站時，在開始指派之後，「Azure 原則」會自動將所列出的角色授與受控識別。 受控識別的_位置_不會影響其對 Azure 原則的操作。

:::image type="content" source="../media/remediate-resources/missing-role.png" alt-text="受控識別 - 遺漏角色" border="false":::

> [!IMPORTANT]
> 如果 **deployIfNotExists** 或 **modify** 所修改的資源在原則指派的範圍外，或範本會存取在原則指派範圍外資源的屬性，您就必須對該指派項目的受控識別[手動授與存取權](#manually-configure-the-managed-identity)，否則補救部署將會失敗。

## <a name="configure-policy-definition"></a>設定原則定義

第一步是在原則定義中定義 **deployIfNotExists** 和 **modify**，以成功部署所含範本內容所需的角色。 請在 **details** 屬性底下，新增 **roleDefinitionIds** 屬性。 此屬性是一個與您環境中角色相符的字串陣列。 如需完整範例，請參閱 [deployIfNotExists 範例](../concepts/effects.md#deployifnotexists-example)或 [modify 範例](../concepts/effects.md#modify-examples)。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**roleDefinitionIds** 屬性會使用完整資源識別碼，而不會採用角色的簡短 **roleName**。 若要取得您環境中 'Contributor' 角色的識別碼，請使用下列程式碼：

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>手動設定受控識別

使用入口網站來建立指派時，「Azure 原則」會產生受控識別並授與其 **roleDefinitionIds** 中所定義的角色。 在下列情況下，必須手動執行建立受控識別並為它指派權限的步驟：

- 使用 SDK (例如 Azure PowerShell) 時
- 當範本修改指派範圍外的資源時
- 當範本讀取指派範圍外的資源時

### <a name="create-managed-identity-with-powershell"></a>使用 PowerShell 來建立受控識別

若要在指派原則的期間建立受控識別，必須定義 **Location** 並使用 **AssignIdentity**。 下列範例會取得 [部署 SQL DB 透明資料加密] 內建原則的定義、設定目標資源群組，然後建立指派。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

`$assignment` 變數現在包含受控識別的主體識別碼，以及建立原則指派時所傳回的標準值。 透過 `$assignment.Identity.PrincipalId` 即可存取它。

### <a name="grant-defined-roles-with-powershell"></a>使用 PowerShell 來授與已定義的角色

您必須先透過 Azure Active Directory 完成新受控識別的複寫，才能將所需的角色授與它。 完成複寫之後，下列範例會逐一查看 `$policyDef` 中的原則定義來尋找 **roleDefinitionIds**，然後使用 [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) 將角色授與新的受控識別。

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>透過入口網站授與已定義的角色

使用入口網站將已定義的角色授與指派項目的受控識別時，有兩種方法：使用**存取控制 (IAM)** ，或是編輯原則或方案指派，然後按一下 [儲存]。

若要將角色新增至指派項目的受控識別，請依照下列步驟進行操作：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

1. 選取 Azure 原則分頁左側的 [指派]。

1. 找出具有受控識別的指派項目，然後按一下其名稱。

1. 在編輯頁面上尋找 [指派識別碼] 屬性。 指派識別碼會類似於：

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   受控識別的名稱是指派資源識別碼中的最後一個部分，在此範例中為 `2802056bfc094dfb95d4d7a5`。 請複製指派資源識別碼的這個部分。

1. 瀏覽至需要手動新增角色定義的資源或資源父容器 (資源群組、訂用帳戶、管理群組)。

1. 按一下資源頁面中的 [存取控制 (IAM)] 連結，然後按一下存取控制頁面頂端的 [+ 新增角色指派]。

1. 從原則定義中選取符合 **roleDefinitionIds** 的適當角色。
   將 [存取權指派對象為] 保留設定為預設的 [Azure AD 使用者、群組或應用程式]。 在 [選取] 方塊中，貼上或輸入稍早找到的指派資源識別碼部分。 在搜尋完成之後，按一下具有相同名稱的物件以選取識別碼，然後按一下 [儲存]。

## <a name="create-a-remediation-task"></a>建立補救工作

### <a name="create-a-remediation-task-through-portal"></a>透過入口網站建立補救工作

在評估期間，具有 **deployIfNotExists** 或 **modify** 效果的原則指派會判斷是否有不符合規範的資源。 當找到不符合規範的資源時，會在 [補救] 頁面上提供詳細資料。 具有不符合規範之資源的原則清單會隨附一個可觸發**補救工作**的選項。 此選項會從 **deployIfNotExists** 範本或 **modify** 作業建立部署。

若要建立**補救工作**，請依照下列步驟進行操作：

1. 藉由按一下 [所有服務] 然後搜尋並選取 [原則]，在 Azure 入口網站中啟動 Azure 原則服務。

   :::image type="content" source="../media/remediate-resources/search-policy.png" alt-text="搜尋所有服務中的原則" border="false":::

1. 選取「Azure 原則」頁面左側的 [補救]。

   :::image type="content" source="../media/remediate-resources/select-remediation.png" alt-text="在 [原則] 頁面上選取 [補救]" border="false":::

1. [用以補救的原則] 索引標籤和資料表格上會包含所有具有不符合規範資源的 **deployIfNotExists** 和 **modify** 原則指派。 按一下具有不符合規範之資源的原則。 [新的補救工作] 頁面隨即開啟。

   > [!NOTE]
   > 有一個開啟 [補救工作] 頁面的替代方式，就是從 [合規性] 頁面尋找並按一下原則，然後按一下 [建立補救工作] 按鈕。

1. 在 [新的補救工作] 頁面上，使用 [範圍] 的省略符號，從已被指派該原則的資源中挑選子資源 (包括一直到個別資源物件)，以篩選要補救的資源。 此外，請使用 [位置] 下拉式清單來進一步篩選資源。 將只會補救表格中所列出的資源。

   :::image type="content" source="../media/remediate-resources/select-resources.png" alt-text="補救 - 選取要補救的資源" border="false":::

1. 篩選資源之後，按一下 [補救] 來開始補救工作。 原則合規性頁面會開啟至 [補救工作] 索引標籤，以顯示工作進度的狀態。 補救工作所建立的部署會立即開始。

   :::image type="content" source="../media/remediate-resources/task-progress.png" alt-text="補救 - 補救工作的進度" border="false":::

1. 從原則合規性頁面按一下 [補救工作] 以取得進度的相關詳細資料。 這會顯示針對該工作所使用的篩選，以及所要補救的資源清單。

1. 從 [補救工作] 頁面中，在資源上按一下滑鼠右鍵，以檢視補救工作的部署或資源。 在資料列結尾，按一下 [相關事件] 以查看詳細資料，例如錯誤訊息。

   :::image type="content" source="../media/remediate-resources/resource-task-context-menu.png" alt-text="補救 - 資源工作操作功能表" border="false":::

透過**補救工作**部署的資源會新增至原則合規性頁面上的 [已部署資源] 索引標籤中。

### <a name="create-a-remediation-task-through-azure-cli"></a>透過 Azure CLI 建立補救工作

若要使用 Azure CLI 建立**補救工作**，請使用 `az policy remediation` 命令。 將 `{subscriptionId}` 取代為您的訂用帳戶識別碼，以及將 `{myAssignmentId}` 取代為您的 **deployIfNotExists** 或 **modify** 原則指派識別碼。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

如需其他補救命令和範例，請參閱 [az policy remediation](/cli/azure/policy/remediation) 命令。

### <a name="create-a-remediation-task-through-azure-powershell"></a>透過 Azure PowerShell 建立補救工作

若要透過 Azure PowerShell 建立**補救工作**，請使用 `Start-AzPolicyRemediation` 命令。 將 `{subscriptionId}` 取代為您的訂用帳戶識別碼，以及將 `{myAssignmentId}` 取代為您的 **deployIfNotExists** 或 **modify** 原則指派識別碼。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

如需其他補救 Cmdlet 和範例，請參閱 [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) 模組。

### <a name="create-a-remediation-task-during-policy-assignment-in-the-azure-portal"></a>在 Azure 入口網站中於原則指派期間建立補救工作

建立補救工作的簡化方式是在原則指派期間從 Azure 入口網站執行此動作。 如果要指派的原則定義是**deployIfNotExists**或**修改**效果，[**補救**] 索引標籤上的 wizard 會提供 [_建立補救_工作] 選項。 如果選取此選項，則會在原則指派的同時建立補救工作。

## <a name="next-steps"></a>後續步驟

- 在 [Azure 原則範例](../samples/index.md)檢閱範例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 檢閱[了解原則效果](../concepts/effects.md)。
- 了解如何[以程式設計方式建立原則](programmatically-create.md)。
- 了解如何[取得合規性資料](get-compliance-data.md)。
- 透過[使用 Azure 管理群組來組織資源](../../management-groups/overview.md)來檢閱何謂管理群組。
