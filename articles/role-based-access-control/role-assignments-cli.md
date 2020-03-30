---
title: 使用 Azure RBAC 和 Azure CLI 添加或刪除角色指派
description: 瞭解如何使用基於 Azure 角色的存取控制 （RBAC） 和 Azure CLI 為使用者、組、服務主體或託管標識授予對 Azure 資源的訪問。
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
ms.openlocfilehash: b32df50715d5e7276861e0696df1bd6ceb3f684e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245664"
---
# <a name="add-or-remove-role-assignments-using-azure-rbac-and-azure-cli"></a>使用 Azure RBAC 和 Azure CLI 添加或刪除角色指派

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]本文介紹如何使用 Azure CLI 分配角色。

## <a name="prerequisites"></a>Prerequisites

要添加或刪除角色指派，您必須具有：

- `Microsoft.Authorization/roleAssignments/write` 和 `Microsoft.Authorization/roleAssignments/delete` 權限，例如[使用者存取系統管理員](built-in-roles.md#user-access-administrator)或[擁有者](built-in-roles.md#owner)
- [在 Azure 雲外殼](/azure/cloud-shell/overview)或[Azure CLI 中](/cli/azure)打舍

## <a name="get-object-ids"></a>獲取物件指示

要添加或刪除角色指派，可能需要指定物件的唯一 ID。 ID 的格式為： `11111111-1111-1111-1111-111111111111`。 您可以使用 Azure 門戶或 Azure CLI 獲取 ID。

### <a name="user"></a>User

要獲取 Azure AD 使用者的物件識別碼，可以使用[az 廣告使用者顯示](/cli/azure/ad/user#az-ad-user-show)。

```azurecli
az ad user show --id "{email}" --query objectId --output tsv
```

### <a name="group"></a>群組

要獲取 Azure AD 組的物件識別碼，可以使用[az 廣告組顯示](/cli/azure/ad/group#az-ad-group-show)或[az 廣告組清單](/cli/azure/ad/group#az-ad-group-list)。

```azurecli
az ad group show --group "{name}" --query objectId --output tsv
```

### <a name="application"></a>Application

要獲取 Azure AD 服務主體的物件識別碼（應用程式使用的身份），可以使用 az [ad sp 清單](/cli/azure/ad/sp#az-ad-sp-list)。 對於服務主體，請使用物件識別碼**而不是應用程式**ID。

```azurecli
az ad sp list --display-name "{name}" --query [].objectId --output tsv
```

## <a name="add-a-role-assignment"></a>新增角色指派

在 RBAC 中，要授予存取權限，您需要添加角色指派。

### <a name="user-at-a-resource-group-scope"></a>資源組作用域的使用者

要為資源組作用域中的使用者添加角色指派，請使用 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
```

以下示例將*虛擬機器參與者*角色指派給*製藥銷售*資源組作用域*\@中的contoso.com*使用者：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="using-the-unique-role-id"></a>使用唯一角色 ID

有時角色名稱可能會更改，例如：

- 您正在使用自己的自訂角色，並決定更改名稱。
- 您使用的是名稱中具有 **（預覽）** 的預覽角色。 角色釋放時，角色將重命名。

> [!IMPORTANT]
> 預覽版在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

即使重命名角色，角色 ID 也不會更改。 如果使用腳本或自動化來創建角色指派，則最佳做法是使用唯一角色 ID 而不是角色名稱。 因此，如果角色重命名，腳本更有可能工作。

要使用唯一角色 ID 而不是角色名稱添加角色指派，請使用[az 角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。

```azurecli
az role assignment create --role <role_id> --assignee <assignee> --resource-group <resource_group>
```

下面的示例將[虛擬機器參與者](built-in-roles.md#virtual-machine-contributor)角色指派給*製藥銷售*資源組作用域中的*\@patlong contoso.com*使用者。 要獲取唯一角色 ID，可以使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)或查看[Azure 資源的內置角色](built-in-roles.md)。

```azurecli
az role assignment create --role 9980e02c-c2be-4d73-94e8-173b1dc7cf3c --assignee patlong@contoso.com --resource-group pharma-sales
```

### <a name="group-at-a-subscription-scope"></a>訂閱範圍內的組

要添加組的角色指派，請使用 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。 有關如何獲取組的物件識別碼 的資訊，請參閱[獲取物件識別碼](#get-object-ids)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

下面的示例將*讀取器*角色指派給訂閱範圍內的 Id 22222222-2222-2222-2222-222222222222222 的*Ann Mack 團隊*組。

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000
```

### <a name="group-at-a-resource-scope"></a>資源作用域的組

要添加組的角色指派，請使用 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。 有關如何獲取組的物件識別碼 的資訊，請參閱[獲取物件識別碼](#get-object-ids)。

下面的示例將*虛擬機器參與者*角色指派給 Ann Mack*團隊*組，該組 ID 為 ID 2222222-2222-2222-2222-2222222222，該工作組位於名為*Pharma-sales-project-網路*的虛擬網路的資源範圍內。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="application-at-a-resource-group-scope"></a>資源組作用域中的應用程式

要為應用程式添加角色指派，請使用[az 角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。 有關如何獲取應用程式的物件識別碼 的資訊，請參閱[獲取物件識別碼。](#get-object-ids)

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --resource-group <resource_group>
```

下面的示例將*虛擬機器參與者*角色指派給*在醫藥銷售*資源組範圍內的物件識別碼 4444444-4444-4444-4444444444 的應用程式。

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales
```

### <a name="user-at-a-subscription-scope"></a>訂閱範圍內的使用者

要在訂閱範圍內為使用者添加角色指派，請使用 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。 要獲取訂閱 ID，可以在 Azure 門戶中的 **"訂閱"** 邊欄選項卡上找到它，也可以使用[az 帳戶清單](/cli/azure/account#az-account-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --subscription <subscription_name_or_id>
```

下面的示例將*讀取器*角色指派給訂閱作用域中的*annm\@example.com*使用者。

```azurecli
az role assignment create --role "Reader" --assignee annm@example.com --subscription 00000000-0000-0000-0000-000000000000
```

### <a name="user-at-a-management-group-scope"></a>管理組作用域的使用者

要為管理組作用域中的使用者添加角色指派，請使用 az[角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)。 要獲取管理組 ID，可以在 Azure 門戶中的 **"管理組**"邊欄選項卡上找到它，也可以使用[az 帳戶管理組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

下面的示例將*計費讀取器*角色指派給管理組作用域中的*\@alain example.com*使用者。

```azurecli
az role assignment create --role "Billing Reader" --assignee alain@example.com --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

### <a name="new-service-principal"></a>新的服務主體

如果創建新的服務主體並立即嘗試為該服務主體分配角色，則在某些情況下，該角色指派可能會失敗。 例如，如果使用腳本創建新的託管標識，然後嘗試將角色指派給該服務主體，則角色指派可能會失敗。 此失敗的原因可能是複寫延遲。 服務主體在一個區域中創建;服務主體在一個區域中創建。但是，角色指派可能發生在尚未複製服務主體的不同區域中。 要解決此問題，應在創建角色指派時指定主體類型。

要添加角色指派，請使用[az 角色指派創建](/cli/azure/role/assignment#az-role-assignment-create)，指定`--assignee-object-id`的值，然後設置為`--assignee-principal-type``ServicePrincipal`。

```azurecli
az role assignment create --role <role_name_or_id> --assignee-object-id <assignee_object_id> --assignee-principal-type <assignee_principal_type> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

以下示例將*虛擬機器參與者*角色指派給*製藥*銷售資源組作用域的*msi 測試*託管標識：

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 33333333-3333-3333-3333-333333333333 --assignee-principal-type ServicePrincipal --resource-group pharma-sales
```

## <a name="remove-a-role-assignment"></a>移除角色指派

在 RBAC 中，若要移除存取權，您可以使用 [az 角色指派刪除](/cli/azure/role/assignment#az-role-assignment-delete)來移除角色指派:

```azurecli
az role assignment delete --assignee <assignee> --role <role_name_or_id> --resource-group <resource_group>
```

以下示例從*製藥銷售*資源組中的 *"餅\@contoso.com*使用者中刪除*虛擬機器參與者*角色指派：

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales
```

下面的示例在訂閱範圍內從 ID 2222222-2222-2222-2222-2222-222222222222 從*Ann Mack 團隊*組中刪除*Reader*角色。 有關如何獲取組的物件識別碼 的資訊，請參閱[獲取物件識別碼](#get-object-ids)。

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --subscription 00000000-0000-0000-0000-000000000000
```

下面的示例從管理組作用域的*alain\@example.com*使用者中刪除*計費讀取器*角色。 要獲取管理組的 ID，可以使用[az 帳戶管理組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment delete --assignee alain@example.com --role "Billing Reader" --scope /providers/Microsoft.Management/managementGroups/marketing-group
```

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure CLI 列出角色指派](role-assignments-list-cli.md)
- [使用 Azure CLI 管理 Azure 資源和資源群組](../azure-resource-manager/cli-azure-resource-manager.md)
