---
title: 將事件網格事件轉發到 IoTHub - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 將事件網格事件轉發到 IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844695"
---
# <a name="tutorial-forward-events-to-iothub"></a>教程：將事件轉發到 IoTHub

本文介紹將事件網格事件轉發到其他 IoT Edge 模組（使用路由的 IoTHub）所需的所有步驟。 您可能需要這樣做的原因如下：

* 繼續使用任何現有投資，並採用 EdgeHub 的路由
* 更喜歡僅通過 IoT 中心從設備路由所有事件

要完成本教程，您需要瞭解以下概念：

- [事件網格概念](concepts.md)
- [IoT Edge 中樞](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Prerequisites 
若要完成這個教學課程，您將需要：

* **Azure 訂閱**- 如果尚未創建[免費帳戶](https://azure.microsoft.com/free)，則創建免費帳戶。 
* **Azure IoT 中心和 IoT 邊緣設備**- 如果尚未啟動[Linux](../../iot-edge/quickstart-linux.md)或[Windows 設備](../../iot-edge/quickstart.md)，請按照快速入門中的步驟操作。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>建立主題

作為事件的發行者，您需要創建事件網格主題。 該主題是指發行者隨後可以將事件發送到的終點。

1. 使用以下內容創建主題4.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 運行以下命令以創建主題。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 運行以下命令以驗證主題已成功創建。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   範例輸出：

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>建立事件訂閱

訂閱者可以註冊發佈到主題的事件。 要接收任何事件，他們需要根據感興趣的主題創建事件網格訂閱。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用以下內容創建訂閱4.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > 指定`endpointType`訂閱者為`edgeHub`。 指定`outputName`事件網格模組將匹配此訂閱的事件路由到 edgeHub 的輸出。 例如，與上述訂閱匹配的事件將寫入`/messages/modules/eventgridmodule/outputs/sampleSub4`。
2. 運行以下命令以創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 運行以下命令以驗證已成功創建訂閱。 應返回 200 OK 的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    範例輸出：

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>設置邊緣中心路由

更新邊緣中心的路線，將事件訂閱的事件轉發到 IoTHub，如下所示：

1. 登錄到 Azure[門戶](https://ms.portal.azure.com)
1. 導航到**IoT 中心**。
1. 從功能表中選擇**IoT 邊緣**
1. 從設備清單中選擇目標設備的 ID。
1. 選擇**設置模組**。
1. 選擇 **"下一步**"和"路徑"部分。
1. 在路由中，添加新路由

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  例如，

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > 上述路由將轉發為此訂閱匹配的任何事件，以轉發到 IoT 中心。 您可以使用[邊緣中心路由](../../iot-edge/module-composition.md)功能進一步篩選，並將事件網格事件路由到其他 IoT Edge 模組。

## <a name="setup-iot-hub-route"></a>設置 IoT 中心路由

請參閱[IoT 中心路由教程](../../iot-hub/tutorial-routing.md)，以設置來自 IoT 中心的路線，以便您可以查看從事件網格模組轉發的事件。 用於`true`查詢以保持教程簡單。  



## <a name="publish-an-event"></a>發佈事件

1. 使用以下內容創建事件4.json。 有關有效負載的詳細資訊，請參閱我們的[API 文檔](api.md)。

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. 運行以下命令以發佈事件：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

有關查看事件的步驟，請參閱 IoT 中心[路由教程](../../iot-hub/tutorial-routing.md)。

## <a name="cleanup-resources"></a>清除資源

* 運行以下命令以刪除主題及其邊緣的所有訂閱：

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 刪除在雲中設置 IoTHub 路由時創建的任何資源。

## <a name="next-steps"></a>後續步驟

在本教程中，您創建了事件網格主題、邊緣中心訂閱和已發佈的事件。 現在，您已經瞭解了轉發到邊緣中心的基本步驟，請參閱以下文章：

* 要解決在 IoT 邊緣上使用 Azure 事件網格的問題，請參閱[故障排除指南](troubleshoot.md)。
* 使用[邊緣中心](../../iot-edge/module-composition.md)路由篩選器對事件進行分區
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上設置事件網格模組的持久性
* 按照[文檔](configure-client-auth.md)配置用戶端身份驗證
* 按照[本教程](forward-events-event-grid-cloud.md)將事件轉發到雲中的 Azure 事件網格
* [監視邊緣的主題和訂閱](monitor-topics-subscriptions.md)