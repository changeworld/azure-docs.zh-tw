---
title: 在 Azure 入口網站中建立和共用儀表板
description: 本文說明如何建立、自訂、發佈和共用 Azure 入口網站中的儀表板。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 06cf0a5692b3d80a07c305f53c9e3801cbdb5110
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84764004"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 入口網站中建立和共用儀表板

在 Azure 入口網站中，儀表板是您雲端資源的焦點和組織式觀點。 使用儀表板作為工作區，以便在其中快速啟動日常作業工作及監視資源。 例如，您可以根據專案、工作或使用者角色來建置自訂儀表板。

Azure 入口網站提供預設儀表板做為起點。 您可以編輯預設的儀表板。 建立和自訂其他儀表板，併發布和共用儀表板，讓其他使用者可以使用它們。 本文說明如何建立新的儀表板、自訂介面，以及發佈和共用儀表板。

## <a name="create-a-new-dashboard"></a>建立新的儀表板

在此範例中，我們會建立新的私用儀表板，並指派名稱。 請遵循下列步驟來開始進行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 從 [Azure 入口網站] 功能表中，選取 [**儀表板**]。 您的預設視圖可能已經設定為 [儀表板]。

    ![開啟儀表板](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. 選取 [**新增儀表板**]。

    ![新儀表板的螢幕擷取畫面](./media/azure-portal-dashboards/create-new-dashboard.png)

    此動作會開啟 [**磚庫**]，您可以在其中選取磚，並使用空白方格來排列磚。

    ![磚庫和空白方格的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-name.png)

1. 選取 [儀表板] 標籤中的 [**我的儀表板**] 文字，然後輸入可協助您輕鬆識別自訂儀表板的名稱。

1. 選取頁首中的 [**完成自訂**] 以結束編輯模式。

[儀表板] 視圖現在會顯示新的儀表板。 選取儀表板名稱旁的箭號，以查看您可以使用的儀表板。 此清單可能包含其他使用者已建立並共用的儀表板。

## <a name="edit-a-dashboard"></a>編輯儀表板

現在，讓我們編輯儀表板來新增、調整大小，並排列代表您 Azure 資源的磚。

### <a name="add-tiles-from-the-dashboard"></a>從儀表板新增磚

若要將磚新增至儀表板，請遵循下列步驟：

1. ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) 從頁面標頭選取 [編輯] 圖示**編輯**。

    ![[儀表板] 反白顯示編輯的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-edit.png)

1. 流覽**磚圖庫**，或使用 [搜尋] 欄位來尋找您想要的磚。

1. 選取 [**新增**]，以預設大小和位置，將磚新增至儀表板。 或者，將磚拖曳至方格，並將它放在您想要的位置。

> [!TIP]
> 如果您使用多個組織，請將 [**組織身分識別**] 磚新增至儀表板，以清楚地顯示資源所屬的組織。

### <a name="add-tiles-from-a-resource-page"></a>從資源頁面新增磚

有另一種方式可以將磚新增至儀表板。 許多資源頁面在命令列中都包含圖釘圖示。 如果您選取圖示，代表來源頁面的磚會釘選到目前作用中的儀表板。 

![具有釘選圖示的頁面命令列螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>調整大小或重新排列磚

若要變更磚的大小或重新排列儀表板上的磚，請遵循下列步驟：

1. ![ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) 從頁面標頭選取 [編輯] 圖示**編輯**。

1. 選取磚右上角的內容功能表。 然後，選擇磚大小。 支援任何大小的磚也會在右下角包含「控制碼」，讓您將磚拖曳至您想要的大小。

    ![開啟 [磚大小] 功能表的儀表板螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 選取磚，然後將它拖曳到方格上的新位置來排列您的儀表板。

### <a name="additional-tile-configuration"></a>其他磚設定

有些磚可能需要更多的設定，以顯示您想要的資訊。 例如，您必須設定 [**計量圖表**] 圖格，以顯示**Azure 監視器**的度量。 您也可以自訂磚資料來覆寫儀表板的預設時間設定。

任何需要設定的磚都會顯示 [**設定磚**] 橫幅，直到您自訂磚為止。 若要自訂磚：

1. 選取頁首中的 [**完成自訂**] 以結束編輯模式。

1. 選取橫幅，然後執行必要的設定。

    ![需要設定之磚的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> Markdown 圖格可讓您在儀表板上顯示自訂的靜態內容。 這可能是基本指示、影像、一組超連結，甚至是連絡人資訊。 如需使用 markdown 磚的詳細資訊，請參閱[在 Azure 儀表板上使用 markdown 磚來顯示自訂內容](azure-portal-markdown-tile.md)。

### <a name="customize-tile-data"></a>自訂磚資料

儀表板上的資料會自動顯示過去24小時的活動。 若只要顯示此磚的不同時間範圍，請遵循下列步驟：

1. 從內容功能表中選取 [**自訂磚資料**]，或 ![ ](./media/azure-portal-dashboards/dashboard-filter.png) 從磚的左上角選取篩選圖示篩選。

    ![磚內容功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. 選取核取方塊，以覆**寫磚層級的儀表板時間設定**。

    ![對話方塊的螢幕擷取畫面，以設定磚時間設定](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 選擇要為此磚顯示的時間範圍。 您可以選擇過去30分鐘到過去30天的時間，或定義自訂範圍。

1. 選擇要顯示的時間細微性。 您可以從一分鐘的增量顯示為一個月。

1. 選取 [套用]。

## <a name="delete-a-tile"></a>刪除磚

若要從儀表板移除磚，請遵循下列步驟：

* 選取磚右上角的內容功能表，然後選取 [**從儀表板移除**]。 或者，

* 選取 [ ![ 編輯] 圖示 [ ](./media/azure-portal-dashboards/dashboard-edit-icon.png) **編輯**] 以進入自訂模式。 將滑鼠停留在磚的右上角，然後選取 [ ![ 刪除] 圖示 ](./media/azure-portal-dashboards/dashboard-delete-icon.png) 刪除圖示，從儀表板移除磚。

   ![顯示如何從儀表板移除磚的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>複製儀表板

若要使用現有儀表板作為新儀表板的範本，請遵循下列步驟：

1. 請確定 [儀表板] 視圖顯示您要複製的儀表板。

1. 在頁首中，選取 [複製 ![ 圖示 ](./media/azure-portal-dashboards/dashboard-clone.png) **複製**]。

1. 儀表板的複本，名為 [**複製***儀表板名稱*] 會在編輯模式中開啟。 請使用本文中的先前步驟來重新命名及自訂儀表板。

## <a name="publish-and-share-a-dashboard"></a>發行及共用儀表板

當您建立儀表板時，預設為私用，這表示您是唯一可以看到它的人。 若要讓其他人能夠使用儀表板，您可以發行並共用它們。 如需詳細資訊，請參閱[使用以角色為基礎的存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>開啟共用的儀表板

若要尋找並開啟共用的儀表板，請遵循下列步驟：

1. 選取儀表板名稱旁邊的箭號。

1. 從顯示的儀表板清單中選取。 如果未列出您想要開啟的儀表板：

    1. 選取 **[流覽所有儀表板]**。

        ![[儀表板] 選取功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-browse.png)

    1. 在 [**類型**] 欄位中，選取 [**共用儀表板**]。

        ![所有儀表板選取功能表的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 選取一個或多個訂閱。 您也可以輸入文字來依名稱篩選儀表板。

    1. 從共用的儀表板清單中選取儀表板。

## <a name="delete-a-dashboard"></a>刪除儀表板

若要永久刪除私人或共用的儀表板，請遵循下列步驟：

1. 從儀表板名稱旁邊的清單中，選取您想要刪除的儀表板。

1. ![ ](./media/azure-portal-dashboards/dashboard-delete-icon.png) 從頁面標頭中選取 [刪除] 圖示**刪除**。

1. 若是私人儀表板，請在確認對話方塊上選取 **[確定]** 以移除儀表板。 針對共用的儀表板，請在 [確認] 對話方塊中選取核取方塊，以確認其他人將不再看到已發佈的儀表板。 然後選取 [確定]。

    ![刪除確認的螢幕擷取畫面](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>後續步驟

* [使用角色型存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [以程式設計方式建立 Azure 儀表板](azure-portal-dashboards-create-programmatically.md)
