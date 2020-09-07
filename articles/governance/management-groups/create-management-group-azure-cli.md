---
title: 快速入門：使用 Azure CLI 建立管理群組
description: 在本快速入門中，您會使用 Azure CLI 來建立管理群組，以將您的資源組織成資源階層。
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: f07ae46c95f9ab9cc1ad973204ac5c50320fdf46
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236890"
---
# <a name="quickstart-create-a-management-group-with-the-azure-cli"></a>快速入門：使用 Azure CLI 建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 本快速入門會要求您執行 Azure CLI 2.0.76 版或更新版本，以在本機安裝和使用 CLI。 若要尋找版本，請執行 `az --version`。 如果您需要安裝或升級，請參閱[安裝 Azure CLI](/cli/azure/install-azure-cli)。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了避免尋找 Azure AD 全域管理員的困難以開始使用管理群組，我們允許在根層級建立初始管理群組。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-the-azure-cli"></a>在 Azure CLI 中建立

針對 Azure CLI，請使用 [az account management-group create](/cli/azure/account/management-group#az-account-management-group-create) 命令來建立新的管理群組。 在此範例中，管理群組 **name** 為 Contoso。

```azurecli-interactive
az account management-group create --name 'Contoso'
```

**name** 是要建立的唯一識別碼。 其他命令會使用此識別碼來參考這個群組，且之後無法變更。

如果您希望管理群組在 Azure 入口網站中顯示不同的名稱，請新增 **display-name** 參數。 例如，若要建立 GroupName 為 Contoso 且顯示名稱為 "Contoso Group" 的管理群組，請使用下列命令：

```azurecli-interactive
az account management-group create --name 'Contoso' --display-name 'Contoso Group'
```

在上述範例中，會在根管理群組下建立新的管理群組。 若要指定不同的管理群組作為父系，請使用 **parent** 參數，並提供父群組的名稱。

```azurecli-interactive
az account management-group create --name 'ContosoSubGroup' --parent 'Contoso'
```

## <a name="clean-up-resources"></a>清除資源

若要移除上方建立的管理群組，請使用 [az account management-group delete](/cli/azure/account/management-group#az-account-management-group-delete) 命令：

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> [利用管理群組來管理您的資源](./manage.md)