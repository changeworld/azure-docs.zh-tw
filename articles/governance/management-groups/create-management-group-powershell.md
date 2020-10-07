---
title: 快速入門：使用 Azure PowerShell 建立管理群組
description: 在本快速入門中，您會使用 Azure PowerShell 來建立管理群組，以將您的資源組織成資源階層。
ms.date: 08/31/2020
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74e4f0769bbf4deafefd27e166e0dae2c2e019a3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "89236873"
---
# <a name="quickstart-create-a-management-group-with-azure-powershell"></a>快速入門：使用 Azure PowerShell 建立管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 如需詳細資訊，請參閱[管理群組的初始設定](./overview.md#initial-setup-of-management-groups)。

## <a name="prerequisites"></a>Prerequisites

- 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/)。

- 開始之前，請確定已安裝最新版 Azure PowerShell。 如需詳細資訊，請參閱[安裝 Azure PowerShell 模組](/powershell/azure/install-az-ps)。

- 如果未啟用[階層保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)，則租用戶中的任何 Azure AD 使用者都可以建立管理群組，而不需將管理群組寫入權限指派給該使用者。 這個新管理群組會變成根管理群組或[預設管理群組](./how-to/protect-resource-hierarchy.md#setting---default-management-group)的子系，而建立者會獲得「擁有者」角色指派。 管理群組服務允許此功能，因此在根層級不需要角色指派。 沒有任何使用者可以存取建立的根管理群組。 為了避免尋找 Azure AD 全域管理員的困難以開始使用管理群組，我們允許在根層級建立初始管理群組。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-in-azure-powershell"></a>在 Azure PowerShell 中建立

若為 PowerShell，使用 [New-AzManagementGroup](/powershell/module/az.resources/new-azmanagementgroup)Cmdlet 建立新的管理群組。 在此範例中，管理群組 **GroupName** 為 _Contoso_。

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要建立的唯一識別碼。 其他命令會使用此識別碼來參考這個群組，且之後無法變更。

如果您希望管理群組在 Azure 入口網站中顯示不同的名稱，請新增 **DisplayName** 參數。 例如，若要建立 GroupName 為 Contoso 且顯示名稱為 "Contoso Group" 的管理群組，請使用下列 Cmdlet：

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

在上述範例中，會在根管理群組下建立新的管理群組。 若要指定不同的管理群組作為父系，請使用 **ParentId** 參數。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

## <a name="clean-up-resources"></a>清除資源

若要移除以上建立的管理群組，請使用 [Remove-AzManagementGroup](/powershell/module/az.resources/remove-azmanagementgroup)Cmdlet：

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

## <a name="next-steps"></a>後續步驟

在本快速入門中，您已建立管理群組來組織您的資源階層。 管理群組可以保留訂用帳戶或其他管理群組。

若要深入了解管理群組，以及如何管理您的資源階層，請繼續：

> [!div class="nextstepaction"]
> [利用管理群組來管理您的資源](./manage.md)