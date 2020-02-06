---
title: 在 Azure 入口網站中建立和共用儀表板
description: 本文說明如何建立、自訂、發佈和共用 Azure 入口網站中的儀表板。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
editor: tysonn
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: fee3d9f0ef4f2c622d42373f4ed8c895f2c76adf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901021"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 入口網站中建立和共用儀表板

在 Azure 入口網站中，儀表板是您雲端資源的焦點和組織式觀點。 使用儀表板作為工作區，您可以在其中快速啟動日常作業和監視資源的工作。 例如，根據專案、工作或使用者角色來建立自訂儀表板。

Azure 入口網站提供預設儀表板做為起點。 您可以編輯預設的儀表板。 建立和自訂其他儀表板，併發布和共用儀表板，讓其他使用者可以使用它們。 本文說明如何建立新的儀表板、自訂介面，以及發佈和共用儀表板。

## <a name="create-a-new-dashboard"></a>建立新的儀表板

在此範例中，我們會建立新的私用儀表板，並指派名稱。 請遵循下列步驟來開始進行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從 [Azure 入口網站] 功能表中，選取 [**儀表板**]。 您的預設視圖可能已經設定為 [儀表板]。

    ![開啟儀表板](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. 選取 [**新增儀表板**]。

    ![預設儀表板的螢幕擷取畫面](./media/azure-portal-dashboards/create-new-dashboard.png)

    此動作會開啟 [**磚庫**]，您可以在其中選取磚，並使用空白方格來排列磚。

    ![磚庫和空白方格的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-name.png)

1. 選取 [儀表板] 標籤中的 [**我的儀表板**] 文字，然後輸入可協助您輕鬆識別自訂儀表板的名稱。

1. 選取頁首中的 [**完成自訂**] 以結束編輯模式。

[儀表板] 視圖現在會顯示您的空白儀表板。 選取儀表板名稱旁的箭號，以查看您可以使用的儀表板。 此清單可能包含其他使用者已建立並共用的儀表板。

## <a name="edit-a-dashboard"></a>編輯儀表板

現在，讓我們編輯儀表板來新增、調整大小，並排列代表您 Azure 資源的磚。

### <a name="add-tiles"></a>新增圖格

若要將磚新增至儀表板，請遵循下列步驟：

1. 從頁面標頭中選取 ![編輯 圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**]。

    ![[儀表板] 反白顯示編輯的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-edit.png)

1. 流覽**磚圖庫**，或使用 [搜尋] 欄位來尋找您想要的磚。

1. 選取 [**新增**]，以預設大小和位置，將磚新增至儀表板。 或者，將磚拖曳至方格，並將它放在您想要的位置。

許多資源頁面在命令列中都包含圖釘圖示。 如果您選取圖示，代表來源頁面的磚會釘選到目前作用中的儀表板。 這個方法是將磚加入儀表板的另一種方式。

![具有釘選圖示的頁面命令列螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-pin-blade.png)

> [!TIP]
> 如果您使用多個組織，請將 [**組織身分識別**] 磚新增至儀表板，以清楚地顯示資源所屬的組織。
>
>

### <a name="resize-or-rearrange-tiles"></a>調整大小或重新排列磚

若要變更磚的大小或重新排列儀表板上的磚，請遵循下列步驟：

1. 從頁面標頭中選取 ![編輯 圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**]。

1. 選取磚右上角的內容功能表。 然後，選擇磚大小。 支援任何大小的磚也會在右下角包含「控制碼」，讓您將磚拖曳至您想要的大小。

    ![開啟 [磚大小] 功能表的儀表板螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 選取磚，然後將它拖曳到方格上的新位置來排列您的儀表板。

### <a name="additional-tile-configuration"></a>其他磚設定

有些磚可能需要更多的設定，以顯示您想要的資訊。 例如，您必須設定 [**計量圖表**] 圖格，以顯示**Azure 監視器**的度量。 您也可以自訂磚資料來覆寫儀表板的預設時間設定。

任何需要設定的磚都會顯示 [**設定磚**] 橫幅，直到您自訂磚為止。 選取該橫幅，然後執行必要的設定。

![需要設定之磚的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown 圖格可讓您在儀表板上顯示自訂的靜態內容。 這可能是基本指示、影像、一組超連結，甚至是連絡人資訊。 如需使用 markdown 磚的詳細資訊，請參閱[在 Azure 儀表板上使用 markdown 磚來顯示自訂內容](azure-portal-markdown-tile.md)。
>
>
### <a name="customize-tile-data"></a>自訂磚資料

儀表板上的資料會自動顯示過去24小時的活動。 若只要顯示此磚的不同時間範圍，請遵循下列步驟：

1. 選取內容功能表中的 [**自訂磚資料**] 或 [![篩選] 圖示，](./media/azure-portal-dashboards/dashboard-filter.png) 磚的左上角篩選。

    ![磚內容功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. 選取核取方塊，以覆**寫磚層級的儀表板時間設定**。

    ![對話方塊的螢幕擷取畫面，以設定磚時間設定](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 選擇要為此磚顯示的時間範圍。 您可以選擇過去30分鐘到過去30天的時間，或定義自訂範圍。

1. 選擇要顯示的時間細微性。 您可以從一分鐘的增量顯示為一個月。

1. 選取 [套用]。

## <a name="delete-a-tile"></a>刪除磚

若要從儀表板移除磚，請遵循下列步驟：

* 選取磚右上角的內容功能表，然後選取 [**從儀表板移除**]。 或者，

* 選取 ![編輯 圖示](./media/azure-portal-dashboards/dashboard-edit-icon.png)**編輯**] 以進入自訂模式。 將滑鼠停留在磚的右上角，然後選取 [![刪除 圖示](./media/azure-portal-dashboards/dashboard-delete-icon.png) 刪除 圖示，以從儀表板移除磚。

   ![顯示如何從儀表板移除磚的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>複製儀表板

若要使用現有儀表板作為新儀表板的範本，請遵循下列步驟：

1. 請確定 [儀表板] 視圖顯示您要複製的儀表板。

1. 在頁首中，選取 ![複製圖示](./media/azure-portal-dashboards/dashboard-clone.png)**複製**]。

1. 儀表板的複本，名為 [**複製***儀表板名稱*] 會在編輯模式中開啟。 請使用本文中的先前步驟來重新命名及自訂儀表板。

## <a name="publish-and-share-a-dashboard"></a>發行及共用儀表板

當您建立儀表板時，預設為私用，這表示您是唯一可以看到它的人。 若要讓其他人能夠使用儀表板，您可以與其他使用者共用。 首先，您必須將儀表板發佈為 Azure 資源。 若要發佈和共用自訂儀表板，請遵循下列步驟：

1. 從頁面標頭中選取 ![共用 圖示](./media/azure-portal-dashboards/dashboard-share-icon.png)**共用**]。 [**共用 + 存取控制**] 隨即開啟。

1. 確認顯示的是正確的儀表板名稱。

1. 選取訂用帳戶**名稱**。 具有訂用帳戶存取權的使用者可以使用共用的儀表板。 個別磚所代表資源的存取權是由 Azure 角色型存取控制所決定。

1. 選取核取方塊，將此儀表板發佈至所選訂用帳戶的 [儀表板] 資源群組。 或者，清除核取方塊，並選擇改為發行至現有的資源群組。

1. 選擇儀表板資源的 [位置]。 我們建議您找出具有其他資源的 [儀表板]。 如果您從現有的資源群組中選擇，儀表板會自動與該資源群組一起找出。

1. 選取 [發佈]。

    ![[儀表板發行] 對話方塊的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-publish.png)

### <a name="set-access-control-on-a-shared-dashboard"></a>在共用的儀表板上設定存取控制

發佈儀表板之後，請遵循下列步驟來管理可存取儀表板的人員：

1. 在 [**共用 + 存取控制**] 窗格中，選取 [**管理使用者**]。

    ![[儀表板] [共用 + 存取控制] 對話方塊的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-share-access-control.png)

1. [**存取控制**] 頁面隨即開啟。 您可以檢查某人的存取層級，或加入新的角色指派。 當您在這裡新增角色指派時，即是授與儀表板的許可權。

> [!NOTE]
> 磚是組織中資源的代表性觀點。 資源的存取權是透過角色型存取控制指派來管理，而許可權則是從訂用帳戶向下繼承到資源。 將存取權提供給儀表板，並不會自動指派儀表板上所顯示資源的許可權。 如需共用儀表板的許可權和資源的角色型存取控制的詳細資訊，請參閱[使用以角色為基礎的存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>開啟共用的儀表板

若要尋找並開啟共用的儀表板，請遵循下列步驟：

1. 選取儀表板名稱旁邊的箭號。

1. 如果未列出您想要開啟的儀表板，請從顯示的儀表板清單中選取或 **[流覽所有儀表板]** 。

    ![[儀表板] 選取功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-browse.png)

1. 在 [**類型**] 欄位中，選取 [**共用儀表板**]。

1. 選取一個或多個訂閱。 您也可以輸入文字來依名稱篩選儀表板。

1. 從共用的儀表板清單中選取儀表板。

## <a name="delete-a-dashboard"></a>刪除儀表板

若要永久刪除私人或共用的儀表板，請遵循下列步驟：

1. 從儀表板名稱旁邊的清單中，選取您想要刪除的儀表板。

1. 從頁面標頭中選取 ![刪除 圖示](./media/azure-portal-dashboards/dashboard-delete-icon.png)**刪除**]。

1. 若是私人儀表板，請在確認對話方塊上選取 **[確定]** 以移除儀表板。 針對共用的儀表板，請在 [確認] 對話方塊中選取核取方塊，以確認其他人將不再看到已發佈的儀表板。 然後選取 [確定]。

    ![刪除確認的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>後續步驟

* [使用以角色為基礎的存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [以程式設計方式建立 Azure 儀表板](azure-portal-dashboards-create-programmatically.md)
