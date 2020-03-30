---
title: 補救不相容的資源
description: 本指南將引導您完成不符合 Azure 策略中策略的資源的修正。
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471382"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>補救不符合 Azure 原則規範的資源

不符合**DeployNot存在**或**修改**策略的資源可以通過**修正**置於合規狀態。 修正是通過指示 Azure 策略在現有資源上運行**DeployNotExists**效果或所分配策略的標記**操作**來實現的，無論該分配是管理組、訂閱組、資源組還是單個資源。 本文演示了使用 Azure 策略理解和完成修正所需的步驟。

## <a name="how-remediation-security-works"></a>補救安全性的運作方式

當 Azure 策略在**DeployIfNotExists**策略定義中運行範本時，它使用[託管標識](../../../active-directory/managed-identities-azure-resources/overview.md)運行該範本。
Azure 策略為每個分配創建託管標識，但必須詳細說明授予託管標識的角色。 如果受控識別缺少角色，在指派原則或方案時，就會顯示此錯誤。 使用門戶時，Azure 策略會在分配開始後自動授予列出的託管標識。 託管標識_的位置_不會影響其使用 Azure 策略的操作。

![受控識別 - 遺漏角色](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> 如果通過**DeployIfNot存在**或**修改**修改的資源超出策略分配範圍，或者範本訪問策略分配範圍以外的資源的屬性，則必須[手動授予](#manually-configure-the-managed-identity)分配的託管標識存取權限，否則修正部署將失敗。

## <a name="configure-policy-definition"></a>設定原則定義

第一步是定義**部署IfNotExists**的角色，並**修改**策略定義中的需求，以成功部署包含範本的內容。 請在 **details** 屬性底下，新增 **roleDefinitionIds** 屬性。 此屬性是一個與您環境中角色相符的字串陣列。 有關完整示例，請參閱[DeployIf存在示例](../concepts/effects.md#deployifnotexists-example)或[修改示例](../concepts/effects.md#modify-examples)。

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

**角色定義 Ids**屬性使用完整的資源識別碼，並且不獲取角色的簡短**角色名稱**。 若要取得您環境中 'Contributor' 角色的識別碼，請使用下列程式碼：

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>手動設定受控識別

使用門戶創建分配時，Azure 策略既生成託管標識，又授予它在**角色定義 Id**中定義的角色。 在下列情況下，必須手動執行建立受控識別並為它指派權限的步驟：

- 使用 SDK (例如 Azure PowerShell) 時
- 當範本修改指派範圍外的資源時
- 當範本讀取指派範圍外的資源時

> [!NOTE]
> 目前支援此功能的 SDK 只有 Azure PowerShell 和 .NET。

### <a name="create-managed-identity-with-powershell"></a>使用 PowerShell 來建立受控識別

若要在指派原則的期間建立受控識別，必須定義 **Location** 並使用 **AssignIdentity**。 下列範例會取得 [部署 SQL DB 透明資料加密]**** 內建原則的定義、設定目標資源群組，然後建立指派。

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

使用入口網站將已定義的角色授與指派項目的受控識別時，有兩種方法：使用**存取控制 (IAM)**，或是編輯原則或方案指派，然後按一下 [儲存]****。

若要將角色新增至指派項目的受控識別，請依照下列步驟進行操作：

1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

1. 選取 Azure 原則分頁左側的 [指派]****。

1. 找出具有受控識別的指派項目，然後按一下其名稱。

1. 在編輯頁面上尋找 [指派識別碼]**** 屬性。 指派識別碼會類似於：

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   受控識別的名稱是指派資源識別碼中的最後一個部分，在此範例中為 `2802056bfc094dfb95d4d7a5`。 請複製指派資源識別碼的這個部分。

1. 瀏覽至需要手動新增角色定義的資源或資源父容器 (資源群組、訂用帳戶、管理群組)。

1. 按一下資源頁面中的 [存取控制 (IAM)]**** 連結，然後按一下存取控制頁面頂端的 [+ 新增角色指派]****。

1. 從原則定義中選取符合 **roleDefinitionIds** 的適當角色。
   將 [存取權指派對象為]**** 保留設定為預設的 [Azure AD 使用者、群組或應用程式]。 在 [選取]**** 方塊中，貼上或輸入稍早找到的指派資源識別碼部分。 在搜尋完成之後，按一下具有相同名稱的物件以選取識別碼，然後按一下 [儲存]****。

## <a name="create-a-remediation-task"></a>建立補救工作

### <a name="create-a-remediation-task-through-portal"></a>通過門戶創建補救任務

在評估期間，具有**DeployNot存在**或**修改**效果的策略分配將確定是否存在不符合的資源。 當找到不符合規範的資源時，會在 [補救]**** 頁面上提供詳細資料。 具有不符合規範之資源的原則清單會隨附一個可觸發**補救工作**的選項。 此選項是從**DeployIfNotExists**範本或**修改**操作創建部署的內容。

若要建立**補救工作**，請依照下列步驟進行操作：

1. 藉由按一下 [所有服務]**** 然後搜尋並選取 [原則]****，在 Azure 入口網站中啟動 Azure 原則服務。

   ![搜尋所有服務中的原則](../media/remediate-resources/search-policy.png)

1. 選取「Azure 原則」頁面左側的 [補救]****。

   ![在"策略"頁上選擇"修復"](../media/remediate-resources/select-remediation.png)

1. 所有**部署IfNot存在**和**修改**策略分配與不符合的資源都包括在**策略中，以修復**選項卡和資料表。 按一下具有不符合規範之資源的原則。 [新的補救工作]**** 頁面隨即開啟。

   > [!NOTE]
   > 有一個開啟 [補救工作]**** 頁面的替代方式，就是從 [合規性]**** 頁面尋找並按一下原則，然後按一下 [建立補救工作]**** 按鈕。

1. 在 [新的補救工作]**** 頁面上，使用 [範圍]**** 的省略符號，從已被指派該原則的資源中挑選子資源 (包括一直到個別資源物件)，以篩選要補救的資源。 此外，請使用 [位置]**** 下拉式清單來進一步篩選資源。 將只會補救表格中所列出的資源。

   ![修復 - 選擇要修復的資源](../media/remediate-resources/select-resources.png)

1. 篩選資源之後，按一下 [補救]**** 來開始補救工作。 策略合規性頁將打開"**修復任務"** 選項卡，以顯示任務進度的狀態。 由修正任務創建的部署立即開始。

   ![修復 - 補救任務的進度](../media/remediate-resources/task-progress.png)

1. 從原則合規性頁面按一下 [補救工作]**** 以取得進度的相關詳細資料。 這會顯示針對該工作所使用的篩選，以及所要補救的資源清單。

1. 從 [補救工作]**** 頁面中，在資源上按一下滑鼠右鍵，以檢視補救工作的部署或資源。 在資料列結尾，按一下 [相關事件]**** 以查看詳細資料，例如錯誤訊息。

   ![補救 - 資源工作操作功能表](../media/remediate-resources/resource-task-context-menu.png)

透過**補救工作**部署的資源會新增至原則合規性頁面上的 [已部署資源]**** 索引標籤中。

### <a name="create-a-remediation-task-through-azure-cli"></a>通過 Azure CLI 創建修正任務

要使用 Azure CLI 創建**修正任務**，`az policy remediation`請使用這些命令。 替換為`{subscriptionId}`訂閱 ID，並`{myAssignmentId}`替換為**部署"存在"** 或**修改**策略分配 ID。

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

有關其他修正命令和示例，請參閱[az 策略修正](/cli/azure/policy/remediation)命令。

### <a name="create-a-remediation-task-through-azure-powershell"></a>通過 Azure PowerShell 創建修正任務

要使用 Azure PowerShell 創建**修正任務**，`Start-AzPolicyRemediation`請使用這些命令。 替換為`{subscriptionId}`訂閱 ID，並`{myAssignmentId}`替換為**部署"存在"** 或**修改**策略分配 ID。

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

有關其他修復 Cmdlet 和示例，請參閱[Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights)模組。

## <a name="next-steps"></a>後續步驟

- 查看[Azure 策略示例](../samples/index.md)中的示例。
- 檢閱 [Azure 原則定義結構](../concepts/definition-structure.md)。
- 回顧[瞭解政策效果](../concepts/effects.md)。
- 瞭解如何[以程式設計方式創建策略](programmatically-create.md)。
- 瞭解如何[獲取合規性資料](get-compliance-data.md)。
- 使用[Azure 管理組查看](../../management-groups/overview.md)管理組的內容。請組織資源。
