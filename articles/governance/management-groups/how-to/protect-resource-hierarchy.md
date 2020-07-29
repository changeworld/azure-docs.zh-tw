---
title: 如何保護您的資源階層 - Azure 治理
description: 了解如何使用包含設定預設管理群組的階層設定來保護您的資源階層。
ms.date: 05/21/2020
ms.topic: conceptual
ms.openlocfilehash: 2f8d3d7ff85552fe18a49137450aefe91d0d02c9
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87366767"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>如何保護您的資源階層

您的資源、資源群組、訂用帳戶、管理群組和租用戶共同組成您的資源階層。 根管理群組中的設定（例如 Azure 自訂角色或 Azure 原則原則指派）可能會影響您資源階層中的每個資源。 請務必保護資源階層，避免受到可能對所有資源造成負面影響的變更。

管理群組現在具有階層設定，可讓租用戶系統管理員控制這些行為。 本文涵蓋每一個可用的階層設定，以及如何加以設定。

## <a name="rbac-permissions-for-hierarchy-settings"></a>階層設定的 RBAC 權限

設定任何階層設定需要在根管理群組上進行以下兩個 RBAC 作業：

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

這些作業僅允許使用者讀取及更新階層設定。 這些作業不會提供對管理群組階層或階層中資源的任何其他存取權。 這兩項作業都可在 Azure 內建角色階層的 [設定] [**系統管理員**] 中取得。

## <a name="setting---default-management-group"></a>設定 - 預設管理群組

根據預設，租用戶內所新增的新訂用帳戶會新增做為根管理群組的成員。 如果原則指派、角色型存取控制 (RBAC) 和其他治理建構已指派給根管理群組，則會立即影響這些新的訂用帳戶。 基於這個理由，許多組織都不會在根管理群組套用這些建構，即使這是所要指派的位置也一樣。 在其他情況下，新的訂用帳戶需要一組限制較嚴格的控制項，但不應指派給所有訂用帳戶。 這項設定支援兩種使用案例。

藉由允許定義新訂用帳戶的預設管理群組，可以在根管理群組中套用整個組織的治理建構，並可定義具有原則指派或 RBAC 指派且較適合新訂用帳戶的個別管理群組。

若要進行此設定，會呼叫[階層設定](/rest/api/resources/hierarchysettings) REST API 端點。 若要這樣做，請使用下列 REST API URI 與主體格式。 將 `{rootMgID}` 取代為根管理群組的識別碼，並將 `{defaultGroupID}` 取代為管理群組的識別碼，以成為預設管理群組：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
      "properties": {
          "defaultManagementGroup": "/providers/Microsoft.Management/managementGroups/{defaultGroupID}"
      }
  }
  ```

若要將預設管理群組設定回根管理群組，請使用相同的端點，並將 **defaultManagementGroup** 設定為 `/providers/Microsoft.Management/managementGroups/{rootMgID}` 的值。

## <a name="setting---require-authorization"></a>設定 - 需要授權

根據預設，任何使用者都可以在租用戶內建立新的管理群組。 租用戶的管理員可能只想要將這些權限提供給特定使用者，以維持管理群組階層中的一致性和符合性。 啟用時，使用者必須在根管理群組上進行 `Microsoft.Management/managementGroups/write` 作業，才能建立新的子管理群組。

若要進行此設定，會呼叫[階層設定](/rest/api/resources/hierarchysettings) REST API 端點。 若要這樣做，請使用下列 REST API URI 與主體格式。 這個值是_布林值_，因此，請為此值提供 **true** 或 **false**。 **true** 的值會啟用這個保護管理群組階層的方法：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{rootMgID}/settings/default?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
      "properties": {
          "requireAuthorizationForGroupCreation": true
      }
  }
  ```

若要關閉設定，請使用相同的端點，並將 **requireAuthorizationForGroupCreation** 設定為 **false** 的值。

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](../create.md)
- [如何變更、刪除或管理您的管理群組](../manage.md)
