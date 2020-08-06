---
title: 管理資源群組-Azure 入口網站
description: 使用 Azure 入口網站透過 Azure Resource Manager 管理您的資源群組。 說明如何建立、列出和刪除資源群組。
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: feee3a0d6de97ac6aaa1f9a92f20860f3f461ae3
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87827226"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>使用 Azure 入口網站來管理 Azure Resource Manager 資源群組

瞭解如何搭配[Azure Resource Manager](overview.md)使用[Azure 入口網站](https://portal.azure.com)來管理您的 Azure 資源群組。 若要管理 Azure 資源，請參閱[使用 Azure 入口網站來管理 azure 資源](manage-resources-portal.md)。

關於管理資源群組的其他文章：

- [使用 Azure CLI 來管理 Azure 資源群組](manage-resources-cli.md)
- [使用 Azure PowerShell 來管理 Azure 資源群組](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。


## <a name="create-resource-groups"></a>建立資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取**資源群組**

    ![新增資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. 選取 [新增]。
4. 輸入下列值：

   - **訂**用帳戶：選取您的 Azure 訂用帳戶。 
   - **資源群組**：輸入新的資源群組名稱。 
   - **區域**：選取 Azure 位置，例如「**美國中部**」。

     ![建立資源群組](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 選取 [檢閱+建立]
6. 選取 [建立]。 建立資源群組需要幾秒鐘的時間。
7. 從頂端功能表選取 [重新整理] 以重新整理資源群組清單，**然後選取新**建立的資源群組以開啟它。 或選取 [**通知**] (頂端的鐘圖示) ，然後選取 [**移至資源群組**] 以開啟新建立的資源群組

    ![前往資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>列出資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 若要列出資源群組，請選取 [**資源群組**]

    ![瀏覽資源群組](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 若要自訂針對資源群組顯示的資訊，請選取 [**編輯資料行**]。 下列螢幕擷取畫面顯示您可以新增至顯示的其他資料行：

## <a name="open-resource-groups"></a>開啟資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選取 [**資源群組**]。
3. 選取您想要開啟的資源群組。

## <a name="delete-resource-groups"></a>刪除資源群組

1. 開啟您想要刪除的資源群組。  請參閱[開啟資源群組](#open-resource-groups)。
2. 選取 [刪除資源群組]。

    ![刪除 azure 資源群組](./media/manage-resource-groups-portal/delete-group.png)

如需 Azure Resource Manager 如何排序資源刪除的詳細資訊，請參閱[Azure Resource Manager 資源群組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署至資源群組

建立 Resource Manager 範本之後，您可以使用 Azure 入口網站來部署您的 Azure 資源。 如需建立範本，請參閱[快速入門：使用 Azure 入口網站建立和部署 Azure Resource Manager 範本](../templates/quickstart-create-templates-use-the-portal.md)。 如需使用入口網站部署範本，請參閱使用[Resource Manager 範本和 Azure 入口網站部署資源](../templates/deploy-portal.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移至另一個資源群組或訂用帳戶

您可以將群組中的資源移至另一個資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止您組織中的其他使用者不小心刪除或修改重要資源，例如 Azure 訂用帳戶、資源群組或資源。 

1. 開啟您想要鎖定的資源群組。  請參閱[開啟資源群組](#open-resource-groups)。
2. 在左窗格中選取 [**鎖定**]。
3. 若要將鎖定新增至資源群組，請選取 [**新增**]。
4. 輸入 [**鎖定名稱**]、[**鎖定類型**] 和 [**附注**]。 鎖定類型包括 [**唯讀**] 和 [**刪除**]。

    ![鎖定 azure 資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

如需詳細資訊，請參閱[鎖定資源以防止非預期的變更](lock-resources.md)。

## <a name="tag-resource-groups"></a>標記資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 如需相關資訊，請參閱[使用標記來組織您的 Azure 資源](tag-resources.md#portal)。

## <a name="export-resource-groups-to-templates"></a>將資源群組匯出至範本

如需匯出範本的詳細資訊，請參閱[單一和多資源匯出至範本-入口網站](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理資源群組的存取權

Azure[角色型存取控制 (AZURE RBAC) ](../../role-based-access-control/overview.md)是您在 azure 中管理資源存取權的方式。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 若要瞭解 Azure Resource Manager，請參閱[Azure Resource Manager 總覽](overview.md)。
- 若要瞭解 Resource Manager 範本語法，請參閱[瞭解 Azure Resource Manager 範本的結構和語法](../templates/template-syntax.md)。
- 若要瞭解如何開發範本，請參閱[逐步教學](../index.yml)課程。
- 若要查看 Azure Resource Manager 範本架構，請參閱[範本參考](/azure/templates/)。
