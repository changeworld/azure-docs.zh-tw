---
title: 管理資源群組 - Azure 門戶
description: 使用 Azure 門戶透過 Azure 資源管理員管理資源組。 展示如何建立、列出和刪除資源組。
author: mumian
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: jgao
ms.openlocfilehash: 622c0cb8d58dddcdb13c7dc9412ada3f2e2d93e4
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632980"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-the-azure-portal"></a>使用 Azure 入口網站來管理 Azure Resource Manager 資源群組

瞭解如何將 Azure[門戶](https://portal.azure.com)與[Azure 資源管理員](overview.md)一起管理 Azure 資源組。 有關管理 Azure 資源,請參閱[使用 Azure 門戶管理 Azure 資源](manage-resources-portal.md)。

有關管理資源群組的其他文章:

- [使用 Azure CLI 管理 Azure 資源群組](manage-resources-cli.md)
- [使用 Azure PowerShell 管理 Azure 資源群組](manage-resources-powershell.md)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="what-is-a-resource-group"></a>什麼是資源群組

資源群組是存放 Azure 方案相關資源的容器。 資源群組可以包含方案的所有資源，或只包含您要以群組方式管理的資源。 您可決定如何根據對組織最有利的方式，將資源配置到資源群組。 一般而言，會新增共用相同生命週期的資源到相同資源群組，因此您可以以群組為單位輕鬆地部署、更新、刪除它們。

資源群組會儲存資源相關中繼資料。 因此，當您指定資源群組的位置時，您便是指定中繼資料的儲存位置。 基於相容性理由，您可能需要確保您的資料存放在特定區域中。


## <a name="create-resource-groups"></a>建立資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇**資源群組**

    ![新增資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group.png)
3. 選取 [新增]  。
4. 輸入下列值：

   - **訂閱**:選擇 Azure 訂閱。 
   - **資源群組**：輸入新的資源群組名稱。 
   - **區域**:選擇 Azure 位置,如**美國中部**。

     ![建立資源群組](./media/manage-resource-groups-portal/manage-resource-groups-create-group.png)
5. 選擇**稽核與建立**
6. 選取 [建立]  。 創建資源組需要幾秒鐘。
7. 從頂部功能表中選擇 **「刷新**」以刷新資源組清單,然後選擇新創建的資源組以將其打開。 或者從頂部選擇**通知**(鈴形圖示),然後選擇 **「轉到資源群組」** 以開啟新建立的資源群組

    ![跳到資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-group-go-to-resource-group.png)

## <a name="list-resource-groups"></a>列出資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 要列出資源群組,請選擇 **「資源群組**」

    ![瀏覽資源群組](./media/manage-resource-groups-portal/manage-resource-groups-list-groups.png)

3. 要自定義為資源組顯示的資訊,請選擇 **「編輯列**」。 以下螢幕截圖顯示了可以新增到顯示器中的新增欄位:

## <a name="open-resource-groups"></a>開啟的資源群組

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 選擇**資源群組**。
3. 選擇要打開的資源組。

## <a name="delete-resource-groups"></a>刪除資源群組

1. 開啟要刪除的資源群組。  請參考[資源群組](#open-resource-groups)。
2. 選取 [刪除資源群組]****。

    ![移除 Azure 資源群組](./media/manage-resource-groups-portal/delete-group.png)

有關 Azure 資源管理員如何指令刪除資源的詳細資訊,請參考 Azure[資源管理員資源群組刪除](delete-resource-group.md)。

## <a name="deploy-resources-to-a-resource-group"></a>將資源部署到資源群組

創建資源管理器範本後,可以使用 Azure 門戶部署 Azure 資源。 有關建立樣本,請參閱[快速入門:使用 Azure 門戶建立和部署 Azure 資源管理員樣本](../templates/quickstart-create-templates-use-the-portal.md)。 有關使用門戶部署樣本,請參閱[使用資源管理器樣本和 Azure 門戶部署資源](../templates/deploy-portal.md)。

## <a name="move-to-another-resource-group-or-subscription"></a>移到其他資源群組或訂閱

您可以將群組中的資源移動到其他資源群組。 如需詳細資訊，請參閱 [將資源移動到新的資源群組或訂用帳戶](move-resource-group-and-subscription.md)。

## <a name="lock-resource-groups"></a>鎖定資源群組

鎖定可防止組織中的其他使用者意外刪除或修改關鍵資源,如 Azure 訂閱、資源組或資源。 

1. 開啟要刪除的資源群組。  請參考[資源群組](#open-resource-groups)。
2. 在左側窗格中,選擇 **「鎖定**」。
3. 要向資源群組添加鎖,請選擇"**添加**"。
4. 輸入**鎖定名稱**、**鎖定類型**與**註解**。 鎖類型包括**唯讀**與**刪除**。

    ![鎖定 Azure 資源群組](./media/manage-resource-groups-portal/manage-resource-groups-add-lock.png)

有關詳細資訊,請參閱[鎖定資源以防止意外變更](lock-resources.md)。

## <a name="tag-resource-groups"></a>標籤資源群組

您可以將標籤套用至資源群組和資源，以便以邏輯方式組織您的資產。 有關詳細資訊,請參閱[使用標記來組織 Azure 資源](tag-resources.md#portal)。

## <a name="export-resource-groups-to-templates"></a>匯出資源群組匯出到樣本

有關匯出樣本的資訊,請參閱[單資源和多資源匯出到樣本 - 門戶](../templates/export-template-portal.md)。

## <a name="manage-access-to-resource-groups"></a>管理對資源群組的存取

[基於角色的存取控制 (RBAC)](../../role-based-access-control/overview.md)是管理對 Azure 中資源的訪問的方式。 如需詳細資料，請參閱[使用 RBAC 和 Azure 入口網站來管理存取權](../../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>後續步驟

- 要瞭解 Azure 資源管理員,請參考[Azure 資源管理員概述](overview.md)。
- 要瞭解資源管理員樣本語法,請參閱瞭解[Azure 資源管理器樣本的結構和語法](../templates/template-syntax.md)。
- 要瞭解如何開發範本,請參閱分[步教程](/azure/azure-resource-manager/)。
- 要檢視 Azure 資源管理員樣本架構,請參考[樣本參考](/azure/templates/)。
