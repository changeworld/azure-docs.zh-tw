---
title: 使用 Azure CLI 創建或更新 Azure 資源的自訂角色 |微軟文檔
description: 瞭解如何使用 Azure CLI 使用基於角色的存取控制 （RBAC） 列出、創建、更新或刪除自訂角色。
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
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 44676f7b92c2bcd30612295840054ab2f0c0cf12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062219"
---
# <a name="create-or-update-custom-roles-for-azure-resources-using-azure-cli"></a>使用 Azure CLI 為 Azure 資源創建或更新自訂角色

> [!IMPORTANT]
> 將管理組添加到`AssignableScopes`當前處於預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 資源的內置角色](built-in-roles.md)不能滿足組織的特定需求，則可以創建自己的自訂角色。 本文介紹如何使用 Azure CLI 列出、創建、更新或刪除自訂角色。

有關如何創建自訂角色的分步教程，請參閱[教程：使用 Azure CLI 為 Azure 資源創建自訂角色](tutorial-custom-role-cli.md)。

## <a name="prerequisites"></a>Prerequisites

若要建立自訂角色，您需要：

- 建立自訂角色的權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取管理員](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) 或 [Azure CLI](/cli/azure/install-azure-cli)

## <a name="list-custom-roles"></a>列出自訂角色

若要列出可供指派的自訂角色，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。 下列範例會列出目前訂用帳戶中的所有自訂角色。

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "roleType":.roleType}'
```

```azurecli
az role definition list --output json | jq '.[] | if .roleType == "CustomRole" then {"roleName":.roleName, "roleType":.roleType} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}
{
  "roleName": "Virtual Machine Operator",
  "type": "CustomRole"
}

...
```

## <a name="list-a-custom-role-definition"></a>列出自訂角色定義

要列出自訂角色定義，請使用[az 角色定義清單](/cli/azure/role/definition#az-role-definition-list)。 這是用於內置角色的命令。

```azurecli
az role definition list --name <role_name>
```

以下示例列出了*虛擬機器操作員*角色定義：

```azurecli
az role definition list --name "Virtual Machine Operator"
```

```Output
[
  {
    "assignableScopes": [
      "/subscriptions/11111111-1111-1111-1111-111111111111"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/00000000-0000-0000-0000-000000000000",
    "name": "00000000-0000-0000-0000-000000000000",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

以下示例僅列出了*虛擬機器操作員*角色的操作：

```azurecli
az role definition list --name "Virtual Machine Operator" --output json | jq '.[] | .permissions[0].actions'
```

```Output
[
  "Microsoft.Storage/*/read",
  "Microsoft.Network/*/read",
  "Microsoft.Compute/*/read",
  "Microsoft.Compute/virtualMachines/start/action",
  "Microsoft.Compute/virtualMachines/restart/action",
  "Microsoft.Authorization/*/read",
  "Microsoft.ResourceHealth/availabilityStatuses/read",
  "Microsoft.Resources/subscriptions/resourceGroups/read",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Insights/diagnosticSettings/*",
  "Microsoft.Support/*"
]
```

## <a name="create-a-custom-role"></a>建立自訂角色

若要建立自訂角色，請使用 [az role definition create](/cli/azure/role/definition#az-role-definition-create)。 角色定義可以是 JSON 描述或包含 JSON 描述的檔案路徑。

```azurecli
az role definition create --role-definition <role_definition>
```

下列範例會建立一個名為 Virtual Machine Operator** 的自訂角色。 這個自訂角色會指派 Microsoft.Compute**、Microsoft.Storage** 和 Microsoft.Network** 資源提供者之所有讀取作業的存取權，以及指派啟動、重新啟動和監視虛擬機器的存取權。 這個自訂角色可用於兩個訂用帳戶中。 這個範例使用 JSON 檔案做為輸入。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

## <a name="update-a-custom-role"></a>更新自訂角色

若要更新自訂角色，請先使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list) 來擷取角色定義。 接著，對角色定義進行想要的變更。 最後，使用 [az role definition update](/cli/azure/role/definition#az-role-definition-update)儲存更新的角色定義。

```azurecli
az role definition update --role-definition <role_definition>
```

下面的示例將*Microsoft.Insights/診斷設置/* 操作添加到虛擬機器`Actions`操作員自訂角色中，並將`AssignableScopes`管理組*Virtual Machine Operator*添加到其中。 將管理組添加到`AssignableScopes`當前處於預覽狀態。

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333",
    "/providers/Microsoft.Management/managementGroups/marketing-group"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

## <a name="delete-a-custom-role"></a>刪除自訂角色

若要刪除自訂角色，請使用 [az role definition delete](/cli/azure/role/definition#az-role-definition-delete)。 若要指定要刪除的角色，請使用角色名稱或角色識別碼。 若要決定角色識別碼，請使用 [az role definition list](/cli/azure/role/definition#az-role-definition-list)。

```azurecli
az role definition delete --name <role_name or role_id>
```

下面的示例刪除*虛擬機器操作員*自訂角色。

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>後續步驟

- [教程：使用 Azure CLI 為 Azure 資源創建自訂角色](tutorial-custom-role-cli.md)
- [適用於 Azure 資源的自訂角色](custom-roles.md)
- [Azure 資源管理器資源管理器提供程式操作](resource-provider-operations.md)