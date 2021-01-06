---
title: 包含檔案
description: 包含檔案
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: b487dcad83ccbc31adf2d7ec2dd77c490db2c68e
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/06/2021
ms.locfileid: "97935186"
---
Azure Functions 可讓您建立以預先建立的進入點為基礎的僅限設定複寫工作。 Azure Functions 的設定 [式複寫範例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) 說明如何在您自己的程式碼中運用 [預先建立](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 的協助程式，或完全避免處理常式代碼，並只使用設定。

## <a name="create-a-replication-task"></a>建立複寫工作
若要建立這類複寫工作，請先在專案資料夾底下建立新的資料夾。 新資料夾的名稱是函數的名稱，例如 `europeToAsia` 或 `telemetry` 。 名稱與所使用的訊息實體沒有直接相互關聯，而且只能供您識別。 您可以在同一個專案中建立數十個函數。

接下來， `function.json` 在資料夾中建立檔案。 檔案會設定函數。 從下列內容開始：

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

在該檔案中，您必須完成三個設定步驟，取決於您想要連接的實體：

1. [設定輸入方向](#configure-the-input-direction)
2. [設定輸出方向](#configure-the-output-direction)
3. [設定進入點](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>設定輸入方向

#### <a name="event-hub-input"></a>事件中樞輸入

如果您想要從事件中樞接收事件，請將設定資訊新增至設定的「系結」內的最上方區段

* **輸入** -"eventHubTrigger" 類型。
* **連接** -事件中樞連接字串的應用程式設定值的名稱。 
* **eventHubName** -連接字串所識別的命名空間內的事件中樞名稱。
* **consumerGroup** -取用者群組的名稱。 請注意，名稱會以百分比符號括住，因此也會參考應用程式設定值。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>服務匯流排佇列輸入

如果您想要接收來自服務匯流排佇列的事件，請將設定資訊新增至設定的「系結」內的最上層區段

* **輸入** -"serviceBusTrigger" 類型。
* **連接** -服務匯流排連接字串之應用程式設定值的名稱。
* **queueName** -連接字串所識別之命名空間內的服務匯流排佇列名稱。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>服務匯流排主題輸入

如果您想要接收來自服務匯流排主題的事件，請將設定資訊新增至設定的「系結」內的最上層區段

* **輸入** -"serviceBusTrigger" 類型。
* **連接** -服務匯流排連接字串之應用程式設定值的名稱。 `{FunctionName}-source-connection`如果您想要使用提供的腳本，這個值必須是。
* **topicName** -連接字串所識別的命名空間內服務匯流排主題的名稱。
* **subscriptionName** -連接字串所識別的命名空間中，指定主題的服務匯流排訂用帳戶名稱。

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>設定輸出方向

#### <a name="event-hub-output"></a>事件中樞輸出

如果您想要將事件轉寄至事件中樞，請將設定資訊新增至設定的「系結」中的底部區段

* **輸入** -"eventHub" 類型。
* **連接** -事件中樞連接字串的應用程式設定值的名稱。 
* **eventHubName** -連接字串所識別的命名空間內的事件中樞名稱。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>服務匯流排佇列輸出

如果您想要將事件轉寄至服務匯流排佇列，請將設定資訊新增至設定的「系結」中的底部區段

* **輸入** -「匯流排匯流排」類型。
* **連接** -服務匯流排連接字串之應用程式設定值的名稱。 
* **queueName** -連接字串所識別之命名空間內的服務匯流排佇列名稱。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>服務匯流排主題輸出

如果您想要將事件轉寄至服務匯流排主題，請將設定資訊新增至設定的「系結」中的底部區段

* **輸入** -「匯流排匯流排」類型。
* **連接** -服務匯流排連接字串之應用程式設定值的名稱。 
* **topicName** -連接字串所識別的命名空間內服務匯流排主題的名稱。

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>設定進入點

進入點設定會挑選其中一個標準複寫工作。 如果您要修改 `Azure.Messaging.Replication` 專案，也可以加入工作，並在這裡參考。 例如：

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

下表提供來源和目標群組合的正確值：

| 來源      | 目標      | 進入點 
|-------------|-------------|------------------------------------------------------------------------
| 事件中樞   | 事件中樞   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 事件中樞   | 服務匯流排 | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| 服務匯流排 | 事件中樞   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| 服務匯流排 | 服務匯流排 | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>重試原則

請參閱 [Azure Functions 檔](/azure/azure-functions/functions-bindings-error-pages) ，以重試設定重試原則的相關資訊。 在此存放庫的整個專案中選擇的原則設定，會將具有重試間隔的指數輪詢策略設定為5秒到5分鐘，並無限期地重試以避免資料遺失。

針對服務匯流排，請參閱「 [在觸發程式恢復功能之上使用重試支援](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) 」一節，以瞭解觸發程式的互動和為佇列定義的最大傳遞計數。

### <a name="build-deploy-and-configure"></a>組建、部署和設定

當您將焦點放在設定時，工作仍然需要建立可部署的應用程式，並設定 Azure Functions 主機，使其擁有連接至指定端點的所有必要資訊。 

在 Azure Functions 的設定 [式複寫範例](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config)中，會說明這一點，以及可重複使用的腳本。

