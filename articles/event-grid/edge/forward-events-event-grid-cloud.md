---
title: 將邊緣事件轉發到事件網格雲 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 將邊緣事件轉發到事件網格雲
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844712"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>教程：將事件轉發到事件網格雲

本文介紹將邊緣事件轉發到 Azure 雲中的事件網格所需的所有步驟。 您可能需要這樣做的原因如下：

* 回應雲中的邊緣事件。
* 將事件轉發到雲中的事件網格，並使用 Azure 事件中心或 Azure 存儲佇列在雲中處理事件之前緩衝事件。

 要完成本教程，您需要瞭解[邊緣](concepts.md)和[Azure](../concepts.md)上的事件網格概念。 有關其他目標型別，請參閱[事件處理常式](event-handlers.md)。 

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂閱**- 如果尚未創建[免費帳戶](https://azure.microsoft.com/free)，則創建免費帳戶。 
* **Azure IoT 中心和 IoT 邊緣設備**- 如果尚未啟動[Linux](../../iot-edge/quickstart-linux.md)或[Windows 設備](../../iot-edge/quickstart.md)，請按照快速入門中的步驟操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>在雲中創建事件網格主題和訂閱

通過按照[本教程](../custom-event-quickstart-portal.md)創建事件網格主題和在雲中的訂閱。 記下`topicURL`，`sasKey`以及`topicName`本教程稍後將使用的新創建主題。

例如，如果您創建了在美國西部命名的`testegcloudtopic`主題，則這些值將如下所示：

* **主題Url**：`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **主題名稱**：`testegcloudtopic`
* **SasKey**： 可在主題**的便捷鍵**下提供。 使用**鍵1**。

## <a name="create-event-grid-topic-at-the-edge"></a>在邊緣創建事件網格主題

1. 使用以下內容創建主題3.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 運行以下命令以創建主題。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 運行以下命令以驗證主題已成功創建。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   範例輸出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>在邊緣創建事件網格訂閱

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下內容創建訂閱3.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **終結點 Url**指定雲中的事件網格主題 URL。 **sasKey**是指事件網格雲主題的鍵。 **主題名稱**中的值將用於將所有傳出事件標記到事件網格。 這在發佈到事件網格域主題時非常有用。 有關事件網格域主題的詳細資訊，請參閱[事件域](../event-domains.md)

    例如，
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. 運行以下命令以創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 運行以下命令以驗證已成功創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    範例輸出：

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>在邊緣發佈事件

1. 使用以下內容創建 event3.json。 有關有效負載的詳細資訊，請參閱[API 文檔](api.md)。

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. 執行以下命令：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>在雲中驗證邊緣事件

有關查看雲主題傳遞的事件的資訊，請參閱[教程](../custom-event-quickstart-portal.md)。

## <a name="cleanup-resources"></a>清除資源

* 運行以下命令以刪除主題及其所有訂閱

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 刪除在雲（Azure 事件網格）中創建的主題和訂閱。

## <a name="next-steps"></a>後續步驟

在本教程中，您將在邊緣上發佈一個事件，並轉發到 Azure 雲中的事件網格。 現在您已經瞭解了在雲中轉發到事件網格的基本步驟：

* 要解決在 IoT 邊緣上使用 Azure 事件網格的問題，請參閱[故障排除指南](troubleshoot.md)。
* 按照[本教程](forward-events-iothub.md)將事件轉發到 IoTHub
* 按照[本教程](pub-sub-events-webhook-cloud.md)將事件轉發到雲中的 Webhook
* [監視邊緣的主題和訂閱](monitor-topics-subscriptions.md)
