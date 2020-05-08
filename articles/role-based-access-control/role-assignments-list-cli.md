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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f4b635d6867c36b8b0f385320e3720bea41b54d1
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735737"
---
# <a name="list-azure-role-assignments-using-azure-cli"></a>使用 Azure CLI 列出 Azure 角色指派

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]本文說明如何使用 Azure CLI 列出角色指派。

> [!NOTE]
> 如果您的組織具有外包管理功能給使用[Azure 委派資源管理](../lighthouse/concepts/azure-delegated-resource-management.md)的服務提供者，該服務提供者所授權的角色指派將不會顯示在這裡。

## <a name="prerequisites"></a>Prerequisites

- Azure Cloud Shell 或[Azure CLI](/cli/azure) [中的 Bash](/azure/cloud-shell/overview)

## <a name="list-role-assignments-for-a-user"></a>列出使用者的角色指派

若要列出特定使用者的角色指派，請使用 [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --assignee <assignee>
```

根據預設，只會顯示目前訂用帳戶的角色指派。 若要查看目前訂用帳戶和以下的角色指派， `--all`請新增參數。 若要查看繼承的`--include-inherited`角色指派，請新增參數。

下列範例會列出直接指派給*patlong\@contoso.com*使用者的角色指派：

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

若要列出存在於資源群組範圍的角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)：

```azurecli-interactive
az role assignment list --resource-group <resource_group>
```

下列範例會列出*醫藥-sales*資源群組的角色指派：

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

若要列出訂用帳戶範圍中的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得訂用帳戶識別碼，您可以在 Azure 入口網站的 [**訂閱**] 分頁上找到它，或者您可以使用[az account list](/cli/azure/account#az-account-list)。

```azurecli-interactive
az role assignment list --subscription <subscription_name_or_id>
```

範例：

```azurecli-interactive
az role assignment list --subscription 00000000-0000-0000-0000-000000000000 --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-management-group"></a>列出管理群組的角色指派

若要列出管理群組範圍的所有角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。 若要取得管理群組識別碼，您可以在 [**管理群組**] 分頁的 [Azure 入口網站中找到，也可以使用[az 帳戶管理-群組清單](/cli/azure/account/management-group#az-account-management-group-list)。

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/<group_id>
```

範例：

```azurecli-interactive
az role assignment list --scope /providers/Microsoft.Management/managementGroups/marketing-group --output json | jq '.[] | {"principalName":.principalName, "roleDefinitionName":.roleDefinitionName, "scope":.scope}'
```

## <a name="list-role-assignments-for-a-managed-identity"></a>列出受控識別的角色指派

1. 取得系統指派或使用者指派受控識別的物件識別碼。

    若要取得使用者指派受控識別的物件識別碼，您可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)或[az identity list](/cli/azure/identity#az-identity-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<name>" --query [].objectId --output tsv
    ```

    若要取得系統指派之受控識別的物件識別碼，您可以使用[az ad sp list](/cli/azure/ad/sp#az-ad-sp-list)。

    ```azurecli-interactive
    az ad sp list --display-name "<vmname>" --query [].objectId --output tsv
    ```

1. 若要列出角色指派，請使用[az role 指派 list](/cli/azure/role/assignment#az-role-assignment-list)。

    根據預設，只會顯示目前訂用帳戶的角色指派。 若要查看目前訂用帳戶和以下的角色指派， `--all`請新增參數。 若要查看繼承的`--include-inherited`角色指派，請新增參數。

    ```azurecli-interactive
    az role assignment list --assignee <objectid>
    ```

## <a name="next-steps"></a>後續步驟

- [使用 Azure CLI 新增或移除 Azure 角色指派](role-assignments-cli.md)
