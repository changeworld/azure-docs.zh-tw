---
title: 了解 Azure 資源的拒絕指派
description: 了解 Azure 資源之角色型存取控制 (RBAC) 中的拒絕指派。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/26/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: db249ccde1026cd468a1c30942891119482697ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372475"
---
# <a name="understand-deny-assignments-for-azure-resources"></a>了解 Azure 資源的拒絕指派

與角色指派相同，「拒絕指派」** 也會基於拒絕存取權的目的來附加一組在特定範圍內拒絕使用者、群組或服務主體的動作。 拒絕指派會封鎖使用者執行指定的 Azure 資源動作，即使角色指派授予他們存取權也一樣。

此文章說明如何定義拒絕指派。

## <a name="how-deny-assignments-are-created"></a>如何創建拒絕分配

拒絕分配由 Azure 創建和管理，以保護資源。 Azure 藍圖和 Azure 託管應用使用拒絕分配來保護系統管理的資源。 Azure 藍圖和 Azure 託管應用是創建拒絕分配的唯一方法。 不能直接創建自己的拒絕分配。 有關藍圖如何使用拒絕分配來鎖定資源的詳細資訊，請參閱瞭解 Azure[藍圖中的資源鎖定](../governance/blueprints/concepts/resource-locking.md)。

> [!NOTE]
> 不能直接創建自己的拒絕分配。

## <a name="compare-role-assignments-and-deny-assignments"></a>比較角色指派和拒絕分配

拒絕分配遵循與角色指派類似的模式，但也有一些差異。

| 功能 | 角色指派 | 拒絕分配 |
| --- | --- | --- |
| 授與存取權 | :heavy_check_mark: |  |
| 拒絕存取 |  | :heavy_check_mark: |
| 可以直接創建 | :heavy_check_mark: |  |
| 在作用域中應用 | :heavy_check_mark: | :heavy_check_mark: |
| 排除主體 |  | :heavy_check_mark: |
| 防止繼承到子作用域 |  | :heavy_check_mark: |
| 應用於[經典訂閱管理員](rbac-and-directory-admin-roles.md)分配 |  | :heavy_check_mark: |

## <a name="deny-assignment-properties"></a>拒絕指派屬性

 拒絕指派有下列屬性：

> [!div class="mx-tableFixed"]
> | 屬性 | 必要 | 類型 | 描述 |
> | --- | --- | --- | --- |
> | `DenyAssignmentName` | 是 | String | 拒絕指派的顯示名稱。 名稱在指定範圍內必須是唯一的。 |
> | `Description` | 否 | String | 拒絕指派的描述。 |
> | `Permissions.Actions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的管理作業。 |
> | `Permissions.NotActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的管理作業。 |
> | `Permissions.DataActions` | 至少一個 Actions 或一個 DataActions | String[] | 一個字串陣列，指定拒絕指派要封鎖存取權的資料作業。 |
> | `Permissions.NotDataActions` | 否 | String[] | 一個字串陣列，指定要從拒絕指派排除的資料作業。 |
> | `Scope` | 否 | String | 一個字串， 指定拒絕指派要套用的範圍。 |
> | `DoNotApplyToChildScopes` | 否 | Boolean | 指定拒絕指派是否要套用到子範圍。 預設值為 false。 |
> | `Principals[i].Id` | 是 | String[] | 要套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 設定為空 GUID `00000000-0000-0000-0000-000000000000` 以代表所有主體。 |
> | `Principals[i].Type` | 否 | String[] | 由主體表示的物件類型的陣列，用於`SystemDefined`表示所有主體。 |
> | `ExcludePrincipals[i].Id` | 否 | String[] | 不套用拒絕指派的 Azure AD 主體物件識別碼 (使用者、群組、服務主體或受控識別) 陣列。 |
> | `ExcludePrincipals[i].Type` | 否 | String[] | 由 ExcludePrincipals[i].Id 代表的物件類型陣列。 |
> | `IsSystemProtected` | 否 | Boolean | 指定此拒絕指派是否由 Azure 建立且無法編輯或刪除。 目前，所有拒絕指派都受系統保護。 |

## <a name="the-all-principals-principal"></a>校長

為了支援拒絕分配，引入了名為 *"所有主體"* 的系統定義主體。 此主體代表 Azure AD 目錄中的所有使用者、群組、服務主體和受控識別。 若主體識別碼是零值 GUID `00000000-0000-0000-0000-000000000000` 且主體類型是 `SystemDefined`，則主體代表所有主體。 在 Azure PowerShell 輸出中，所有主體如下所示：

```azurepowershell
Principals              : {
                          DisplayName:  All Principals
                          ObjectType:   SystemDefined
                          ObjectId:     00000000-0000-0000-0000-000000000000
                          }
```

可以與所有`ExcludePrincipals`主體組合以拒絕除某些使用者之外的所有主體。 所有主體都具有以下約束：

- 只能在 `Principals` 中使用，而無法在 `ExcludePrincipals` 中使用。
- `Principals[i].Type` 必須設為 `SystemDefined`。

## <a name="next-steps"></a>後續步驟

* [教程：使用 Azure 藍圖資源鎖保護新資源](../governance/blueprints/tutorials/protect-new-resources.md)
* [使用 Azure 門戶列出 Azure 資源的拒絕分配](deny-assignments-portal.md)
