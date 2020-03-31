---
title: 創建管理組以組織資源 - Azure 治理
description: 了解如何使用入口網站、Azure PowerShell 和 Azure CLI 建立 Azure 管理群組來管理多個資源。
ms.date: 12/18/2019
ms.topic: conceptual
ms.openlocfilehash: d9bb2e82404c0188094298f40da3346ee132eec3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436538"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>建立用於資源組織及管理的管理群組

管理群組是可協助您跨多個訂用帳戶管理容器存取、原則及合規性的容器。 建立這些容器來建置可與 [Azure 原則](../policy/overview.md)和 [Azure 角色型存取控制](../../role-based-access-control/overview.md)搭配使用的高效率階層。 如需有關管理群組的詳細資訊，請參閱[使用 Azure 管理群組來組織資源](overview.md)。

在目錄中建立的第一個管理群組可能需要 15 分鐘的時間才能完成。 第一次在 Azure 中設定目錄的管理群組服務時會執行一些程序。 程序完成時，您會收到通知。 有關詳細資訊，請參閱[管理組的初始設置](./overview.md#initial-setup-of-management-groups)。 

## <a name="create-a-management-group"></a>建立管理群組

租戶中的任何 Azure AD 使用者可以創建管理組，而無需分配給該使用者的管理組寫入權限。  此新管理組將是根管理組的子組，建立者將獲得"擁有者"角色指派。 管理組服務允許此功能，因此在根級別不需要角色指派。 創建根管理組時，沒有使用者有權訪問該組。  為了避免找到 Azure AD 全域管理員開始使用管理組的障礙，我們允許在根級別創建初始管理組。      

您可以使用入口網站、PowerShell 或 Azure CLI 來建立管理群組。 目前，您無法使用 Resource Manager 範本來建立管理群組。

### <a name="create-in-portal"></a>在入口網站中建立

1. 登錄到[Azure 門戶](https://portal.azure.com)。

1. 選擇 **"所有服務** > **管理 + 治理**"。

1. 選擇**成本管理和計費**

1. 在成本管理 + 計費 - 管理組頁面上，選擇**管理組**

1. 選擇 **= 添加管理組**。

   ![用於使用管理組的頁面](./media/main.png)

1. 填寫管理群組識別碼欄位。

   - [管理群組識別碼]**** 是用來在此管理群組上提交命令的目錄唯一識別碼。 此識別碼在建立後即無法編輯，因為整個 Azure 系統會使用它來識別此群組。 [根管理組](overview.md#root-management-group-for-each-directory)使用 Azure 活動目錄 ID 的 ID 自動創建。 對於所有其他管理組，分配唯一 ID。
   - [顯示名稱] 欄位是顯示在 Azure 入口網站內的名稱。 在建立管理群組時，不同的顯示名稱是選擇性欄位，並且可以隨時進行變更。  

   ![用於創建新管理組的選項窗格](./media/create_context_menu.png)  

1. 選取 [儲存]****。

### <a name="create-in-powershell"></a>在 PowerShell 中建立

對於 PowerShell，使用[新 Az 管理組](/powershell/module/az.resources/new-azmanagementgroup)Cmdlet 創建新的管理組。

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** 是要建立的唯一識別碼。 此 ID 被其他命令用於引用此組，以後無法更改它。

如果希望管理組在 Azure 門戶中顯示其他名稱，則添加 **"顯示名稱"** 參數。 例如，要創建具有 Contoso 的組名和"Contoso 組"的顯示名稱的管理組，請使用以下 Cmdlet：

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

在前面的示例中，在根管理組下創建新的管理組。 要將不同的管理組指定為父組，請使用**ParentId**參數。

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>在 Azure CLI 中建立

對於 Azure CLI，使用[az 帳戶管理組創建](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create)命令創建新的管理組。

```azurecli-interactive
az account management-group create --name Contoso
```

該**名稱**是正在創建的唯一識別碼。 此 ID 被其他命令用於引用此組，以後無法更改它。

如果希望管理組在 Azure 門戶中顯示其他名稱，則添加**顯示名稱**參數。 例如，要創建具有 Contoso 的組名和"Contoso 組"的顯示名稱的管理組，請使用以下命令：

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

在前面的示例中，在根管理組下創建新的管理組。 要將不同的管理組指定為父組，請使用**父**參數並提供父組的名稱。

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>後續步驟

若要深入了解管理群組，請參閱：

- [建立管理群組以組織 Azure 資源](create.md)
- [如何變更、刪除或管理您的管理群組](manage.md)
- [檢閱 Azure PowerShell 資源模組中的管理群組](/powershell/module/az.resources#resources)
- [檢閱 REST API 中的管理群組](/rest/api/resources/managementgroups)
- [檢閱 Azure CLI 中的管理群組](/cli/azure/account/management-group)
