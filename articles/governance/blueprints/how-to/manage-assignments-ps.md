---
title: 如何使用 PowerShell 管理分配
description: 瞭解如何使用正式的 Azure 藍圖 PowerShell 模組 Az.Blueprint 管理藍圖分配。
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: b16cf887ba8bfc51616839db5f4af87944ec686d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247396"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理分配

可以使用**Az.藍圖**Azure PowerShell 模組管理藍圖分配。 該模組支援提取、創建、更新和刪除分配。 該模組還可以獲取有關現有藍圖定義的詳細資訊。 本文介紹如何安裝模組並開始使用它。

## <a name="add-the-azblueprint-module"></a>添加 Az.藍圖模組

要使 Azure PowerShell 能夠管理藍圖分配，必須添加模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)。

### <a name="base-requirements"></a>基底需求

Azure 藍圖模組需要以下軟體：

- Azure 電源外殼 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/scripting/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

PowerShell 的藍圖模組是**Az.藍圖**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安裝**Az.帳戶**，則可能需要使用`-AllowClobber`強制安裝。

1. 驗證模組是否已導入且是否正確版本 （0.2.6）：

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>獲取藍圖定義

使用分配的第一步通常是獲得對藍圖定義的引用。
Cmdlet`Get-AzBlueprint`獲取一個或多個藍圖定義。 Cmdlet 可以從 具有`-ManagementGroupId {mgId}`的管理組獲取藍圖定義，也可以從 訂閱`-SubscriptionId {subId}`獲取藍圖定義。 **Name**參數獲取藍圖定義，但必須與**管理組Id**或**訂閱 Id**一起使用。 **版本**可以與**Name**一起使用，以便更明確地確定返回的藍圖定義。 交換器`-LatestPublished`獲取最近發佈的版本，而不是**版本**。

以下示例用於`Get-AzBlueprint`從表示為的特定訂閱獲取名為"101-藍圖-定義-訂閱"的藍圖定義的所有版本`{subId}`：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多個版本的藍圖定義的示例輸出如下所示：

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

可以擴展藍圖定義的[藍圖參數](../concepts/parameters.md#blueprint-parameters)以提供更多資訊。

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

## <a name="get-blueprint-assignments"></a>獲取藍圖分配

如果藍圖分配已存在，則可以使用`Get-AzBlueprintAssignment`Cmdlet 獲取對它的引用。 Cmdlet 將**訂閱 Id**和**Name**作為可選參數。 如果未指定**訂閱 Id，** 則使用當前訂閱上下文。

以下示例用於`Get-AzBlueprintAssignment`從表示為的特定訂閱中獲取名為"分配-鎖定資源組"的單個藍圖分配`{subId}`：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

藍圖分配的示例輸出如下所示：

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

## <a name="create-blueprint-assignments"></a>創建藍圖分配

如果藍圖分配尚不存在，您可以使用`New-AzBlueprintAssignment`Cmdlet 創建它。 此 Cmdlet 使用以下參數：

- **名稱**[必需]
  - 指定藍圖分配的名稱
  - 必須是唯一的，並且在**訂閱 Id**中不存在
- **藍圖**[必需]
  - 指定要分配的藍圖定義
  - 用於`Get-AzBlueprint`獲取引用物件
- **位置**[必需]
  - 指定要在中創建的系統分配的託管標識和訂閱部署物件的區域
- **訂閱**（可選）
  - 指定分配部署到的訂閱
  - 如果未提供，則預設為當前訂閱上下文
- **鎖定**（可選）
  - 定義用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項：_無_，_所有資源唯讀_，_所有資源不刪除_
  - 如果未提供，則預設為 _"無"_
- **系統分配標識**（可選）
  - 選擇該選擇可為分配創建系統分配的託管標識並部署資源
  - "標識"參數集的預設值
  - 不能與**使用者分配的身份**一起使用
- **使用者分配的身份**（可選）
  - 指定使用者分配的託管標識，用於分配和部署資源
  - "標識"參數集的一部分
  - 不能與**系統分配的身份**一起使用
- **參數**（可選）
  - 用於在藍圖分配上設置[動態參數](../concepts/parameters.md#dynamic-parameters)的鍵/值對雜湊[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值是定義中的**預設值**
  - 如果未提供參數，並且沒有**預設值**，則該參數不是可選的

    > [!NOTE]
    > **參數**不支援安全字串。

- **資源組參數**（可選）
  - 資源組工件的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源組專案預留位置具有鍵/值對，用於動態設置該資源組專案**的名稱**和**位置**
  - 如果未提供資源組參數，並且沒有**預設值**，則資源組參數不是可選的
- **分配檔**（可選）
  - 藍圖分配 JSON 檔表示的路徑
  - 此參數是 PowerShell 參數集的一部分，該參數僅包括**名稱**、**藍圖**和**訂閱 Id，** 以及公共參數。

### <a name="example-1-provide-parameters"></a>示例 1：提供參數

下面的示例`Get-AzBlueprint`創建與"我的藍圖"藍圖定義"1.1"版本"1.1"的新分配，將託管標識和分配物件位置設置為"westus2"，使用_AllResourcesReadOnly_鎖定資源，並在表示的特定訂閱上設置**參數**和資源**組參數**的雜湊表，表示為： `{subId}`

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

創建藍圖分配的示例輸出如下所示：

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

### <a name="example-2-use-a-json-assignment-definition-file"></a>示例 2：使用 JSON 分配定義檔

下面的示例創建與[示例 1](#example-1-provide-parameters)幾乎相同的賦值。
該示例顯示 JSON 分配定義檔和**分配檔**參數的使用，而不是將參數傳遞給 Cmdlet。 此外，**排除主體**屬性配置為**鎖**的一部分。 沒有**排除主體**的 PowerShell 參數，只能通過 JSON 分配定義檔設置該屬性來配置該屬性。

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

有關使用者分配的託管標識的 JSON 分配定義檔的示例，請參閱示例中的請求正文：使用 REST API[的使用者分配的託管標識分配](/rest/api/blueprints/assignments/createorupdate#examples)。

## <a name="update-blueprint-assignments"></a>更新藍圖分配

有時，需要更新已創建的藍圖分配。 `Set-AzBlueprintAssignment` Cmdlet 處理此操作。 Cmdlet 採用 Cmdlet 執行的大多數相同`New-AzBlueprintAssignment`參數，允許更新在賦值上設置的任何參數。 例外情況是_名稱_、_藍圖_和_訂閱 Id_。 僅更新提供的值。

要瞭解更新藍圖分配時會發生什麼情況，請參閱[更新分配的規則](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名稱**[必需]
  - 指定要更新的藍圖分配的名稱
  - 用於查找要更新的分配，而不是更改分配
- **藍圖**[必需]
  - 指定藍圖分配的藍圖定義
  - 用於`Get-AzBlueprint`獲取引用物件
  - 用於查找要更新的分配，而不是更改分配
- **位置**（可選）
  - 指定要在中創建的系統分配的託管標識和訂閱部署物件的區域
- **訂閱**（可選）
  - 指定分配部署到的訂閱
  - 如果未提供，則預設為當前訂閱上下文
  - 用於查找要更新的分配，而不是更改分配
- **鎖定**（可選）
  - 定義用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項：_無_，_所有資源唯讀_，_所有資源不刪除_
- **系統分配標識**（可選）
  - 選擇該選擇可為分配創建系統分配的託管標識並部署資源
  - "標識"參數集的預設值
  - 不能與**使用者分配的身份**一起使用
- **使用者分配的身份**（可選）
  - 指定使用者分配的託管標識，用於分配和部署資源
  - "標識"參數集的一部分
  - 不能與**系統分配的身份**一起使用
- **參數**（可選）
  - 用於在藍圖分配上設置[動態參數](../concepts/parameters.md#dynamic-parameters)的鍵/值對雜湊[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值是定義中的**預設值**
  - 如果未提供參數，並且沒有**預設值**，則該參數不是可選的

    > [!NOTE]
    > **參數**不支援安全字串。

- **資源組參數**（可選）
  - 資源組工件的[雜湊表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源組專案預留位置具有鍵/值對，用於動態設置該資源組專案**的名稱**和**位置**
  - 如果未提供資源組參數，並且沒有**預設值**，則資源組參數不是可選的

以下示例更新`Get-AzBlueprint`通過更改鎖定模式獲取的"我的藍圖"藍圖定義的版本"1.1"的分配：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

創建藍圖分配的示例輸出如下所示：

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

## <a name="remove-blueprint-assignments"></a>刪除藍圖分配

當刪除藍圖分配時，Cmdlet`Remove-AzBlueprintAssignment`處理此操作。 Cmdlet 採用**名稱**或**輸入物件**來指定要刪除的藍圖分配。 **訂閱 Id** _是必需的_，必須在所有情況下都提供。

下面的示例獲取具有`Get-AzBlueprintAssignment`的現有藍圖分配，然後將其從表示為`{subId}`的特定訂閱中刪除：

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端到端代碼示例

將所有步驟組合在一起，以下示例獲取藍圖定義，然後創建、更新和刪除表示為`{subId}`的特定訂閱中的藍圖分配：

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

- 瞭解[藍圖生命週期](../concepts/lifecycle.md)。
- 了解如何使用[靜態與動態參數](../concepts/parameters.md)。
- 了解如何自訂[藍圖排序順序](../concepts/sequencing-order.md)。
- 了解如何使用[藍圖資源鎖定](../concepts/resource-locking.md)。
- 在分配藍圖期間使用[常規故障排除時](../troubleshoot/general.md)解決問題。