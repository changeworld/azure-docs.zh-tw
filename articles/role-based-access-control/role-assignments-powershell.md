---
title: 使用 Azure PowerShell 新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure PowerShell 和 Azure 角色型存取控制，為使用者、群組、服務主體或受控識別授與 azure 資源的存取權 (Azure RBAC) 。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2020
ms.author: rolyon
ms.openlocfilehash: 3bb09133ba6991554072b4bf68b5306c78f868a7
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964281"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-powershell"></a>使用 Azure PowerShell 新增或移除 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文說明如何使用 Azure PowerShell 指派角色。

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>先決條件

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)
- Azure Cloud Shell 或[Azure PowerShell](/powershell/azure/install-az-ps) [的 PowerShell](../cloud-shell/overview.md)
- 您用來執行 PowerShell 命令的帳戶必須具有 Microsoft Graph `Directory.Read.All` 許可權。

## <a name="steps-to-add-a-role-assignment"></a>新增角色指派的步驟

在 Azure RBAC 中，若要授與存取權，您可以新增角色指派。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 若要新增角色指派，請遵循下列步驟。

### <a name="step-1-determine-who-needs-access"></a>步驟1：判斷需要存取的人員

您可以將角色指派給使用者、群組、服務主體或受控識別。 若要加入角色指派，您可能需要指定物件的唯一識別碼。 識別碼的格式如下：`11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 入口網站或 Azure PowerShell 來取得識別碼。

**使用者**

若為 Azure AD 使用者，請取得使用者主體名稱，例如 *patlong \@ contoso.com* 或使用者物件識別碼。 若要取得物件識別碼，您可以使用 [>get-azaduser](/powershell/module/az.resources/get-azaduser)。

```azurepowershell
Get-AzADUser -StartsWith <userName>
(Get-AzADUser -DisplayName <userName>).id
```

**群組**

針對 Azure AD 群組，您需要群組物件識別碼。 若要取得物件識別碼，您可以使用 [remove-azadgroup](/powershell/module/az.resources/get-azadgroup)。

```azurepowershell
Get-AzADGroup -SearchString <groupName>
(Get-AzADGroup -DisplayName <groupName>).id
```

**服務主體**

針對 Azure AD 服務主體 (應用程式) 所使用的身分識別，您需要服務主體物件識別碼。 若要取得物件識別碼，您可以使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal)。 對於服務主體，請使用物件識別碼，而 **不是** 應用程式識別碼。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```

**受控識別**

針對系統指派或使用者指派的受控識別，您需要物件識別碼。 若要取得物件識別碼，您可以使用 [>get-azadserviceprincipal](/powershell/module/az.resources/get-azadserviceprincipal)。

```azurepowershell
Get-AzADServicePrincipal -SearchString <principalName>
(Get-AzADServicePrincipal -DisplayName <principalName>).id
```
    
### <a name="step-2-find-the-appropriate-role"></a>步驟2：尋找適當的角色

許可權會群組在一起成為角色。 您可以從數個 [Azure 內建角色](built-in-roles.md) 的清單中選取，也可以使用自己的自訂角色。 最佳做法是以所需的最低許可權授與存取權，因此請避免指派更廣泛的角色。

若要列出角色並取得唯一角色識別碼，您可以使用 [>get-azroledefinition](/powershell/module/az.resources/get-azroledefinition)。

```azurepowershell
Get-AzRoleDefinition | FT Name, IsCustom, Id
```

以下說明如何列出特定角色的詳細資料。

```azurepowershell
Get-AzRoleDefinition <roleName>
```

如需詳細資訊，請參閱 [列出 Azure 角色定義](role-definitions-list.md#azure-powershell)。
 
### <a name="step-3-identify-the-needed-scope"></a>步驟3：識別所需的範圍

Azure 提供四個範圍層級：資源、 [資源群組](../azure-resource-manager/management/overview.md#resource-groups)、訂用帳戶和 [管理群組](../governance/management-groups/overview.md)。 最佳做法是以所需的最低許可權授與存取權，因此請避免在更廣泛的範圍指派角色。 如需範圍的詳細資訊，請參閱[了解範圍](scope-overview.md)。

**資源範圍**

針對資源範圍，您需要資源的資源識別碼。 您可以藉由查看 Azure 入口網站中資源的屬性來尋找資源識別碼。 資源識別碼具有下列格式。

```
/subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

**資源群組範圍**

針對資源群組範圍，您需要資源群組的名稱。 您可以在 Azure 入口網站中的 [ **資源群組** ] 頁面上找到名稱，也可以使用 [>new-azresourcegroup](/powershell/module/az.resources/get-azresourcegroup)。

```azurepowershell
Get-AzResourceGroup
```

**訂用帳戶範圍** 

針對訂用帳戶範圍，您需要訂用帳戶識別碼。 您可以在 Azure 入口網站的 [訂用帳戶 **] 頁面上** 找到識別碼，也可以使用 [>select-azsubscription](/powershell/module/az.accounts/get-azsubscription)。

```azurepowershell
Get-AzSubscription
```

**管理群組領域** 

針對管理群組領域，您需要管理組名。 您可以在 Azure 入口網站的 [ **管理群組** ] 頁面上找到名稱，也可以使用 [AzManagementGroup](/powershell/module/az.resources/get-azmanagementgroup)。

```azurepowershell
Get-AzManagementGroup
```
    
### <a name="step-4-add-role-assignment"></a>步驟4：新增角色指派

若要新增角色指派，請使用 [>new-azroleassignment](/powershell/module/az.resources/new-azroleassignment) 命令。 視範圍而定，命令通常具有下列其中一種格式。

**資源範圍**

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>/resourcegroups/<resourceGroupName>/providers/<providerName>/<resourceType>/<resourceSubType>/<resourceName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionId <roleId> `
-ResourceName <resourceName> `
-ResourceType <resourceType> `
-ResourceGroupName <resourceGroupName>
```

**資源群組範圍**

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-ResourceGroupName <resourceGroupName>
```

**訂用帳戶範圍** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /subscriptions/<subscriptionId>
```

**管理群組領域** 

```azurepowershell
New-AzRoleAssignment -SignInName <emailOrUserprincipalname> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 

```azurepowershell
New-AzRoleAssignment -ObjectId <objectId> `
-RoleDefinitionName <roleName> `
-Scope /providers/Microsoft.Management/managementGroups/<groupName>
``` 
    
## <a name="add-role-assignment-examples"></a>新增角色指派範例

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>為儲存體帳戶資源範圍中的所有 blob 容器新增角色指派

針對名為 *storage12345* 的儲存體帳戶，將 [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色指派給資源範圍中具有物件識別碼 *55555555-5555-5555-5555-555555555555* 的服務主體。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/providers/Microsoft.Authorization/roleAssignments/cccccccc-cccc-cccc-cccc-cccccccccccc
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>新增特定 blob 容器資源範圍的角色指派

將 [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色指派給 blob 容器 *-01* blob 容器之資源範圍中的物件識別碼為 *55555555-5555-5555-5555-555555555555* 的服務主體。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 55555555-5555-5555-5555-555555555555 `
-RoleDefinitionName "Storage Blob Data Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignm
                     ents/dddddddd-dddd-dddd-dddd-dddddddddddd
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01
DisplayName        : example-identity
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 55555555-5555-5555-5555-555555555555
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>在特定虛擬網路資源範圍中新增群組的角色指派

將「 [虛擬機器參與者](built-in-roles.md#virtual-machine-contributor) 」角色指派給 *醫藥 Sales Admins* 群組，在名為 *醫藥-Sales-project-network* 的虛擬網路的資源範圍中具有識別碼 aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceName pharma-sales-project-network `
-ResourceType Microsoft.Network/virtualNetworks `
-ResourceGroupName MyVirtualNetworkResourceGroup

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network/providers/Microsoft.Authorizat
                     ion/roleAssignments/bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup
                     /providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
DisplayName        : Pharma Sales Admins
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa
ObjectType         : Group
CanDelegate        : False
```

#### <a name="add-a-role-assignment-for-a-user-at-a-resource-group-scope"></a>在資源群組範圍新增使用者的角色指派

將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給 *醫藥-sales* 資源群組範圍的 *patlong \@ contoso.com* 使用者。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/pr
                     oviders/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

或者，您可以使用參數指定完整的資源群組 `-Scope` ：

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>在資源群組範圍中使用唯一角色識別碼為使用者新增角色指派

有幾個時候角色名稱可能會變更，例如：

- 您正在使用自己的自訂角色，並決定變更名稱。
- 您使用的預覽角色在名稱中有 **(預覽)** 。 釋放角色時，就會重新命名角色。

即使角色已重新命名，角色識別碼也不會變更。 如果您使用腳本或自動化來建立角色指派，最好使用唯一角色識別碼，而不是角色名稱。 因此，如果角色已重新命名，您的腳本可能會更有作用。

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給 *醫藥-sales* 資源群組範圍的 *patlong \@ contoso.com* 使用者。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 44444444-4444-4444-4444-444444444444 `
-RoleDefinitionId 9980e02c-c2be-4d73-94e8-173b1dc7cf3c `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales/providers/Microsoft.Authorization/roleAssignments/55555555-5555-5555-5555-555555555555
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : Pat Long
SignInName         : patlong@contoso.com
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>在資源群組範圍中新增應用程式的角色指派

將「 [虛擬機器參與者](built-in-roles.md#virtual-machine-contributor) 」角色指派給 *醫藥-sales* 資源群組範圍的服務主體物件識別碼為77777777-7777-7777-7777-777777777777 的應用程式。

```azurepowershell
PS C:\> New-AzRoleAssignment -ObjectId 77777777-7777-7777-7777-777777777777 `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales
DisplayName        : MyApp1
SignInName         :
RoleDefinitionName : Virtual Machine Contributor
RoleDefinitionId   : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>在訂用帳戶範圍為使用者新增角色指派

將「 [讀取](built-in-roles.md#reader) 者」角色指派給訂用帳戶範圍內的 *annm \@ example.com* 使用者。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName annm@example.com `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"

RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleAssignments/66666666-6666-6666-6666-666666666666
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000
DisplayName        : Ann M
SignInName         : annm@example.com
RoleDefinitionName : Reader
RoleDefinitionId   : acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId           : 77777777-7777-7777-7777-777777777777
ObjectType         : ServicePrincipal
CanDelegate        : False
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>為管理群組範圍的使用者新增角色指派

將「 [帳單讀者](built-in-roles.md#billing-reader) 」角色指派給位於管理群組範圍的 *alain \@ example.com* 使用者。

```azurepowershell
PS C:\> New-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"

RoleAssignmentId   : /providers/Microsoft.Management/managementGroups/marketing-group/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /providers/Microsoft.Management/managementGroups/marketing-group
DisplayName        : Alain Charon
SignInName         : alain@example.com
RoleDefinitionName : Billing Reader
RoleDefinitionId   : fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64
ObjectId           : 44444444-4444-4444-4444-444444444444
ObjectType         : User
CanDelegate        : False
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除存取權，您可以使用 [>new-azroleassignment](/powershell/module/az.resources/remove-azroleassignment)移除角色指派。

下列範例會從 *醫藥-sales* 資源群組上的 *patlong \@ Contoso.com* 使用者移除「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派：

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName patlong@contoso.com `
-RoleDefinitionName "Virtual Machine Contributor" `
-ResourceGroupName pharma-sales
```

移除在訂用帳戶範圍中識別碼為22222222-2222-2222-2222-222222222222 的 *王 Mack」團隊* 群組中的 [讀取](built-in-roles.md#reader)者角色。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -ObjectId 22222222-2222-2222-2222-222222222222 `
-RoleDefinitionName "Reader" `
-Scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

從管理群組範圍的 *alain \@ example.com* 使用者移除 [帳單讀者](built-in-roles.md#billing-reader)角色。

```azurepowershell
PS C:\> Remove-AzRoleAssignment -SignInName alain@example.com `
-RoleDefinitionName "Billing Reader" `
-Scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

如果您收到錯誤訊息：「提供的資訊未對應至角色指派」，請確定您也指定了 `-Scope` 或 `-ResourceGroupName` 參數。 如需詳細資訊，請參閱 [疑難排解 AZURE RBAC](troubleshooting.md#role-assignments-with-identity-not-found)。

## <a name="next-steps"></a>後續步驟

- [使用 Azure PowerShell 列出 Azure 角色指派](role-assignments-list-powershell.md)
- [教學課程：使用 Azure PowerShell 為群組授與 Azure 資源的存取權](tutorial-role-assignments-group-powershell.md)
- [使用 Azure PowerShell 管理資源](../azure-resource-manager/management/manage-resources-powershell.md)
