---
title: 如何使用管理群組 - Azure 治理
description: 了解如何檢視、維護、更新及刪除您的管理群組階層。
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 423d1837c3d5710e24abb94f5411200319e8a8aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381670"
---
# <a name="manage-your-resources-with-management-groups"></a>利用管理群組來管理您的資源

如果貴組織有多個訂用帳戶，您可能需要一個方法來有效率地管理這些訂用帳戶的存取、原則和相容性。 Azure 管理群組可以在訂用帳戶之上提供範圍層級。 您要將訂用帳戶整理到稱為「管理群組」的容器中，並將治理條件套用至管理群組。 管理群組內的所有訂用帳戶都會自動繼承套用到管理群組的條件。

無論具有何種類型的訂用帳戶，管理群組都可為您提供企業級的大規模管理功能。 若要深入了解管理群組，請參閱[使用 Azure 管理群組來組織資源](./overview.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Azure 資源管理員使用者權杖和管理組緩存在強制刷新之前持續30分鐘。 執行移動管理組或訂閱等任何操作后,最多可能需要 30 分鐘才能顯示。 要更快地查看更新,您需要通過刷新瀏覽器、登錄和註銷或請求新令牌來更新權杖。  

## <a name="change-the-name-of-a-management-group"></a>變更管理群組的名稱

您可以使用入口網站、PowerShell 或 Azure CLI 來變更管理群組的名稱。

### <a name="change-the-name-in-the-portal"></a>在入口網站中變更名稱

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 選取您需要重新命名的管理群組。

1. 選擇**詳細資訊**。

1. 選取頁面頂端的 [重新命名群組]**** 選項。

   :::image type="content" source="./media/detail_action_small.png" alt-text="重新命名管理群組頁面上的群組選項" border="false":::

1. 功能表開啟時，輸入您要顯示的新名稱。

   :::image type="content" source="./media/rename_context.png" alt-text="重新命名群組面板以重新命名管理群組" border="false":::

1. 選取 [儲存]  。

### <a name="change-the-name-in-powershell"></a>在 PowerShell 中變更名稱

要更新顯示名稱,請使用**Update-Az 管理群組**。 例如,要將顯示名稱的管理組從「Contoso IT」更改為「Contoso」」,可以執行以下命令:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>在 Azure CLI 中變更名稱

對於 Azure CLI，請使用 update 命令。

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>刪除管理群組

若要刪除管理群組，必須符合下列需求：

1. 管理群組下沒有任何子管理群組或訂用帳戶。

   - 要將訂閱或管理群組移到其他管理群組,請參閱[在層次結構中移動管理群組和訂閱](#moving-management-groups-and-subscriptions)。

1. 您需要對管理組("擁有者"、"參與者"或"管理組參與者")寫入許可權。 若要查看您有哪些權限，請選取管理群組，然後選取 **IAM**。 要瞭解有關 RBAC 角色的更多詳細資訊,請參閱  
   [使用 RBAC 管理權限與權限 。](../../role-based-access-control/overview.md)

### <a name="delete-in-the-portal"></a>在入口網站中刪除

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 選取您要刪除的管理群組。

1. 選擇**詳細資訊**。

1. 選擇 **"刪除"**

   :::image type="content" source="./media/delete.png" alt-text="[刪除群組] 選項" border="false":::

   > [!TIP]
   > 如果無法使用圖示，將您的滑鼠選取器停留在圖示上會顯示原因。

1. 視窗隨即開啟，確認您是否要刪除管理群組。

   :::image type="content" source="./media/delete_confirm.png" alt-text="[刪除群組] 確認視窗" border="false":::

1. 選取 [是]  。

### <a name="delete-in-powershell"></a>在 PowerShell 中刪除

使用 PowerShell 中的 **「刪除-Az管理群組」** 命令刪除管理組。

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>在 Azure CLI 中刪除

針對 Azure CLI，使用 az account management-group delete 命令。

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>檢視管理群組

只您在管理群組中具有直接或繼承的 RBAC 角色，您就可以檢視該管理群組。  

### <a name="view-in-the-portal"></a>在入口網站中檢視

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 管理組層次結構頁將載入。 此頁是您可以瀏覽有權存取的所有管理組和訂閱的位置。 選取群組名稱即可前往階層中的下一個層級。 瀏覽方式與檔案總管相同。

1. 若要查看管理群組的詳細資訊，請選取管理群組標題旁的 **(詳細資料)** 連結。 如果此連結無法使用，表示您沒有檢視該管理群組的權限。

   :::image type="content" source="./media/main.png" alt-text="主要" border="false":::

### <a name="view-in-powershell"></a>在 PowerShell 中檢視

您可以使用「取得-Az 管理群組」命令檢索所有群組。 有關管理組 GET PowerShell 命令的完整清單,請參閱[Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup)模組。  

```azurepowershell-interactive
Get-AzManagementGroup
```

對於單一管理群組的資訊，請使用 -GroupName 參數

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

要返回特定的管理組及其下層次結構的所有級別,請使用 **-展開**和 **-Recurse**參數。  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>在 Azure CLI 中檢視

您可以使用 list 命令來擷取所有群組。  

```azurecli-interactive
az account management-group list
```

如需單一管理群組的資訊，請使用 show 命令

```azurecli-interactive
az account management-group show --name 'Contoso'
```

要返回特定的管理組及其下層次結構的所有級別,請使用 **-展開**和 **-Recurse**參數。

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>移動管理群組和訂用帳戶   

建立管理群組的其中一個原因是要將訂用帳戶組合在一起。 只有管理群組和訂用帳戶才能設為另一個管理群組的子群組。 移至管理群組的訂閱從父管理群組繼承所有使用者存取權限和原則

將管理組或訂閱移到另一個管理組的子級時,需要將三個規則評估為 true。

如果您要執行移動動作，您需要： 

- 子訂用帳戶或管理群組上的管理群組寫入和角色指派寫入權限。
  - 內建角色範例**擁有者**
- 目標父管理群組上的管理群組寫入權限。
  - 內建角色範例:**擁有者**,**參與者**,**管理群組者**
- 現有父管理群組上的管理群組寫入權限。
  - 內建角色範例:**擁有者**,**參與者**,**管理群組者**

**例外情況**:如果目標或現有父管理組是根管理組,則許可權要求不適用。 因為根管理群組是所有新管理群組和訂用帳戶的預設登陸點，所以您不需要其權限即可移動項目。

如果訂用帳戶上的擁有者角色繼承自目前的管理群組，則您的移動目標會受到限制。 您只能將訂用帳戶移至具有擁有者角色的另一個管理群組。 因為您會失去訂用帳戶的擁有權，所以無法將其移至您是參與者的管理群組。 如果您直接分配給訂閱的所有者角色(不是從管理組繼承的),則可以將其移動到您作為參與者的任何管理組。

要檢視 Azure 門戶中的權限,請選擇管理群組,然後選擇**IAM**。 若要深入了解 RBAC 角色，請參閱[使用 RBAC 管理存取權與權限](../../role-based-access-control/overview.md)。

## <a name="move-subscriptions"></a>移動訂用帳戶 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>將現有訂閱新增到門戶中的管理群組

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 選取您計畫要作為父代的管理群組。

1. 在頁面頂端，選取 [新增訂用帳戶]****。

1. 在清單中選取具有正確識別碼的訂用帳戶。

   :::image type="content" source="./media/add_context_sub.png" alt-text="可新增到管理群組的訂閱" border="false":::

1. 選取 [儲存]。

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>從門戶中的管理群組中移除訂閱

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 選取您正在規劃且目前為父代的管理群組。  

1. 針對清單中您需要移動的訂用帳戶資料列，選取資料列結尾省略符號。

   :::image type="content" source="./media/move_small.png" alt-text="在管理群組中移動選項" border="false":::

1. 選取 [移動]****。

1. 在開啟的功能表上，選取**父管理群組**。

   :::image type="content" source="./media/move_small_context.png" alt-text="移動窗格以變更父群組" border="false":::

1. 選取 [儲存]  。

### <a name="move-subscriptions-in-powershell"></a>在 PowerShell 中移動訂用帳戶

要在 PowerShell 中移動訂閱,請使用「新阿茲管理組訂閱」命令。  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

要刪除和訂閱之間的連結,使用刪除-AzManagementGroup 訂閱命令。

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>在 Azure CLI 中移動訂用帳戶

若要在 CLI 移動訂用帳戶，您可以使用 add 命令。

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

若要將訂用帳戶從管理群組中移除，請使用 subscription remove 命令。  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups"></a>移動管理群組 

### <a name="move-management-groups-in-the-portal"></a>在入口網站中移動管理群組

1. 登入[Azure 門戶](https://portal.azure.com)。

1. 選擇**所有服務** > **管理群組**。

1. 選取您計畫要作為父代的管理群組。

1. 在頁面頂端，選取 [新增管理群組]****。

1. 在開啟的功能表中，選取是要新的管理群組還是使用現有管理群組。

   - 選取新項目即會建立新的管理群組。
   - 選取現有項目即會顯示您可移至此管理群組之所有管理群組的下拉式清單。  

   :::image type="content" source="./media/add_context_MG.png" alt-text="將管理群組移至新群組或現有群組" border="false":::

1. 選取 [儲存]  。

### <a name="move-management-groups-in-powershell"></a>在 PowerShell 中移動管理群組

使用 PowerShell 中的 Update-AzManagementGroup 命令將管理組移到其他組下。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>在 Azure CLI 中移動管理群組

您可以透過 Azure CLI 使用 update 命令來移動管理群組。

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>使用活動記錄稽核管理群組

[Azure 活動記錄](../../azure-monitor/platform/platform-logs-overview.md)中支援管理群組。 可以查詢與其他 Azure 資源在同一中心位置的管理組發生的所有事件。 例如，您可以看到對特定的管理群組的所有角色指派或原則指派變更。

:::image type="content" source="./media/al-mg.png" alt-text="具有管理群組的活動紀錄" border="false":::

在 Azure 入口網站外部查詢管理群組時，管理群組的目標範圍像是 **"/providers/Microsoft.Management/managementGroups/{yourMgID}"**。

## <a name="referencing-management-groups-from-other-resource-providers"></a>從其他資源提供者引用管理群組

從其他資源提供程式的操作中引用管理組時,請使用以下路徑作為範圍。 使用 PowerShell、Azure CLI 和 REST API 時,使用此路徑。  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

使用此路徑的一個範例是在將新角色分配分配給 PowerShell 中的管理群組時:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

在管理組中檢索策略定義時使用相同的作用域路徑。

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](./create.md)
- [如何變更、刪除或管理您的管理群組](./manage.md)
- [檢閱 Azure PowerShell 資源模組中的管理群組](/powershell/module/az.resources#resources)
- [檢閱 REST API 中的管理群組](/rest/api/resources/managementgroups)
- [檢閱 Azure CLI 中的管理群組](/cli/azure/account/management-group)
