---
title: 事件處理常式和目的地-Azure 事件方格 IoT Edge |Microsoft Docs
description: 邊緣上事件方格中的事件處理常式和目的地
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 18e2b944f60ebdf8a1b0c60ba446a13df13134c5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171579"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>邊緣上事件方格中的事件處理常式和目的地

事件處理常式是事件進行進一步動作或處理事件的位置。 使用 Edge 模組上的事件方格時，事件處理常式可以位於相同的邊緣裝置、另一個裝置或雲端中。 您可以使用任何 WebHook 來處理事件，或將事件傳送至其中一個原生處理常式，例如 Azure 事件方格。

本文提供如何設定每個專案的相關資訊。

## <a name="webhook"></a>WebHook

若要發佈至 WebHook 端點，請將設定 `endpointType` 為， `WebHook` 並提供：

* endpointUrl： WebHook 端點 URL

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

若要發佈至 Azure 事件方格雲端端點，請將設定 `endpointType` 為， `eventGrid` 並提供：

* endpointUrl：雲端中的事件方格主題 URL
* sasKey：事件方格主題的 SAS 金鑰
* topicName：用來將所有傳出事件戳記至事件方格的名稱。 張貼至事件方格網域主題時，主題名稱很有用。

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

## <a name="iot-edge-hub"></a>IoT Edge 中樞

若要發佈至 Edge 中樞模組，請將設定 `endpointType` 為， `edgeHub` 並提供：

* outputName：事件方格模組會將符合此訂用帳戶的事件路由至 edgeHub 的輸出。 例如，符合下列訂用帳戶的事件將會寫入至/messages/modules/eventgridmodule/outputs/sampleSub4。

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

若要發佈至事件中樞，請將設定 `endpointType` 為， `eventHub` 並提供：

* connectionString：透過共用存取原則所產生之特定事件中樞的連接字串。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 您可以藉由流覽至您想要在 Azure 入口網站中發佈的特定事件中樞，然後按一下 [ **共用存取原則** ] 來產生新的實體特定 connecection 字串，藉以產生實體特定的連接字串。

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

若要發行至服務匯流排佇列，請將設定 `endpointType` 為， `serviceBusQueue` 並提供：

* connectionString：您的目標特定服務匯流排佇列的連接字串，是透過共用存取原則所產生。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排佇列，然後按一下 [ **共用存取原則** ] 來產生新的實體特定 connecection 字串，以產生實體特定的連接字串。

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

若要發佈至服務匯流排主題，請將設定 `endpointType` 為， `serviceBusTopic` 並提供：

* connectionString：您的目標特定服務匯流排主題的連接字串，是透過共用存取原則所產生。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法運作。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排主題，然後按一下 [ **共用存取原則** ] 以產生新的實體特定 connecection 字串，來產生實體特定的連接字串。

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

若要發行至儲存體佇列，請將設定  `endpointType` 為， `storageQueue` 並提供：

* queueName：您要發佈的儲存體佇列的名稱。
* connectionString：儲存體佇列所在之儲存體帳戶的連接字串。

    >[!NOTE]
    > Unline 事件中樞、服務匯流排佇列和服務匯流排主題，用於儲存體佇列的連接字串不是實體特有的。 相反地，它必須是儲存體帳戶的連接字串。

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