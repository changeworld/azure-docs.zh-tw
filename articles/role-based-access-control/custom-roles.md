---
title: Azure 自訂角色-Azure RBAC
description: 瞭解如何使用 Azure 角色型存取控制（Azure RBAC）來建立 Azure 自訂角色，以對 Azure 資源進行更細緻的存取管理。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5030fb50313e1db2173990c55930c22fdf58f559
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/03/2020
ms.locfileid: "82734785"
---
# <a name="azure-custom-roles"></a>Azure 自訂角色

> [!IMPORTANT]
> 將管理群組新增至`AssignableScopes`目前為預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 內建角色](built-in-roles.md)不符合您組織的特定需求，您可以建立自己的自訂角色。 就像內建角色一樣，您可以在管理群組、訂用帳戶和資源群組範圍中，將自訂角色指派給使用者、群組和服務主體。

您可以在信任相同 Azure AD 目錄的訂用帳戶之間共用自訂角色。 每個目錄有**5000**個自訂角色的限制。 （針對 Azure 德國和 Azure 中國的世紀，限制為2000個自訂角色）。您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 或 REST API 來建立自訂角色。

## <a name="custom-role-example"></a>自訂角色範例

以下顯示自訂角色以 JSON 格式顯示時的外觀。 此自訂角色可用於監視和重新啟動虛擬機器。

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
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
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

建立自訂角色時，它會以橙色資源圖示顯示在 Azure 入口網站中。

![自訂角色圖示](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>建立自訂角色的步驟

1. 決定您要如何建立自訂角色

    您可以使用[Azure 入口網站](custom-roles-portal.md)、 [Azure PowerShell](custom-roles-powershell.md)、 [Azure CLI](custom-roles-cli.md)或[REST API](custom-roles-rest.md)來建立自訂角色。

1. 判斷您所需的權限

    在建立自訂角色時，您必須知道可用來定義權限的資源提供者作業。 若要查看作業清單，請參閱[Azure Resource Manager 資源提供者作業](resource-provider-operations.md)。 您會將作業新增至`Actions` [角色定義](role-definitions.md)的或`NotActions`屬性。 如果您有資料作業，則會將其加入至`DataActions`或`NotDataActions`屬性。

1. 建立自訂角色

    一般而言，您可以從使用現有的內建角色開始，然後針對您的需求進行修改。 接著，您可以使用 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 或 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 命令來建立自訂角色。 若要建立自訂角色，您必須擁有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。

1. 測試自訂角色

    一旦具有自訂角色，您必須測試它來驗證是否如預期般運作。 稍後如需進行調整，您可以更新自訂角色。

如需如何建立自訂角色的逐步教學課程，請參閱[教學課程：使用 Azure PowerShell 建立 azure 自訂角色](tutorial-custom-role-powershell.md)或教學課程[：使用 Azure CLI 建立 azure 自訂角色](tutorial-custom-role-cli.md)。

## <a name="custom-role-properties"></a>自訂角色屬性

自訂角色具有下列屬性。

| 屬性 | 必要 | 類型 | 說明 |
| --- | --- | --- | --- |
| `Name` | 是 | String | 自訂角色的顯示名稱。 當角色定義是管理群組或訂用帳戶層級資源時，角色定義可以用於多個共用相同 Azure AD 目錄的訂用帳戶。 此顯示名稱在 Azure AD 目錄範圍中必須是唯一的。 可以包含字母、數字、空格和特殊字元。 字元數目上限是 128。 |
| `Id` | 是 | String | 自訂角色的唯一識別碼。 針對 Azure PowerShell 和 Azure CLI，當您建立新角色時，會自動產生這個識別碼。 |
| `IsCustom` | 是 | String | 表示這是否為自訂角色。 若為自訂角色，請設定為 `true`。 |
| `Description` | 是 | String | 自訂角色的描述。 可以包含字母、數字、空格和特殊字元。 字元數目上限是 1024。 |
| `Actions` | 是 | String[] | 字串陣列，指定角色允許執行的管理作業。 如需詳細資訊，請參閱 [Actions](role-definitions.md#actions)。 |
| `NotActions` | 否 | String[] | 字串陣列，指定從所允許 `Actions` 中排除的管理作業。 如需詳細資訊，請參閱 [NotActions](role-definitions.md#notactions)。 |
| `DataActions` | 否 | String[] | 字串陣列，指定角色允許對物件內資料執行的管理作業。 如果您使用`DataActions`建立自訂角色，該角色就無法在管理群組範圍中指派。 如需詳細資訊，請參閱[DataActions](role-definitions.md#dataactions)。 |
| `NotDataActions` | 否 | String[] | 字串陣列，指定從所允許 `DataActions` 中排除的資料作業。 如需詳細資訊，請參閱[NotDataActions](role-definitions.md#notdataactions)。 |
| `AssignableScopes` | 是 | String[] | 字串陣列，指定自訂角色可用於指派的範圍。 您只能在自訂角色中`AssignableScopes`定義一個管理群組。 將管理群組新增至`AssignableScopes`目前為預覽狀態。 如需詳細資訊，請參閱 [AssignableScopes](role-definitions.md#assignablescopes)。 |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>誰可以建立、刪除、更新或檢視自訂角色

就像內建角色一樣，`AssignableScopes` 屬性會指定角色可用於指派的範圍。 自訂角色的 `AssignableScopes` 屬性也會控制誰可以建立、刪除、更新或檢視自訂角色。

| Task | 作業 | 描述 |
| --- | --- | --- |
| 建立/刪除自訂角色 | `Microsoft.Authorization/ roleDefinitions/write` | 獲得授權可對自訂角色的所有 `AssignableScopes` 執行此作業的使用者，可以建立 (或刪除) 用於這些範圍的自訂角色。 例如，管理群組、訂用帳戶和資源群組的[擁有](built-in-roles.md#owner)者和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。 |
| 更新自訂角色 | `Microsoft.Authorization/ roleDefinitions/write` | 獲得授權可對自訂角色的所有 `AssignableScopes` 執行此作業的使用者，可以在這些範圍中更新自訂角色。 例如，管理群組、訂用帳戶和資源群組的[擁有](built-in-roles.md#owner)者和[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。 |
| 檢視自訂角色 | `Microsoft.Authorization/ roleDefinitions/read` | 獲得授權可在範圍中執行此作業的使用者，可以檢視可指派給該範圍的自訂角色。 所有內建角色都允許指派自訂角色。 |

## <a name="custom-role-limits"></a>自訂角色限制

下列清單描述自訂角色的限制。

- 每個目錄最多可以有**5000**個自訂角色。
- Azure 德國和 Azure 中國世紀每個目錄最多可以有2000個自訂角色。
- 您無法將`AssignableScopes`設定為根範圍（`"/"`）。
- 您只能在自訂角色中`AssignableScopes`定義一個管理群組。 將管理群組新增至`AssignableScopes`目前為預覽狀態。
- 無法在管理`DataActions`群組範圍指派具有的自訂角色。
- Azure Resource Manager 不會驗證管理群組是否存在於角色定義的可指派範圍中。

如需有關自訂角色和管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。

## <a name="next-steps"></a>後續步驟
- [使用 Azure 入口網站建立或更新 Azure 自訂角色](custom-roles-portal.md)
- [瞭解 Azure 角色定義](role-definitions.md)
- [針對 Azure RBAC 進行疑難排解](troubleshooting.md)
