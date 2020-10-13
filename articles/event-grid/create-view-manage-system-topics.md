---
title: " (入口網站中建立、查看和管理 Azure 事件方格中的系統主題) "
description: 本文說明如何使用 Azure 入口網站來查看現有的系統主題、建立 Azure 事件方格系統主題。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2dbb0893668d72cfebf2d64e6515ff6ab6fed9bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86115106"
---
# <a name="create-view-and-manage-event-grid-system-topics-in-the-azure-portal"></a>在 Azure 入口網站中建立、查看和管理事件方格系統主題
本文說明如何使用 Azure 入口網站建立和管理系統主題。 如需系統主題的總覽，請參閱 [系統主題](system-topics.md)。

## <a name="create-a-system-topic"></a>建立系統主題
您可以透過兩種方式，建立 Azure 資源 (儲存體帳戶、事件中樞命名空間等 ) 的系統主題：

- 使用資源的 [ **事件** ] 頁面，例如儲存體帳戶或事件中樞命名空間。 當您使用 Azure 入口網站中的 [ **事件** ] 頁面，為 azure 來源所引發的事件建立事件訂閱時 (例如： Azure 儲存體帳戶) ，入口網站會為 azure 資源建立系統主題，然後為系統主題建立訂用帳戶。 如果您是第一次在 Azure 資源上建立事件訂用帳戶，您可以指定系統主題的名稱。 從第二次開始，系統主題名稱會以唯讀模式顯示。 如需詳細步驟，請參閱 [快速入門：使用 Azure 入口網站將 Blob 儲存體事件路由至 web 端點](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage) 。
- 使用 **事件方格系統主題** 頁面。 下列步驟適用于使用 **事件方格系統主題** 頁面建立系統主題。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端的搜尋方塊中，輸入 **事件方格系統主題**，然後按 **enter**。 

    ![搜尋系統主題](./media/create-view-manage-system-topics/search-system-topics.png)
3. 在 [ **事件方格系統主題** ] 頁面上，選取工具列上的 [ **+ 新增** ]。

    ![新增系統主題-工具列按鈕](./media/create-view-manage-system-topics/add-system-topic-menu.png)
4. 在 [ **建立事件方格系統] 主題** 頁面上，執行下列步驟：
    1. 選取 **主題類型**。 在下列範例中，已選取 [ **儲存體帳戶** ] 選項。 
    2. 選取具有儲存體帳戶資源的 **Azure 訂** 用帳戶。 
    3. 選取具有儲存體帳戶的 **資源群組** 。 
    4. 選取 **[儲存體帳戶]**。 
    5. 輸入要建立之系統主題的 **名稱** 。 
    
        > [!NOTE]
        > 您可以使用此系統主題名稱來搜尋計量和診斷記錄。
    6. 選取 [檢閱 + 建立]****。

        ![建立系統主題](./media/create-view-manage-system-topics/create-event-grid-system-topic-page.png)
    5. 檢查設定，然後選取 [ **建立**]。 
        
        ![複習和建立系統主題](./media/create-view-manage-system-topics/system-topic-review-create.png)
    6. 部署成功後，請選取 [ **移至資源** ]，以查看您所建立之系統主題的 **Event Grid 系統主題** 頁面。 

        ![系統主題頁面](./media/create-view-manage-system-topics/system-topic-page.png)


## <a name="view-all-system-topics"></a>查看所有系統主題
遵循下列步驟以查看所有現有的事件方格系統主題。 

1. 登入 [Azure 入口網站](https://portal.azure.com)。
2. 在頂端的搜尋方塊中，輸入 **事件方格系統主題**，然後按 **enter**。 

    ![搜尋系統主題](./media/create-view-manage-system-topics/search-system-topics.png)
3. 在 [ **事件方格系統主題** ] 頁面上，您會看到所有系統主題。 

    ![系統主題清單](./media/create-view-manage-system-topics/list-system-topics.png)
4. 從清單中選取 **系統主題** 以查看其詳細資料。 

    ![系統主題詳細資料](./media/create-view-manage-system-topics/system-topic-details.png)

    此頁面會顯示有關系統主題的詳細資料，例如下列資訊： 
    - 來源。 建立系統主題的資源名稱。
    - 來源類型。 資源類型。 例如： `Microsoft.Storage.StorageAccounts` 、 `Microsoft.EventHub.Namespaces` 等等 `Microsoft.Resources.ResourceGroups` 。
    - 為系統主題建立的任何訂閱。

    此頁面可讓您執行下列作業：
    - 在工具列上建立事件訂用帳戶選取 [ **+ 事件訂** 用帳戶]。 
    - 刪除事件訂用帳戶。 選取工具列上的 [ **刪除** ]。 
    - 新增系統主題的標記。 選取左側功能表上的 [ **標記** ]，然後指定標記名稱和值。 


## <a name="delete-a-system-topic"></a>刪除系統主題
1. 遵循 [ [view system 主題](#view-all-system-topics) ] 區段中的指示來查看所有系統主題，然後從清單中選取您要刪除的系統主題。 
2. 在 [ **事件方格系統] 主題** 頁面上，選取工具列上的 [ **刪除** ]。 

    ![系統主題-刪除按鈕](./media/create-view-manage-system-topics/system-topic-delete-button.png)
3. 在 [確認] 頁面上，選取 **[確定]** 確認刪除。 它會刪除系統主題，也會刪除系統主題的所有事件訂閱。  

## <a name="create-an-event-subscription"></a>建立事件訂閱
1. 遵循 [ [view system 主題](#view-all-system-topics) ] 區段中的指示來查看所有系統主題，然後從清單中選取您要刪除的系統主題。 
2. 在 [ **事件方格系統] 主題** 頁面的工具列中，選取 [ **+ 事件訂閱** ]。 

    ![系統主題-新增事件訂用帳戶按鈕](./media/create-view-manage-system-topics/add-event-subscription-button.png)
3. 確認 [ **主題類型**]、[ **來源資源**] 和 [ **主題名稱** ] 都會自動填入。 輸入名稱、選取 **端點類型**，然後指定 **端點**。 然後，選取 [ **建立** ] 以建立事件訂用帳戶。 

    ![系統主題-建立事件訂閱](./media/create-view-manage-system-topics/create-event-subscription.png)

## <a name="next-steps"></a>接下來的步驟
若要深入瞭解 Azure 事件方格所支援的系統主題和主題類型，請參閱 [Azure 事件方格中的系統主題](system-topics.md) 一節。 
