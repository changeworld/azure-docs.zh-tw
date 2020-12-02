---
title: 使用 Azure Functions 透過事件方格處理服務匯流排事件
description: 本文提供使用 Azure Functions 透過事件方格處理服務匯流排事件的步驟。
documentationcenter: .net
author: spelluru
ms.topic: tutorial
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-csharp
ms.openlocfilehash: afc0a5bf9b83363d1f4baab955b55148fe3a8498
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95818432"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions"></a>教學課程：使用 Azure Functions 來回應透過 Azure 事件方格所收到的 Azure 服務匯流排事件
在本教學課程中，您將了解如何使用 Azure Functions 和 Azure Logic Apps 來回應透過 Azure 事件方格所收到的 Azure 服務匯流排事件。 

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立服務匯流排命名空間
> * 準備用來傳送訊息的應用程式範例
> * 對服務匯流排主題傳送訊息
> * 使用 Logic Apps 接收訊息
> * 在 Azure 上設定測試函式
> * 透過 Event Grid 連線函式和命名空間
> * 使用 Azure Functions 接收訊息

[!INCLUDE [service-bus-event-grid-prerequisites](../../includes/service-bus-event-grid-prerequisites.md)]

## <a name="additional-prerequisites"></a>其他先決條件
安裝 [Visual Studio 2019](https://www.visualstudio.com/vs) 並納入 **Azure 開發** 工作負載。 此工作負載包含要在 Visual Studio 中建立、建置及部署 Azure Functions 專案所必須擁有的 **Azure Function Tools**。 

## <a name="deploy-the-function-app"></a>部署函數應用程式 

>[!NOTE]
> 若要深入了解如何建立和部署 Azure Functions 應用程式，請參閱[使用 Visual Studio 開發 Azure Functions](../azure-functions/functions-develop-vs.md)

1. 從 **SBEventGridIntegration.sln** 的 **FunctionApp1** 專案中，開啟 **ReceiveMessagesOnEvent.cs** 檔案。 
1. 將 `<SERCICE BUS NAMESPACE - CONNECTION STRING>` 取代為服務匯流排命名空間的連接字串。 其應該與您在同一個解決方案中 **MessageSender** 專案的 **Program.cs** 檔案所使用的連接字串相同。 
1. 以滑鼠右鍵按一下 [FunctionApp1]，然後選取 [發佈]。 
1. 在 [發佈] 頁面上選取 [開始]。 這些步驟可能會與您所看到的不同，但發佈程序應該會類似。 
1. 在 [發佈] 精靈的 [目標] 頁面上，選取 [Azure] 作為 [目標]。 
1. 在 [特定目標] 頁面上，選取 [Azure 函式應用程式 (Windows)]。 
1. 在 [函式執行個體] 頁面上，選取 [建立新的 Azure 函式]。 
1. 在 [函式應用程式 (Windows)] 頁面上，遵循下列步驟：
    1. 輸入函式應用程式的 **名稱**。
    1. 選取 Azure **訂用帳戶**。
    1. 選取現有的 **資源群組** 或建立新的資源群組。 在本教學課程中，請選取具有服務匯流排命名空間的資源群組。 
    1. 選取 App Service 的 **方案類型**。
    1. 選取 **位置**。 選取與服務匯流排命名空間相同的位置。 
    1. 選取現有的 [Azure 儲存體] 或選取 [新增]，以建立新的儲存體帳戶供函式應用程式使用。 
    1. 選取 [建立] 以建立函式應用程式。 
1. 回到 [發佈] 精靈的 [函式執行個體] 頁面上，選取 [完成]。 
1. 在 Visual Studio 的 [發佈] 頁面上，選取 [發佈] 以將函式應用程式發佈到 Azure。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/publish-function-app.png" alt-text="發佈函式應用程式":::    
1. 在 [輸出] 視窗中，查看來自建置和發佈的訊息，並確認兩者皆已成功。 
1. 現在，在 [發佈] 頁面上，選取 [在 Azure 入口網站中管理]。 
1. 在 Azure 入口網站的 [函式應用程式] 頁面上，選取左側功能表中的 [函式]，並確認您有看到兩個函式： 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/functions.png" alt-text="用來處理事件方格觸發程序和 HTTP 觸發程序的兩個函式":::

    > [!NOTE]
    > `EventGridTriggerFunction` 會使用[事件方格觸發程序](../azure-functions/functions-bindings-event-grid-trigger.md)，而 `HTTPTriggerFunction` 會使用 [HTTP 觸發程序](../azure-functions/functions-bindings-http-webhook-trigger.md)。
1. 從清單中選取 [EventGridTriggerFunction]。 建議您使用事件方格觸發程序來搭配 Azure Functions，因為這會比使用 HTTP 觸發程序多幾個好處。 如需詳細資訊，請參閱[以 Azure 函式作為事件方格事件的事件處理常式](../event-grid/handler-functions.md)。
1. 在 **EventGridTriggerFunction** 的 [函式] 頁面上，選取左側功能表上的 [監視器]。 
1. 選取 [設定] 來設定 Application Insights 以擷取叫用記錄。 您可以使用此頁面來監視從事件方格接收服務匯流排事件時的函式執行情形。 
1. 在 [Application Insights] 頁面上輸入資源的名稱、為資源選取 **位置**，然後選取 [確定]。 
1. 選取頂端 (階層連結功能表) 的函式 **EventGridTriggerFunction**，以瀏覽回 [函式] 頁面。 
1. 確認您在 [監視器] 頁面上。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/monitor-before.png" alt-text="函式叫用之前函式的 [監視器] 頁面":::
    
    讓此頁面在網頁瀏覽器的索引標籤中保持開啟。 您稍後要重新整理此頁面，以查看此函式的叫用。

## <a name="connect-the-function-and-namespace-via-event-grid"></a>透過 Event Grid 連線函式和命名空間
在這一節中，請使用 Azure 入口網站將函式與服務匯流排命名空間繫結在一起。 

若要建立 Azure 事件方格訂用帳戶，請遵循下列步驟：

1. 在 Azure 入口網站中移至您的命名空間，然後在左窗格中選取 [事件]。 您的命名空間視窗隨即開啟，其右窗格中顯示兩個 Event Grid 訂用帳戶。 
    
    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png" alt-text="服務匯流排 - 事件頁面":::
2. 選取工具列上的 [+ 事件訂用帳戶]。 
3. 在 [建立事件訂用帳戶] 頁面上，執行下列步驟：
    1. 輸入訂用帳戶的 **名稱**。 
    2. 輸入 **系統主題** 的 **名稱**。 系統主題是針對 Azure 資源所建立的主題，例如 Azure 儲存體帳戶和 Azure 服務匯流排。 若要深入了解系統主題，請參閱[系統主題概觀](../event-grid/system-topics.md)。
    2. 針對 [端點類型] 選取 [Azure Function]，然後按一下 [選取端點]。 

        ![服務匯流排 - 事件方格訂用帳戶](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. 在 [選取 Azure 函式] 頁面上，選取訂用帳戶、資源群組、函式應用程式、位置和函式，然後選取 [確認選取項目]。 

        ![函式 - 選取端點](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. 在 [建立事件訂用帳戶] 頁面上，切換至 [篩選] 索引標籤，並執行下列工作：
        1. 選取 [啟用主旨篩選]
        2. 針對您稍早建立的服務匯流排主題，輸入其訂用帳戶的名稱 (**S1**)。
        3. 選取 [建立] 按鈕。 

            ![事件訂用帳戶篩選條件](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. 切換至 [事件] 頁面的 [事件訂閱] 索引標籤，並確認您有在清單中看到所需的事件訂閱。

    ![清單中的事件訂用帳戶](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="monitor-the-functions-app"></a>監視函式應用程式
您先前傳送至服務匯流排主題的訊息會轉送到訂用帳戶 (S1)。 事件方格會將訂用帳戶上的訊息轉送至 Azure 函式。 在教學課程的這個步驟中，您會確認函式已完成叫用，並檢視已記錄的參考訊息。 

1. 在 Azure 函式應用程式的頁面上，切換至 [監視器] 索引標籤 (如果此索引標籤尚未啟用的話)。 對於張貼至服務匯流排主題的每則訊息，您應會看到一個項目。 如果您看不到，請在等候幾分鐘後重新整理頁面。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-monitor.png" alt-text="叫用之後函式的 [監視器] 頁面":::
2. 從清單中選取叫用，以查看詳細資料。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/invocation-details.png" alt-text="函式叫用詳細資料" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::

    您也可以使用 [監視器] 頁面的 [記錄] 索引標籤，在訊息傳送後查看記錄資訊。 可能會有一些延遲，所以幾分鐘後再查看記錄的訊息。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/function-logs.png" alt-text="函式記錄" lightbox="./media/service-bus-to-event-grid-integration-example/function-logs.png":::

## <a name="troubleshoot"></a>疑難排解
如果您等候一會兒並重新整理之後沒有看到任何函式叫用，請遵循下列步驟： 

1. 確認訊息已抵達服務匯流排主題。 查看 [服務匯流排主題] 頁面上的 [傳入訊息] 計數器。 在此案例中，我執行了 **MessageSender** 應用程式兩次，因此我看到 10 則訊息 (每次執行各 5 則訊息)。

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/topic-incoming-messages.png" alt-text="[服務匯流排主題] 頁面 - 傳入訊息":::    
1. 確認服務匯流排訂用帳戶中 **沒有任何作用中訊息**。 
    如果您在此頁面上未看到任何事件，請確認 [服務匯流排訂用帳戶] 頁面未顯示任何 [作用中訊息計數]。 如果此計數器的數字大於零，則會因為某些原因而無法將訂用帳戶的訊息轉送至處理常式函式 (事件訂用帳戶處理常式)。 請確認您已正確設定事件訂用帳戶。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/subscription-active-message-count.png" alt-text="服務匯流排訂用帳戶的作用中訊息計數":::    
1. 您也會在服務匯流排命名空間的 [事件] 頁面上看到 [已傳遞的事件]。 

    :::image type="content" source="./media/service-bus-to-event-grid-integration-example/event-subscription-page.png" alt-text="[事件] 頁面 - 已傳遞的事件" lightbox="./media/service-bus-to-event-grid-integration-example/invocation-details.png":::
1. 您也可以在 [事件訂用帳戶] 頁面上看到事件已傳遞。 您可以在 [事件] 頁面上選取事件訂用帳戶，來進入此頁面。 
    
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