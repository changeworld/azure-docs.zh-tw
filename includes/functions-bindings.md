---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.custom: fasttrack-edit
ms.openlocfilehash: 574544df1ad89b277f2e07103b02b9b591fb3924
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002374"
---
此表顯示主要版本的 Azure Functions 執行階段中所支援的繫結：


| 類型 | 1.x | 2.x 和更新版本<sup>1</sup> | 觸發程序 | 輸入 | 輸出 |
| ---- | :-: | :-: | :------: | :---: | :----: |
| [Blob 儲存體](../articles/azure-functions/functions-bindings-storage-blob.md)          |✔|✔|✔|✔|✔|
| [Azure Cosmos DB](../articles/azure-functions/functions-bindings-cosmosdb-v2.md)               |✔|✔|✔|✔|✔|
| [Dapr](https://github.com/dapr/azure-functions-extension)<sup>3</sup>             | |✔|✔|✔|✔|
| [Event Grid](../articles/azure-functions/functions-bindings-event-grid.md)              |✔|✔|✔| |✔|
| [事件中樞](../articles/azure-functions/functions-bindings-event-hubs.md)              |✔|✔|✔| |✔|
| [HTTP 和 Webhook](../articles/azure-functions/functions-bindings-http-webhook.md)             |✔|✔|✔| |✔|
| [IoT 中心](../articles/azure-functions/functions-bindings-event-iot.md)             |✔|✔|✔| |✔|
| [Kafka](https://github.com/azure/azure-functions-kafka-extension)<sup>2</sup>             | |✔|✔| |✔|
| [Mobile Apps](../articles/azure-functions/functions-bindings-mobile-apps.md)             |✔| | |✔|✔|
| [通知中樞](../articles/azure-functions/functions-bindings-notification-hubs.md) |✔|| | |✔|
| [佇列儲存體](../articles/azure-functions/functions-bindings-storage-queue.md)         |✔|✔|✔| |✔|
| [RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension)<sup>2</sup>             | |✔|✔| |✔|
| [SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md)                   |✔|✔| | |✔|
| [服務匯流排](../articles/azure-functions/functions-bindings-service-bus.md)             |✔|✔|✔| |✔|
| [SignalR](../articles/azure-functions/functions-bindings-signalr-service.md)             | |✔| |✔|✔|
| [表格儲存體](../articles/azure-functions/functions-bindings-storage-table.md)         |✔|✔| |✔|✔|
| [計時器](../articles/azure-functions/functions-bindings-timer.md)                         |✔|✔|✔| | |
| [Twilio](../articles/azure-functions/functions-bindings-twilio.md)                       |✔|✔| | |✔|

<sup>1</sup> 從 2.x 版執行階段開始，必須註冊 HTTP 和計時器以外的所有繫結。 請參閱[註冊繫結延伸模組](../articles/azure-functions/functions-bindings-register.md)。

<sup>2</sup> 觸發程序在取用方案中不受支援。 需要[執行階段驅動的觸發程序](../articles/azure-functions/functions-networking-options.md#premium-plan-with-virtual-network-triggers)。

<sup>3</sup> 僅在 Kubernetes、IoT Edge 和其他自我裝載模式中受到支援。
