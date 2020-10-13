---
title: 將邊緣事件轉送至事件方格雲端-Azure 事件方格 IoT Edge |Microsoft Docs
description: 將邊緣事件轉送至事件方格雲端
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171596"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>教學課程：將事件轉送至事件方格雲端

本文將逐步解說將邊緣事件轉寄至 Azure 雲端中事件方格所需的所有步驟。 基於下列原因，您可能會想要這樣做：

* 回應雲端中的邊緣事件。
* 將事件轉送到雲端中的事件方格，並使用 Azure 事件中樞或 Azure 儲存體佇列來緩衝處理事件，然後在雲端中處理它們。

 若要完成本教學課程，您需要瞭解 [edge](concepts.md) 和 [Azure](../concepts.md)上的事件方格概念。 如需其他目的地類型，請參閱 [事件處理常式](event-handlers.md)。 

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂** 用帳戶-如果您還沒有帳戶，請建立一個 [免費帳戶](https://azure.microsoft.com/free) 。 
* **Azure IoT 中樞和 IoT Edge 裝置** -如果您還沒有 [Linux](../../iot-edge/quickstart-linux.md) 或 [Windows 裝置](../../iot-edge/quickstart.md) 的快速入門中的步驟，請遵循這些步驟。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>在雲端中建立事件方格主題和訂用帳戶

遵循 [本教學](../custom-event-quickstart-portal.md)課程，在雲端中建立事件方格主題和訂用帳戶。 記下 `topicURL` 、 `sasKey` 和 `topicName` 您稍後將在本教學課程中使用的新建立主題。

例如，如果您建立了一個名為 `testegcloudtopic` 美國西部的主題，這些值看起來會像這樣：

* **TopicUrl**： `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**： `testegcloudtopic`
* **SasKey**：適用于您主題的 **AccessKey** 。 使用 **key1**。

## <a name="create-event-grid-topic-at-the-edge"></a>在邊緣建立事件方格主題

1. 使用下列內容建立 topic3.js。 如需承載的詳細資料，請參閱我們的 [API 檔](api.md) 。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 執行下列命令來建立主題。 應傳回 HTTP 狀態碼 200 OK。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 執行下列命令以確認已成功建立主題。 應傳回 HTTP 狀態碼 200 OK。

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>在邊緣建立事件方格訂用帳戶

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用下列內容建立 subscription3.js。 如需承載的詳細資料，請參閱我們的 [API 檔](api.md) 。

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
   > **EndpointUrl**會指定雲端中的事件方格主題 URL。 **SasKey**是指事件方格雲端主題的索引鍵。 **TopicName**中的值將用來將所有傳出事件戳記至事件方格。 當張貼至事件方格網域主題時，這會很有用。 如需事件方格網域主題的詳細資訊，請參閱 [事件網域](../event-domains.md)

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

2. 執行下列命令以建立訂用帳戶。 應傳回 HTTP 狀態碼 200 OK。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 執行下列命令以確認已成功建立訂用帳戶。 應傳回 HTTP 狀態碼 200 OK。

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

1. 使用下列內容建立 event3.js。 如需承載的詳細資訊，請參閱 [API 檔](api.md) 。

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

1. 執行下列命令：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>確認雲端中的邊緣事件

如需有關如何查看雲端主題所提供事件的詳細資訊，請參閱 [教學](../custom-event-quickstart-portal.md)課程。

## <a name="cleanup-resources"></a>清除資源

* 執行下列命令來刪除主題及其所有訂用帳戶

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 刪除在雲端中建立的主題和訂用帳戶 (Azure 事件方格) 。

## <a name="next-steps"></a>接下來的步驟

在本教學課程中，您已在 edge 上發佈事件，並將事件轉送至 Azure 雲端中的事件方格。 現在您已瞭解轉送至雲端中事件方格的基本步驟：

* 若要針對在 IoT Edge 上使用 Azure 事件方格的問題進行疑難排解，請參閱 [疑難排解指南](troubleshoot.md)。
* 遵循本[教學](forward-events-iothub.md)課程來將事件轉送至 IoTHub
* 遵循本[教學](pub-sub-events-webhook-cloud.md)課程，將事件轉寄至雲端中的 Webhook
* [監視邊緣上的主題和訂用帳戶](monitor-topics-subscriptions.md)
