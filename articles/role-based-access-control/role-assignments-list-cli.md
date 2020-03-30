---
title: 使用 Azure RBAC 和 Azure CLI 列出角色指派
description: 瞭解如何確定使用者、組、服務主體或託管標識可以使用基於 Azure 角色的存取控制 （RBAC） 和 Azure CLI 訪問哪些資源。
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
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 5716e7bb89d017866bd1575256e2d119bb7acbe5
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385056"
---
# <a name="list-role-assignments-using-azure-rbac-and-azure-cli"></a>使用 Azure RBAC 和 Azure CLI 列出角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文介紹如何使用 Azure CLI 列出角色指派。

> [!NOTE]
> 如果您的組織將管理功能外包給使用[Azure 委派資源的](../lighthouse/concepts/azure-delegated-resource-management.md)服務提供者，則該服務提供者授權的角色指派將不會在此處顯示。

## <a name="prerequisites"></a>Prerequisites

- [在 Azure 雲外殼](/azure/cloud-shell/overview)或[Azure CLI 中](/cli/azure)打舍

## <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --assignee <assignee>
```

預設情況下，僅顯示當前訂閱的角色指派。 要查看當前訂閱和下面的角色指派，添加參數`--all`。 要查看繼承的角色指派，添加參數`--include-inherited`。

以下示例列出了直接分配給*patlong\@contoso.com*使用者的角色指派：

```azurecli-interactive
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

要列出資源組作用域中存在的角色指派，請使用 az[角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

以下示例列出了*製藥銷售*資源組的角色指派：

```azurecli-interactive
az role assignment list --resource-group pharma-sales --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

```
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
}

...
```

## <a name="list-role-assignments-for-a-subscription"></a>列出訂用帳戶的角色指派

要在訂閱範圍內列出所有角色指派，請使用[az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)。 要獲取訂閱 ID，可以在 Azure 門戶中的 **"訂閱"** 邊欄選項卡上找到它，也可以使用[az 帳戶清單](/cli/azure/account#az-account-list)。

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

範例：

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理群組的角色指派

要列出管理組作用域中的所有角色指派，請使用[az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)。 要獲取管理組 ID，可以在 Azure 門戶中的 **"管理組**"邊欄選項卡上找到它，也可以使用[az 帳戶管理組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

範例：

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出託管標識的角色指派

1. 獲取系統分配或使用者分配的託管標識的物件識別碼。

    要獲取使用者分配的託管標識的物件識別碼，可以使用 az ad [sp 清單](/cli/azure/ad/sp#az-ad-sp-list)或[az 識別欄位表](/cli/azure/identity#az-identity-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    要獲取系統分配的託管標識的物件識別碼，可以使用 az ad [sp 清單](/cli/azure/ad/sp#az-ad-sp-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 要列出角色指派，請使用[az 角色指派清單](/cli/azure/role/assignment#az-role-assignment-list)。

    預設情況下，僅顯示當前訂閱的角色指派。 要查看當前訂閱和下面的角色指派，添加參數`--all`。 要查看繼承的角色指派，添加參數`--include-inherited`。

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure RBAC 和 Azure CLI 添加或刪除角色指派](role-assignments-cli.md)
