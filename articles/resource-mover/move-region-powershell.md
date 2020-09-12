---
title: 在 Azure 資源移動器中使用 PowerShell 跨區域移動資源
description: 瞭解如何在 Azure 資源移動器中使用 PowerShell 跨區域移動資源。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 3236e0a95c6a4b4f57ac38ed067011c3d6848b5a
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670332"
---
# <a name="move-resources-across-regions-in-powershell"></a>在 PowerShell 中跨區域移動資源

瞭解如何使用 Azure 資源移動器中的 PowerShell 將 Azure 資源移至不同的區域。 

> [!NOTE]
> Azure 資源移動器目前為預覽狀態。



## <a name="before-you-start"></a>開始之前

- 您的 Azure 訂用帳戶應該能夠存取資源移動器，而且您應該擁有訂用帳戶的 [擁有](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) 者或 [使用者存取系統管理員](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator) 許可權。
- 資源移動器不會追蹤變更和升級，因此在您開始移動資源之前，請先對資源進行任何必要的變更。
- 當您使用 PowerShell 移動資源時，您目前無法編輯任何目的地區域設定。 直接在入口網站中修改這些設定。
- 當您將資源新增至移動集合，以準備將其移至另一個區域時，有關移動的中繼資料會儲存在為目的而建立的資源群組中。 此資源群組目前可以位於美國東部2或北歐地區。 您可以使用其中一個區域的中繼資料，在任何公用區域之間移動 Azure 資源。

## <a name="sample-values"></a>範例值

我們在腳本範例中使用這些值：

**設定** | **值** 
--- | ---
訂用帳戶識別碼 | subscription-id
資源移動器服務位置 | 美國東部 2
中繼資料資源群組 | RegionMoveRG-centralus-westcentralus
中繼資料資源群組位置 | 美國東部 2
移動集合名稱 | MoveCollection-centralus-westcentralus
來源區域 |  centralus
來源資源群組 | PSDemoRM
目的地區域 | westcentralus
目標資源群組 | PSDemoRMtgt
要移動的 VM | PSDemoVM

## <a name="sign-in-to-azure"></a>登入 Azure

登入 Azure 訂用帳戶。

```azurepowershell-interactive
# Sign in to an Azure subscription
Connect-AzAccount – Subscription "<subscription-id>"
```

## <a name="create-the-resource-group-for-metadata"></a>建立中繼資料的資源群組

建立資源群組，以保存移動集合中繼資料和設定資訊。

```azurepowershell-interactive
# Sign in to an Azure subscription
New-AzResourceGroup -Name MoveCollection-centralus-westcentralus -Location "East US 2"
```

**預期的輸出**：

![建立資源群組之後的輸出文字](./media/move-region-powershell/output-create-resource-group.png)

## <a name="register-the-resource-provider"></a>註冊資源提供者

註冊資源提供者 Microsoft. 遷移，以便建立 MoveCollection 資源。

```azurepowershell-interactive
# Register the provider
Register-AzResourceProvider -ProviderNamespace Microsoft.Migrate 

# Wait for registration
While(((Get-AzResourceProvider -ProviderNamespace Microsoft.Migrate)| where {$_.RegistrationState -eq "Registered" -and $_.ResourceTypes.ResourceTypeName -eq "moveCollections"}|measure).Count -eq 0)
  {
      Start-Sleep -Seconds 5
      Write-Output "Waiting for registration to complete."
  }
```

## <a name="create-the-move-collection"></a>建立移動集合

MoveCollection 物件會儲存您想要移動之資源的中繼資料和設定資訊。

- 若要讓 MoveCollection 物件存取 Azure 資源移動器服務所在的訂用帳戶，它需要 [系統指派的受控識別](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (先前稱為「受控服務識別」 (MSI) # A3 （由訂用帳戶信任）。
- 系統會將來源訂用帳戶的「參與者」或「使用者存取系統管理員」角色指派給身分識別。
- 若要將角色指派給身分識別，您需要訂用帳戶中的角色 (例如擁有者或使用者存取系統管理員) ，並具備下列許可權：
    -  Microsoft.Authorization/roleAssignments/write
    - Microsoft.Authorization/roleAssignments/delete


```azurepowershell-interactive
# Create a MoveCollection object
New-AzResourceMoverMoveCollection -Name "MoveCollection-centralus-westcentralus " -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -SubscriptionId "<subscription-id>" -SourceRegion "centralus" -TargetRegion "westcentralus" -Location  "East US 2" -IdentityType SystemAssigned
```
**預期的輸出**：

![建立移動集合之後的輸出文字](./media/move-region-powershell/output-move-collection.png)


## <a name="assign-a-managed-identity"></a>指派受控識別 

設定訂用帳戶識別碼、取出 MoveCollection 物件的身分識別主體，並將 Azure 角色指派給它。


```azurepowershell-interactive
# Set the subscriptionId
$subscriptionId = "<subscription-id>"

# Retrieve the principal managed identity of the MoveCollection
$moveCollection = Get-AzResourceMoverMoveCollection -SubscriptionId $subscriptionId -ResourceGroupName "RegionMoveRG-centralus-westcentralus " -Name "MoveCollection-centralus-westcentralus "

# Set the IdentityPrincipalID
$identityPrincipalId = $moveCollection.IdentityPrincipalId

# Assign the role to the IdentityPrincipalID
New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName Contributor -Scope "/subscriptions/$subscriptionId"

New-AzRoleAssignment -ObjectId $identityPrincipalId -RoleDefinitionName "User Access Administrator" -Scope "/subscriptions/$subscriptionId"

```

## <a name="add-resources-to-the-move-collection"></a>將資源新增至移動集合

取得您想要移動之來源資源的識別碼。 然後，將它新增至移動集合。

```azurepowershell-interactive
# Retrieve the resource ID
Get-AzResource -Name PSDemoVM -ResourceGroupName PSDemoRM
```

**預期的輸出**

![在抓取資源識別碼之後輸出文字](./media/move-region-powershell/output-retrieve-resource.png)


```azurepowershell-interactive
# Add the resource to the move collection
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/e80eb9fa-c996-4435-aa32-5af6f3d3077c/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM” -Name “PSDemoVM” -ResourceSettingResourceType “ Microsoft.Compute/virtualMachines” -ResourceSettingTargetResourceName “PSDemoVM”
```

**預期的輸出** 
 ![新增資源之後的輸出文字](./media/move-region-powershell/output-add-resource.png)

## <a name="resolve-dependencies"></a>解析相依性

驗證您新增的資源，並解決其他資源的任何相依性。

```azurepowershell-interactive
# Resolve dependencies
Resolve-AzResourceMoverMoveCollectionDependency -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus”
```
**如果相依性存在，則為輸出**

如果移動集合中的資源相依于其他資源，則會發出失敗的訊息。

![檢查相依性之後的輸出文字](./media/move-region-powershell/dependency-error.png)

### <a name="retrieve-dependencies"></a>取出相依性

如果您想要移動的資源與其他資源具有相依性，您可以取得遺漏相依性的相關資訊。

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**預期的輸出**

![在抓取相依性之後輸出文字](./media/move-region-powershell/missing-dependencies.png)

在此範例中，會將兩個相依性識別為遺漏：

- 來源資源群組： PSDemoRM
- VM 上的 NIC： PSDemoVM62

## <a name="add-dependencies-to-collection"></a>將相依性新增至集合


使用您所抓取的資源識別碼，找出遺漏資源的名稱，並將其新增至移動集合。

### <a name="add-the-nic"></a>新增 NIC

取出 NIC 名稱和類型，並將它新增至集合。

```azurepowershell-interactive
# Get the NIC name and resource type
Get-AzResource -ResourceId “/subscriptions/ <subscription-id> /resourcegroups/psdemorm/providers/microsoft.network/networkinterfaces/psdemovm62”

```

**預期的輸出**

![在取出 NIC 之後輸出文字](./media/move-region-powershell/output-retrieve-nic.png)

現在將 NIC 新增至移動集合。 此範例會將相同的名稱提供給目標 NIC。 保持設定和大小寫一致。

```azurepowershell-interactive
# Add the NIC to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -SourceId “/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkInterfaces/psdemovm62” -Name “psdemovm62” -ResourceSettingResourceType “Microsoft.Network/networkInterfaces” -ResourceSettingTargetResourceName “psdemovm62”
```

**預期的輸出**

![將 NIC 新增至集合之後的輸出文字](./media/move-region-powershell/add-nic.png)

## <a name="add-the-source-resource-group"></a>新增來源資源群組

```azurepowershell-interactive
# Get the resource group name and resource type
Get-AzResource -ResourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm”

# Add the resource group to the collection. 
New-AzResourceMoverMoveResource -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -MoveCollectionName “MoveCollection-centralus-westcentralus” -SourceId “/subscriptions/<subscription-id>/resourcegroups/psdemorm” -Name “psdemorm” -ResourceSettingResourceType “resourcegroups” -ResourceSettingTargetResourceName “psdemorm”
```

**預期的輸出**

![將資源群組新增至集合之後的輸出文字](./media/move-region-powershell/add-source-resource-group.png)

## <a name="recheck-dependencies"></a>重新檢查相依性

再次檢查是否有遺漏的相依性。

```azurepowershell-interactive
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -SubscriptionId  “<subscription-id>”
```

**輸出**

還有其他遺漏的相依性。

![再次檢查相依性之後輸出文字](./media/move-region-powershell/recheck-dependencies.png)

## <a name="retrieve-additional-dependencies"></a>取得其他相依性

```azurepowershell-interactive
# Identify dependencies
Get-AzResourceMoverUnresolvedDependency -MoveCollectionName “MoveCollection-centralus-westcentralus ” -ResourceGroupName “RegionMoveRG-centralus-westcentralus” -SubscriptionId  “<subscription-id>”
```

**預期的輸出**


![在抓取其他相依性之後輸出文字](./media/move-region-powershell/additional-missing-dependencies.png)

在此範例中，會將三個相依性識別為遺漏：

- 公用 IP 位址： psdemovm-ip
- Azure 虛擬網路： psdemorm-vnet
- 網路安全性群組 (NSG) ： psdemovm-NSG

## <a name="add-additional-dependencies"></a>新增其他相依性

1. [依照指示](#add-dependencies-to-collection) 將這些資源新增至移動集合。
2. 新增資源之後，請重新驗證，直到新增所有相依性為止。


## <a name="prepare-and-move-the-source-resource-group"></a>準備和移動來源資源群組

在來源區域中，您會在移動資源之前先進行準備。 準備程式取決於您要移動的資源。 例如，針對無狀態資源準備，可能會準備及匯出 Azure Resource Manager (ARM) 範本。 或者，對於具狀態的資源，複寫可能會啟動。 

您必須先準備並移動來源資源群組，才可以準備來源資源。

### <a name="prepare"></a>準備

```azurepowershell-interactive
# Prepare the source resource group
Invoke-AzResourceMoverPrepare -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”
```

**預期的輸出**

![準備來源資源群組之後的輸出文字](./media/move-region-powershell/prepare-source-resource-group.png)

### <a name="initiate-move"></a>起始移動

```azurepowershell-interactive
# Initiate move
Invoke-AzResourceMoverInitiateMove -SubscriptionId “<subscription-id>” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ” -MoveResource “PSDemoRM”

**Expected output**

![Output text after initiating move of the source resource group](./media/move-region-powershell/initiate-move-resource-group.png)


```azurepowershell-interactive
# Commit move
Invoke-AzResourceMoverCommit -SubscriptionId “<subscription-id” -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “PS-centralus-
westcentralus-demoRM” -MoveResource “PSDemoRM”
```

**預期的輸出**

![認可來源資源群組之後的輸出文字](./media/move-region-powershell/commit-move-resource-group.png)


## <a name="prepare-resources"></a>準備資源

將來源資源群組移至目的地區域之後，請取出移動集合中的資源清單，然後準備移動它們。

```azurepowershell-interactive
# Retrieve resources in the collection
Get-AzResourceMoverMoveResource  -SubscriptionId “ <subscription-id> “ -ResourceGroupName “RegionMoveRG-centralus-westcentralus ” -MoveCollectionName “MoveCollection-centralus-westcentralus ”   | Where-Object {  $_.MoveStatusMoveState -eq “PreparePending” } | Select Name
```
**預期的輸出**

![在集合中取出資源之後輸出文字](./media/move-region-powershell/collection-resources.png)

現在，準備資源。

```azurepowershell-interactive
# Prepare the resources, using the resource name
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
或者，您可以使用資源識別碼來準備和移動資源，而不是使用名稱：

```azurepowershell-interactive
# Prepare the resources using the resource ID
Invoke-AzResourceMoverPrepare -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus  -MoveResourceInputType MoveResourceSourceId  -MoveResource $('/subscriptions/<subscription-id>/resourceGroups/PSDemoRM/providers/Microsoft.Network/networkSecurityGroups/PSDemoVM-nsg')
```

**預期的輸出**

![在集合中準備資源之後輸出文字](./media/move-region-powershell/collection-prepare.png)

## <a name="initiate-resource-move"></a>起始資源移動

準備資源之後，請起始移動。

```azurepowershell-interactive
# Initiate the move 
Invoke-AzResourceMoverInitiateMove -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**預期的輸出** 
 ![起始資源移動之後的輸出文字](./media/move-region-powershell/initiate-resource-move.png)

## <a name="discard-or-commit-the-move"></a>捨棄或認可移動

在初始移動之後，您可以決定是否要認可移動或捨棄它。 

- **捨棄**：如果您要進行測試，而且不想要實際移動來源資源，您可能會捨棄移動。 捨棄 move 會將資源傳回 *起始移動暫*止的狀態。 然後，您可以視需要再次起始移動。
- **Commit**： commit 完成移至目的地區域。 認可之後，來源資源會處於「 *刪除來源擱置*中」狀態，您可以決定是否要將它刪除。

### <a name="discard"></a>捨棄

若要捨棄移動：

```azurepowershell-interactive
# Discard the move 
Invoke-AzResourceMoverDiscard -SubscriptionId  <subscription-id> `-ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**預期的輸出** 
 ![捨棄移動後的輸出文字](./media/move-region-powershell/discard-move.png)


### <a name="commit"></a>Commit

若要認可移動：

```azurepowershell-interactive
# Commit the move 
Invoke-AzResourceMoverCommit -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus   -MoveResource $('psdemovm62', 'PSDemoVM-ip', 'PSDemoRM-vnet','PSDemoVM-nsg', ‘PSDemoVM’)
```
**預期的輸出**

![認可移動之後的輸出文字](./media/move-region-powershell/commit-move.png)

## <a name="delete-source-resources"></a>刪除來源資源

認可移動之後，以及確認資源在目的地區域中如預期般運作之後，您可以[使用 PowerShell](../azure-resource-manager/management/manage-resources-powershell.md#delete-resources)或[Azure CLI](../azure-resource-manager/management/manage-resources-cli.md#delete-resources)刪除[Azure 入口網站](../azure-resource-manager/management/manage-resources-portal.md#delete-resources)中的來源資源。

## <a name="next-steps"></a>接下來的步驟

[瞭解如何](remove-move-resources.md) 移除移動集合中的資源。