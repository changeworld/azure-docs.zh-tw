---
title: 以程式設計方式建立原則
description: 本文將引導您使用 Azure CLI、Azure PowerShell 和 REST API 以程式設計方式創建和管理 Azure 策略的策略。
ms.date: 01/31/2019
ms.topic: how-to
ms.openlocfilehash: 08ed43a464d1dd7de8220428dbc1c61ce9fc3ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264540"
---
# <a name="programmatically-create-policies"></a>以程式設計方式建立原則

本文會逐步引導您以程式設計方式建立及管理原則。 Azure 策略定義對資源強制實施不同的規則和效果。 強制作業可確保資源會符合您的公司標準及服務等級協定規範。

如需合規性相關資訊，請參閱[取得合規性資料](get-compliance-data.md)。

## <a name="prerequisites"></a>Prerequisites

開始之前，請確定您已符合下列必要條件：

1. 請安裝 [ARMClient](https://github.com/projectkudu/ARMClient) (如果尚未安裝)。 此工具會將 HTTP 要求傳送至以 Azure Resource Manager 為基礎的 API。

1. 將您的 Azure PowerShell 模組更新為最新版本。 如需詳細資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。 如需最新版本的詳細資訊，請參閱 [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)。

1. 使用 Azure PowerShell 註冊 Azure 策略見解資來源提供者，以驗證訂閱是否與資來源提供者一起使用。 若要註冊資源提供者，您必須有權執行資源提供者的註冊動作作業。 這項作業包含在「參與者」和「擁有者」角色中。 執行下列命令以註冊資源提供者：

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   有關註冊和查看資來源提供者的詳細資訊，請參閱[資來源提供者和類型](../../../azure-resource-manager/management/resource-providers-and-types.md)。

1. 如果尚未安裝 Azure CLI，請先安裝。 您可以於[在 Windows 上安裝 Azure CLI](/cli/azure/install-azure-cli-windows) 中取得最新版本。

## <a name="create-and-assign-a-policy-definition"></a>建立及指派原則定義

要更清楚看見您資源的首要步驟，是透過您的資源建立及指派原則。 下一步是了解如何以程式設計方式建立及指派原則。 範例原則會使用 PowerShell、Azure CLI 和 HTTP 要求，來稽核已對所有公用網路開放的儲存體帳戶。

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>使用 PowerShell 建立並指派原則定義

1. 使用下列 JSON 程式碼片段建立名稱為 AuditStorageAccounts.json 的 JSON 檔案。

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   如需撰寫原則定義的詳細資訊，請參閱 [Azure 原則定義結構](../concepts/definition-structure.md)。

1. 使用 AuditStorageAccounts.json 檔案，執行下列命令來建立原則定義。

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   此命令會建立名為_對公用網路開放的稽核儲存體帳戶_的原則定義。
   如需您可使用的其他參數詳細資訊，請參閱 [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)。

   若在未指定位置參數的情況下呼叫，`New-AzPolicyDefinition` 會預設儲存工作階段內容中所選訂用帳戶的原則定義。 若要將定義儲存至不同位置，請使用下列參數：

   - **SubscriptionId** - 儲存到不同的訂用帳戶。 需要 _GUID_ 值。
   - **ManagementGroupName** - 儲存至管理群組。 需要_字串_值。

1. 建立原則定義之後，您可以執行下列命令來建立原則指派：

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   以您想要的資源群組名稱取代 _ContosoRG_。

   `New-AzPolicyAssignment`上的 **"範圍"** 參數適用于管理組、訂閱、資源組或單個資源。 此參數會使用 `Get-AzResourceGroup` 上 **ResourceId** 屬性傳回的完整資源路徑。 以下是每個容器的 **Scope** 模式。 請將 `{rName}`、`{rgName}`、`{subId}` 和 `{mgName}` 分別取代為您的資源名稱、資源群組名稱、訂用帳戶 ID 及管理群組名稱。
   `{rType}` 會取代為資源的**資源類型**，例如，如果是 VM，則為 `Microsoft.Compute/virtualMachines`。

   - 資源 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - 資源群組 - `/subscriptions/{subId}/resourceGroups/{rgName}`
   - 訂用帳戶 - `/subscriptions/{subId}/`
   - 管理群組 - `/providers/Microsoft.Management/managementGroups/{mgName}`

如需使用 Azure Resource Manager PowerShell 模組來管理資源原則的詳細資訊，請參閱 [Az.Resources](/powershell/module/az.resources/#policies)。

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>使用 ARMClient 來建立及指派原則定義

請使用下列程序來建立原則定義。

1. 複製下列 JSON 程式碼片段以建立 JSON 檔案。 您會在下一個步驟中呼叫該檔案。

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. 使用下列其中一個呼叫來建立原則定義︰

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2016-12-01" @<path to policy definition JSON file>
   ```

   使用您訂用帳戶的 ID 來取代上述 {subscriptionId}，或使用[管理群組](../../management-groups/overview.md)的 ID 來取代 {managementGroupId}。

   有關查詢結構的詳細資訊，請參閱[Azure 策略定義 - 創建或更新](/rest/api/resources/policydefinitions/createorupdate)[和策略定義 - 在管理組中創建或更新](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

使用下列程序來建立原則指派，並在資源群組層級指派原則定義。

1. 複製下列 JSON 程式碼片段以建立 JSON 原則指派檔案。 以您自己的值取代 &lt;&gt; 符號中的範例資訊。

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. 使用下列呼叫建立原則指派︰

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2017-06-01-preview" @<path to Assignment JSON file>
   ```

   以您自己的值取代 &lt;&gt; 符號中的範例資訊。

   如需對 REST API 執行 HTTP 呼叫的詳細資訊，請參閱 [Azure REST API 資源](/rest/api/resources/)。

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>使用 Azure CLI 建立並指派原則定義

若要建立原則定義，請使用下列程序：

1. 複製下列 JSON 程式碼片段以建立 JSON 原則指派檔案。

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   如需撰寫原則定義的詳細資訊，請參閱 [Azure 原則定義結構](../concepts/definition-structure.md)。

1. 執行下列命令以建立原則定義：

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   此命令會建立名為_對公用網路開放的稽核儲存體帳戶_的原則定義。
   如需有關其他可供您使用之參數的詳細資訊，請參閱 [az policy definition create](/cli/azure/policy/definition#az-policy-definition-create)。

   若在未指定位置參數的情況下呼叫，`az policy definition creation` 會預設儲存工作階段內容中所選訂用帳戶的原則定義。 若要將定義儲存至不同位置，請使用下列參數：

   - **--subscription** - 儲存到不同的訂用帳戶。 需要一個 _GUID_ 值來用於訂用帳戶 ID，或需要一個「字串」__ 值來用於訂用帳戶名稱。
   - **--management-group** - 儲存到管理群組。 需要_字串_值。

1. 使用下列命令以建立原則指派。 以您自己的值取代 &lt;&gt; 符號中的範例資訊。

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   `az policy assignment create` 上的 **--scope**參數可與管理群組、訂用帳戶、資源群組或單一資源搭配使用。 此參數使用完整資源路徑。 **每個容器的 -scope**的模式如下。 請將 `{rName}`、`{rgName}`、`{subId}` 和 `{mgName}` 分別取代為您的資源名稱、資源群組名稱、訂用帳戶 ID 及管理群組名稱。 `{rType}` 會取代為資源的**資源類型**，例如，如果是 VM，則為 `Microsoft.Compute/virtualMachines`。

   - 資源 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - 資源群組 - `/subscriptions/{subID}/resourceGroups/{rgName}`
   - 訂用帳戶 - `/subscriptions/{subID}`
   - 管理群組 - `/providers/Microsoft.Management/managementGroups/{mgName}`

通過使用 PowerShell 具有以下命令，可以獲取 Azure 策略定義 ID：

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

針對您所建立的原則定義，其原則定義識別碼應該類似下列範例：

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

如需有關如何使用 Azure CLI 來管理資源原則的詳細資訊，請參閱 [Azure CLI 資源原則](/cli/azure/policy?view=azure-cli-latest)。

## <a name="next-steps"></a>後續步驟

如需本文中查詢與命令的詳細資訊，請檢閱以下文章。

- [Azure REST API 資源](/rest/api/resources/)
- [Azure 電源外殼模組](/powershell/module/az.resources/#policies)
- [Azure CLI 原則命令](/cli/azure/policy?view=azure-cli-latest)
- [Azure 策略見解資來源提供者 REST API 引用](/rest/api/policy-insights)
- [使用 Azure 管理組組織資源](../../management-groups/overview.md)。