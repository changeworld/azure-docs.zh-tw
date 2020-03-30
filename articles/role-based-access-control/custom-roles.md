---
title: 適用於 Azure 資源的自訂角色 | Microsoft Docs
description: 了解如何使用角色型存取控制 (RBAC) 建立自訂角色，以對 Azure 資源進行微調存取管理。
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
ms.date: 03/19/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9454962e210781559f2fdceb1c36f499c4ae8ff7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062177"
---
# <a name="custom-roles-for-azure-resources"></a>適用於 Azure 資源的自訂角色

> [!IMPORTANT]
> 將管理組添加到`AssignableScopes`當前處於預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。
> 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

如果[Azure 資源的內置角色](built-in-roles.md)不能滿足組織的特定需求，則可以創建自己的自訂角色。 與內置角色一樣，您可以將自訂角色指派給管理組、訂閱和資源組作用域中的使用者、組和服務主體。

自訂角色可以在信任同一 Azure AD 目錄的訂閱之間共用。 每個目錄有**5，000**個自訂角色的限制。 （對於 Azure 德國和 Azure 中國 21Vianet，限制為 2，000 個自訂角色。可以使用 Azure 門戶（預覽）、Azure PowerShell、Azure CLI 或 REST API 創建自訂角色。

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

1. 決定如何創建自訂角色

    可以使用[Azure 門戶](custom-roles-portal.md)（預覽[）、Azure PowerShell、Azure](custom-roles-powershell.md) [CLI](custom-roles-cli.md)或[REST API](custom-roles-rest.md)創建自訂角色。

1. 判斷您所需的權限

    在建立自訂角色時，您必須知道可用來定義權限的資源提供者作業。 要查看動作清單，請參閱[Azure 資源管理器資源管理器提供程式操作](resource-provider-operations.md)。 您將操作添加到`Actions`[角色定義的](role-definitions.md)或`NotActions`屬性。 如果您有資料操作，則將這些操作添加到 或`DataActions``NotDataActions`屬性。

1. 建立自訂角色

    一般而言，您可以從使用現有的內建角色開始，然後針對您的需求進行修改。 接著，您可以使用 [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) 或 [az role definition create](/cli/azure/role/definition#az-role-definition-create) 命令來建立自訂角色。 若要建立自訂角色，您必須擁有所有 `AssignableScopes` 的 `Microsoft.Authorization/roleDefinitions/write` 權限，例如[擁有者](built-in-roles.md#owner)或[使用者存取系統管理員](built-in-roles.md#user-access-administrator)。

1. 測試自訂角色

    一旦具有自訂角色，您必須測試它來驗證是否如預期般運作。 稍後如需進行調整，您可以更新自訂角色。

如需如何建立自訂角色的逐步教學課程，請參閱[教學課程：使用 Azure PowerShell 建立自訂角色](tutorial-custom-role-powershell.md)或[教學課程：使用 Azure CLI 建立自訂角色](tutorial-custom-role-cli.md)。

## <a name="custom-role-properties"></a>自訂角色屬性

自訂角色具有下列屬性。

| 屬性 | 必要 | 類型 | 描述 |
| --- | --- | --- | --- |
| `Name` | 是 | String | 自訂角色的顯示名稱。 雖然角色定義是管理組或訂閱級資源，但角色定義可用於共用同一 Azure AD 目錄的多個訂閱。 此顯示名稱在 Azure AD 目錄範圍中必須是唯一的。 可以包含字母、數字、空格和特殊字元。 字元數目上限是 128。 |
| `Id` | 是 | String | 自訂角色的唯一識別碼。 針對 Azure PowerShell 和 Azure CLI，當您建立新角色時，會自動產生這個識別碼。 |
| `IsCustom` | 是 | String | 表示這是否為自訂角色。 若為自訂角色，請設定為 `true`。 |
| `Description` | 是 | String | 自訂角色的描述。 可以包含字母、數字、空格和特殊字元。 字元數目上限是 1024。 |
| `Actions` | 是 | String[] | 字串陣列，指定角色允許執行的管理作業。 如需詳細資訊，請參閱 [Actions](role-definitions.md#actions)。 |
| `NotActions` | 否 | String[] | 字串陣列，指定從所允許 `Actions` 中排除的管理作業。 如需詳細資訊，請參閱 [NotActions](role-definitions.md#notactions)。 |
| `DataActions` | 否 | String[] | 字串陣列，指定角色允許對物件內資料執行的管理作業。 如果使用 創建自訂角色，`DataActions`則無法在管理組作用域中分配該角色。 有關詳細資訊，請參閱[DataActions](role-definitions.md#dataactions)。 |
| `NotDataActions` | 否 | String[] | 字串陣列，指定從所允許 `DataActions` 中排除的資料作業。 有關詳細資訊，請參閱[不資料操作](role-definitions.md#notdataactions)。 |
| `AssignableScopes` | 是 | String[] | 字串陣列，指定自訂角色可用於指派的範圍。 只能在自訂角色中`AssignableScopes`定義一個管理組。 將管理組添加到`AssignableScopes`當前處於預覽狀態。 如需詳細資訊，請參閱 [AssignableScopes](role-definitions.md#assignablescopes)。 |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>誰可以建立、刪除、更新或檢視自訂角色

就像內建角色一樣，`AssignableScopes` 屬性會指定角色可用於指派的範圍。 自訂角色的 `AssignableScopes` 屬性也會控制誰可以建立、刪除、更新或檢視自訂角色。

| Task | 作業 | 描述 |
| --- | --- | --- |
| 建立/刪除自訂角色 | `Microsoft.Authorization/ roleDefinitions/write` | 獲得授權可對自訂角色的所有 `AssignableScopes` 執行此作業的使用者，可以建立 (或刪除) 用於這些範圍的自訂角色。 例如，管理組、訂閱和資源組[的擁有者](built-in-roles.md#owner)和[使用者訪問管理員](built-in-roles.md#user-access-administrator)。 |
| 更新自訂角色 | `Microsoft.Authorization/ roleDefinitions/write` | 獲得授權可對自訂角色的所有 `AssignableScopes` 執行此作業的使用者，可以在這些範圍中更新自訂角色。 例如，管理組、訂閱和資源組[的擁有者](built-in-roles.md#owner)和[使用者訪問管理員](built-in-roles.md#user-access-administrator)。 |
| 檢視自訂角色 | `Microsoft.Authorization/ roleDefinitions/read` | 獲得授權可在範圍中執行此作業的使用者，可以檢視可指派給該範圍的自訂角色。 所有內建角色都允許指派自訂角色。 |

## <a name="custom-role-limits"></a>自訂角色限制

下面的清單描述了自訂角色的限制。

- 每個目錄最多可以有**5000**個自訂角色。
- Azure 德國和 Azure 中國 21Vianet 每個目錄最多可以具有 2000 個自訂角色。
- 不能設置為`AssignableScopes`根作用域 （`"/"`。
- 只能在自訂角色中`AssignableScopes`定義一個管理組。 將管理組添加到`AssignableScopes`當前處於預覽狀態。
- 無法在管理`DataActions`組作用域中分配 具有的自訂角色。
- Azure 資源管理器不驗證管理組在角色定義的可分配作用域中是否存在。

有關自訂角色和管理組的詳細資訊，請參閱[使用 Azure 管理組組織資源](../governance/management-groups/overview.md#custom-rbac-role-definition-and-assignment)。

## <a name="next-steps"></a>後續步驟
- [使用 Azure 門戶創建或更新 Azure 自訂角色（預覽）](custom-roles-portal.md)
- [了解 Azure 資源的角色定義](role-definitions.md)
- [針對適用於 Azure 資源的 RBAC 進行疑難排解](troubleshooting.md)
