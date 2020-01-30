---
title: 將邊緣事件轉寄至事件方格雲端-Azure Event Grid IoT Edge |Microsoft Docs
description: 將邊緣事件轉寄至事件方格雲端
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844712"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>教學課程：將事件轉寄至事件方格雲端

本文會逐步解說將邊緣事件轉送至 Azure 雲端中事件方格所需的所有步驟。 基於下列原因，您可能會想要這麼做：

* 對雲端中的邊緣事件做出回應。
* 將事件轉送到雲端中的事件方格，並使用 Azure 事件中樞或 Azure 儲存體佇列來緩衝事件，然後再于雲端中處理它們。

 若要完成本教學課程，您需要瞭解[edge](concepts.md)和[Azure](../concepts.md)上的事件方格概念。 如需其他目的地類型，請參閱[事件處理常式](event-handlers.md)。 

## <a name="prerequisites"></a>必要條件 
為了完成本教學課程，您將需要：

* **Azure 訂**用帳戶-如果您還沒有帳戶，請建立一個[免費帳戶](https://azure.microsoft.com/free)。 
* **Azure IoT 中樞和 IoT Edge 裝置**-遵循[Linux](../../iot-edge/quickstart-linux.md)或[Windows 裝置](../../iot-edge/quickstart.md)的 [快速入門] 中的步驟（如果您還沒有的話）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>在雲端中建立事件方格主題和訂用帳戶

遵循[本教學](../custom-event-quickstart-portal.md)課程，在雲端中建立事件方格主題和訂用帳戶。 請記下您稍後將在本教學課程中使用之新建立主題的 `topicURL`、`sasKey`和 `topicName`。

例如，如果您在美國西部建立名為 `testegcloudtopic` 的主題，這些值看起來會像這樣：

* **TopicUrl**： `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **TopicName**： `testegcloudtopic`
* **SasKey**：適用于您的主題**AccessKey** 。 使用**key1**。

## <a name="create-event-grid-topic-at-the-edge"></a>在邊緣建立事件方格主題

1. 使用下列內容建立 topic3。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. 執行下列命令來建立主題。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. 執行下列命令，以確認已成功建立主題。 應傳回200正常的 HTTP 狀態碼。

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

1. 使用下列內容建立 subscription3。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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
   > **EndpointUrl**會指定雲端中的事件方格主題 URL。 **SasKey**是指事件方格雲端主題的金鑰。 **TopicName**中的值將用來將所有傳出事件標記為事件方格。 張貼到 Event Grid 網域主題時，這會很有用。 如需事件方格網域主題的詳細資訊，請參閱[事件網域](../event-domains.md)

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

2. 執行下列命令來建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. 執行下列命令，確認已成功建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

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

1. 使用下列內容建立 event3。 如需裝載的詳細資訊，請參閱[API 檔](api.md)。

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

## <a name="verify-edge-event-in-cloud"></a>確認雲端中的邊緣事件

如需有關如何查看雲端所傳遞之事件的詳細資訊，請參閱[教學](../custom-event-quickstart-portal.md)課程。

## <a name="cleanup-resources"></a>清除資源

* 執行下列命令來刪除主題及其所有訂用帳戶

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* 同時刪除在雲端中建立的主題和訂用帳戶（Azure 事件方格）。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已在 edge 上發佈事件並轉送至 Azure 雲端中的事件方格。 現在您已經知道要轉送到雲端中事件方格的基本步驟：

* 若要針對在 IoT Edge 上使用 Azure 事件方格的問題進行疑難排解，請參閱[疑難排解指南](troubleshoot.md)。
* 遵循本[教學](forward-events-iothub.md)課程將事件轉寄至 IoTHub
* 遵循本[教學](pub-sub-events-webhook-cloud.md)課程，將事件轉寄到雲端中的 Webhook
* [監視邊緣上的主題和訂用帳戶](monitor-topics-subscriptions.md)
