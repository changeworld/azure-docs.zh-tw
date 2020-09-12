---
title: 如何保護您的資源階層 - Azure 治理
description: 了解如何使用包含設定預設管理群組的階層設定來保護您的資源階層。
ms.date: 09/02/2020
ms.topic: conceptual
ms.openlocfilehash: 19d699b54a9979df1030c0f6e294d5a4492f2853
ms.sourcegitcommit: 4feb198becb7a6ff9e6b42be9185e07539022f17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/04/2020
ms.locfileid: "89469774"
---
# <a name="how-to-protect-your-resource-hierarchy"></a>如何保護您的資源階層

您的資源、資源群組、訂用帳戶、管理群組和租用戶共同組成您的資源階層。 根管理群組（例如 Azure 自訂角色或 Azure 原則原則指派）的設定，可能會影響資源階層中的每個資源。 請務必保護資源階層，避免受到可能對所有資源造成負面影響的變更。

管理群組現在具有階層設定，可讓租用戶系統管理員控制這些行為。 本文涵蓋每一個可用的階層設定，以及如何加以設定。

## <a name="rbac-permissions-for-hierarchy-settings"></a>階層設定的 RBAC 權限

設定任何階層設定需要在根管理群組上進行以下兩個 RBAC 作業：

- `Microsoft.Management/managementgroups/settings/write`
- `Microsoft.Management/managementgroups/settings/read`

這些作業僅允許使用者讀取及更新階層設定。 這些作業不會提供對管理群組階層或階層中資源的任何其他存取權。 這兩項作業都可在 Azure 內建角色階層 **設定管理員**中使用。

## <a name="setting---default-management-group"></a>設定 - 預設管理群組

根據預設，租用戶內所新增的新訂用帳戶會新增做為根管理群組的成員。 如果原則指派、角色型存取控制 (RBAC) 和其他治理建構已指派給根管理群組，則會立即影響這些新的訂用帳戶。 基於這個理由，許多組織都不會在根管理群組套用這些建構，即使這是所要指派的位置也一樣。 在其他情況下，新的訂用帳戶需要一組限制較嚴格的控制項，但不應指派給所有訂用帳戶。 這項設定支援兩種使用案例。

藉由允許定義新訂用帳戶的預設管理群組，可在根管理群組中套用整個組織的治理結構，而且可以定義更適合新訂用帳戶的個別管理群組和原則指派或 Azure 角色指派。

### <a name="set-default-management-group-in-portal"></a>在入口網站中設定預設管理群組

若要在 Azure 入口網站中設定這項設定，請遵循下列步驟：

1. 使用搜尋列來搜尋並選取 [管理群組]。

1. 在 [根管理] 群組中，選取管理群組名稱旁邊的 [ **詳細資料** ]。

1. 在 [ **設定**] 底下，選取 [階層 **設定**]。

1. 選取 [ **變更預設管理群組** ] 按鈕。

   > [!NOTE]
   > 如果 [ **變更預設管理群組** ] 按鈕已停用，則表示所要查看的管理群組不是根管理群組，或您的安全性主體沒有變更階層設定的必要許可權。

1. 從階層中選取管理群組，然後使用 [ **選取** ] 按鈕。

### <a name="set-default-management-group-with-rest-api"></a>使用 REST API 設定預設管理群組

若要使用 REST API 來設定此設定，則會呼叫階層 [設定](/rest/api/resources/hierarchysettings) 端點。 若要這樣做，請使用下列 REST API URI 與主體格式。 將 `{rootMgID}` 取代為根管理群組的識別碼，並將 `{defaultGroupID}` 取代為管理群組的識別碼，以成為預設管理群組：

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

### <a name="set-require-authorization-in-portal"></a>在入口網站中設定需要授權

若要在 Azure 入口網站中設定這項設定，請遵循下列步驟：

1. 使用搜尋列來搜尋並選取 [管理群組]。

1. 在 [根管理] 群組中，選取管理群組名稱旁邊的 [ **詳細資料** ]。

1. 在 [ **設定**] 底下，選取 [階層 **設定**]。

1. 切換 [ **需要建立新管理群組的許可權]。** 選項設為 on。

   > [!NOTE]
   > 如果 **需要建立新管理群組的許可權。** 切換已停用，可能是正在查看的管理群組不是根管理群組，或您的安全性主體沒有修改階層設定的必要許可權。

### <a name="set-require-authorization-with-rest-api"></a>Set 需要 REST API 的授權

若要使用 REST API 來設定此設定，則會呼叫階層 [設定](/rest/api/resources/hierarchysettings) 端點。 若要這樣做，請使用下列 REST API URI 與主體格式。 這個值是_布林值_，因此，請為此值提供 **true** 或 **false**。 **true** 的值會啟用這個保護管理群組階層的方法：

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
