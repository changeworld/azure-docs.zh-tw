---
title: 管理資源組 - Azure 門戶
description: 使用 Azure 門戶通過 Azure 資源管理器管理資源組。 演示如何創建、列出和刪除資源組。
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 8087baf7595bfd2497f97fbff4822b356cd1b146
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274563"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>使用 Azure 入口網站來管理 Azure Resource Manager 資源群組

瞭解如何將 Azure[門戶](https://portal.azure.com)與[Azure 資源管理器](overview.md)一起管理 Azure 資源組。 有關管理 Azure 資源，請參閱[使用 Azure 門戶管理 Azure 資源](manage-resources-portal.md)。

有關管理資源組的其他文章：

- [使用 Azure CLI 管理 Azure 資源組](manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 資源組](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>什麼是資源組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。

資源群組會儲存資源相關中繼資料。 當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。

## <a name="create-resource-groups"></a>建立資源群組

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇**資源組**

    ![新增資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. 選取 [加入]****。
4. 輸入下列值：

   - **訂閱**：選擇 Azure 訂閱。 
   - **資源組**：輸入新的資源組名稱。 
   - **區域**：選擇 Azure 位置，如**美國中部**。

     ![建立資源群組](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 選擇**審核和創建**
6. 選取 [建立]****。 創建資源組需要幾秒鐘。
7. 從頂部功能表中選擇 **"刷新**"以刷新資源組清單，然後選擇新創建的資源組以將其打開。 或者從頂部選擇**通知**（鈴形圖示），然後選擇 **"轉到資源組"** 以打開新創建的資源組

    ![轉到資源組](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>列出資源群組

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 要列出資源組，請選擇 **"資源組**"

    ![瀏覽資源群組](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 要自訂為資源組顯示的資訊，請選擇 **"編輯列**"。 以下螢幕截圖顯示了可以添加到顯示器中的添加列：

## <a name="open-resource-groups"></a>打開的資源組

1. 登錄到 Azure[門戶](https://portal.azure.com)。
2. 選擇**資源組**。
3. 選擇要打開的資源組。

## <a name="delete-resource-groups"></a>刪除資源群組

1. 打開要刪除的資源組。  請參閱[打開資源組](#open-resource-groups)。
2. 選取 [刪除資源群組]****。

    ![刪除 Azure 資源組](./media/manage-resource-groups-portal/delete-group.png)

有關 Azure 資源管理器如何命令刪除資源的詳細資訊，請參閱 Azure[資源管理器資源組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署到資源組

創建資源管理器範本後，可以使用 Azure 門戶部署 Azure 資源。 有關創建範本，請參閱[快速入門：使用 Azure 門戶創建和部署 Azure 資源管理器範本](../templates/quickstart-create-templates-use-the-portal.md)。 有關使用門戶部署範本，請參閱[使用資源管理器範本和 Azure 門戶部署資源](../templates/deploy-portal.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移動到其他資源組或訂閱

您可以將組中的資源移動到其他資源組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resource-groups"></a>鎖定資源組

鎖定可防止組織中的其他使用者意外刪除或修改關鍵資源，如 Azure 訂閱、資源組或資源。 

1. 打開要刪除的資源組。  請參閱[打開資源組](#open-resource-groups)。
2. 在左側窗格中，選擇 **"鎖定**"。
3. 要向資源組添加鎖，請選擇"**添加**"。
4. 輸入**鎖定名稱**、**鎖定類型**和**注釋**。 鎖類型包括**唯讀**和**刪除**。

    ![鎖定 Azure 資源組](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

有關詳細資訊，請參閱[鎖定資源以防止意外更改](lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 有關詳細資訊，請參閱[使用標記來組織 Azure 資源](tag-resources.md#portal)。

## <a name="export-resource-groups-to-templates"></a>將資源組匯出到範本

有關匯出範本的資訊，請參閱[單資源和多資源匯出到範本 - 門戶](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理對資源組的訪問

[基於角色的存取控制 （RBAC）](../../role-based-access-control/overview.md)是管理對 Azure 中資源的訪問的方式。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 資源管理器，請參閱[Azure 資源管理器概述](overview.md)。
- 要瞭解資源管理器範本語法，請參閱瞭解[Azure 資源管理器範本的結構和語法](../templates/template-syntax.md)。
- 要瞭解如何開發範本，請參閱分[步教程](/azure/azure-resource-manager/)。
- 要查看 Azure 資源管理器範本架構，請參閱[範本引用](/azure/templates/)。