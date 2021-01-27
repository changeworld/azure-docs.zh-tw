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
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/27/2021
ms.locfileid: "98900817"
---
## <a name="what-is-a-replication-task"></a>什麼是複寫工作？

複寫工作會從來源接收事件，並將事件轉送至目標。
如果來源和目標通訊協定不同，大部分的複寫工作都會將事件轉送為未變更，而且最多會在元資料結構之間進行對應。 

複寫工作通常是無狀態的，這表示它們不會在一或多個工作的連續或平行執行之間共用狀態或其他副作用。 這也適用于批次處理和連結，這兩者都可以在資料流程的現有狀態之上執行。 

這會讓複寫工作與匯總工作不同，這些工作通常是具狀態的，而且是分析架構和服務（例如 [Azure 串流分析](../articles/stream-analytics/stream-analytics-introduction.md)）的網域。

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Azure Functions 中的複寫應用程式和工作

在 Azure Functions 中，會使用會從已設定來源取得一或多個輸入訊息的 [觸發](../articles/azure-functions/functions-triggers-bindings.md) 程式來執行複寫工作，並使用 [輸出](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) 系結將從來源複製的訊息轉送至設定的目標。 

| 觸發程序  | 輸出 |
|----------|--------|
| [Azure 事件中樞觸發程式](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Azure 事件中樞輸出系結](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure 服務匯流排觸發程式](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure 服務匯流排輸出系結](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT 中樞觸發程式](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Azure IoT 中樞輸出系結](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure 事件方格觸發程式](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure 事件方格輸出系結](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure 佇列儲存體觸發程序](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Azure 佇列儲存體輸出系結](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka 觸發程式](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka 輸出系結](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ 觸發程式](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ 輸出系結](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Azure 通知中樞輸出系結](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Azure SignalR 服務輸出系結](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid 輸出系結](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

複寫工作會透過與任何其他 Azure Functions 應用程式相同的部署方法，部署至複寫應用程式。 您可以將多個工作設定為相同的應用程式。 

使用 Azure Functions Premium 時，多個複寫應用程式可以共用相同的基礎資源集區，稱為 App Service 方案。 這表示您可以輕鬆地使用以 JAVA 撰寫的複寫工作，來共置以 .NET 撰寫的複寫工作。 如果您想要利用僅適用于 JAVA 的特定程式庫，以及這些程式庫是否是特定整合路徑的最佳選項，雖然您通常偏好針對其他複寫工作使用不同的語言和執行時間，這會是很重要的。 

如果有的話，您應該偏好透過觸發程式的批次導向觸發程式來傳遞個別事件或訊息，您應該一律取得完整事件或訊息結構，而不是依賴 Azure 函數的參數系結 [運算式](../articles/azure-functions/functions-bindings-expressions-patterns.md)。

函式的名稱應該反映您所連接的來源和目標的配對，而您應該在應用程式佈建檔中使用該名稱的連接字串或其他設定專案的參考前置詞。 

### <a name="data-and-metadata-mapping"></a>資料和中繼資料對應

一旦決定一組輸入觸發程式和輸出系結，就必須在不同的事件或訊息類型之間執行一些對應，除非觸發程式的類型和輸出相同。

針對在事件中樞和服務匯流排之間複製訊息的簡單複寫工作，您不需要撰寫自己的程式碼，但可以在複寫範例所 [提供的公用程式程式庫](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 上進行精簡。

### <a name="retry-policy"></a>重試原則

若要避免在複寫函數的任一邊的可用性事件期間遺失資料，您必須將重試原則設定為健全。 請參閱 [Azure Functions 檔](../articles/azure-functions/functions-bindings-error-pages.md) ，以重試設定重試原則的相關資訊。 

針對 [範例存放庫](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) 中的範例專案所選擇的原則設定，會將重試間隔的指數輪詢策略設定為5秒到15分鐘，並且無限次重試以避免資料遺失。 

針對服務匯流排，請參閱「 [在觸發程式恢復功能之上使用重試支援](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) 」一節，以瞭解觸發程式的互動和為佇列定義的最大傳遞計數。

### <a name="setting-up-a-replication-application-host"></a>設定複寫應用程式主機

複寫應用程式是一或多個複寫工作的執行主機。 

它是一種 Azure Functions 的應用程式，設定為在取用方案上執行，或 (Azure Functions 高階方案的建議) 。 所有複寫應用程式都必須以 [系統或使用者指派的受控識別](../articles/app-service/overview-managed-identity.md)來執行。 

連結的 Azure Resource Manager (ARM) 範本會使用下列方式建立和設定複寫應用程式：

* 用來追蹤複寫進度和記錄檔的 Azure 儲存體帳戶，
* 系統指派的受控識別，以及 
* 用於監視的 Azure 監視和 Application Insights 整合。

必須存取系結至 Azure 虛擬網路 (VNet) 之事件中樞的複寫應用程式，必須使用 Azure Functions 高階方案，並設定為連接至相同的 VNet，也就是其中一個可用的選項。


|       | 部署 | 視覺化  
|-------|------------------|--------------|---------------|
| **Azure Functions 取用方案** | [![部署至 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![視覺化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions Premium 方案** |[![部署至 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![視覺化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **使用 VNet Azure Functions Premium 方案** | [![部署至 Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![視覺化](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>範例

[範例存放庫](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/)包含數個複寫工作的範例，這些工作會在事件中樞和/或服務匯流排實體之間複製事件。

針對事件中樞之間的複製事件，您可以使用事件中樞觸發程式搭配事件中樞輸出系結：

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

若要在服務匯流排實體之間複製訊息，您可以使用服務匯流排觸發程式和輸出系結：

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Helper 方法可讓您輕鬆地在事件中樞和服務匯流排之間進行複寫：

| 來源      | 目標      | 進入點 
|-------------|-------------|------------------------------------------------------------------------
| 事件中樞   | 事件中樞   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| 事件中樞   | 服務匯流排 | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| 服務匯流排 | 事件中樞   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| 服務匯流排 | 服務匯流排 | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>監視

若要瞭解如何監視您的複寫應用程式，請參閱 Azure Functions 檔的 [ [監視] 區段](../articles/azure-functions/configure-monitoring.md) 。

監視複寫工作的一個特別有用的視覺化檢視是 Application Insights 的 [應用程式對應](../articles/azure-monitor/app/app-map.md)，它會從已捕捉的監視資訊自動產生，並可讓您探索複寫工作來源和目標傳輸的可靠性和效能。

如需立即診斷見解，您可以使用 [即時計量](../articles/azure-monitor/app/live-stream.md) 入口網站工具，以提供記錄詳細資料的低延遲視覺效果。

## <a name="next-steps"></a>後續步驟

* [Azure Functions 部署](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions 診斷](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions 網路功能選項](../articles/azure-functions/functions-networking-options.md)
* [Azure Application Insights](../articles/azure-monitor/app/app-insights-overview.md)