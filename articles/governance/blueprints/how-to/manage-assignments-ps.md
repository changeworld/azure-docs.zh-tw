---
title: 如何使用 PowerShell 管理配置
description: 瞭解如何使用正式的 Azure 藍圖 PowerShell 模組 Az.Blueprint 管理藍圖分配。
ms.date: 09/30/2019
ms.topic: how-to
ms.openlocfilehash: 0868e5e207202511c1981a930870bfdc68a77a8f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677433"
---
# <a name="how-to-manage-assignments-with-powershell"></a>如何使用 PowerShell 管理配置

可以使用**Az.藍圖**Azure PowerShell 模組管理藍圖分配。 該模組支援提取、創建、更新和刪除分配。 該模組還可以獲取有關現有藍圖定義的詳細資訊。 本文介紹如何安裝模組並開始使用它。

## <a name="add-the-azblueprint-module"></a>新增 Az.藍圖模組

要使 Azure PowerShell 能夠管理藍圖分配,必須添加模組。 此模組適用於在本機安裝的 PowerShell、[Azure Cloud Shell](https://shell.azure.com) 或 [Azure PowerShell Docker 映像](https://hub.docker.com/r/azuresdk/azure-powershell/)。

### <a name="base-requirements"></a>基底需求

Azure 藍圖模組需要以下軟體:

- Azure 電源外殼 1.5.0 或更高版本。 如果尚未安裝，請依照[這些指示](/powershell/azure/install-az-ps)操作。
- PowerShellGet 2.0.1 或更新版本。 如果未安裝或更新，請依照[這些指示](/powershell/scripting/gallery/installing-psget)操作。

### <a name="install-the-module"></a>安裝模組

PowerShell 的 Azure 藍圖模組是**Az.藍圖**。

1. 從**系統管理** PowerShell 提示字元中，執行下列命令：

   ```azurepowershell-interactive
   # Install the Azure Blueprints module from PowerShell Gallery
   Install-Module -Name Az.Blueprint
   ```

   > [!NOTE]
   > 如果已安裝**Az.帳戶**,則可能需要`-AllowClobber`使用 強制安裝。

1. 驗證模組是否已匯入且是否正確版本 (0.2.6):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.Blueprint module
   Get-Command -Module 'Az.Blueprint' -CommandType 'Cmdlet'
   ```

## <a name="get-blueprint-definitions"></a>取得藍圖定義

使用分配的第一步通常是獲得對藍圖定義的引用。
cmdlet`Get-AzBlueprint`獲取一個或多個藍圖定義。 cmdlet 可以從`-ManagementGroupId {mgId}`具有的管理組獲取藍圖定義,也可以`-SubscriptionId {subId}`從 訂閱 獲取藍圖定義。 **Name**參數取得藍圖定義,但必須與**管理組Id**或**訂閱 Id**一起使用。 **版本**可以與**Name**一起使用,以便更明確地確定返回的藍圖定義。 交換機`-LatestPublished`抓取最近的版本,而不是**版本**。

以下範例用於`Get-AzBlueprint`從表示為的特定訂閱取得名為「101-藍圖-定義-訂閱」的藍圖定義的所有`{subId}`版本 :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get all versions of the blueprint definition in the specified subscription
$blueprints = Get-AzBlueprint -SubscriptionId '{subId}' -Name '101-blueprints-definition-subscription'

# Display the blueprint definition object
$blueprints
```

具有多個版本的藍圖定義的範例輸出如下所示:

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

## <a name="get-blueprint-assignments"></a>取得藍圖配置

如果藍圖分配已存在,則可以使用`Get-AzBlueprintAssignment`cmdlet 獲取對它的引用。 cmdlet 將**訂閱 Id**和**Name**作為可選參數。 如果未指定**訂閱 Id,** 則使用目前訂閱上下文。

以下範例用於`Get-AzBlueprintAssignment`從表示為的特定訂閱中取得名為「分配-鎖定資源群組」的單個藍圖`{subId}`分配 :

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -SubscriptionId '{subId}' -Name 'Assignment-lock-resource-groups'

# Display the blueprint assignment object
$blueprintAssignment
```

藍圖分配的範例輸出如下所示:

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

## <a name="create-blueprint-assignments"></a>建立藍圖配置

如果藍圖分配尚不存在,您可以使用`New-AzBlueprintAssignment`cmdlet 創建它。 此 cmdlet 使用以下參數:

- **名稱**[必需]
  - 指定藍圖配置的名稱
  - 必須是唯一的,並且在**訂閱 Id**中不存在
- **藍圖**[必需]
  - 指定要配置的藍圖定義
  - 取得`Get-AzBlueprint`參考物件
- **位置**[必需]
  - 指定要建立的系統分配的託管識別與訂閱部署物件的區域
- **訂閱**(選擇性的)
  - 指定指定指定訂閱
  - 如果未提供,則預設為目前訂閱上下文
- **鎖定**(選擇性的)
  - 定義用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項:_無_,_所有資源唯讀_,_所有資源不刪除_
  - 如果未提供,則默認為 _"無"_
- **系統配置識別**(選擇性的)
  - 選擇此選擇可為分配建立系統分配的託管識別並部署資源
  - 「識別」參數集的預設值
  - 無法與**使用者配置的身份**一起使用
- **使用者配置的身份**(選擇性的)
  - 指定使用者配置的託管識別,用於配置及部署資源
  - "識別"參數集的一部分
  - 無法與**系統分配的身份**一起使用
- **參數**(選擇性的)
  - 在藍圖配置上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的鍵/值對哈希[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值是定義的**預設值**
  - 如果未提供參數,並且沒有**預設值**,則該參數不是可選的

    > [!NOTE]
    > **參數**不支援安全字串。

- **資源群組參數**(選擇性的)
  - 資源元件的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組項目占位元具有鍵/值對,用於動態設定該資源群組項目**的名稱**和**位置**
  - 如果未提供資源群組參數,並且沒有**預設值**,則資源群組參數不是可選的
- **配置檔案**(選擇性的)
  - 藍圖配置 JSON 檔表示的路徑
  - 此參數是 PowerShell 參數集的一部分,該參數僅包括**名稱**、**藍圖**和**訂閱 Id,** 以及公共參數。

### <a name="example-1-provide-parameters"></a>範例 1:提供參數

下面的示例`Get-AzBlueprint`創建與"我的藍圖"藍圖定義"1.1"版本"1.1"的新分配,將託管標識和分配物件位置設置為"westus2",使用_AllResourcesReadOnly_鎖定資源,並在表示的特定訂閱上設置**參數**和資源**組參數**的哈`{subId}`希錶,表示為:

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

建立藍圖分配的範例輸出如下所示:

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

### <a name="example-2-use-a-json-assignment-definition-file"></a>範例 2:使用 JSON 分配定義檔

下面的示例創建與[示例 1](#example-1-provide-parameters)幾乎相同的賦值。
該範例顯示 JSON 分配定義檔和**分配檔**參數的使用,而不是將參數傳遞給 cmdlet。 此外,**排除主體**屬性配置為**鎖**的一部分。 沒有**排除主體**的 PowerShell 參數,只能透過 JSON 分配定義檔設定該屬性來配置該屬性。

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

有關使用者配置的託管識別的 JSON 分配定義檔的範例,請參考範例中的請求正文:使用 REST API[的使用者分配的託管識別分配](/rest/api/blueprints/assignments/createorupdate#examples)。

## <a name="update-blueprint-assignments"></a>更新藍圖配置

有時,需要更新已創建的藍圖分配。 `Set-AzBlueprintAssignment` cmdlet 處理此操作。 cmdlet 採用 cmdlet 執行`New-AzBlueprintAssignment`的大多數相同 參數,允許更新在賦值上設置的任何參數。 例外情況是_名稱_、_藍圖_與_訂閱 Id_。 僅更新提供的值。

要瞭解更新藍圖分配時會發生什麼情況,請參閱[更新分配的規則](./update-existing-assignments.md#rules-for-updating-assignments)。

- **名稱**[必需]
  - 指定要更新的藍圖分配的名稱
  - 以尋找搜尋的配置,而不是變更配置
- **藍圖**[必需]
  - 指定藍圖分配的藍圖定義
  - 取得`Get-AzBlueprint`參考物件
  - 以尋找搜尋的配置,而不是變更配置
- **位置**(選擇性的)
  - 指定要建立的系統分配的託管識別與訂閱部署物件的區域
- **訂閱**(選擇性的)
  - 指定指定指定訂閱
  - 如果未提供,則預設為目前訂閱上下文
  - 以尋找搜尋的配置,而不是變更配置
- **鎖定**(選擇性的)
  - 定義用於已部署資源的[藍圖資源鎖定](../concepts/resource-locking.md)
  - 支援的選項:_無_,_所有資源唯讀_,_所有資源不刪除_
- **系統配置識別**(選擇性的)
  - 選擇此選擇可為分配建立系統分配的託管識別並部署資源
  - 「識別」參數集的預設值
  - 無法與**使用者配置的身份**一起使用
- **使用者配置的身份**(選擇性的)
  - 指定使用者配置的託管識別,用於配置及部署資源
  - "識別"參數集的一部分
  - 無法與**系統分配的身份**一起使用
- **參數**(選擇性的)
  - 在藍圖配置上設定[動態參數](../concepts/parameters.md#dynamic-parameters)的鍵/值對哈希[表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 動態參數的預設值是定義的**預設值**
  - 如果未提供參數,並且沒有**預設值**,則該參數不是可選的

    > [!NOTE]
    > **參數**不支援安全字串。

- **資源群組參數**(選擇性的)
  - 資源元件的[哈希表](/powershell/module/microsoft.powershell.core/about/about_hash_tables)
  - 每個資源群組項目占位元具有鍵/值對,用於動態設定該資源群組項目**的名稱**和**位置**
  - 如果未提供資源群組參數,並且沒有**預設值**,則資源群組參數不是可選的

以下範例更新`Get-AzBlueprint`透過變更鎖定模式取得的「我的藍圖」藍圖定義的版本「1.1」的分配:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get version '1.1' of the blueprint definition in the specified subscription
$bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'my-blueprint' -Version '1.1'

# Update the existing blueprint assignment
$bpAssignment = Set-AzBlueprintAssignment -Name 'my-blueprint-assignment' -Blueprint $bpDefinition `
    -SubscriptionId '{subId}' -Lock AllResourcesDoNotDelete
```

建立藍圖分配的範例輸出如下所示:

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

## <a name="remove-blueprint-assignments"></a>刪除藍圖配置

當刪除藍圖分配時,cmdlet`Remove-AzBlueprintAssignment`處理此操作。 cmdlet 採用**名稱**或**輸入物件**來指定要刪除的藍圖分配。 **訂閱 Id** _是必需的_,必須在所有情況下都提供。

下面的範例取得具有`Get-AzBlueprintAssignment`的現有藍圖分配,然後將其從表示為`{subId}`的特定訂閱中刪除:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the blueprint assignment in the specified subscription
$blueprintAssignment = Get-AzBlueprintAssignment -Name 'Assignment-lock-resource-groups'

# Remove the existing blueprint assignment
Remove-AzBlueprintAssignment -InputObject $blueprintAssignment -SubscriptionId '{subId}'
```

## <a name="end-to-end-code-example"></a>端到端代碼範例

將所有步驟組合在一起,以下範例取得藍圖定義,然後創建、更新和刪除表示為`{subId}`的特定訂閱中的藍圖分配:

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