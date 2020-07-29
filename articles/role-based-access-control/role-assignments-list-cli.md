---
title: 使用 Azure CLI 列出 Azure 角色指派-Azure RBAC
description: 瞭解如何使用 Azure CLI 和 Azure 角色型存取控制（Azure RBAC）來判斷哪些資源的使用者、群組、服務主體或受控識別可以存取。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/17/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 9087722b54a805a0c217c236263bdcb39e5456e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84986256"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 列出 Azure 角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文說明如何使用 Azure CLI 列出角色指派。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

## <a name="prerequisites"></a>必要條件

- Azure Cloud Shell 或[Azure CLI](/cli/azure) [中的 Bash](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --assignee {assignee}
```

根據預設，只會顯示目前訂用帳戶的角色指派。 若要查看目前訂用帳戶和以下的角色指派，請新增 `--all` 參數。 若要查看繼承的角色指派，請新增 `--include-inherited` 參數。

下列範例會列出直接指派給*patlong \@ contoso.com*使用者的角色指派：

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  }
]
```

## <a name="list-role-assignments-for-a-resource-group"></a>列出資源群組的角色指派

若要列出存在於資源群組範圍的角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli
az role assignment list --resource-group {resourceGroup}
```

下列範例會列出*醫藥-sales*資源群組的角色指派：

```azurecli
az role assignment list --resource-group pharma-sales --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Backup Operator",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  {
    "principalName": "patlong@contoso.com",
    "roleDefinitionName": "Virtual Machine Contributor",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales"
  },
  
  ...

]
```

## <a name="list-role-assignments-for-a-subscription"></a>列出訂用帳戶的角色指派

若要列出訂用帳戶範圍中的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli
az role assignment list --subscription {subscriptionNameOrId}
```

範例：

```azurecli
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "Subscription Admins",
    "roleDefinitionName": "Owner",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/subscriptions/00000000-0000-0000-0000-000000000000"
  },

  ...

]
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理群組的角色指派

若要列出管理群組範圍的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得管理群組識別碼，您可以在 [**管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/{groupId}
```

範例：

```azurecli
az role assignment list --scope /providers/Microsoft.Management/managementGroups/sales-group --output json --query '[].{principalName:principalName, roleDefinitionName:roleDefinitionName, scope:scope}'
```

```json
[
  {
    "principalName": "admin@contoso.com",
    "roleDefinitionName": "Owner",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  },
  {
    "principalName": "alain@contoso.com",
    "roleDefinitionName": "Reader",
    "scope": "/providers/Microsoft.Management/managementGroups/sales-group"
  }
]
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出受控識別的角色指派

1. 取得系統指派或使用者指派受控識別的物件識別碼。

    若要取得使用者指派受控識別的物件識別碼，您可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)或[az identity list](/cli/azure/identity#az-identity-list)。

    ```azurecli
    az ad sp list --display-name "{name}" --query [].objectId --output tsv
    ```

    若要取得系統指派之受控識別的物件識別碼，您可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)。

    ```azurecli
    az ad sp list --display-name "{vmname}" --query [].objectId --output tsv
    ```

1. 若要列出角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。

    根據預設，只會顯示目前訂用帳戶的角色指派。 若要查看目前訂用帳戶和以下的角色指派，請新增 `--all` 參數。 若要查看繼承的角色指派，請新增 `--include-inherited` 參數。

    ```azurecli
    az role assignment list --assignee {objectId}
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 新增或移除 Azure 角色指派](role-assignments-cli.md)
