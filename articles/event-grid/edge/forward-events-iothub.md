---
title: 將事件方格事件轉寄至 IoTHub-Azure Event Grid IoT Edge |Microsoft Docs
description: 將事件方格事件轉送至 IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844695"
---
# <a name="tutorial-forward-events-to-iothub"></a>教學課程：將事件轉寄至 IoTHub

本文會逐步解說將事件方格事件轉送至其他 IoT Edge 模組所需的所有步驟，並使用路由 IoTHub。 基於下列原因，您可能會想要這麼做：

* 繼續使用已有的任何現有投資搭配 edgeHub 的路由
* 偏好僅透過 IoT 中樞路由傳送裝置上的所有事件

若要完成本教學課程，您必須瞭解下列概念：

- [Event Grid 概念](concepts.md)
- [IoT Edge 中樞](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>必要條件 
為了完成本教學課程，您將需要：

* **Azure 訂**用帳戶-如果您還沒有帳戶，請建立一個[免費帳戶](https://azure.microsoft.com/free)。 
* **Azure IoT 中樞和 IoT Edge 裝置**-遵循[Linux](../../iot-edge/quickstart-linux.md)或[Windows 裝置](../../iot-edge/quickstart.md)的 [快速入門] 中的步驟（如果您還沒有的話）。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>建立主題

身為事件的發行者，您必須建立事件方格主題。 本主題會參考一個結束點，讓發行者可以將事件傳送至該端點。

1. 使用下列內容建立 topic4。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. 執行下列命令來建立主題。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. 執行下列命令，以確認已成功建立主題。 應傳回200正常的 HTTP 狀態碼。

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

訂閱者可以註冊發佈至主題的事件。 若要接收任何事件，他們必須在感關注的主題上建立 Event grid 訂用帳戶。

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. 使用下列內容建立 subscription4。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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
   > `endpointType` 指定訂閱者 `edgeHub`。 `outputName` 指定事件方格模組將符合此訂用帳戶的事件路由至 edgeHub 的輸出。 例如，符合上述訂閱的事件將會寫入 `/messages/modules/eventgridmodule/outputs/sampleSub4`。
2. 執行下列命令來建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. 執行下列命令，確認已成功建立訂用帳戶。 應傳回200正常的 HTTP 狀態碼。

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

## <a name="set-up-an-edge-hub-route"></a>設定 edge 中樞路由

將 edge 中樞的路由更新為轉送事件訂用帳戶的事件，以轉送至 IoTHub，如下所示：

1. 登入 [Azure 入口網站](https://ms.portal.azure.com)
1. 流覽至 [ **IoT 中樞**]。
1. 從功能表中選取 [ **IoT Edge** ]
1. 從裝置清單中選取目標裝置的識別碼。
1. 選取 [設定模組]。
1. 選取 **[下一步]** 和 [路由] 區段。
1. 在路由中，新增路由

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
   > 上述路由會轉送符合此訂用帳戶的任何事件，以轉送到 IoT 中樞。 您可以使用[Edge 中樞路由](../../iot-edge/module-composition.md)功能進一步篩選，並將事件方格事件路由至其他 IoT Edge 模組。

## <a name="setup-iot-hub-route"></a>設定 IoT 中樞路由

請參閱[IoT 中樞路由教學](../../iot-hub/tutorial-routing.md)課程來設定 IoT 中樞的路由，讓您可以查看從事件方格模組轉送的事件。 使用查詢的 `true`，讓教學課程保持簡單。  



## <a name="publish-an-event"></a>發佈事件

1. 使用下列內容建立 event4。 如需裝載的詳細資訊，請參閱我們的[API 檔](api.md)。

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

1. 執行下列命令以發佈事件：

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>驗證事件傳遞

如需查看事件的步驟，請參閱 IoT 中樞[路由教學](../../iot-hub/tutorial-routing.md)課程。

## <a name="cleanup-resources"></a>清除資源

* 執行下列命令，以刪除主題及其在邊緣的所有訂用帳戶：

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* 刪除在雲端中設定 IoTHub 路由時所建立的任何資源。

## <a name="next-steps"></a>後續步驟

在本教學課程中，您已建立事件方格主題、edge 中樞訂用帳戶和已發佈的事件。 既然您已經知道要轉送至 edge 中樞的基本步驟，請參閱下列文章：

* 若要針對在 IoT Edge 上使用 Azure 事件方格的問題進行疑難排解，請參閱[疑難排解指南](troubleshoot.md)。
* 使用[edge 中樞](../../iot-edge/module-composition.md)路由篩選來分割事件
* 在[linux](persist-state-linux.md)或[Windows](persist-state-windows.md)上設定事件方格模組的持續性
* 遵循[檔](configure-client-auth.md)以設定用戶端驗證
* 遵循本[教學](forward-events-event-grid-cloud.md)課程，將事件轉寄到雲端中的 Azure 事件方格
* [監視邊緣上的主題和訂用帳戶](monitor-topics-subscriptions.md)