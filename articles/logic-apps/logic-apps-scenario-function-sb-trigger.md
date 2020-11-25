---
title: 使用 Azure Functions 呼叫邏輯應用程式
description: 使用 Azure Functions 和 Azure 服務匯流排來呼叫或觸發邏輯應用程式
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 25f761d85ebfd0ac16f182941c5b5c29636066bf
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000329"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>使用 Azure Functions 和 Azure 服務匯流排來呼叫或觸發邏輯應用程式

當您需要部署長時間執行的接聽程式或工作時，可以使用 [Azure Functions](../azure-functions/functions-overview.md) 來觸發邏輯應用程式。 例如，您可以建立一個函式，以在 [Azure 服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md) 佇列上接聽，並立即將邏輯應用程式引發為推送觸發程式。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 服務匯流排命名空間。 如果您沒有命名空間，請 [先建立命名空間](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* 函數應用程式，這是您函式的容器。 如果您沒有函式應用程式，請 [先建立函式應用程式](../azure-functions/functions-create-first-azure-function.md)，並確定您選取的是 [.net] 做為執行時間堆疊。

* [如何建立邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

## <a name="create-logic-app"></a>建立邏輯應用程式

在此案例中，您有一個函式正在執行您想要觸發的每個邏輯應用程式。 首先，建立以 HTTP 要求觸發程式開頭的邏輯應用程式。 每當收到佇列訊息時，函數即會呼叫該端點。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後建立空白的邏輯應用程式。

   如果您還不熟悉邏輯應用程式，請檢閱[快速入門：如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在搜尋方塊中，輸入 `http request`。 從觸發程式清單中，選取 [ **收到 HTTP 要求時** ] 觸發程式。

   ![選取觸發程序](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   使用要求觸發程式時，您可以選擇性地輸入要搭配佇列訊息使用的 JSON 架構。 JSON 架構可協助邏輯應用程式設計工具瞭解輸入資料的結構，並讓您更輕鬆地在工作流程中使用輸出。

1. 若要指定結構描述，請在 [要求本文 JSON 結構描述] 方塊中輸入結構描述，例如：

   ![指定 JSON 結構描述](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   如果您沒有結構描述，但是有 JSON 格式的範例承載，即可從該承載產生 JSON 結構描述。

   1. 在要求觸發程序中，選取 [使用範例承載來產生結構描述]。

   1. 在 [ **輸入或貼上範例 JSON** 承載] 下，輸入您的範例承載，然後選取 [ **完成**]。

      ![輸入範例承載](./media/logic-apps-scenario-function-sb-trigger/enter-sample-payload.png)

   此範例承載會產生出現在觸發程序中的這個結構描述：

   ```json
   {
      "type": "object",
      "properties": {
         "address": {
            "type": "object",
            "properties": {
               "number": {
                  "type": "integer"
               },
               "street": {
                  "type": "string"
               },
               "city": {
                  "type": "string"
               },
               "postalCode": {
                  "type": "integer"
               },
               "country": {
                  "type": "string"
               }
            }
         }
      }
   }
   ```

1. 新增您想要在收到佇列訊息之後執行的任何其他動作。

   例如，您可以使用 Office 365 Outlook 連接器傳送電子郵件。

1. 儲存邏輯應用程式，進而為此邏輯應用程式中的觸發程序產生回呼 URL。 稍後，您會在程式碼中針對 Azure 服務匯流排佇列觸發程式使用此回呼 URL。

   回呼 URL 會出現在 **HTTP POST url** 屬性中。

   ![為觸發程序產生的回呼 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-a-function"></a>建立函式

接著，建立一個函式，作為觸發程序並接聽佇列。

1. 在 Azure 入口網站中，開啟並展開函式應用程式 (如果尚未開啟)。 

1. 在函式應用程式名稱之下，展開 [Functions]。 在 [ **函數** ] 窗格中，選取 [ **新增函數**]。

   ![展開 [函數]，然後選取 [新增函式]](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. 根據您是否已建立新的函式應用程式，並在其中選取 .NET 作為執行時間堆疊，或您使用現有的函數應用程式，來選取此範本。

   * 針對新的函數應用程式，請選取此範本：**服務匯流排佇列觸發** 程式

     ![選取新函數應用程式的範本](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 針對現有的函數應用程式，請選取此範本： **服務匯流排佇列觸發程式-c #**

     ![選取現有函數應用程式的範本](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. 在 [ **Azure 服務匯流排佇列觸發** 程式] 窗格中，為您的觸發程式提供名稱，並設定佇列的 **服務匯流排連接** （使用 Azure 服務匯流排 SDK 接聽程式 `OnMessageReceive()` ），然後選取 [ **建立**]。

1. 撰寫基本函式，以使用佇列訊息做為觸發程式來呼叫先前建立的邏輯應用程式端點。 撰寫您的函式之前，請先參閱下列考慮：

   * 此範例會使用 `application/json` 訊息內容類型，但是您可以視需要變更此類型。
   
   * 由於可能同時執行的函式、高磁片區或大量載入，請避免使用語句來具現化 [HTTPClient 類別](/dotnet/api/system.net.http.httpclient) ， `using` 並針對每個要求直接建立 HTTPClient 實例。 如需詳細資訊，請參閱 [使用 HttpClientFactory 來執行復原的 HTTP 要求](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)。
   
   * 可能的話，請重複使用 HTTP 用戶端的實例。 如需詳細資訊，請參閱 [Azure Functions 中的管理連接](../azure-functions/manage-connections.md)。

   這個範例會使用[非同步](/dotnet/csharp/language-reference/keywords/async)模式的[ `Task.Run` 方法](/dotnet/api/system.threading.tasks.task.run)。 如需詳細資訊，請參閱 [使用 async 和 await 進行非同步程式設計](/dotnet/csharp/programming-guide/concepts/async/)。

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Net.Http;
   using System.Text;

   // Can also fetch from App Settings or environment variable
   private static string logicAppUri = @"https://prod-05.westus.logic.azure.com:443/workflows/<remaining-callback-URL>";

   // Reuse the instance of HTTP clients if possible: https://docs.microsoft.com/azure/azure-functions/manage-connections
   private static HttpClient httpClient = new HttpClient();

   public static async Task Run(string myQueueItem, TraceWriter log) 
   {
      log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
      var response = await httpClient.PostAsync(logicAppUri, new StringContent(myQueueItem, Encoding.UTF8, "application/json")); 
   }
   ```

1. 若要測試此函式，請使用 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer) 等工具來新增佇列訊息。

   在函式收到訊息之後，邏輯應用程式會立即觸發。

## <a name="next-steps"></a>後續步驟

* [使用 HTTP 端點呼叫、觸發或嵌套工作流程](../logic-apps/logic-apps-http-endpoint.md)
