---
title: 使用 Azure 應用程式 Insights .NET SDK 追蹤自訂作業
description: 使用 Azure Application Insights .NET SDK 追蹤自訂作業
ms.topic: conceptual
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 316c1b7ea32f661b009bfee7a89cb7e5ed082f3b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690852"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>使用 Application Insights .NET SDK 追蹤自訂作業

Azure Application Insights SDK 會自動追蹤相依服務的連入 HTTP 要求和呼叫，例如 HTTP 要求、SQL 查詢。 追蹤和相互關聯要求與相依性，可讓您深入了解橫跨所有微服務 (合併此應用程式) 的整體應用程式回應能力和可靠性。 

有一類應用程式模式無法以一般方式支援。 適當監視這類模式時，需要進行手動程式碼檢測。 本文涵蓋可能需要手動檢測的一些模式，例如自訂佇列處理和執行長時間執行背景工作。

本文提供有關如何使用 ApplicationInsights SDK 追蹤自訂作業的指引。 本文件相關於：

- 適用於 .NET (也稱為 Base SDK) 的 Application Insights 版本 2.4+。
- 適用於 Web 應用程式 (執行 ASP.NET) 的 Application Insights 版本 2.4+。
- Application Insights for ASP.NET Core 版本 2.1+。

## <a name="overview"></a>總覽
作業是應用程式執行的邏輯部分。 它具有名稱、開始時間、持續時間、結果和執行的內容，例如使用者名稱、屬性和結果。 如果作業 A 是由作業 B 起始，則作業 B 設為 A 的父代。作業只能有一個父代，但是可以有多個子系作業。 如需有關作業和遙測相互關聯的詳細資訊，請參閱 [Azure Application Insights 遙測相互關聯](correlation.md)。

在 Application Insights.NET SDK 中，作業是由抽象類別 [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) 及其子系 [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) 和 [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs) 描述。

## <a name="incoming-operations-tracking"></a>傳入作業追蹤 
Application Insights Wb SDK 會針對在 IIS 管線中執行的 ASP.NET 應用程式和所有的 ASP.NET Core 應用程式，自動收集 HTTP 要求。 其他平台和架構有社群支援的解決方案。 不過，如果任何標準或社群支援的解決方案不支援應用程式，您可以用手動方式進行檢測。

接收佇列中項目的背景工作是另一個需要自訂追蹤的範例。 對於某些佇列，將訊息新增至此佇列的呼叫會作為相依性追蹤。 但是，不會自動收集描述處理訊息的高階作業。

我們來看看追蹤這類作業的方式。

在較高的層級中，工作是建立 `RequestTelemetry` 並且設定已知的屬性。 作業完成之後，您會追蹤遙測。 下列範例示範此工作。

### <a name="http-request-in-owin-self-hosted-app"></a>Owin 自我裝載應用程式中的 HTTP 要求
在此範例中，追蹤內容會根據[相互關聯的 HTTP 通訊協定](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)傳播。 您應該預期會收到該處所述的標題。

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

相互關聯的 HTTP 通訊協定也會宣告 `Correlation-Context` 標題。 不過，為了簡單起見在這裡省略。

## <a name="queue-instrumentation"></a>佇列檢測
雖然相互關聯的[W3C 追蹤內容](https://www.w3.org/TR/trace-context/)和[HTTP 通訊協定](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md)會透過 HTTP 要求傳遞相互關聯詳細資料，但每個佇列通訊協定都必須定義如何沿著佇列訊息傳遞相同的詳細資料。 有些佇列通訊協定 (例如 AMQP) 允許傳遞其他中繼資料，其他 (例如 Azure 儲存體佇列) 則需要將內容編碼為 	訊息承載。

> [!NOTE]
> * **佇列尚不支援跨元件追蹤**使用 HTTP 時，如果您的生產者和取用者將遙測傳送至不同的 Application Insights 資源，交易診斷體驗和應用程式對應會顯示交易和端對端對應。 若為佇列，則尚不支援這種情況。 

### <a name="service-bus-queue"></a>服務匯流排佇列
Application Insights 會使用[適用於 .NET 的 Microsoft Azure 服務匯流排用戶端](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) 3.0.0 版本和更新版本來追蹤服務匯流排通訊呼叫。
如果您使用[訊息處理常式模式](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler)來處理訊息，便無需採取任何動作，系統會自動追蹤由您的服務所完成的所有服務匯流排呼叫，並將它們與其他遙測項目相互關聯。 如果您手動處理訊息，請參閱[使用 Microsoft Application Insights 追蹤服務匯流排用戶端](../../service-bus-messaging/service-bus-end-to-end-tracing.md)。

如果您使用 [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 套件，請進一步閱讀 - 下列範例示範如何追蹤 (和相互關聯) 服務匯流排的呼叫，因為服務匯流排佇列使用 AMQP 通訊協定，而 Application Insights 不會自動追蹤佇列作業。
相互關聯識別碼會傳入訊息屬性。

#### <a name="enqueue"></a>加入佇列

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure 儲存體佇列
下列範例示範如何追蹤 [Azure 儲存體佇列](../../storage/queues/storage-dotnet-how-to-use-queues.md)作業，並且讓產生者、取用者與 Azure 儲存體之間的遙測相互關聯。 

儲存體佇列有 HTTP API。 HTTP 要求的 Application Insights 相依性收集器會追蹤對佇列的所有呼叫。
根據預設，它是在 ASP.NET 和 ASP.NET Core 應用程式上設定，而在其他類型的應用程式中，您可以參考[主控台應用程式檔](../../azure-monitor/app/console.md)

您可能也想要相互關聯 Application Insights 作業識別碼與儲存體要求識別碼。 如需如何設定及取得儲存體要求用戶端和伺服器要求識別碼的詳細資訊，請參閱[監視、診斷 Azure 儲存體及進行移難排解](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing)。

#### <a name="enqueue"></a>加入佇列
因為 Azure 儲存體佇列支援 HTTP API，Application Insights 會自動追蹤所有作業與佇列。 在許多情況下，此檢測應該就足夠了。 不過，若要讓取用者端追蹤與生產者追蹤相互關聯，您必須傳遞一些相互關聯內容，類似於我們在「相互關聯的 HTTP 通訊協定」中的作業方式。 

這個範例顯示如何追蹤 `Enqueue` 作業。 您可以：

 - **相互關聯重試 (如果有的話)**：全部都有一個通用父代，也就是 `Enqueue` 作業。 否則會作為連入要求的子系追蹤。 如果佇列有多個邏輯要求，可能難以找到導致重試的呼叫。
 - **相互關聯儲存體記錄 (必要時)**：與 Application Insights 遙測相互關聯。

`Enqueue` 作業是父代作業 (例如，連入 HTTP 要求) 的子系。 HTTP 相依性呼叫是 `Enqueue` 作業的子系，是連入要求的孫系：

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

如果您因為其他原因，而想要減少您應用程式回報的遙測資料量，或不想追蹤 `Enqueue` 作業，您可以直接使用 `Activity` API：

- 建立 (和啟動) 新的 `Activity`，而不是啟動 Application Insights 作業。 您不** 需要在上面指派作業名稱以外的任何屬性。
- 將 `yourActivity.Id` 序列化成為訊息承載，而不是 `operation.Telemetry.Id`。 您也可以使用 `Activity.Current.Id`。


#### <a name="dequeue"></a>清除佇列
類似於 `Enqueue`，Application Insights 會自動追蹤儲存體佇列的實際 HTTP 要求。 不過 `Enqueue` 作業可能發生於父代內容，例如連入要求內容。 Application Insights SDK 會使這類作業 (及其 HTTP 部分) 與父代要求和相同範圍中報告的其他遙測自動相互關聯。

`Dequeue` 作業有些麻煩。 Application Insights SDK 會自動追蹤 HTTP 要求。 不過，它在剖析訊息之前並不知道相互關聯內容。 您無法將 HTTP 要求相互關聯，以取得具有其餘遙測資料的訊息，特別是在收到多個訊息時。

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Process

在下列範例中，追蹤連入訊息的方式類似於追蹤連入 HTTP 要求的方式：

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

同樣地，可能會檢測其他佇列作業。 預覽 (Peek) 作業應以類似清除佇列作業的方式進行檢測。 不一定要檢測佇列管理作業。 Application Insights 會追蹤 HTTP 這類作業，這在大多數情況下已足夠。

檢測訊息刪除時，請務必設定作業 (相互關聯) 識別碼。 或者，您可使用 `Activity` API。 您就不需要在遙測項目上設定作業識別碼，因為 Application Insights SDK 會為您設定：

- 取得佇列中的項目後，建立新的 `Activity`。
- 使用 `Activity.SetParentId(message.ParentId)` 讓取用者和產生者記錄相互關聯。
- 啟動 `Activity`。
- 使用 `Start/StopOperation` 協助程式追蹤清除佇列、處理和刪除作業。 從相同的非同步控制流程 (執行內容) 執行此作業。 如此一來，這些作業就會正確地相互關聯。
- 停止 `Activity`。
- 使用 `Start/StopOperation` 或手動呼叫 `Track` 遙測。

### <a name="dependency-types"></a>相依性類型

Application Insights 使用相依性類型來自訂 UI 體驗。 對於佇列，它會識別下列`DependencyTelemetry`可改善[交易診斷體驗](/azure/azure-monitor/app/transaction-diagnostics)的類型：
- `Azure queue`針對 Azure 儲存體佇列
- `Azure Event Hubs`針對 Azure 事件中樞
- `Azure Service Bus`針對 Azure 服務匯流排

### <a name="batch-processing"></a>批次處理
有些佇列中，您可以使用一個要求清除佇列多個訊息。 處理這類訊息可能是獨立的，並且屬於不同的邏輯作業。 不可能將作業與所處理`Dequeue`的特定訊息相互關聯。

每個訊息應該在自己的非同步控制流程中處理。 如需詳細資訊，請參閱[連出相依性追蹤](#outgoing-dependencies-tracking)一節。

## <a name="long-running-background-tasks"></a>長時間執行的背景工作

有些應用程式可能會應使用者要求，啟動長時間執行的作業。 就追蹤/檢測觀點而言，這與要求或相依性檢測並無不同： 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

在此範例中，`telemetryClient.StartOperation` 會建立 `DependencyTelemetry` 並填滿相互關聯內容。 假設您有一項父代作業，由排程作業的連入要求所建立。 只要 `BackgroundTask` 在與連入要求相同的非同步控制流程中啟動，它就會與該父代作業相互關聯。 `BackgroundTask` 和所有巢狀遙測項目將會自動與造成它的要求相互關聯，即使在要求結束後亦然。

從沒有任何相關聯作業 (`Activity`) 的背景執行緒啟動工作時，`BackgroundTask` 沒有任何父代。 不過，它可以有巢狀作業。 工作回報的所有遙測項目會與在 `BackgroundTask` 中建立的 `DependencyTelemetry` 相互關聯。

## <a name="outgoing-dependencies-tracking"></a>連出相依性追蹤
您可以追蹤自己的相依性種類或 Application Insights 不支援的作業。

服務匯流排佇列或儲存體佇列中的 `Enqueue` 方法可作為這類自訂追蹤的範例。

自訂相依性追蹤的一般方法如下：

- 呼叫 `TelemetryClient.StartOperation` (擴充) 方法，以填滿相互關聯所需的 `DependencyTelemetry` 屬性和其他一些屬性 (開始時間戳記、持續時間)。
- 在 `DependencyTelemetry` 上設定其他自訂屬性，例如名稱與您需要的任何其他內容。
- 進行相依性呼叫並且等候。
- 完成時使用 `StopOperation` 停止作業。
- 處理例外狀況。

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

處置作業會導致作業停止，因此您可以執行而不是呼叫 `StopOperation`。

*警告*：在某些情況下，未處理的例外狀況可能導致[無法呼叫 ](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-finally) `finally`，因此可能無法追蹤作業。

### <a name="parallel-operations-processing-and-tracking"></a>平行的作業處理和追蹤

`StopOperation` 只會停止已啟動的作業。 如果目前執行中作業不符合您想要停止的作業，則 `StopOperation` 不會有任何動作。 如果您以平行方式在相同的執行內容中啟動多項作業，可能會發生這種情形：

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

請務必一律以相同的**非同步**方法來呼叫 `StartOperation` 和處理作業，以隔離平行執行的作業。 如果作業是同步的 (或不是非同步的)，請包裝處理序並使用 `Task.Run` 追蹤：

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights 作業與系統診斷。活動
`System.Diagnostics.Activity`表示分散式追蹤內容，並由架構和程式庫用來建立和傳播進程內部和外部的內容，以及將遙測專案相互關聯。 活動搭配使用- `System.Diagnostics.DiagnosticSource`架構/程式庫之間的通知機制，用來通知相關事件（傳入或傳出要求、例外狀況等等）。

活動是 Application Insights 中的第一方公民，而自動相依性和要求集合會隨著`DiagnosticSource`事件而高度依賴它們。 如果您在應用程式中建立活動，則不會導致建立 Application Insights 的遙測。 Application Insights 需要接收 DiagnosticSource 事件，並知道要將活動轉譯成遙測的事件名稱和承載。

每個 Application Insights 作業（要求或相依性`Activity` ）都`StartOperation`牽涉到-呼叫時，它會在下方建立活動。 `StartOperation`是手動追蹤要求或相依性遙測，並確保所有專案都相互關聯的建議方式。

## <a name="next-steps"></a>後續步驟

- 了解 Application Insights 中[遙測相互關聯](correlation.md)的基本概念。
- 查看相互關聯的資料如何支援[交易診斷體驗](../../azure-monitor/app/transaction-diagnostics.md)和[應用程式對應](../../azure-monitor/app/app-map.md)。
- 如需 Application Insights 類型和資料模型，請參閱[資料模型](../../azure-monitor/app/data-model.md)。
- 向 Application Insights 報告自訂[事件和計量](../../azure-monitor/app/api-custom-events-metrics.md)。
- 請查看內容屬性集合的標準[設定](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet)。
- 查看 [System.Diagnostics.Activity 使用者指南](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md)以了解如何使遙測相互關聯。
