---
title: 教學課程：Azure 服務匯流排與事件方格的整合範例
description: 教學課程：本文提供服務匯流排傳訊與 Event Grid 的整合範例。
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: spelluru
ms.openlocfilehash: fef325b67c38eda09a05dac9d74bd5b97df164cc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067775"
---
# <a name="tutorial-respond-to-azure-service-bus-events-received-via-azure-event-grid-by-using-azure-functions-and-azure-logic-apps"></a>教學課程：使用 Azure Functions 和 Azure Logic Apps 來回應透過 Azure 事件方格所收到的 Azure 服務匯流排事件
在本教學課程中，您將了解如何使用 Azure Functions 和 Azure Logic Apps 來回應透過 Azure 事件方格所收到的 Azure 服務匯流排事件。 

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立服務匯流排命名空間
> * 準備用來傳送訊息的應用程式範例
> * 在 Azure 上設定測試函式
> * 透過 Event Grid 連線函式和命名空間
> * 對服務匯流排主題傳送訊息
> * 使用 Azure Functions 接收訊息
> * 使用 Logic Apps 接收訊息

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請確定您已安裝︰

- [Visual Studio 2017 Update 3 (版本 15.3, 26730.01)](https://www.visualstudio.com/vs) 或更新版本。
- [.NET Core SDK](https://www.microsoft.com/net/download/windows)，2.0 版或更新版本。

## <a name="create-a-service-bus-namespace"></a>建立服務匯流排命名空間
請遵循下列教學課程中的指示：[快速入門：使用 Azure 入口網站建立服務匯流排主題和主題的訂用帳戶](service-bus-quickstart-topics-subscriptions-portal.md)，以執行下列工作：

- 建立**進階**服務匯流排命名空間。 
- 取得連接字串。 
- 建立服務匯流排主題。
- 針對該主題建立兩個訂用帳戶。 

## <a name="prepare-a-sample-application-to-send-messages"></a>準備用來傳送訊息的應用程式範例
您可以使用任何方法，將訊息傳送至服務匯流排主題。 此程序結尾的範例程式碼假設您目前使用 Visual Studio 2017。

1. 複製 [GitHub azure-service-bus 存放庫](https://github.com/Azure/azure-service-bus/)。
2. 在 Visual Studio 中，移至 \samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration  資料夾，然後開啟 SBEventGridIntegration.sln  檔案。
3. 移至 **MessageSender** 專案，然後選取 **Program.cs**。
4. 填入您從上一個步驟獲得的服務匯流排主題名稱和連接字串：

    ```csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    const string TopicName = "YOUR TOPIC NAME";
    ```
5. 建置並執行程式，以將測試訊息傳送至服務匯流排主題。 

## <a name="set-up-a-test-function-on-azure"></a>在 Azure 上設定測試函式 
在處理整個案例之前，至少設定一個小型測試函式，以便用來偵錯及觀察正在流動的事件。 請遵循[在 Azure 入口網站中建立您的第一個函式](../azure-functions/functions-create-first-azure-function.md)一文中的指示，以執行下列工作： 

1. 建立函式應用程式。
2. 建立由 HTTP 觸發的函式。 

然後，執行下列步驟： 


# <a name="azure-functions-v2"></a>[Azure Functions V2](#tab/v2)

1. 在樹狀檢視中展開 [Functions]  ，然後選取您的函式。 使用下列程式碼取代該函式的程式碼： 

    ```csharp
    #r "Newtonsoft.Json"
    
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");
        var content = req.Body;
        string jsonContent = await new StreamReader(content).ReadToEndAsync();
        log.LogInformation($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        headerValues = req.Headers.GetCommaSeparatedValues("Aeg-Event-Type");
    
        if (headerValues.Count() != 0)
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
                var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                var code = events[0].Data["validationCode"];
                log.LogInformation("Validation code: {code}");
                return (ActionResult) new OkObjectResult(new { validationResponse = code });
            }
        }
    
        return jsonContent == null
            ? new BadRequestObjectResult("Please pass a name on the query string or in the request body")
            : (ActionResult)new OkObjectResult($"Hello, {jsonContent}");
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    
    ```
2. 選取 [儲存並執行]  。

    ![函式應用程式輸出](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
3. 選取 [取得函式 URL]  ，然後記下該 URL。 

    ![取得函式 URL](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

# <a name="azure-functions-v1"></a>[Azure Functions V1](#tab/v1)

1. 將函式設定為使用 **V1** 版本： 
    1. 在樹狀檢視中選取您的函式應用程式，然後選取 [函式應用程式設定]  。 

        ![函數應用程式設定]()./media/service-bus-to-event-grid-integration-example/function-app-settings.png)
    2. 選取 [~1]  作為 [執行階段版本]  。 
2. 在樹狀檢視中展開 [Functions]  ，然後選取您的函式。 使用下列程式碼取代該函式的程式碼： 

    ```csharp
    #r "Newtonsoft.Json"
    using System.Net;
    using Newtonsoft.Json;
    using Newtonsoft.Json.Linq;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info("C# HTTP trigger function processed a request.");
        // parse query parameter
        var content = req.Content;
    
        string jsonContent = await content.ReadAsStringAsync(); 
        log.Info($"Received Event with payload: {jsonContent}");
    
        IEnumerable<string> headerValues;
        if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
        {
            var validationHeaderValue = headerValues.FirstOrDefault();
            if(validationHeaderValue == "SubscriptionValidation")
            {
            var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
                 var code = events[0].Data["validationCode"];
                 return req.CreateResponse(HttpStatusCode.OK,
                 new { validationResponse = code });
            }
        }
    
        return jsonContent == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
    }
    
    public class GridEvent
    {
        public string Id { get; set; }
        public string EventType { get; set; }
        public string Subject { get; set; }
        public DateTime EventTime { get; set; }
        public Dictionary<string, string> Data { get; set; }
        public string Topic { get; set; }
    }
    ```
4. 選取 [儲存並執行]  。

    ![函式應用程式輸出](./media/service-bus-to-event-grid-integration-example/function-run-output.png)
4. 選取 [取得函式 URL]  ，然後記下該 URL。 

    ![取得函式 URL](./media/service-bus-to-event-grid-integration-example/get-function-url.png)

---

## <a name="connect-the-function-and-namespace-via-event-grid"></a>透過 Event Grid 連線函式和命名空間
在這一節中，請使用 Azure 入口網站將函式與服務匯流排命名空間繫結在一起。 

若要建立 Azure 事件方格訂用帳戶，請遵循下列步驟：

1. 在 Azure 入口網站中移至您的命名空間，然後在左窗格中選取 [事件]  。 您的命名空間視窗隨即開啟，其右窗格中顯示兩個 Event Grid 訂用帳戶。 
    
    ![服務匯流排 - 事件頁面](./media/service-bus-to-event-grid-integration-example/service-bus-events-page.png)
2. 選取工具列上的 [+ 事件訂用帳戶]  。 
3. 在 [建立事件訂用帳戶]  頁面上，執行下列步驟：
    1. 輸入訂用帳戶的**名稱**。 
    2. 選取 [Web Hook]  作為 [端點類型]  。 

        ![服務匯流排 - 事件方格訂用帳戶](./media/service-bus-to-event-grid-integration-example/event-grid-subscription-page.png)
    3. 選擇 [選取端點]  、貼上函式 URL，然後選取 [確認選取項目]  。 

        ![函式 - 選取端點](./media/service-bus-to-event-grid-integration-example/function-select-endpoint.png)
    4. 切換至 [篩選條件]  索引標籤，輸入稍早所建立服務匯流排主題**第一個訂用帳戶**的名稱，然後選取 [建立]  按鈕。 

        ![事件訂用帳戶篩選條件](./media/service-bus-to-event-grid-integration-example/event-subscription-filter.png)
4. 確認您有在清單中看到事件訂用帳戶。

    ![清單中的事件訂用帳戶](./media/service-bus-to-event-grid-integration-example/event-subscription-in-list.png)

## <a name="send-messages-to-the-service-bus-topic"></a>對服務匯流排主題傳送訊息
1. 執行 .NET C# 應用程式，以將訊息傳送至服務匯流排主題。 

    ![主控台應用程式輸出](./media/service-bus-to-event-grid-integration-example/console-app-output.png)
1. 在 Azure 函式應用程式的頁面上，依序展開 [Functions]  和您的 [函式]  ，然後選取 [監視]  。 

    ![監視函式](./media/service-bus-to-event-grid-integration-example/function-monitor.png)

## <a name="receive-messages-by-using-azure-functions"></a>使用 Azure Functions 接收訊息
在前一節中，您已觀察到簡單測試和偵錯案例，並確定事件正在流動。 

在這一節中，您將了解如何在收到事件之後接收和處理訊息。

### <a name="publish-a-function-from-visual-studio"></a>從 Visual Studio 發佈函式
1. 在您開啟的同一個 Visual Studio 解決方案 (**SBEventGridIntegration**) 中，選取 **SBEventGridIntegration** 專案中的 **ReceiveMessagesOnEvent.cs**。 
2. 在下列程式碼中輸入您的服務匯流排連接字串：

    ```Csharp
    const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
    ```
3. 下載函式的**發行設定檔**：
    1. 選取您的函式應用程式。 
    2. 選取 [概觀]  索引標籤 (如果尚未選取)。 
    3. 選取工具列上的 [取得發行設定檔]  。 

        ![取得函式的發行設定檔](./media/service-bus-to-event-grid-integration-example/function-download-publish-profile.png)
    4. 將該檔案儲存到您專案的資料夾中。 
4. 在 Visual Studio 中，以滑鼠右鍵按一下 [SBEventGridIntegration]  ，然後選取 [發佈]  。 
5. 在 [發佈]  頁面上選取 [開始]  。 
6. 在 [挑選發佈目標]  頁面上執行下列步驟，選取 [匯入設定檔]  。 

    ![Visual Studio - [匯入設定檔] 按鈕](./media/service-bus-to-event-grid-integration-example/visual-studio-import-profile-button.png)
7. 選取您稍早下載的 [發行設定檔]  。 
8. 選取 [發佈]  頁面上的 [發佈]  。 

    ![Visual Studio - 發佈](./media/service-bus-to-event-grid-integration-example/select-publish.png)
9. 確認您有看到新的 Azure 函式 **ReceiveMessagesOnEvent**。 請視需要重新整理頁面。 

    ![確認已建立新的函式](./media/service-bus-to-event-grid-integration-example/function-receive-messages.png)
10. 取得新函式的 URL 並記下來。 

### <a name="event-grid-subscription"></a>事件方格訂用帳戶

1. 刪除現有的事件方格訂用帳戶：
    1. 在 [服務匯流排命名空間]  頁面上，選取左側功能表上的 [事件]  。 
    2. 選取現有的事件訂用帳戶。 
    3. 在 [事件訂用帳戶]  頁面上，選取 [刪除]  。
2. 遵循[透過事件方格連接函式和命名空間](#connect-the-function-and-namespace-via-event-grid)一節中的指示，以使用新的函式 URL 建立事件方格訂用帳戶。
3. 遵循[對服務匯流排主題傳送訊息](#send-messages-to-the-service-bus-topic)一節中的指示，來對主題傳送訊息並監視函式。 

## <a name="receive-messages-by-using-logic-apps"></a>使用 Logic Apps 接收訊息
請遵循下列步驟，來連接邏輯應用程式與 Azure 服務匯流排和 Azure 事件方格：

1. 在 Azure 入口網站中建立邏輯應用程式。
    1. 依序選取 [+ 建立資源]  、[整合]  和 [邏輯應用程式]  。 
    2. 在 [邏輯應用程式 - 建立]  頁面上，輸入邏輯應用程式的**名稱**。
    3. 選取您的 Azure **訂用帳戶**。 
    4. 在 [資源群組]  中選取 [使用現有項目]  ，然後選取要用於稍早所建立其他資源 (例如 Azure 函式、服務匯流排命名空間) 的資源群組。 
    5. 選取邏輯應用程式的 [位置]  。 
    6. 選取 [建立]  以建立邏輯應用程式。 
2. 在 [Logic Apps 設計工具]  頁面上，選取 [範本]  底下的 [空白邏輯應用程式]  。 
3. 在設計工具上執行下列步驟：
    1. 搜尋**事件方格**。 
    2. 選取 [發生資源事件時 (預覽) - Azure 事件方格]  。 

        ![Logic Apps 設計工具 - 選取事件方格觸發程序](./media/service-bus-to-event-grid-integration-example/logic-apps-event-grid-trigger.png)
4. 選取 [登入]  、輸入 Azure 認證，然後選取 [允許存取]  。 
5. 在 [發生資源事件時]  頁面上，執行下列步驟：
    1. 選取 Azure 訂用帳戶。 
    2. 在 [資源類型]  中，選取 [Microsoft.ServiceBus.Namespaces]  。 
    3. 在 [資源名稱]  中，選取您的服務匯流排命名空間。 
    4. 選取 [新增參數]  ，然後選取 [尾碼篩選]  。 
    5. 在 [尾碼篩選]  中，輸入第二個服務匯流排主題訂用帳戶的名稱。 
        ![Logic Apps 設計工具 - 設定事件](./media/service-bus-to-event-grid-integration-example/logic-app-configure-event.png)
6. 在設計工具中選取 [+ 新增步驟]  ，然後執行下列步驟：
    1. 搜尋**服務匯流排**。
    2. 在清單中選取 [服務匯流排]  。 
    3. 在 [動作]  清單中針對 [取得訊息]  進行選取。 
    4. 選取 [從主題訂用帳戶取得訊息 (查看鎖定)]  。 

        ![Logic Apps 設計工具 - 取得訊息動作](./media/service-bus-to-event-grid-integration-example/service-bus-get-messages-step.png)
    5. 輸入**連線的名稱**。 例如：**從主題訂用帳戶取得訊息**，然後選取服務匯流排命名空間。 

        ![Logic Apps 設計工具 - 選取服務匯流排命名空間](./media/service-bus-to-event-grid-integration-example/logic-apps-select-namespace.png) 
    6. 選取 [RootManageSharedAccessKey]  。

        ![Logic Apps 設計工具 - 選取共用存取金鑰](./media/service-bus-to-event-grid-integration-example/logic-app-shared-access-key.png) 
    7. 選取 [建立]  。 
    8. 選取您的主題和訂用帳戶。 
    
        ![Logic Apps 設計工具 - 選取您的服務匯流排主題和訂用帳戶](./media/service-bus-to-event-grid-integration-example/logic-app-select-topic-subscription.png)
7. 選取 [+ 新增步驟]  ，然後執行下列步驟： 
    1. 選取 [服務匯流排]  。
    2. 從動作清單中，選取 [完成主題訂用帳戶中的訊息]  。 
    3. 選取您的服務匯流排**主題**。
    4. 選取主題的第二個**訂用帳戶**。
    5. 在 [訊息的鎖定權杖]  中，從 [動態內容]  選取 [鎖定權杖]  。 

        ![Logic Apps 設計工具 - 選取您的服務匯流排主題和訂用帳戶](./media/service-bus-to-event-grid-integration-example/logic-app-complete-message.png)
8. 在 Logic Apps 設計工具的工具列上選取 [儲存]  ，以儲存邏輯應用程式。 
9. 遵循[對服務匯流排主題傳送訊息](#send-messages-to-the-service-bus-topic)一節中的指示，來對主題傳送訊息。 
10. 切換至邏輯應用程式的 [概觀]  頁面。 您便會在所傳送訊息的 [執行歷程記錄]  中看到邏輯應用程式在執行。

    ![Logic Apps 設計工具 - 邏輯應用程式執行](./media/service-bus-to-event-grid-integration-example/logic-app-runs.png)

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/)。
* 深入了解 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/)。
* 深入了解 [Azure App Service 的 Logic Apps 功能](https://docs.microsoft.com/azure/logic-apps/)。
* 深入了解 [Azure 服務匯流排](https://docs.microsoft.com/azure/service-bus/)。


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
