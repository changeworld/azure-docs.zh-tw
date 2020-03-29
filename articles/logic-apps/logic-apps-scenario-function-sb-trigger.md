---
title: 使用 Azure Functions 呼叫邏輯應用程式
description: 通過偵聽 Azure 服務匯流排創建調用或觸發邏輯應用的 Azure 函數
services: logic-apps
ms.suite: integration
ms.reviewer: jehollan, klam, logicappspm
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: afd2735bae2a79ad942c347219019ef200b61070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428712"
---
# <a name="call-or-trigger-logic-apps-by-using-azure-functions-and-azure-service-bus"></a>使用 Azure 函數和 Azure 服務匯流排調用或觸發邏輯應用

當您需要部署長時間運行的攔截器或任務時，可以使用[Azure 函數](../azure-functions/functions-overview.md)觸發邏輯應用。 例如，您可以創建 Azure 函數，該函數偵聽 Azure[服務匯流排](../service-bus-messaging/service-bus-messaging-overview.md)佇列，並立即觸發邏輯應用作為推送觸發器。

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請先[註冊免費的 Azure 帳戶](https://azure.microsoft.com/free/)。

* Azure 服務匯流排命名空間。 如果沒有命名空間，[請先創建命名空間](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* Azure 函數應用，它是 Azure 函數的容器。 如果沒有函數應用，請[先創建函數應用](../azure-functions/functions-create-first-azure-function.md)，並確保選擇 .NET 作為運行時堆疊。

* [有關如何創建邏輯應用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知識

## <a name="create-logic-app"></a>建立邏輯應用程式

對於此方案，您有一個函數運行要觸發的每個邏輯應用。 首先，創建一個以 HTTP 要求觸發器開頭的邏輯應用。 每當收到佇列訊息時，函數即會呼叫該端點。

1. 登入 [Azure 入口網站](https://portal.azure.com)，然後建立空白的邏輯應用程式。

   如果您還不熟悉邏輯應用程式，請檢閱[快速入門：如何建立第一個邏輯應用程式](../logic-apps/quickstart-create-first-logic-app-workflow.md)。

1. 在搜尋方塊中，輸入 `http request`。 從觸發器清單中選擇 **"何時收到 HTTP 要求**"觸發器。

   ![選取觸發程序](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger.png)

   使用"請求"觸發器，您可以選擇輸入 JSON 架構以與佇列消息一起使用。 JSON 架構可説明邏輯應用設計器瞭解輸入資料的結構，並使輸出更易於在工作流中使用。

1. 若要指定結構描述，請在 [要求本文 JSON 結構描述]**** 方塊中輸入結構描述，例如：

   ![指定 JSON 結構描述](./media/logic-apps-scenario-function-sb-trigger/when-http-request-received-trigger-schema.png)

   如果您沒有結構描述，但是有 JSON 格式的範例承載，即可從該承載產生 JSON 結構描述。

   1. 在要求觸發程序中，選取 [使用範例承載來產生結構描述]****。

   1. **在"輸入或粘貼 JSON 有效負載示例**"下，輸入示例有效負載，然後選擇 **"完成**"。

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

1. 添加在接收佇列消息後要運行的任何其他操作。

   例如，您可以使用 Office 365 Outlook 連接器傳送電子郵件。

1. 儲存邏輯應用程式，進而為此邏輯應用程式中的觸發程序產生回呼 URL。 稍後，在 Azure 服務匯流排佇列觸發器的代碼中使用此回檔 URL。

   回檔 URL 將顯示在**HTTP POST URL 屬性**中。

   ![為觸發程序產生的回呼 URL](./media/logic-apps-scenario-function-sb-trigger/callback-URL-for-trigger.png)

## <a name="create-azure-function"></a>建立 Azure 函式

接著，建立一個函式，作為觸發程序並接聽佇列。

1. 在 Azure 入口網站中，開啟並展開函式應用程式 (如果尚未開啟)。 

1. 在函式應用程式名稱之下，展開 [Functions]****。 在 **"功能"** 窗格中，選擇 **"新建"功能**。

   ![展開"功能"並選擇"新功能"](./media/logic-apps-scenario-function-sb-trigger/add-new-function-to-function-app.png)

1. 根據是否創建了一個新函數應用，選擇 .NET 作為運行時堆疊，還是正在使用現有函數應用，選擇此範本。

   * 對於新功能應用，選擇此範本：**服務匯流排佇列觸發器**

     ![為新功能應用選擇範本](./media/logic-apps-scenario-function-sb-trigger/current-add-queue-trigger-template.png)

   * 對於現有函數應用，選擇此範本：**服務匯流排佇列觸發器 - C#**

     ![為現有功能應用選擇範本](./media/logic-apps-scenario-function-sb-trigger/legacy-add-queue-trigger-template.png)

1. 在**Azure 服務匯流排佇列觸發器**窗格中，為觸發器提供名稱，並為使用 Azure 服務匯流排`OnMessageReceive()`SDK 攔截器的佇列設置**服務匯流排連接**，然後選擇"**創建**"。

1. 編寫基本函數，使用佇列消息作為觸發器調用以前創建的邏輯應用終結點。 在編寫函數之前，請查看以下注意事項：

   * 此範例會使用 `application/json` 訊息內容類型，但是您可以視需要變更此類型。
   
   * 由於可能同時運行功能、高容量或負載大，因此避免使用`using`語句具現化[HTTPClient 類](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient)，並直接創建每個請求的 HTTPClient 實例。 有關詳細資訊，請參閱使用[HttpClientFactory 實現彈性 HTTP 要求](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests#issues-with-the-original-httpclient-class-available-in-net-core)。
   
   * 如果可能，請重用 HTTP 用戶端的實例。 有關詳細資訊，請參閱在[Azure 函數中管理連接](../azure-functions/manage-connections.md)。

   本示例在[`Task.Run`](https://docs.microsoft.com/dotnet/api/system.threading.tasks.task.run)[非同步](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/async)模式下使用 該方法。 有關詳細資訊，請參閱[具有非同步和等待的非同步程式設計](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)。

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

* [使用 HTTP 終結點調用、觸發或嵌套工作流](../logic-apps/logic-apps-http-endpoint.md)
