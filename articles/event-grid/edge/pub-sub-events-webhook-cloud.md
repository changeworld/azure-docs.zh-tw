---
title: 發佈、訂閱雲中的事件 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 使用 Webhook 在 IoT 邊緣使用事件網格發佈、訂閱雲中的事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844579"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>教程：發佈、訂閱雲中的事件

本文介紹使用 IoT Edge 上的事件網格發佈和訂閱事件所需的所有步驟。 本教程使用 Azure 函數作為事件處理常式。 有關其他目標型別，請參閱[事件處理常式](event-handlers.md)。

請參閱[事件網格概念](concepts.md)，以瞭解事件網格主題和訂閱是什麼，然後再繼續。

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂閱**- 如果尚未創建[免費帳戶](https://azure.microsoft.com/free)，則創建免費帳戶。 
* **Azure IoT 中心和 IoT 邊緣設備**- 如果尚未啟動[Linux](../../iot-edge/quickstart-linux.md)或[Windows 設備](../../iot-edge/quickstart.md)，請按照快速入門中的步驟操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>在 Azure 門戶中創建 Azure 函數

按照[本教程](../../azure-functions/functions-create-first-azure-function.md)中概述的步驟創建 Azure 函數。 

將程式碼片段替換為以下代碼：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

在新功能中，選擇右上角**的獲取函數 URL，** 選擇預設（**函數鍵**），然後選擇 **"複製**"。 您將在本教程的後面部分使用函數 URL 值。

> [!NOTE]
> 有關對事件網格事件觸發器的事件做出反應的更多示例和教程，請參閱[Azure 函數](../../azure-functions/functions-overview.md)文檔。

## <a name="create-a-topic"></a>建立主題

作為事件的發行者，您需要創建事件網格主題。 主題是指發行者可以向其發送事件的結束點。

1. 使用以下內容創建主題2.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 運行以下命令以創建主題。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 運行以下命令以驗證主題已成功創建。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   範例輸出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>建立事件訂閱

訂閱者可以註冊發佈到主題的事件。 要接收任何事件，訂閱者將需要創建感興趣的主題的事件網格訂閱。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下內容創建訂閱2.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **終結點類型**指定訂閱者是 Webhook。  **終結點 Url**指定訂閱者偵聽事件的 URL。 此 URL 對應于之前設置的 Azure 函數示例。
2. 運行以下命令以創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 運行以下命令以驗證已成功創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    範例輸出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>發佈事件

1. 使用以下內容創建 event2.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. 運行以下命令以發佈事件

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

您可以在函數的 **"監視器"** 選項下查看在 Azure 門戶中傳遞的事件。

## <a name="cleanup-resources"></a>清除資源

* 運行以下命令以刪除主題及其所有訂閱

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* 刪除在 Azure 門戶中創建的 Azure 函數。

## <a name="next-steps"></a>後續步驟

在本教程中，您創建了事件網格主題、訂閱和已發佈的事件。 現在您已經瞭解了基本步驟，請參閱以下文章：

* 要解決在 IoT 邊緣上使用 Azure 事件網格的問題，請參閱[故障排除指南](troubleshoot.md)。
* 使用篩選器創建/更新[訂閱](advanced-filtering.md)。
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上設置事件網格模組的持久性
* 按照[文檔](configure-client-auth.md)配置用戶端身份驗證
* 按照[本教程](forward-events-event-grid-cloud.md)將事件轉發到雲中的 Azure 事件網格
* [監視邊緣的主題和訂閱](monitor-topics-subscriptions.md)
