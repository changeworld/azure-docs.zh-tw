---
title: 發佈、訂閱雲端中的事件-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 IoT Edge 上使用 Webhook 搭配事件方格來發佈、訂閱雲端中的事件
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844579"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>教學課程：發佈、訂閱雲端中的事件

本文會逐步解說在 IoT Edge 上使用事件方格發佈和訂閱事件所需的所有步驟。 本教學課程會使用和 Azure Function 作為事件處理常式。 如需其他目的地類型，請參閱[事件處理常式](event-handlers.md)。

請參閱[事件方格概念](concepts.md)，以瞭解事件方格主題和訂用帳戶在繼續之前的功能。

## <a name="prerequisites"></a>必要條件 
為了完成本教學課程，您將需要：

* **Azure 訂**用帳戶-如果您還沒有帳戶，請建立一個[免費帳戶](https://azure.microsoft.com/free)。 
* **Azure IoT 中樞和 IoT Edge 裝置**-遵循[Linux](../../iot-edge/quickstart-linux.md)或[Windows 裝置](../../iot-edge/quickstart.md)的 [快速入門] 中的步驟（如果您還沒有的話）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>在 Azure 入口網站中建立 Azure 函式

請依照[教學](../../azure-functions/functions-create-first-azure-function.md)課程中概述的步驟來建立 Azure 函式。 

將程式碼片段取代為下列程式碼：

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

在您的新函式中，選取右上方的 [取得函式**URL** ]，選取 [預設（**功能鍵**）]，然後選取 [**複製**]。 您稍後會在本教學課程中使用函數 URL 值。

> [!NOTE]
> 如需使用 EventGrid 事件觸發程式回應事件的更多範例和教學課程，請參閱[Azure Functions](../../azure-functions/functions-overview.md)檔。

## <a name="create-a-topic"></a>建立主題

身為事件的發行者，您必須建立事件方格主題。 主題是指發行者可以將事件傳送至其中的端點。

1. 使用下列內容建立 topic2。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 執行下列命令來建立主題。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. 執行下列命令，以確認已成功建立主題。 應傳回200正常的 HTTP 狀態碼。

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

訂閱者可以註冊發佈至主題的事件。 若要接收任何事件，訂閱者必須在感關注的主題上建立 Event grid 訂用帳戶。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用下列內容建立 subscription2。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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
   > **EndpointType**指定訂閱者為 Webhook。  **EndpointUrl**會指定訂閱者接聽事件的 URL。 此 URL 會對應至您稍早設定的 Azure 函式範例。
2. 執行下列命令來建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. 執行下列命令，確認已成功建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

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

1. 使用下列內容建立 event2。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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
1. 執行下列命令以發佈事件

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

您可以在函式的 [**監視**] 選項底下，查看 Azure 入口網站中傳遞的事件。

## <a name="cleanup-resources"></a>清除資源

* 執行下列命令來刪除主題及其所有訂用帳戶

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* 刪除在 Azure 入口網站中建立的 Azure function。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立事件方格主題、訂用帳戶和已發佈的事件。 現在您已瞭解基本步驟，請參閱下列文章：

* 若要針對在 IoT Edge 上使用 Azure 事件方格的問題進行疑難排解，請參閱[疑難排解指南](troubleshoot.md)。
* 使用[篩選器](advanced-filtering.md)建立/更新訂用帳戶。
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上設定事件方格模組的持續性
* 遵循[檔](configure-client-auth.md)以設定用戶端驗證
* 遵循本[教學](forward-events-event-grid-cloud.md)課程，將事件轉寄到雲端中的 Azure 事件方格
* [監視邊緣上的主題和訂用帳戶](monitor-topics-subscriptions.md)
