---
title: 如何使用 PowerShell 管理指派
description: 瞭解如何使用官方 Azure 藍圖 PowerShell 模組 Az. 藍圖來管理藍圖指派。
ms.date: 05/06/2020
ms.topic: how-to
ms.openlocfilehash: fa0f89df79c4ae1c5b66998089f04575bd53ea37
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "82863972"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理指派

您可以使用**Az. 藍圖**Azure PowerShell 模組來管理藍圖指派。 此模組支援提取、建立、更新和移除指派。 此模組也可以提取現有藍圖定義的詳細資料。 本文涵蓋如何安裝模組並開始使用它。

## <a name="add-the-azblueprint-module"></a>新增 Az. 藍圖模組

若要讓 Azure PowerShell 管理藍圖指派，必須新增模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)。

### <a name="base-requirements"></a>基底需求

Azure 藍圖模組需要下列軟體：

- Azure PowerShell 1.5.0 或更新版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/scripting/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

適用于 PowerShell 的 Azure 藍圖模組是**Az. 藍圖**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安裝**Az** ，則可能需要使用 `-AllowClobber` 來強制安裝。

1. 驗證模組是否已匯入，而且是正確的版本（0.2.6）：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>取得藍圖定義

使用指派的第一個步驟通常是取得藍圖定義的參考。
`Get-AzBlueprint`Cmdlet 會取得一或多個藍圖定義。 Cmdlet 可以從管理群組取得藍圖定義， `-ManagementGroupId {mgId}` 或使用訂用帳戶 `-SubscriptionId {subId}` 。 **Name**參數會取得藍圖定義，但必須與**ManagementGroupId**或**SubscriptionId**搭配使用。 **版本**可以與**名稱**搭配使用，以更明確地瞭解傳回的藍圖定義。 參數不是**版本**，而是會 `-LatestPublished` 抓取最近發行的版本。

下列範例會使用 `Get-AzBlueprint` ，從表示為的特定訂用帳戶，取得名為 ' 101-藍圖-定義-訂用帳戶 ' 的藍圖定義的所有版本 `{subId}` ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多個版本之藍圖定義的範例輸出看起來像這樣：

```output
Name                 : 101-blueprints-definition-subscription
Id                   : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101
                       -blueprints-definition-subscription
DefinitionLocationId : {subId}
Versions             : {1.0, 1.1}
TimeCreated          : 2019-02-25
TargetScope          : Subscription
Parameters           : {storageAccount_storageAccountType, storageAccount_location,
                       allowedlocations_listOfAllowedLocations, [Usergrouporapplicationname]:Reader_RoleAssignmentName}
ResourceGroups       : ResourceGroup
```

藍圖定義上的[藍圖參數](../concepts/parameters.md#blueprint-parameters)可以擴充以提供詳細資訊。

```azurepowershell-interactive
$blueprints.Parameters
```

```output
Key                                                    Value
---                                                    -----
storageAccount_storageAccountType                      Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
storageAccount_location                                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
allowedlocations_listOfAllowedLocations                Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
[Usergrouporapplicationname]:Reader_RoleAssignmentName Microsoft.Azure.Commands.Blueprint.Models.PSParameterDefinition
```

## <a name="get-blueprint-assignments"></a>取得藍圖指派

如果藍圖指派已存在，您可以使用 Cmdlet 取得它的參考 `Get-AzBlueprintAssignment` 。 此 Cmdlet 會採用**SubscriptionId**和**Name**做為選擇性參數。 如果未指定**SubscriptionId** ，則會使用目前的訂用帳戶內容。

下列範例會使用 `Get-AzBlueprintAssignment` ，從表示為的特定訂用帳戶，取得名為「指派-鎖定-資源群組」的單一藍圖指派 `{subId}` ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

藍圖指派的範例輸出如下所示：

```output
Name              : Assignment-lock-resource-groups
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssignme
                    nts/Assignment-lock-resource-groups
Scope             : /subscriptions/{subId}
LastModified      : 2019-02-19
LockMode          : AllResourcesReadOnly
ProvisioningState : Succeeded
Parameters        :
ResourceGroups    : ResourceGroup
```

## <a name="create-blueprint-assignments"></a>建立藍圖指派

如果藍圖指派尚不存在，您可以使用 Cmdlet 加以建立 `New-AzBlueprintAssignment` 。 此 Cmdlet 會使用下列參數：

- **名稱**[必要]
  - 指定藍圖指派的名稱
  - 必須是唯一的，而且不存在於**SubscriptionId**中
- **藍圖**[必要]
  - 指定要指派的藍圖定義
  - 使用 `Get-AzBlueprint` 取得參考物件
- **位置**[必要]
  - 指定要在其中建立系統指派的受控識別和訂用帳戶部署物件的區域
- **訂**用帳戶（選擇性）
  - 指定要部署指派的訂用帳戶
  - 如果未提供，則預設為目前的訂用帳戶內容
- **Lock** （選擇性）
  - 定義要用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項： _None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
  - 如果未提供，則預設為_None_
- **SystemAssignedIdentity** （選擇性）
  - 選取即可建立指派給系統指派的受控識別，並部署資源
  - 已設定 "identity" 參數的預設值
  - 不能與**UserAssignedIdentity**搭配使用
- **UserAssignedIdentity** （選擇性）
  - 指定要用於指派和部署資源的使用者指派受控識別
  - 「身分識別」參數集的一部分
  - 不能與**SystemAssignedIdentity**搭配使用
- **參數**（選擇性）
  - 在藍圖指派上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的索引鍵/值組[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值為定義中的**defaultValue**
  - 如果未提供參數，而且沒有**defaultValue**，則參數不是選擇性的

    > [!NOTE]
    > **參數**不支援 secureStrings。

- **ResourceGroupParameter** （選擇性）
  - 資源群組成品的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組成品預留位置都有索引鍵/值組，可動態設定該資源群組成品上的**名稱**和**位置**
  - 如果未提供資源群組參數，而且沒有**defaultValue**，則資源群組參數不是選擇性的
- **AssignmentFile** （選擇性）
  - 藍圖指派的 JSON 檔案表示路徑
  - 此參數是 PowerShell 參數集的一部分，其中只包含**名稱**、**藍圖**和**SubscriptionId**，以及一般參數。

### <a name="example-1-provide-parameters"></a>範例1：提供參數

下列範例會建立以所提取之 ' my 藍圖 ' 藍圖定義版本 ' 1.1 ' 的新指派 `Get-AzBlueprint` 、將受控識別和指派物件位置設定為 ' westus2 '、使用_AllResourcesReadOnly_鎖定資源，以及針對以下列方式表示的特定訂用帳戶，設定**參數**和**ResourceGroupParameter**的雜湊表。 `{subId}`

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
```

建立藍圖指派的範例輸出看起來像這樣：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesReadOnly
ProvisioningState : Creating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

### <a name="example-2-use-a-json-assignment-definition-file"></a>範例2：使用 JSON 指派定義檔

下列範例會建立與[範例 1](#example-1-provide-parameters)幾乎相同的指派。 範例不會將參數傳遞給 Cmdlet，而是會顯示 JSON 指派定義檔和**AssignmentFile**參數的用法。 此外， **excludedPrincipals**屬性會設定為**鎖定**的一部分。 沒有適用于**excludedPrincipals**的 PowerShell 參數，而且只能透過 JSON 指派定義檔來設定屬性。

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "westus2",
  "properties": {
    "description": "Assignment of the 101-blueprint-definition-subscription",
    "blueprintId": "/subscriptions/{subId}/providers/Microsoft.Blueprint/blueprints/101-blueprints-definition-subscription",
    "locks": {
      "mode": "AllResourcesReadOnly",
      "excludedPrincipals": [
          "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
          "38833b56-194d-420b-90ce-cff578296714"
      ]
    },
    "parameters": {
      "storageAccount_storageAccountType": {
        "value": "Standard_GRS"
      }
    },
    "resourceGroups": {
      "ResourceGroup": {
        "name": "storage_rg",
        "location": "westus2"
      }
    }
  }
}
```

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -SubscriptionId '{subId}' `
    -AssignmentFile '.\assignment.json'
```

如需使用者指派的受控識別之 JSON 指派定義檔的範例，請參閱要求主體，[例如：使用 REST API 的使用者指派受控識別進行指派](/rest/api/blueprints/assignments/createorupdate#examples)。

## <a name="update-blueprint-assignments"></a>更新藍圖指派

有時候，必須更新已建立的藍圖指派。 `Set-AzBlueprintAssignment`Cmdlet 會處理此動作。 此 Cmdlet 會採用 Cmdlet 所執行的大部分相同參數 `New-AzBlueprintAssignment` ，以允許更新指派上設定的任何專案。 例外狀況包括_名稱_、_藍圖_和_SubscriptionId_。 只會更新提供的值。

若要瞭解更新藍圖指派時所發生的情況，請參閱[更新指派的規則](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名稱**[必要]
  - 指定要更新之藍圖指派的名稱
  - 用來找出要更新的指派，而不是變更指派
- **藍圖**[必要]
  - 指定藍圖指派的藍圖定義
  - 使用 `Get-AzBlueprint` 取得參考物件
  - 用來找出要更新的指派，而不是變更指派
- **Location** （選擇性）
  - 指定要在其中建立系統指派的受控識別和訂用帳戶部署物件的區域
- **訂**用帳戶（選擇性）
  - 指定要部署指派的訂用帳戶
  - 如果未提供，則預設為目前的訂用帳戶內容
  - 用來找出要更新的指派，而不是變更指派
- **Lock** （選擇性）
  - 定義要用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項： _None_、 _AllResourcesReadOnly_、 _AllResourcesDoNotDelete_
- **SystemAssignedIdentity** （選擇性）
  - 選取即可建立指派給系統指派的受控識別，並部署資源
  - 已設定 "identity" 參數的預設值
  - 不能與**UserAssignedIdentity**搭配使用
- **UserAssignedIdentity** （選擇性）
  - 指定要用於指派和部署資源的使用者指派受控識別
  - 「身分識別」參數集的一部分
  - 不能與**SystemAssignedIdentity**搭配使用
- **參數**（選擇性）
  - 在藍圖指派上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的索引鍵/值組[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值為定義中的**defaultValue**
  - 如果未提供參數，而且沒有**defaultValue**，則參數不是選擇性的

    > [!NOTE]
    > **參數**不支援 secureStrings。

- **ResourceGroupParameter** （選擇性）
  - 資源群組成品的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組成品預留位置都有索引鍵/值組，可動態設定該資源群組成品上的**名稱**和**位置**
  - 如果未提供資源群組參數，而且沒有**defaultValue**，則資源群組參數不是選擇性的

下列範例會變更鎖定模式，以更新提取的「我的藍圖」藍圖定義版本 ' 1.1 ' 的指派 `Get-AzBlueprint` ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

建立藍圖指派的範例輸出看起來像這樣：

```output
Name              : my-blueprint-assignment
Id                : /subscriptions/{subId}/providers/Microsoft.Blueprint/blueprintAssi
                    gnments/my-blueprint-assignment
Scope             : /subscriptions/{subId}
LastModified      : 2019-03-13
LockMode          : AllResourcesDoNotDelete
ProvisioningState : Updating
Parameters        : {storageAccount_storageAccountType}
ResourceGroups    : ResourceGroup
```

## <a name="remove-blueprint-assignments"></a>移除藍圖指派

當您想要移除藍圖指派時，此 `Remove-AzBlueprintAssignment` Cmdlet 會處理此動作。 Cmdlet 會使用**Name**或**InputObject**來指定要移除哪一個藍圖指派。 **SubscriptionId**是_必要_的，而且必須在所有情況下提供。

下列範例會使用來提取現有的藍圖指派 `Get-AzBlueprintAssignment` ，然後從表示為的特定訂用帳戶中移除它 `{subId}` ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="code-example"></a>程式碼範例

將所有步驟結合在一起，下列範例會取得藍圖定義，然後建立、更新和移除特定訂用帳戶中的藍圖指派，表示為 `{subId}` ：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

#region GetBlueprint
# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'
#endregion

#region CreateAssignment
# Create the hash table for Parameters
$bpParameters = @{storageAccount_storageAccountType='Standard_GRS'}

# Create the hash table for ResourceGroupParameters
# ResourceGroup is the resource group artifact placeholder name
$bpRGParameters = @{ResourceGroup=@{name='storage_rg';location='westus2'}}

# Create the new blueprint assignment
$bpAssignment = New-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Location 'westus2' -Lock AllResourcesReadOnly `
    -Parameter $bpParameters -ResourceGroupParameter $bpRGParameters
#endregion CreateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region UpdateAssignment
# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
#endregion UpdateAssignment

# Wait for the blueprint assignment to finish deployment prior to the next steps

#region RemoveAssignment
# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $bpAssignment -SubscriptionId '{subId}'
#endregion
```

## <a name="next-steps"></a>後續步驟

- 了解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 使用[一般疑難排解](../troubleshoot/general.md)來解決藍圖指派期間發生的問題。