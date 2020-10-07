---
title: 快速入門：透過 REST API 建立管理群組
description: 在本快速入門中，您會使用 REST API 來建立管理群組，以將您的資源組織成資源階層。
ms.date: 08/31/2020
ms.topic: quickstart
ms.openlocfilehash: b19fddf8215a1b133254c2a31bbea568a315f721
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236892"
---
# <a name="quickstart-create-a-management-group-with-rest-api"></a>快速入門：透過 REST API 建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 請安裝 [ARMClient](https://github.com/projectkudu/ARMClient) (如果尚未安裝)。 此工具會將 HTTP 要求傳送至以 Azure Resource Manager 為基礎的 REST API。 或者，您可以使用 REST 文件中的「試試看」功能或 PowerShell 的 [RestMethod](/powershell/module/microsoft.powershell.utility/invoke-restmethod) 或 [Postman](https://www.postman.com)等工具。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了克服需找到 Azure AD 全域管理員，才能開始使用管理群組的困難，我們允許在根層級建立初始管理群組。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-rest-api"></a>在 REST API 中建立

針對 REST API，請使用[管理群組 - 建立或更新](/rest/api/resources/managementgroups/createorupdate)端點來建立新的管理群組。 在此範例中，管理群組的 **groupId** 為 Contoso。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 無要求本文

**groupId** 是要建立的唯一識別碼。 其他命令會使用此識別碼來參考這個群組，且之後無法變更。

如果您想要讓管理群組在 Azure 入口網站中顯示不同的名稱，請在要求本文中新增 **properties.displayName** 屬性。 例如，若要建立 **groupId** 為 Contoso 且顯示名稱為 Contoso Group 的管理群組，請使用下列端點和要求本文：

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
    "properties": {
      "displayName": "Contoso Group"
    }
  }
  ```

在上述範例中，會在根管理群組下建立新的管理群組。 若要指定不同的管理群組作為父系，請使用 **properties.parent.id** 屬性。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 要求本文

  ```json
  {
    "properties": {
      "displayName": "Contoso Group",
      "parent": {
        "id": "/providers/Microsoft.Management/managementGroups/HoldingGroup"
      }
    }
  }
  ```

## <a name="clean-up-resources"></a>清除資源

若要移除上方所建立的管理群組，請使用[管理群組 - 刪除](/rest/api/resources/managementgroups/delete)端點：

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/Contoso?api-version=2020-02-01
  ```

- 無要求本文

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> [利用管理群組來管理您的資源](./manage.md)