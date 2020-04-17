---
title: 在 Azure 入口網站中建立和共用儀表板
description: 本文介紹如何在 Azure 門戶中創建、自定義、發佈和共用儀錶板。
services: azure-portal
documentationcenter: ''
author: sewatson
manager: mtillman
ms.assetid: ff422f36-47d2-409b-8a19-02e24b03ffe7
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: e6eda3b522a5b46cd82ef29ae493891a624d3272
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459282"
---
# <a name="create-and-share-dashboards-in-the-azure-portal"></a>在 Azure 入口網站中建立和共用儀表板

儀錶板是 Azure 門戶中雲資源的集中且有組織的視圖。 使用儀表板作為工作區，以便在其中快速啟動日常作業工作及監視資源。 例如，您可以根據專案、工作或使用者角色來建置自訂儀表板。

Azure 門戶提供預設儀錶板作為起點。 您可以編輯預設儀錶板。 創建和自定義其他儀錶板,併發佈和共用儀錶板,使其可供其他使用者使用。 本文介紹如何創建新儀錶板、自定義介面以及發佈和共用儀錶板。

## <a name="create-a-new-dashboard"></a>建立新的儀表板

在此示例中,我們創建一個新的專用儀錶板並分配名稱。 請遵循下列步驟來開始進行：

1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 門戶選單中,選擇**儀表板**。 默認視圖可能已設置為儀錶板。

    ![開啟儀表板](./media/azure-portal-dashboards/portal-menu-dashboard.png)

1. 選擇 **「新建儀錶板**」 。

    ![新儀表板的螢幕截圖](./media/azure-portal-dashboards/create-new-dashboard.png)

    此操作將打開 **「磁貼庫**」,從中選擇磁貼,並打開一個空網格,其中將排列切片。

    ![磁貼庫與空格格的螢幕截圖](./media/azure-portal-dashboards/dashboard-name.png)

1. 在儀表板標籤中選擇 **「我的儀錶板」** 文字,並輸入可説明您輕鬆辨識自訂儀錶板的名稱。

1. 選擇 **「在**頁面標題中完成自訂」 以退出編輯模式。

儀錶板視圖現在顯示新儀錶板。 選擇儀錶板名稱旁邊的箭頭以查看可供您使用的儀錶板。 該清單可能包括其他使用者創建和共用的儀表板。

## <a name="edit-a-dashboard"></a>編輯儀表板

現在,讓我們編輯儀錶板以添加、調整大小和排列表示 Azure 資源的磁貼。

### <a name="add-tiles-from-the-dashboard"></a>從儀表板加入磁貼

要向儀錶板添加磁貼,請按照以下步驟操作:

1. 從![頁面](./media/azure-portal-dashboards/dashboard-edit-icon.png)標題 中選擇編輯圖示 **「編輯**」。

    ![儀表板突顯編輯的螢幕截圖](./media/azure-portal-dashboards/dashboard-edit.png)

1. 瀏覽**磁貼庫**或使用搜索欄位搜尋所需的磁貼。

1. 選擇 **「新增」** 可將磁貼新增到具有預設大小和位置的儀表板。 或者,將磁貼拖動到網格,並將其放置在所需的位置。

> [!TIP]
> 如果與多個組織合作,請將**組織標識**磁貼添加到儀錶板,以清楚地顯示資源屬於哪個組織。

### <a name="add-tiles-from-a-resource-page"></a>從資源頁加入磁貼

還有一種將磁貼添加到儀錶板的替代方法。 許多資源頁在命令列中包括一個圖釘圖示。 如果選擇該圖示,則表示源頁的磁貼將固定到當前處於活動狀態的儀錶板。 

![帶有引文圖示的頁面指令列的螢幕擷取](./media/azure-portal-dashboards/dashboard-pin-blade.png)

### <a name="resize-or-rearrange-tiles"></a>調整磁貼的大小或重新排列

要改變磁貼的大小或重新排列儀表板上的磁貼,請按照以下步驟操作:

1. 從![頁面](./media/azure-portal-dashboards/dashboard-edit-icon.png)標題 中選擇編輯圖示 **「編輯**」。

1. 選擇磁貼右上角的上下文菜單。 然後,選擇磁貼大小。 支援任何大小的磁貼還包括右下角的「句柄」,允許您將磁貼拖動到所需的大小。

    ![開啟磁貼大小選單的儀表板螢幕擷取](./media/azure-portal-dashboards/dashboard-tile-resize.png)

1. 選擇磁貼並將其拖動到網格上的新位置以排列儀錶板。

### <a name="additional-tile-configuration"></a>其他磁貼設定

某些磁貼可能需要更多配置才能顯示所需的資訊。 例如,必須設置**指標圖表**磁貼以顯示**Azure 監視器**中的指標。 您還可以自定義磁貼數據以覆蓋儀錶板的預設時間設置。

任何需要設置的磁貼都會顯示 **「設定」磁貼**橫幅,直到您自定義磁貼。 要自訂磁貼:

1. 選擇 **「在**頁面標題中完成自訂」 以退出編輯模式。

1. 選擇橫幅,然後執行所需的設置。

    ![需要設定的磁貼螢幕擷取](./media/azure-portal-dashboards/dashboard-configure-tile.png)

> [!NOTE]
> 標記磁貼允許您在儀表板上顯示自定義靜態內容。 這可能是基本說明、圖像、一組超連結,甚至是聯繫資訊。 有關使用標記磁貼的詳細資訊,請參閱在 Azure[儀表板上使用標記磁貼來顯示自訂內容](azure-portal-markdown-tile.md)。

### <a name="customize-tile-data"></a>自訂切片資料

儀錶板上的數據會自動顯示過去 24 小時的活動。 要顯示此磁貼的不同時間跨度,請按照以下步驟操作:

1. 從上下文選單中選擇 **「自訂磁貼資料」** 或者從磁貼的左上角![選擇 篩選器](./media/azure-portal-dashboards/dashboard-filter.png)圖示 篩選器。

    ![磁貼內容選單的螢幕擷取](./media/azure-portal-dashboards/dashboard-customize-tile-data.png)

1. 選擇此選項以**覆寫磁貼層的儀表板時間設定**。

    ![設定磁貼時間設定的對話框螢幕擷取](./media/azure-portal-dashboards/dashboard-override-time-settings.png)

1. 選擇要顯示此磁貼的時間範圍。 您可以選擇過去 30 分鐘到過去 30 天,也可以定義自定義範圍。

1. 選擇要顯示的時間粒度。 您可以顯示從一分鐘增量到一個月的任意位置。

1. 選取 [套用]  。

## <a name="delete-a-tile"></a>刪除磚

要從儀表板中刪除磁貼,請按照以下步驟操作:

* 選擇磁貼右上角的上下文菜單,然後選擇 **「從儀錶板中刪除**」。。 或者，

* 選擇![編輯圖](./media/azure-portal-dashboards/dashboard-edit-icon.png)示 **「編輯」** 以進入自訂模式。 將滑鼠懸停在磁貼的右上角,然後選擇![刪除圖](./media/azure-portal-dashboards/dashboard-delete-icon.png)示 刪除圖示以從儀表板中刪除磁貼。

   ![展示如何從儀表板中移除磁貼的螢幕擷取](./media/azure-portal-dashboards/dashboard-delete-tile.png)

## <a name="clone-a-dashboard"></a>複製儀表板

要將現有儀錶板用作新儀錶本的範本,請按照以下步驟操作:

1. 確保儀錶板檢視顯示要複製的儀錶板。

1. 在頁眉中,選擇![克隆圖示](./media/azure-portal-dashboards/dashboard-clone.png)**「克隆**」。。

1. 儀錶板的副本名為*儀錶板名稱***的克隆**,在編輯模式下打開。 使用本文中的上述步驟重命名和自定義儀錶板。

## <a name="publish-and-share-a-dashboard"></a>發佈和共用儀表板

創建儀錶板時,默認情況下它是私有的,這意味著您是唯一可以看到它的人。 要使儀錶板可供其他人使用,可以發佈和共用儀錶板。 有關詳細資訊,請參閱[使用基於角色的存取控制共享 Azure 儀表板](azure-portal-dashboard-share-access.md)。

### <a name="open-a-shared-dashboard"></a>開啟共享儀表板

要查找並打開共享儀錶板,請按照以下步驟操作:

1. 選擇儀錶板名稱旁邊的箭頭。

1. 從顯示的儀錶板清單中選擇。 如果未列出要開啟的儀表板:

    1. 選擇 **「瀏覽所有儀錶板**」。

        ![儀表板選擇選單的螢幕截圖](./media/azure-portal-dashboards/dashboard-browse.png)

    1. 在 **"類型'** 欄位中,選擇 **"共用儀錶板**"。

        ![所有儀表板選擇選單的螢幕截圖](./media/azure-portal-dashboards/dashboard-browse-all.png)

    1. 選擇一個或多個訂閱。 您還可以輸入文本以按名稱篩選儀錶板。

    1. 從共享儀錶板清單中選擇儀錶板。

## <a name="delete-a-dashboard"></a>刪除儀表板

要永久刪除專用或共用儀錶板,請按照以下步驟操作:

1. 選擇要從儀錶板名稱旁邊的清單中刪除的儀錶板。

1. 選擇![刪除圖](./media/azure-portal-dashboards/dashboard-delete-icon.png)示 從頁面標題**中移除**。

1. 對於專用儀錶板,請在確認對話框中選擇 **「確定」** 以刪除儀表板。 對於共用儀錶板,在確認對話框中,選擇複選框以確認其他人不再查看已發佈的儀錶板。 然後,選擇 **"確定**"。

    ![刪除確認的螢幕擷取](./media/azure-portal-dashboards/dashboard-delete-dash.png)

## <a name="next-steps"></a>後續步驟

* [使用角色型存取控制來共用 Azure 儀表板](azure-portal-dashboard-share-access.md)
* [以程式設計方式建立 Azure 儀表板](azure-portal-dashboards-create-programmatically.md)
