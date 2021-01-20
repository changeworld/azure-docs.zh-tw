---
title: Azure 服務匯流排端對端追蹤與診斷 | Microsoft Docs
description: '概述服務匯流排用戶端診斷和端對端追蹤 (用戶端透過所有與處理相關的服務。 ) '
ms.topic: article
ms.date: 01/17/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: edfd789f8803acf9fc8d76202805dec0187d220e
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601255"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>透過服務匯流排傳訊進行分散式追蹤與相互關聯

微服務開發的其中一個常見問題是能夠從用戶端透過處理所涉及的所有服務來追蹤作業。 此能力對於進行偵錯、效能分析、A/B 測試，以及其他一般診斷案例皆相當有用。
此問題的其中一部份，辨識追蹤作業的邏輯片段。 它包含訊息處理結果及延遲，以及外部相依性呼叫。 另外一部分，則是這些診斷事件於程序界線之外的關聯性。

當產生者透過佇列傳送訊息時，它通常會發生於其他邏輯作業的範圍中，並由其他用戶端或服務起始。 當取用者接收到訊息時，也會繼續相同的作業。 產生者與取用者 (以及其他處理該作業的服務) 應該都會發出遙測事件，以追蹤作業流程和結果。 若要將此類事件相互關聯並以端對端的方式追蹤作業，每個回報遙測的服務都必須為每個事件提供追蹤內容的戳記。

Microsoft Azure 服務匯流排傳訊已定義產生者與取用者應用來傳遞此類追蹤內容的裝載屬性。
該通訊協定是以 [HTTP 關聯性通訊協定](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) \(英文\) 為基礎。

# <a name="azuremessagingservicebus-sdk-latest"></a>[ (最新) 的 Azure 訊息匯流排 SDK ](#tab/net-standard-sdk-2)
| 屬性名稱        | 描述                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 產生者針對佇列之外部呼叫的唯一識別碼。 請參閱 [HTTP 通訊協定中的 Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) \(英文\) 以了解邏輯依據、考量及格式 |

## <a name="service-bus-net-client-autotracing"></a>服務匯流排 .NET 用戶端 autotracing
`ServiceBusProcessor`[適用于 .Net 的 Azure 訊息服務匯流排用戶端](/dotnet/api/azure.messaging.servicebus.servicebusprocessor)類別提供可由追蹤系統或用戶端程式代碼部分連結的追蹤檢測點。 該檢測允許從用戶端追蹤針對服務匯流排傳訊服務的所有呼叫。 如果訊息處理是藉[ `ProcessMessageAsync` `ServiceBusProcessor` 由使用 (](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync)訊息處理常式模式) 來完成，則也會檢測訊息處理。

### <a name="tracking-with-azure-application-insights"></a>透過 Azure Application Insights 進行追蹤

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) 能提供豐富的效能監視功能，包括自動要求和相依性追蹤。

請根據您的專案類型安裝 Application Insights SDK：
- [ASP.NET](../azure-monitor/app/asp-net.md) - 安裝 2.5-beta2 版或更新版本
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - 安裝 2.2.0-beta2 版或更新版本。
這些連結提供安裝 SDK、建立資源及設定 SDK (若有需要) 的詳細資料。 針對非 ASP.NET 應用程式，請參閱[適用於主控台應用程式的 Azure Application Insights](../azure-monitor/app/console.md) 一文。

如果[ `ProcessMessageAsync` `ServiceBusProcessor` 您使用 (](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync)訊息處理常式模式) 來處理訊息，則也會檢測訊息處理。 您服務所完成的所有服務匯流排呼叫都會自動進行追蹤，並與其他遙測專案相互關聯。 否則，請參考下列範例以進行手動的訊息處理追蹤。

#### <a name="trace-message-processing"></a>追蹤訊息處理

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

在此範例中，會針對每個已處理的訊息報告要求遙測，且具有時間戳記、持續時間和結果 (成功) 。 遙測也具有相互關聯屬性的集合。 於訊息處理期間回報的巢狀追蹤和例外狀況，也會具有相互關聯屬性的戳記，以代表它們是 `RequestTelemetry` 的「子系」。

如果您在訊息處理期間對支援的外部元件進行呼叫，也會自動追蹤和相互關聯。 請參閱[使用 Application Insights .NET SDK 追蹤自訂作業](../azure-monitor/app/custom-operations-tracking.md)以了解手動追蹤及相互關聯。

如果您除了 Application Insights SDK 之外，還在執行任何外部程式碼，則在查看 Application Insights 記錄時，預期會看到較長的 **持續時間** 。 

![Application Insights 記錄檔中的持續時間較長](./media/service-bus-end-to-end-tracing/longer-duration.png)

這並不表示接收訊息有延遲。 在此案例中，已經收到訊息，因為訊息會以參數的形式傳入 SDK 程式碼。 而且，App Insights 記錄檔中的 **名稱** 標記 (**進程**) 指出訊息現在正由您的外部事件處理常式代碼處理。 此問題與 Azure 無關。 相反地，這些計量會參考您外部程式碼的效率，因為已經從服務匯流排收到該訊息。 

### <a name="tracking-without-tracing-system"></a>在沒有追蹤系統下進行追蹤
如果您的追蹤系統不支援自動服務匯流排呼叫追蹤，您可能會想要將這類支援新增至追蹤系統或您的應用程式。 本節說明由服務匯流排 .NET 用戶端所傳送的診斷事件。  

服務匯流排 .NET 用戶端是使用 .NET 追蹤基本類型 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) \(英文\) 和 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) \(英文\) 進行檢測。

`Activity` 會作為追蹤內容，而 `DiagnosticSource` 則為通知機制。 

如果沒有 DiagnosticSource 事件的接聽程式，就會關閉檢測，並保留零的檢測成本。 DiagnosticSource 會將所有控制項賦予接聽程式：
- 接聽程式能控制要接聽的來源和事件
- 接聽程式能控制事件速率和取樣
- 事件會搭配裝載傳送，該承載能提供完整內容，使您可以在事件期間存取並修改 Message 物件

在繼續進行實作之前，請先熟悉 [DiagnosticSource 使用者指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) \(英文\)。

讓我們在 ASP.NET Core 應用程式中針對服務匯流排事件建立接聽程式，並使它可以透過 Microsoft.Extension.Logger 寫入記錄。
它會使用 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) \(英文\) 程式庫來訂閱 DiagnosticSource (不使用它來訂閱 DiagnosticSource 的方式也十分簡單)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

在此範例中，接聽程式會記錄每個服務匯流排作業的持續期間、結果、唯一識別碼，以及開始時間。

### <a name="events"></a>事件
針對每個作業，系統會傳送兩個事件：'Start' 和 'Stop'。 最可能的情況是，您只對「停止」事件有興趣。 它們提供作業的結果，以及開始時間和持續時間的活動屬性。

每個裝載都會為接聽程式提供作業的內容，它會複寫 API 傳入參數和傳回值。 'Stop' 事件裝載具有 'Start' 事件裝載的所有屬性，因此您可以完全忽略 'Start' 事件。

每個 'Stop' 事件都具有 `TaskStatus` 非同步作業為已完成的 `Status` 屬性，這也會在下方表格中省略以求精簡。

所有事件都會有下列符合開啟的遙測規格的屬性： https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md 。

- `message_bus.destination` –佇列/主題/訂用帳戶路徑
- `peer.address` –完整命名空間
- `kind` –生產者、取用者或用戶端。 產生者會在傳送訊息、接收時取用者，以及在進行用戶端時使用。
- `component` – `servicebus`

所有事件也都有 ' Entity ' 和 ' Endpoint ' 屬性，在下表中會省略它們
  * `string Entity` --實體 (佇列、主題等 ) 的名稱。
  * `Uri Endpoint`：服務匯流排端點 URL

### <a name="instrumented-operations"></a>檢測的作業
以下是已檢測作業的完整清單：

| 作業名稱 | 追蹤的 API |
| -------------- | ----------- | 
| ServiceBusSender 傳送 | ServiceBusSender. >sendmessageasync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender。排程 | ServiceBusSender. >schedulemessageasync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender。取消 | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver 接收 | ServiceBusReceiver. >receivemessageasync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver 查看 | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver。放棄 | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver。完成 | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. DeadLetter | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. 延遲 |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor. ProcessMessage | ServiceBusProcessor 上的處理器回呼集。 ProcessMessageAsync 屬性 |
| ServiceBusSessionProcessor.ProcessSessionMessage | ServiceBusSessionProcessor 上的處理器回呼集。 ProcessMessageAsync 屬性 |

### <a name="filtering-and-sampling"></a>篩選和取樣

在某些情況下，您可能只會想記錄部分的事件，以減少效能額外負荷或儲存空間耗用量。 您可以僅記錄 'Stop' 事件 (如上述範例所示)，或是對特定百分比的事件進行取樣。 
`DiagnosticSource` 提供搭配 `IsEnabled` 述詞來達成它的方式。 如需詳細資訊，請參閱 [DiagnosticSource 中以內容為基礎的篩選](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) \(英文\)。

`IsEnabled` 可以針對單一作業呼叫數次，以將效能影響降至最低。

系統會於下列序列中呼叫 `IsEnabled`：

1. 以 `IsEnabled(<OperationName>, string entity, null)` 為例，`IsEnabled("ServiceBusSender.Send", "MyQueue1")`。 請注意，結尾沒有「啟動」或「停止」。 請使用它來將特定作業或佇列篩選出來。 如果回呼方法傳回，就不會傳送作業的 `false` 事件。

   * 針對 'Process' 和 'ProcessSession' 作業，您也會接收到 `IsEnabled(<OperationName>, string entity, Activity activity)` 回呼。 請使用它來根據 `activity.Id` 或 Tags 屬性篩選事件。
  
2. 以 `IsEnabled(<OperationName>.Start)` 為例，`IsEnabled("ServiceBusSender.Send.Start")`。 檢查是否應該引發 'Start' 事件。 結果只會影響「啟動」事件，但進一步的檢測不會依賴它。

沒有 `IsEnabled` ' Stop ' 事件。

若某個作業結果為例外狀況，系統便會呼叫 `IsEnabled("ServiceBusSender.Send.Exception")`。 您只能訂閱 'Exception' 事件並避免剩下的檢測。 在此情況下，您仍然必須處理這類例外狀況。 因為已停用其他檢測，所以您不應該預期追蹤內容會與取用者的訊息一起傳送給生產者。

您也可以使用 `IsEnabled` 來實作取樣策略。 `Activity.Id` `Activity.RootId` 只要是由追蹤系統或您自己的程式碼) ，就可以根據或在所有輪 (之間進行取樣，以確保取樣一致。

如果有相同來源的多個 `DiagnosticSource` 接聽程式，就足以讓一個接聽程式接受事件，因此不保證 `IsEnabled` 會呼叫。



# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

| 屬性名稱        | 描述                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | 產生者針對佇列之外部呼叫的唯一識別碼。 請參閱 [HTTP 通訊協定中的 Request-Id](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) \(英文\) 以了解邏輯依據、考量及格式 |
|  Correlation-Context | 作業內容，系統會將它傳播至涉及作業處理的所有服務。 如需詳細資訊，請參閱 [HTTP 通訊協定中的 Correlation-Context](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) \(英文\) |

## <a name="service-bus-net-client-autotracing"></a>服務匯流排 .NET 用戶端 autotracing

從 3.0.0 版開始，[適用於 .NET 的 Microsoft Azure 服務匯流排用戶端](/dotnet/api/microsoft.azure.servicebus.queueclient)會提供追蹤檢測點，可由追蹤系統或用戶端程式碼片段連結。
該檢測允許從用戶端追蹤針對服務匯流排傳訊服務的所有呼叫。 若訊息處理是透過[訊息處理常式模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)完成，則訊息處理也會進行檢測處理

### <a name="tracking-with-azure-application-insights"></a>透過 Azure Application Insights 進行追蹤

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) 能提供豐富的效能監視功能，包括自動要求和相依性追蹤。

請根據您的專案類型安裝 Application Insights SDK：
- [ASP.NET](../azure-monitor/app/asp-net.md) - 安裝 2.5-beta2 版或更新版本
- [ASP.NET Core](../azure-monitor/app/asp-net-core.md) - 安裝 2.2.0-beta2 版或更新版本。
這些連結提供安裝 SDK、建立資源及設定 SDK (若有需要) 的詳細資料。 針對非 ASP.NET 應用程式，請參閱[適用於主控台應用程式的 Azure Application Insights](../azure-monitor/app/console.md) 一文。

如果您使用 [訊息處理常式模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) 來處理訊息，您已完成：您的服務所完成的所有服務匯流排呼叫都會自動追蹤，並與其他遙測專案相互關聯。 否則，請參考下列範例以進行手動的訊息處理追蹤。

#### <a name="trace-message-processing"></a>追蹤訊息處理

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

在此範例中，系統會針對每個已處理的訊息回報 `RequestTelemetry`，並具有時間戳記、持續期間及結果 (成功)。 遙測也具有相互關聯屬性的集合。
於訊息處理期間回報的巢狀追蹤和例外狀況，也會具有相互關聯屬性的戳記，以代表它們是 `RequestTelemetry` 的「子系」。

如果您在訊息處理期間對支援的外部元件進行呼叫，也會自動追蹤和相互關聯。 請參閱[使用 Application Insights .NET SDK 追蹤自訂作業](../azure-monitor/app/custom-operations-tracking.md)以了解手動追蹤及相互關聯。

如果您除了 Application Insights SDK 之外，還在執行任何外部程式碼，則在查看 Application Insights 記錄時，預期會看到較長的 **持續時間** 。 

![Application Insights 記錄檔中的持續時間較長](./media/service-bus-end-to-end-tracing/longer-duration.png)

這並不表示接收訊息有延遲。 在此案例中，已經收到訊息，因為訊息會以參數的形式傳入 SDK 程式碼。 而且，App Insights 記錄檔中的 **名稱** 標記 (**進程**) 指出訊息現在正由您的外部事件處理常式代碼處理。 此問題與 Azure 無關。 相反地，這些計量會參考您外部程式碼的效率，因為已經從服務匯流排收到該訊息。 請參閱 [GitHub 上的這個](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) 檔案，以瞭解在從服務匯流排收到訊息之後，會產生並指派 **進程** 標籤的位置。 

### <a name="tracking-without-tracing-system"></a>在沒有追蹤系統下進行追蹤
如果您的追蹤系統不支援自動服務匯流排呼叫追蹤，您可能會想要將這類支援新增至追蹤系統或您的應用程式。 本節說明由服務匯流排 .NET 用戶端所傳送的診斷事件。  

服務匯流排 .NET 用戶端是使用 .NET 追蹤基本類型 [System.Diagnostics.Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) \(英文\) 和 [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) \(英文\) 進行檢測。

`Activity` 會作為追蹤內容，而 `DiagnosticSource` 則為通知機制。 

如果沒有 DiagnosticSource 事件的接聽程式，就會關閉檢測，並保留零的檢測成本。 DiagnosticSource 會將所有控制項賦予接聽程式：
- 接聽程式能控制要接聽的來源和事件
- 接聽程式能控制事件速率和取樣
- 事件會搭配裝載傳送，該承載能提供完整內容，使您可以在事件期間存取並修改 Message 物件

在繼續進行實作之前，請先熟悉 [DiagnosticSource 使用者指南](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) \(英文\)。

讓我們在 ASP.NET Core 應用程式中針對服務匯流排事件建立接聽程式，並使它可以透過 Microsoft.Extension.Logger 寫入記錄。
它會使用 [System.Reactive.Core](https://www.nuget.org/packages/System.Reactive.Core) \(英文\) 程式庫來訂閱 DiagnosticSource (不使用它來訂閱 DiagnosticSource 的方式也十分簡單)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

在此範例中，接聽程式會記錄每個服務匯流排作業的持續期間、結果、唯一識別碼，以及開始時間。

#### <a name="events"></a>事件

針對每個作業，系統會傳送兩個事件：'Start' 和 'Stop'。 最可能的情況是，您只對「停止」事件有興趣。 它們提供作業的結果，以及開始時間和持續時間的活動屬性。

每個裝載都會為接聽程式提供作業的內容，它會複寫 API 傳入參數和傳回值。 'Stop' 事件裝載具有 'Start' 事件裝載的所有屬性，因此您可以完全忽略 'Start' 事件。

所有事件也都有 ' Entity ' 和 ' Endpoint ' 屬性，在下表中會省略它們
  * `string Entity`：實體 (佇列、主題等) 的名稱
  * `Uri Endpoint`：服務匯流排端點 URL

每個 'Stop' 事件都具有 `TaskStatus` 非同步作業為已完成的 `Status` 屬性，這也會在下方表格中省略以求精簡。

以下是已檢測作業的完整清單：

| 作業名稱 | 追蹤的 API | 特定的裝載屬性|
|----------------|-------------|---------|
| Microsoft.Azure.ServiceBus.Send | [MessageSender.SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` - 正在傳送之訊息的清單 |
| Microsoft.Azure.ServiceBus.ScheduleMessage | [MessageSender.ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` - 正在處理的訊息<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` - 排定的訊息位移<br/>`long SequenceNumber` - 已排程訊息的序號 ('Stop' 事件裝載) |
| Microsoft.Azure.ServiceBus.Cancel | [MessageSender.CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` - 要取消之訊息的序號 | 
| Microsoft.Azure.ServiceBus.Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` - 可接收訊息的數目上限。<br/>`IList<Message> Messages` - 已接收訊息的清單 ('Stop' 事件裝載) |
| Microsoft.Azure.ServiceBus.Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` - 瀏覽訊息批次的起點。<br/>`int RequestedMessageCount` - 要擷取之訊息的數目。<br/>`IList<Message> Messages` - 已接收訊息的清單 ('Stop' 事件裝載) |
| Microsoft.Azure.ServiceBus.ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` - 包含要接收之序號的清單。<br/>`IList<Message> Messages` - 已接收訊息的清單 ('Stop' 事件裝載) |
| Microsoft.Azure.ServiceBus.Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` - 包含要完成對應訊息之鎖定 Token 的清單。|
| Microsoft.Azure.ServiceBus.Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` - 要放棄之對應訊息的鎖定 Token。 |
| Microsoft.Azure.ServiceBus.Defer | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` - 要延後之對應訊息的鎖定 Token。 | 
| Microsoft.Azure.ServiceBus.DeadLetter | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` - 要進行無效信件處理之對應訊息的鎖定 Token。 | 
| Microsoft.Azure.ServiceBus.RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` - 要更新鎖定之對應訊息的鎖定 Token。<br/>`DateTime LockedUntilUtc` - 以 UTC 格式呈現的新鎖定 Token 到期日和時間。 ('Stop' 事件裝載)|
| Microsoft.Azure.ServiceBus.Process | 於 [IReceiverClient.RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) 中提供的訊息處理常式 Lambda 函式 | `Message Message` - 正在處理的訊息。 |
| Microsoft.Azure.ServiceBus.ProcessSession | 於 [IQueueClient.RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) 中提供的訊息工作階段處理常式 Lambda 函式 | `Message Message` - 正在處理的訊息。<br/>`IMessageSession Session` - 正在處理的工作階段 |
| Microsoft.Azure.ServiceBus.AddRule | [SubscriptionClient.AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` - 提供要新增之規則的規則描述。 |
| Microsoft.Azure.ServiceBus.RemoveRule | [SubscriptionClient.RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` - 要移除之規則的名稱。 |
| Microsoft.Azure.ServiceBus.GetRules | [SubscriptionClient.GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` - 與訂用帳戶相關聯的所有規則。 (僅限 'Stop' 裝載) |
| Microsoft.Azure.ServiceBus.AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` - 存在於訊息中的 sessionId。 |
| Microsoft.Azure.ServiceBus.GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` - 存在於訊息中的 sessionId。<br/>`byte [] State` - 工作階段狀態 ('Stop' 事件裝載) |
| Microsoft.Azure.ServiceBus.SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` - 存在於訊息中的 sessionId。<br/>`byte [] State` - 工作階段狀態 |
| Microsoft.Azure.ServiceBus.RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` - 存在於訊息中的 sessionId。 |
| Microsoft.Azure.ServiceBus.Exception | 任何經檢測的 API| `Exception Exception` - 例外狀況執行個體 |

在每個事件中，您都可以存取保存目前作業內容的 `Activity.Current`。

#### <a name="logging-more-properties"></a>記錄更多屬性

`Activity.Current` 會提供目前作業及其父系的詳細內容。 如需詳細資訊，請參閱 [使用中的檔案](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)。
服務匯流排檢測可在其保存中提供詳細資訊 `Activity.Current.Tags` `MessageId` ，並在 `SessionId` 可用時提供更多資訊。

追蹤 'Receive'、'Peek' 及 'ReceiveDeferred' 事件的活動，也可能會有 `RelatedTo` 標記。 它會保存結果所接收到訊息之 `Diagnostic-Id` 的相異清單。
這類作業可能會導致接收到數個不相關的訊息。 此外， `Diagnostic-Id` 當作業開始時，並不知道「接收」作業只能與使用此標記的「處理」作業相互關聯。 它在分析效能問題時相當有用，並可以檢查接收訊息所需花費的時間。

記錄 Tags 的有效方式為重複處理它們，因此若要將 Tags 新增至上述範例，將會如下所示： 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>篩選和取樣

在某些情況下，您可能只會想記錄部分的事件，以減少效能額外負荷或儲存空間耗用量。 您可以僅記錄 'Stop' 事件 (如上述範例所示)，或是對特定百分比的事件進行取樣。 
`DiagnosticSource` 提供搭配 `IsEnabled` 述詞來達成它的方式。 如需詳細資訊，請參閱 [DiagnosticSource 中以內容為基礎的篩選](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering) \(英文\)。

`IsEnabled` 可以針對單一作業呼叫數次，以將效能影響降至最低。

系統會於下列序列中呼叫 `IsEnabled`：

1. 以 `IsEnabled(<OperationName>, string entity, null)` 為例，`IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")`。 請注意，結尾沒有「啟動」或「停止」。 請使用它來將特定作業或佇列篩選出來。 如果回呼方法傳回 `false` ，則不會傳送作業的事件

   * 針對 'Process' 和 'ProcessSession' 作業，您也會接收到 `IsEnabled(<OperationName>, string entity, Activity activity)` 回呼。 請使用它來根據 `activity.Id` 或 Tags 屬性篩選事件。
  
2. 以 `IsEnabled(<OperationName>.Start)` 為例，`IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")`。 檢查是否應該引發 'Start' 事件。 結果只會影響「啟動」事件，但進一步的檢測不會依賴它。

沒有 `IsEnabled` ' Stop ' 事件。

若某個作業結果為例外狀況，系統便會呼叫 `IsEnabled("Microsoft.Azure.ServiceBus.Exception")`。 您只能訂閱 'Exception' 事件並避免剩下的檢測。 在此情況下，您仍然必須處理這類例外狀況。 因為已停用其他檢測，所以您不應該預期追蹤內容會與取用者的訊息一起傳送給生產者。

您也可以使用 `IsEnabled` 來實作取樣策略。 `Activity.Id` `Activity.RootId` 只要是由追蹤系統或您自己的程式碼) ，就可以根據或在所有輪 (之間進行取樣，以確保取樣一致。

如果有相同來源的多個 `DiagnosticSource` 接聽程式，就足以讓一個接聽程式接受事件，因此不保證 `IsEnabled` 會呼叫。

---

## <a name="next-steps"></a>後續步驟

* [Application Insights 相互關聯](../azure-monitor/app/correlation.md)
* [Application Insights 監視相依性](../azure-monitor/app/asp-net-dependencies.md) 以查看 REST、SQL 或其他外部資源是否會降低您的效能。
* [使用 Application Insights .NET SDK 追蹤自訂作業](../azure-monitor/app/custom-operations-tracking.md)

