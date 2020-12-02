---
title: 使用 Azure Logic Apps 來處理透過事件方格收到的服務匯流排事件
description: 本文提供使用 Azure Logic Apps 來處理透過事件方格收到的服務匯流排事件的步驟。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 10/16/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: 93375f6047fbe4eda2132e024dab0e067e83ccf1
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95998971"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-logic-apps"></a>教學課程：使用 Azure Logic Apps，回應透過 Azure 事件方格接收到的 Azure 服務匯流排事件
在本教學課程中，您將了解如何使用 Azure Logic Apps 來回應透過 Azure 事件方格接收到的 Azure 服務匯流排事件。 

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="receive-messages-by-using-logic-apps"></a>使用 Logic Apps 接收訊息
在此步驟中，您會建立 Azure 邏輯應用程式，以透過 Azure 事件方格接收服務匯流排事件。 

1. 在 Azure 入口網站中建立邏輯應用程式。
    1. 依序選取 [+ 建立資源]、[整合] 和 [邏輯應用程式]。 
    2. 在 [邏輯應用程式 - 建立] 頁面上，輸入邏輯應用程式的 **名稱**。
    3. 選取您的 Azure **訂用帳戶**。 
    4. 在 [資源群組] 中選取 [使用現有項目]，然後選取要用於稍早所建立其他資源 (例如 Azure 函式、服務匯流排命名空間) 的資源群組。 
    5. 選取邏輯應用程式的 [位置]。 
    6. 選取 [檢閱 + 建立]  。 
    1. 在 [檢閱 + 建立] 頁面上選取 [建立]，以建立邏輯應用程式。 
1. 在 [Logic Apps 設計工具] 頁面上，選取 [範本] 底下的 [空白邏輯應用程式]。 
1. 在設計工具上執行下列步驟：
    1. 搜尋 **事件方格**。 
    2. 選取 [發生資源事件時 - Azure 事件方格]。 

        ![Logic Apps 設計工具 - 選取事件方格觸發程序](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. 選取 [登入]、輸入 Azure 認證，然後選取 [允許存取]。 
5. 在 [發生資源事件時] 頁面上，執行下列步驟：
    1. 選取 Azure 訂用帳戶。 
    2. 在 [資源類型] 中，選取 [Microsoft.ServiceBus.Namespaces]。 
    3. 在 [資源名稱] 中，選取您的服務匯流排命名空間。 
    4. 選取 [新增參數]，然後選取 [尾碼篩選]。 
    5. 在 [尾碼篩選] 中，輸入第二個服務匯流排主題訂用帳戶的名稱。 
        ![Logic Apps 設計工具 - 設定事件](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. 在設計工具中選取 [+ 新增步驟]，然後執行下列步驟：
    1. 搜尋 **服務匯流排**。
    2. 在清單中選取 [服務匯流排]。 
    3. 在 [動作] 清單中針對 [取得訊息] 進行選取。 
    4. 選取 [從主題訂用帳戶取得訊息 (查看鎖定)]。 

        ![Logic Apps 設計工具 - 取得訊息動作](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. 輸入 **連線的名稱**。 例如：**從主題訂用帳戶取得訊息**，然後選取服務匯流排命名空間。 

        ![Logic Apps 設計工具 - 選取服務匯流排命名空間](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. 選取 [RootManageSharedAccessKey]，然後選取 [建立]。

        ![Logic Apps 設計工具 - 選取共用存取金鑰](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    8. 選取您的 **主題** 和 **訂用帳戶**。 
    
        ![此螢幕擷取畫面顯示您選取主題和訂用帳戶的位置。](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. 選取 [+ 新增步驟]，然後執行下列步驟： 
    1. 選取 [服務匯流排]。
    2. 從動作清單中，選取 [完成主題訂用帳戶中的訊息]。 
    3. 選取您的服務匯流排 **主題**。
    4. 選取主題的第二個 **訂用帳戶**。
    5. 在 [訊息的鎖定權杖] 中，從 [動態內容] 選取 [鎖定權杖]。 

        ![Logic Apps 設計工具 - 完成訊息](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. 在 Logic Apps 設計工具的工具列上選取 [儲存]，以儲存邏輯應用程式。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/save-logic-app.png" alt-text="儲存邏輯應用程式":::
1. 如果您尚未將測試訊息傳送至主題，請依照[對服務匯流排主題傳送訊息](#send-messages-to-the-service-bus-topic)一節中的指示，將訊息傳送至主題。 
1. 切換至邏輯應用程式的 [概觀] 頁面。 您便會在所傳送訊息的 [執行歷程記錄] 中看到邏輯應用程式在執行。 可能需要幾分鐘的時間，您才會看到邏輯應用程式執行。 選取工具列上的 [重新整理]，以重新整理頁面。 

    ![Logic Apps 設計工具 - 邏輯應用程式執行](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)
1. 選取邏輯應用程式執行以查看詳細資料。 您可以看到它在 For 迴圈中處理了 5 則訊息。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/logic-app-run-details.png" alt-text="邏輯應用程式執行詳細資料":::    

## <a name="troubleshoot"></a>疑難排解
如果您在等候並重新整理後並未看到任何叫用，請執行下列步驟： 

1. 確認訊息已抵達服務匯流排主題。 查看 [服務匯流排主題] 頁面上的 [傳入訊息] 計數器。 在此案例中，我執行了 **MessageSender** 應用程式兩次，因此我看到 10 則訊息 (每次執行各 5 則訊息)。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="[服務匯流排主題] 頁面 - 傳入訊息":::    
1. 確認服務匯流排訂用帳戶中 **沒有任何作用中訊息**。 
    如果您在此頁面上未看到任何事件，請確認 [服務匯流排訂用帳戶] 頁面未顯示任何 [作用中訊息計數]。 如果此計數器的數字大於零，表示訂用帳戶的訊息因故無法轉送至處理常式函式 (事件訂用帳戶處理常式)。 請確認您已正確設定事件訂用帳戶。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="服務匯流排訂用帳戶的作用中訊息計數":::    
1. 您也會在服務匯流排命名空間的 [事件] 頁面上看到 [已傳遞的事件]。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="[事件] 頁面 - 已傳遞的事件" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. 您也可以在 [事件訂用帳戶] 頁面上看到事件已傳遞。 您可以在 [事件] 頁面上選取事件訂用帳戶，以進入此頁面。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-delivered-events.png" alt-text="事件訂用帳戶頁面 - 已傳遞的事件":::
## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Event Grid](../event-grid/index.yml)。
* 深入了解 [Azure Functions](../azure-functions/index.yml)。
* 深入了解 [Azure App Service 的 Logic Apps 功能](../logic-apps/index.yml)。
* 深入了解 [Azure 服務匯流排](/azure/service-bus/)。


[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png