---
title: 事件處理常式和目的地-Azure Event Grid IoT Edge |Microsoft Docs
description: 邊緣上事件方格中的事件處理常式和目的地
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 35bf5af90aa5f0456aa8d68f0e4e8aaacc6cf84f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "76849743"
---
# <a name="event-handlers-and-destinations-in-event-grid-on-edge"></a>邊緣上事件方格中的事件處理常式和目的地

事件處理常式是事件進行進一步動作或處理事件的位置。 透過邊緣模組上的事件方格，事件處理常式可以位於相同的邊緣裝置、另一個裝置或雲端。 您可以使用任何 WebHook 來處理事件，或將事件傳送至 Azure 事件方格之類的其中一個原生處理常式。

本文提供如何設定每個專案的相關資訊。

## <a name="webhook"></a>WebHook

若要發行至 WebHook 端點，請將`endpointType`設定`WebHook`為，並提供：

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

若要發佈至 Azure 事件方格雲端端點，請將`endpointType`設定`eventGrid`為，並提供：

* endpointUrl：雲端中的事件方格主題 URL
* sasKey：事件方格主題的 SAS 金鑰
* topicName：用來將所有傳出事件標記為事件方格的名稱。 當您張貼到事件方格網域主題時，主題名稱會很有用。

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

若要發佈至 Edge 中樞模組，請將`endpointType`設定`edgeHub`為，並提供：

* outputName：事件方格模組會將符合此訂用帳戶的事件路由至 edgeHub 的輸出。 例如，符合下列訂用帳戶的事件會寫入至/messages/modules/eventgridmodule/outputs/sampleSub4。

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

若要發佈至事件中樞，請將`endpointType`設定`eventHub`為，並提供：

* connectionString：透過共用存取原則產生的特定事件中樞的連接字串。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法正常執行。 您可以在 Azure 入口網站中流覽至您想要發佈至的特定事件中樞，然後按一下 [**共用存取原則**] 來產生新實體的特定 connecection 字串，以產生實體特定的連接字串。

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

若要發行至服務匯流排的佇列，請`endpointType`將`serviceBusQueue`設定為，並提供：

* connectionString：透過共用存取原則所設定的目標特定服務匯流排佇列的連接字串。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法正常執行。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排佇列，然後按一下 [**共用存取原則**] 來產生新實體的特定 connecection 字串，以產生實體特定的連接字串。

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

若要發行至服務匯流排主題，請將`endpointType`設定`serviceBusTopic`為，並提供：

* connectionString：您透過共用存取原則所設定的目標特定服務匯流排主題的連接字串。

    >[!NOTE]
    > 連接字串必須是實體特定的。 使用命名空間連接字串將無法正常執行。 藉由流覽至您想要在 Azure 入口網站中發佈的特定服務匯流排主題，然後按一下 [**共用存取原則**] 來產生新實體的特定 connecection 字串，以產生實體特定的連接字串。

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

若要發行至儲存體佇列，請將`endpointType`設定`storageQueue`為，並提供：

* queueName：您要發行的儲存體佇列的名稱。
* connectionString：儲存體佇列所在儲存體帳戶的連接字串。

    >[!NOTE]
    > Unline 事件中樞、服務匯流排佇列和服務匯流排主題，用於儲存體佇列的連接字串不是實體特定的。 相反地，它必須是儲存體帳戶的連接字串。

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