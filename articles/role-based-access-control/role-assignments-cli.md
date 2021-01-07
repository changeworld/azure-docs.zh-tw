---
title: 使用 Azure CLI 新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure CLI 和 Azure 角色型存取控制，為使用者、群組、服務主體或受控識別授與 azure 資源的存取權 (Azure RBAC) 。
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: e1aa4945391e159f99c82fecff99c238ae0e7e93
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964400"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 新增或移除 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 本文說明如何使用 Azure CLI 指派角色。

## <a name="prerequisites"></a>先決條件

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)
- Azure Cloud Shell 或[Azure CLI](/cli/azure) [中的 Bash](../cloud-shell/overview.md)

## <a name="steps-to-add-a-role-assignment"></a>新增角色指派的步驟

在 Azure RBAC 中，若要授與存取權，您可以新增角色指派。 角色指派由三項元素所組成：安全性主體、角色定義和範圍。 若要新增角色指派，請遵循下列步驟。

### <a name="step-1-determine-who-needs-access"></a>步驟1：判斷需要存取的人員

您可以將角色指派給使用者、群組、服務主體或受控識別。 若要加入角色指派，您可能需要指定物件的唯一識別碼。 識別碼的格式如下：`11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 入口網站或 Azure CLI 來取得識別碼。

**使用者**

若為 Azure AD 使用者，請取得使用者主體名稱，例如 *patlong \@ contoso.com* 或使用者物件識別碼。 若要取得物件識別碼，您可以使用 [az ad user show](/cli/azure/ad/user#az_ad_user_show)。

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**群組**

針對 Azure AD 群組，您需要群組物件識別碼。 若要取得物件識別碼，您可以使用 [az ad group show](/cli/azure/ad/group#az_ad_group_show) 或 [az ad 群組清單](/cli/azure/ad/group#az_ad_group_list)。

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**服務主體**

針對 Azure AD 服務主體 (應用程式) 所使用的身分識別，您需要服務主體物件識別碼。 若要取得物件識別碼，您可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)。 對於服務主體，請使用物件識別碼，而 **不是** 應用程式識別碼。

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**受控識別**

針對系統指派或使用者指派的受控識別，您需要物件識別碼。 若要取得物件識別碼，您可以使用 [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list)。

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

若只要列出使用者指派的受控識別，您可以使用 [az 身分識別清單](/cli/azure/identity#az_identity_list)。

```azurecli
az identity list
```
    
### <a name="step-2-find-the-appropriate-role"></a>步驟2：尋找適當的角色

許可權會群組在一起成為角色。 您可以從數個 [Azure 內建角色](built-in-roles.md) 的清單中選取，也可以使用自己的自訂角色。 最佳做法是以所需的最低許可權授與存取權，因此請避免指派更廣泛的角色。

若要列出角色並取得唯一角色識別碼，您可以使用 [az 角色定義清單](/cli/azure/role/definition#az_role_definition_list)。

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

以下說明如何列出特定角色的詳細資料。

```azurecli
az role definition list --name "{roleName}"
```

如需詳細資訊，請參閱 [列出 Azure 角色定義](role-definitions-list.md#azure-cli)。
 
### <a name="step-3-identify-the-needed-scope"></a>步驟3：識別所需的範圍

Azure 提供四個範圍層級：資源、 [資源群組](../azure-resource-manager/management/overview.md#resource-groups)、訂用帳戶和 [管理群組](../governance/management-groups/overview.md)。 最佳做法是以所需的最低許可權授與存取權，因此請避免在更廣泛的範圍指派角色。 如需範圍的詳細資訊，請參閱[了解範圍](scope-overview.md)。

**資源範圍**

針對資源範圍，您需要資源的資源識別碼。 您可以藉由查看 Azure 入口網站中資源的屬性來尋找資源識別碼。 資源識別碼具有下列格式。

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**資源群組範圍**

針對資源群組範圍，您需要資源群組的名稱。 您可以在 Azure 入口網站中的 [ **資源群組** ] 頁面上找到名稱，也可以使用 [az 群組清單](/cli/azure/group#az_group_list)。

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**訂用帳戶範圍** 

針對訂用帳戶範圍，您需要訂用帳戶識別碼。 您可以在 Azure 入口網站的 [訂用帳戶 **] 頁面上** 找到識別碼，也可以使用 [az account list](/cli/azure/account#az_account_list)。

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**管理群組領域** 

針對管理群組領域，您需要管理組名。 您可以在 Azure 入口網站的 [ **管理群組** ] 頁面上找到名稱，也可以使用 [ [az 帳戶管理-群組清單](/cli/azure/account/management-group#az_account_management_group_list)]。

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-add-role-assignment"></a>步驟4：新增角色指派

若要新增角色指派，請使用 [az role 指派 create](/cli/azure/role/assignment#az_role_assignment_create) 命令。 視範圍而定，命令通常具有下列其中一種格式。

**資源範圍**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**資源群組範圍**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**訂用帳戶範圍** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**管理群組領域** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

以下顯示當您將「 [虛擬機器參與者](built-in-roles.md#virtual-machine-contributor) 」角色指派給資源群組範圍中的使用者時的輸出範例。

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="add-role-assignment-examples"></a>新增角色指派範例

#### <a name="add-role-assignment-for-all-blob-containers-in-a-storage-account-resource-scope"></a>為儲存體帳戶資源範圍中的所有 blob 容器新增角色指派

針對名為 *storage12345* 的儲存體帳戶，將 [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色指派給資源範圍中具有物件識別碼 *55555555-5555-5555-5555-555555555555* 的服務主體。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="add-role-assignment-for-a-specific-blob-container-resource-scope"></a>新增特定 blob 容器資源範圍的角色指派

將 [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色指派給 blob 容器 *-01* blob 容器之資源範圍中的物件識別碼為 *55555555-5555-5555-5555-555555555555* 的服務主體。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="add-role-assignment-for-a-group-in-a-specific-virtual-network-resource-scope"></a>在特定虛擬網路資源範圍中新增群組的角色指派

針對名為 *醫藥* 的虛擬網路，將 [虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色指派給名為22222222-2222-2222-2222-222222222222 的 *王 mack」團隊* 群組（名為的資源範圍）。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="add-role-assignment-for-a-user-at-a-resource-group-scope"></a>在資源群組範圍新增使用者的角色指派

將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給 *醫藥-sales* 資源群組範圍的 *patlong \@ contoso.com* 使用者。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>在資源群組範圍中使用唯一角色識別碼為使用者新增角色指派

有幾個時候角色名稱可能會變更，例如：

- 您正在使用自己的自訂角色，並決定變更名稱。
- 您使用的預覽角色在名稱中有 **(預覽)** 。 釋放角色時，就會重新命名角色。

即使角色已重新命名，角色識別碼也不會變更。 如果您使用腳本或自動化來建立角色指派，最好使用唯一角色識別碼，而不是角色名稱。 因此，如果角色已重新命名，您的腳本可能會更有作用。

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給 *醫藥-sales* 資源群組範圍的 *patlong \@ contoso.com* 使用者。

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-resource-group-scope"></a>為資源群組範圍中的所有 blob 容器新增角色指派

在 *範例-rg* 資源群組範圍中，將 [儲存體 Blob 資料參與者](built-in-roles.md#storage-blob-data-contributor)角色指派給物件識別碼為 *55555555-5555-5555-5555-555555555555* 的服務主體。

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

或者，您可以使用參數指定完整的資源群組 `--scope` ：

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="add-role-assignment-for-an-application-at-a-resource-group-scope"></a>在資源群組範圍中新增應用程式的角色指派

將「 [虛擬機器參與者](built-in-roles.md#virtual-machine-contributor) 」角色指派給 *醫藥-sales* 資源群組範圍的服務主體物件識別碼為44444444-4444-4444-4444-444444444444 的應用程式。

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-new-service-principal-at-a-resource-group-scope"></a>在資源群組範圍為新的服務主體新增角色指派

如果您建立新的服務主體，並立即嘗試將角色指派給該服務主體，在某些情況下，該角色指派可能會失敗。 例如，如果您使用腳本來建立新的受控識別，然後嘗試將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體建立在某個區域中；不過，角色指派可能發生在另一個尚未複寫服務主體的區域中。 若要解決這種情況，您應該在建立角色指派時指定主體類型。

若要新增角色指派，請使用 [az role 指派 create](/cli/azure/role/assignment#az_role_assignment_create)，指定的值 `--assignee-object-id` ，然後設定 `--assignee-principal-type` 為 `ServicePrincipal` 。

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給 *醫藥-sales* 資源群組範圍的 *msi 測試* 受控識別：

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="add-role-assignment-for-a-user-at-a-subscription-scope"></a>在訂用帳戶範圍為使用者新增角色指派

將「 [讀取](built-in-roles.md#reader) 者」角色指派給訂用帳戶範圍內的 *annm \@ example.com* 使用者。

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-group-at-a-subscription-scope"></a>在訂用帳戶範圍新增群組的角色指派

將 [讀者](built-in-roles.md#reader) 角色指派給訂用帳戶範圍中識別碼為22222222-2222-2222-2222-222222222222 的 *王 mack」團隊* 群組。

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-all-blob-containers-at-a-subscription-scope"></a>為訂用帳戶範圍內的所有 blob 容器新增角色指派

將 [儲存體 Blob 資料讀取器](built-in-roles.md#storage-blob-data-reader) 角色指派給訂用帳戶範圍內的 *alain \@ example.com* 使用者。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="add-role-assignment-for-a-user-at-a-management-group-scope"></a>為管理群組範圍的使用者新增角色指派

將「 [帳單讀者](built-in-roles.md#billing-reader) 」角色指派給位於管理群組範圍的 *alain \@ example.com* 使用者。

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除存取權，您可以使用 [az 角色指派刪除](/cli/azure/role/assignment#az_role_assignment_delete)來移除角色指派。

下列範例會從 *醫藥-sales* 資源群組上的 *patlong \@ Contoso.com* 使用者移除「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派：

```azurecli
az role assignment delete --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

移除在訂用帳戶範圍中識別碼為22222222-2222-2222-2222-222222222222 的 *王 Mack」團隊* 群組中的 [讀取](built-in-roles.md#reader)者角色。

```azurecli
az role assignment delete --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

從管理群組範圍的 *alain \@ example.com* 使用者移除 [帳單讀者](built-in-roles.md#billing-reader)角色。

```azurecli
az role assignment delete --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 列出 Azure 角色指派](role-assignments-list-cli.md)
- [使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/management/manage-resources-cli.md)
