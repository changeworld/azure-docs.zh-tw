---
title: 事件處理常式和目標 - Azure 事件網格 IoT 邊緣 |微軟文檔
description: 邊緣事件網格中的事件處理常式和目標
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76849743"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>邊緣事件網格中的事件處理常式和目標

事件處理常式是事件進行進一步操作或處理事件的位置。 使用"邊緣事件網格"模組時，事件處理常式可以位於同一邊緣設備、另一個設備或雲中。 可以使用任何 WebHook 來處理事件，也可以將事件發送到 Azure 事件網格等本機處理常式之一。

本文提供有關如何配置每個產品的資訊。

## <a name="webhook"></a>網路胡克

要發佈到 WebHook 終結點，請設置為`endpointType``WebHook`和 提供：

* 終結點 URL：WebHook 終結點 URL

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-endpoint>"
              }
            }
          }
        }
    ```

## <a name="azure-event-grid"></a>Azure Event Grid

要發佈到 Azure 事件網格雲終結點，請設置為`endpointType``eventGrid`並提供：

* 終結點Url：雲中的事件網格主題 URL
* sasKey：事件網格主題的 SAS 鍵
* 主題名稱：名稱以將所有傳出事件標記到事件網格。 主題名稱在發佈到事件網格域主題時很有用。

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

## <a name="iot-edge-hub"></a>IoT 邊緣中心

要發佈到邊緣中心模組，請設置為`endpointType`和`edgeHub`提供：

* 輸出名稱：事件網格模組將匹配此訂閱的事件路由到 edgeHub 的輸出。 例如，與以下訂閱匹配的事件將寫入 /消息/模組/事件網格模組/輸出/示例Sub4。

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

## <a name="event-hubs"></a>事件中樞

要發佈到事件中心，請設置為`endpointType`並提供： `eventHub`

* 連接字串：通過共用訪問策略生成的特定事件中心的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 可以通過導航到要在 Azure 門戶中發佈到的特定事件中心，然後按一下**共用訪問策略**來生成新的實體特定連接字串，從而生成實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventHub",
              "properties": {
                "connectionString": "<your-event-hub-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-queues"></a>服務匯流排佇列

要發佈到服務匯流排佇列，請設置為`endpointType`和`serviceBusQueue`提供：

* 連接字串：通過共用訪問策略生成的特定服務匯流排佇列的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 通過導航到要在 Azure 門戶中發佈到的特定服務匯流排佇列，然後按一下**共用訪問策略**以生成新的實體特定連接字串，生成實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusQueue",
              "properties": {
                "connectionString": "<your-service-bus-queue-connection-string>"
              }
            }
          }
        }
    ```

## <a name="service-bus-topics"></a>服務匯流排主題

要發佈到服務匯流排主題，請設置為`endpointType`和`serviceBusTopic`提供：

* 連接字串：通過共用訪問策略生成的特定服務匯流排主題的連接字串。

    >[!NOTE]
    > 連接字串必須是特定于實體的。 使用命名空間連接字串將不起作用。 通過導航到要在 Azure 門戶中發佈到的特定服務匯流排主題，然後按一下**共用訪問策略**以生成新的實體特定連接字串，生成實體特定的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "serviceBusTopic",
              "properties": {
                "connectionString": "<your-service-bus-topic-connection-string>"
              }
            }
          }
        }
    ```

## <a name="storage-queues"></a>儲存體佇列

要發佈到存儲佇列，請設置為`endpointType`和`storageQueue`提供：

* 佇列名稱：要發佈到的存儲佇列的名稱。
* 連接字串：存儲佇列中的存儲帳戶的連接字串。

    >[!NOTE]
    > 取消行線事件中心、服務匯流排佇列和服務匯流排主題，用於存儲佇列的連接字串不是特定于實體的。 相反，它必須，但存儲帳戶的連接字串。

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "storageQueue",
              "properties": {
                "queueName": "<your-storage-queue-name>",
                "connectionString": "<your-storage-account-connection-string>"
              }
            }
          }
        }
    ```