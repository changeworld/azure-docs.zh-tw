---
title: 使用 Azure CLI 新增或移除 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure CLI 和 Azure 角色型存取控制（Azure RBAC），為使用者、群組、服務主體或受控識別授與 Azure 資源的存取權。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 3a66482aeee7832baa91fe98357b870e2a280912
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735771"
---
# <a name="add-or-remove-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 新增或移除 Azure 角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]本文說明如何使用 Azure CLI 指派角色。

## <a name="prerequisites"></a>Prerequisites

若要新增或移除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)
- Azure Cloud Shell 或[Azure CLI](/cli/azure) [中的 Bash](/azure/cloud-shell/overview)

## <a name="get-object-ids"></a>取得物件識別碼

若要新增或移除角色指派，您可能需要指定物件的唯一識別碼。 識別碼的格式為： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 入口網站或 Azure CLI 取得識別碼。

### <a name="user"></a>User

若要取得 Azure AD 使用者的物件識別碼，您可以使用[az AD user show](/cli/azure/ad/user#az-ad-user-show)。

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>群組

若要取得 Azure AD 群組的物件識別碼，您可以使用[az ad group show](/cli/azure/ad/group#az-ad-group-show)或[az ad group list](/cli/azure/ad/group#az-ad-group-list)。

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>應用程式

若要取得 Azure AD 服務主體（應用程式所使用的身分識別）的物件識別碼，您可以使用[az AD sp list](/cli/azure/ad/sp#az-ad-sp-list)。 針對服務主體，請使用物件識別碼，而**不**是應用程式識別碼。

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>新增角色指派

在 Azure RBAC 中，若要授與存取權，您可以新增角色指派。

### <a name="user-at-a-resource-group-scope"></a>資源群組範圍中的使用者

若要在資源群組範圍中新增使用者的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍中的*\@patlong contoso.com*使用者：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>使用唯一角色識別碼

有一些時間可能會變更角色名稱，例如：

- 您會使用自己的自訂角色，並決定變更名稱。
- 您在名稱中使用的預覽角色具有 **（預覽）** 。 釋放角色時，會重新命名角色。

> [!IMPORTANT]
> 預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使角色已重新命名，角色識別碼也不會變更。 如果您使用腳本或自動化來建立角色指派，最佳做法是使用唯一角色識別碼，而不是角色名稱。 因此，如果重新命名角色，您的腳本比較有可能會使用。

若要使用唯一角色識別碼（而非角色名稱）新增角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下列範例會將「[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)」角色指派給*醫藥-sales*資源群組範圍中的*\@patlong contoso.com*使用者。 若要取得唯一角色識別碼，您可以使用[az role definition list](/cli/azure/role/definition#az-role-definition-list)或參閱[Azure 內建角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>訂用帳戶範圍中的群組

若要新增群組的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 如需如何取得群組物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將*讀取*者角色指派給訂用帳戶範圍中識別碼為22222222-2222-2222-2222-222222222222 的*王 mack」小組*群組。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>資源範圍中的群組

若要新增群組的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 如需如何取得群組物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

下列範例會將「*虛擬機器參與者*」角色指派給名稱為22222222-2222-2222-2222-222222222222 的*王 mack」小組*群組，其位於名為*醫藥*的虛擬網路的資源範圍。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>資源群組範圍中的應用程式

若要新增應用程式的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 如需如何取得應用程式物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍中物件識別碼為44444444-4444-4444-4444-444444444444 的應用程式。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>訂用帳戶範圍的使用者

若要在訂用帳戶範圍新增使用者的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

下列範例會將「*讀取*者」角色指派給訂用帳戶範圍中的*annm\@example.com*使用者。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>管理群組範圍的使用者

若要在管理群組範圍新增使用者的角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)。 若要取得管理群組識別碼，您可以在 [**管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

下列範例會將「*帳單讀者*」角色指派給管理群組範圍的*alain\@example.com*使用者。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>新增服務主體

如果您建立新的服務主體，並立即嘗試將角色指派給該服務主體，在某些情況下，該角色指派可能會失敗。 例如，如果您使用腳本來建立新的受控識別，然後嘗試將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體會建立在一個區域中;不過，角色指派可能會發生在另一個尚未複寫服務主體的區域中。 若要解決這種情況，您應該在建立角色指派時指定主體類型。

若要新增角色指派，請使用[az role 指派 create](/cli/azure/role/assignment#az-role-assignment-create)，指定的`--assignee-object-id`值，然後將設定`--assignee-principal-type`為`ServicePrincipal`。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下列範例會將「*虛擬機器參與者*」角色指派給*醫藥-sales*資源群組範圍內的*msi 測試*受控識別：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 Azure RBAC 中，若要移除存取權，您可以使用[az role 指派 delete](/cli/azure/role/assignment#az-role-assignment-delete)來移除角色指派：

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

下列範例會從*醫藥-sales*資源群組上的*\@Patlong Contoso.com*使用者移除「*虛擬機器參與者*」角色指派：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下列範例會在訂用帳戶範圍中，從識別碼為22222222-2222-2222-2222-222222222222 的*王 Mack」小組*群組移除*讀取*者角色。 如需如何取得群組物件識別碼的詳細資訊，請參閱[取得物件](#get-object-ids)識別碼。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

下列範例會從管理群組範圍的*alain\@example.com*使用者移除「*帳單讀者*」角色。 若要取得管理群組的識別碼，您可以使用[az account management-group list](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 列出 Azure 角色指派](role-assignments-list-cli.md)
- [使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/cli-azure-resource-manager.md)
