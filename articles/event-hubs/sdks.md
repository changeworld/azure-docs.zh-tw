---
title: Azure 事件中樞-用戶端 Sdk |Microsoft Docs
description: 本文提供 Azure 事件中樞的用戶端 Sdk 的相關資訊。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 2b75a66197b152425198b94a68ac77e392cb4c0e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087555"
---
# <a name="azure-event-hubs---client-sdks"></a>Azure 事件中樞-用戶端 Sdk
本文提供 Azure 事件中樞所支援 Sdk 的下列資訊： 

- 您可以在應用程式中使用的套件位置 
- GitHub 位置，您可以在此找到原始程式碼、範例、讀我檔案、變更記錄檔、回報的問題，也會引發新的問題 
- 快速入門教學課程的連結 

## <a name="client-sdks"></a>用戶端 SDK
下表描述所有目前可用的 Azure 事件中樞執行時間用戶端。 雖然這些程式庫其中也包括有限的管理功能，但也有專用於管理作業的特定程式庫。 這些程式庫的核心重點是從事件中樞**傳送和接收訊息**。

| Language | Package | 參考 | 
| -------- | ------- | --------------- | 
| . NET Standard （**最新版本**，同時支援 .net Core 和 .NET Framework） | [EventHubs](https://www.nuget.org/packages/Azure.Messaging.EventHubs/) |<ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs)</li><li>[教學課程](get-started-dotnet-standard-send-v2.md)</li></ul> |
|       | [EventHubs. Processor](https://www.nuget.org/packages/Azure.Messaging.EventHubs.Processor/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor)</li><li>[教學課程](get-started-dotnet-standard-send-v2.md)</li></ul> |
| . NET Standard （**舊版**和支援 .net Core 和 .NET Framework） | [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs)</li><li>[教學課程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> | 
|       | [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.EventHubs.Processor)</li><li>[教學課程](event-hubs-dotnet-standard-getstarted-send.md)</li></ul> |
| . NET Framework （**舊**） | [WindowsAzure.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) |<ul><li>[教學課程](event-hubs-dotnet-framework-getstarted-send.md)</li></ul> |
| Java | [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs)</li><li>[教學課程](get-started-java-send-v2.md)</li></ul> |
|      | [azure-eventhubs](https://search.maven.org/search?q=a:azure-eventhubs) **（舊版）** | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/microsoft-azure-eventhubs)</li><li>[教學課程](event-hubs-java-get-started-send.md)</li></ul> |
| Python |  [azure-eventhub](https://pypi.org/project/azure-eventhub/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub)</li><li>[教學課程](get-started-python-send-v2.md)</li></ul> |
|        | [azure-eventhub-checkpointstoreblob-aio](https://pypi.org/project/azure-eventhub-checkpointstoreblob-aio/) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio)</li><li>[教學課程](get-started-python-send-v2.md)</li></ul> |
| JavaScript | [azure/事件中樞](https://www.npmjs.com/package/@azure/event-hubs) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs)</li><li>[教學課程](get-started-node-send-v2.md)</li></ul> |
|            | [azure/eventhubs-checkpointstore-blob](https://www.npmjs.com/package/@azure/eventhubs-checkpointstore-blob) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/eventhubs-checkpointstore-blob)</li><li>[教學課程](get-started-node-send-v2.md)</li></ul> |
| Go | [azure-事件中樞-go](https://github.com/Azure/azure-event-hubs-go) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-go)</li><li>[教學課程](event-hubs-go-get-started-send.md)</li></ul> |
| C | [azure-事件-中樞-c](https://github.com/Azure/azure-event-hubs-c) | <ul><li>[GitHub 位置](https://github.com/Azure/azure-event-hubs-c)</li><li>[教學課程](event-hubs-c-getstarted-send.md)</li></ul> |

## <a name="management-sdks"></a>管理 SDK
下表列出目前所有可用的管理特定程式庫。 這些程式庫都不包含執行時間作業，而是用來**管理事件中樞實體**的唯一用途。

| Language | Package | 參考 | 
| -------- | ------- | --------------- | 
| .NET Standard | [Microsoft.Azure.Management.EventHub](https://www.nuget.org/packages/Microsoft.Azure.Management.EventHub) |<ul><li>[GitHub 位置](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Microsoft.Azure.Management.EventHub)</li><li>[教學課程](get-started-dotnet-standard-send-v2.md)</li></ul> |


## <a name="next-steps"></a>後續步驟

您可以造訪下列連結以深入了解事件中樞︰

* [事件中心概觀](./event-hubs-about.md)
* [建立事件中樞](event-hubs-create.md)
* [事件中樞常見問題集](event-hubs-faq.md)
