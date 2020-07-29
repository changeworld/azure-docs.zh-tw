---
title: 使用 Azure 服務匯流排改善效能的最佳做法
description: 描述如何使用服務匯流排來在交換代理訊息時將效能最佳化。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e0a6e54c1e941d7b7ff244ac40066a564e2ebbc4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341096"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>使用服務匯流排傳訊的效能改進最佳作法

本文描述如何使用 Azure 服務匯流排來在交換代理的訊息時將效能最佳化。 本文的第一個部分說明提供協助提高效能的不同機制。 第二個部分針對如何在特定案例中利用可提供最佳效能的方式來使用服務匯流排提供指引。

在本文中，「用戶端」一詞是指任何存取服務匯流排的實體。 用戶端可以擔任傳送者或接收者的角色。 「傳送者」一詞用於將訊息傳送至服務匯流排佇列或主題訂用帳戶的服務匯流排佇列或主題用戶端。 「接收者」一詞指的是從服務匯流排佇列或訂用帳戶接收訊息的服務匯流排佇列或訂用帳戶用戶端。

這些章節介紹幾個服務匯流排用來協助提升效能的概念。

## <a name="protocols"></a>通訊協定

服務匯流排可讓用戶端透過三種通訊協定的其中一種傳送和接收訊息：

1. 進階訊息佇列通訊協定 (AMQP)
2. 服務匯流排傳訊通訊協定 (SBMP)
3. 超文字傳輸通訊協定 (HTTP)

AMQP 是最有效率的，因為它會維護服務匯流排的連接。 它也會實作批次處理和預先擷取作業。 除非明確提到，否則本文中的所有內容都假設為使用 AMQP 和 SBMP。

> [!IMPORTANT]
> SBMP 僅適用于 .NET Framework。 AMQP 是 .NET Standard 的預設值。

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>選擇適當的服務匯流排 .NET SDK

有兩個支援的 .NET Sdk Azure 服務匯流排。 其 Api 非常類似，而且可能會讓您覺得要選擇哪一種。 請參閱下表來協助引導您進行決策。 我們建議您的 Microsoft. Azure 架構匯流排，因為它比較現代化、高效能且跨平臺相容。 此外，它支援透過 Websocket 的 AMQP，而且是開放原始碼專案之 Azure .NET SDK 集合的一部分。

| NuGet 封裝 | 主要命名空間 | 最小平臺 | 通訊協定 |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft Azure。<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>通用 Windows 平台 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">Windowsazure.storage。<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

如需最低 .NET Standard 平臺支援的詳細資訊，請參閱[.net 部署支援](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)。

## <a name="reusing-factories-and-clients"></a>重複使用處理站和用戶端

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

服務匯流排用戶端物件（例如或的 [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] 執行）應該註冊為單次個體（或具現化一次和共用）的相依性插入。 當您傳送一個訊息，並在傳送下一個訊息時重新建立傳訊處理站或佇列、主題及訂用帳戶用戶端之後，建議您不要將其關閉。 關閉傳訊處理站會刪除服務匯流排服務的連接，並在重新建立處理站時建立新的連接。 建立連接是成本高昂的作業，您可以藉由重新使用多項作業的相同處理站和用戶端物件來避免此作業。 您可以安全地使用這些用戶端物件，從多個執行緒進行並行的非同步作業。

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

服務匯流排用戶端物件（例如 `QueueClient` 或 `MessageSender` ）是透過[MessagingFactory][MessagingFactory]物件所建立，它也會提供內部連接管理。 當您傳送一個訊息，並在傳送下一個訊息時重新建立傳訊處理站或佇列、主題及訂用帳戶用戶端之後，建議您不要將其關閉。 關閉傳訊處理站會刪除服務匯流排服務的連接，並在重新建立處理站時建立新的連接。 建立連接是成本高昂的作業，您可以藉由重新使用多項作業的相同處理站和用戶端物件來避免此作業。 您可以安全地使用這些用戶端物件，從多個執行緒進行並行的非同步作業。

---

## <a name="concurrent-operations"></a>並行作業

執行作業 (傳送、接收、刪除等等) 需要一點時間。 這一次包含服務匯流排服務處理作業的作業，以及要求和回應的延遲。 若要增加每次的作業數目，就必須並行執行作業。

用戶端會透過執行非同步作業來排程並行作業。 下一個要求會在前一個要求完成之前啟動。 非同步傳送作業的程式碼片段範例如下：

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

非同步接收作業的程式碼範例如下。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

如需完整的<a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">原始程式碼範例 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>，請參閱 GitHub 存放庫：

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`物件會以連接字串、佇列名稱和「查看」接收模式具現化。 接下來， `receiver` 使用實例來註冊訊息處理常式。

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

如需完整的<a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">原始程式碼範例 <span class="docon docon-navigate-external x-hidden-focus"></span> </a>，請參閱 GitHub 存放庫：

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 20
    });
```

會 `MessagingFactory` `factory` 從連接字串建立物件。 使用 `factory` 實例 `MessageReceiver` 時，會具現化。 接下來， `receiver` 使用實例來註冊訊息處理常式。

---

## <a name="receive-mode"></a>接收模式

建立佇列或訂用帳戶用戶端時，您可以指定接收模式：「查看鎖定」** 或「接收與刪除」**。 預設接收模式是`PeekLock`。 在預設模式下操作時，用戶端會傳送要求以接收來自服務匯流排的訊息。 用戶端收到訊息後，它會傳送要求以完成訊息。

若要將接收模式設定為`ReceiveAndDelete`，這兩個步驟會結合為單一要求。 這些步驟可減少整體的作業數目並可改善整體訊息輸送量。 此效能改善的風險為遺失訊息。

服務匯流排不支援接收和刪除作業的交易。 此外，在用戶端想要延遲或讓訊息[寄不出去](service-bus-dead-letter-queues.md)的任何案例中都需要鎖定語意。

## <a name="client-side-batching"></a>用戶端批次處理

用戶端批次處理可讓佇列或主題用戶端將訊息的傳送延遲一段時間。 如果用戶端在此期間傳送其他訊息，它將以單一批次傳輸訊息。 用戶端批次處理也會導致佇列或訂用帳戶用戶端將多個**完成**要求整批放入單一要求中處理。 批次處理僅適用於非同步**傳送**及**完成**作業。 同步作業會立即傳送至服務匯流排服務。 查看或接收作業不會進行批次處理，也不會跨用戶端進行。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

.NET Standard SDK 的批次處理功能尚未公開要操作的屬性。

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

根據預設，用戶端使用的批次間隔為 20 毫秒。 您可以藉由在建立傳訊處理站之前設定 [BatchFlushInterval][BatchFlushInterval] 屬性來變更批次間隔。 這個設定會影響此處理站所建立的所有用戶端。

若要停用批次處理，請將 [BatchFlushInterval][BatchFlushInterval] 屬性設為 **TimeSpan.Zero**。 例如：

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

批次處理並不會影響可計費的傳訊作業數目，而且僅適用於使用 [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 程式庫的服務匯流排用戶端通訊協定。 HTTP 通訊協定不支援批次處理。

> [!NOTE]
> 設定 `BatchFlushInterval` 可確保批次處理從應用程式的觀點來看。 亦即：應用程式會進行 `SendAsync` 和 `CompleteAsync` 呼叫，而且不會進行特定的批次呼叫。
>
> 明確的用戶端批次處理可以藉由使用下列方法呼叫來執行：
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> 這裡的訊息大小總和必須小於定價層支援的大小上限。

---

## <a name="batching-store-access"></a>批次處理存放區存取

為了提高佇列、主題或訂用帳戶的輸送量，服務匯流排會在寫入至其內部存放區時批次處理多個訊息。 如果在佇列或主題上啟用，將會批次處理訊息寫入至存放區。 如果在佇列或訂用帳戶上啟用，將會批次處理從存放區刪除訊息。 如果某個實體啟用批次處理的存放區存取，服務匯流排會延遲與該實體有關的存放區寫入作業，最多 20 毫秒。

> [!NOTE]
> 批次處理時沒有遺失訊息的風險，即使在 20ms 批次處理間隔結尾有服務匯流排失敗。

在此間隔期間進行的其他存放區作業都會加入至批次。 批次存放區存取只會影響**傳送**和**完成**作業；接收作業不受影響。 批次處理的存放區存取是實體上的屬性。 批次處理會在啟用批次處理存放區存取的所有實體進行。

建立新佇列、主題或訂用帳戶時，預設會啟用批次處理的存放區存取。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

若要停用批次處理的存放區存取，您將需要一個的實例 `ManagementClient` 。 從將屬性設定為的佇列描述建立佇列 `EnableBatchedOperations` `false` 。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

如需詳細資訊，請參閱下列：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

若要停用批次處理的存放區存取，您將需要一個的實例 `NamespaceManager` 。 從將屬性設定為的佇列描述建立佇列 `EnableBatchedOperations` `false` 。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

如需詳細資訊，請參閱下列：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

批次處理的存放區存取並不會影響可計費的傳訊作業數目，並且是佇列、主題或訂用帳戶的屬性。 它獨立於接收模式及用於用戶端和服務匯流排服務之間的通訊協定之外。

## <a name="prefetching"></a>預先擷取

預先[提取](service-bus-prefetch.md)可讓佇列或訂用帳戶用戶端在執行接收作業時，從服務載入額外的訊息。 用戶端會將這些訊息儲存在本機快取中。 快取的大小取決於 `QueueClient.PrefetchCount` 或 `SubscriptionClient.PrefetchCount` 屬性。 啟用預先擷取的每個用戶端會維護自己的快取。 快取不會跨用戶端共用。 如果用戶端起始接收作業且其快取是空的，則服務會傳輸一批次的訊息。 批次的大小等於快取的大小或 256 KB，以較小者為準。 如果用戶端起始接收作業且快取包含一則訊息，訊息會從快取中擷取。

預先擷取訊息時，服務會鎖定預先擷取的訊息。 藉由鎖定，其他接收者就無法接收預先擷取的訊息。 如果接收者無法在鎖定到期之前完成訊息，訊息就會變成可供其他接收者接收。 訊息的預先擷取副本會保留在快取中。 當取用過其快取複本的接收者嘗試完成該訊息時，他會收到例外狀況。 根據預設，訊息鎖定會在 60 秒之後到期。 此值可延長為 5 分鐘。 若要避免過期訊息遭到取用，快取大小應該一律小於用戶端在鎖定逾時間隔內可取用的訊息數目。

使用 60 秒的預設鎖定到期時，`PrefetchCount` 的理想值是中心所有接收者處理速率上限的 20 倍。 例如，處理站建立三個接收者，而每個接收者每秒可以處理最多 10 則訊息。 預先擷取計數不應該超過 20 X 3 X 10 = 600。 根據預設， `PrefetchCount` 會設定為0，這表示不會從服務提取任何額外的訊息。

預先擷取訊息會增加佇列或訂用帳戶的整體輸送量，因為此舉可減少訊息作業的整體數目或來回次數。 然而，擷取第一個訊息需要較長的時間 (因為訊息大小增加)。 接收預先擷取的訊息比較快速，因為用戶端已經下載這些訊息。

伺服器會在它將訊息傳送至用戶端時，檢查訊息的存留時間 (TTL) 屬性。 當收到訊息時，用戶端不會檢查訊息的 TTL 屬性。 取而代之的是，即使在用戶端快取訊息時，訊息的 TTL 已經通過，也可以接收訊息。

預先擷取並不會影響可計費的傳訊作業數目，而且僅適用於服務匯流排用戶端通訊協定。 HTTP 通訊協定不支援預先擷取。 同步和非同步接收作業皆可使用預先擷取。

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft Azure 的匯流排 SDK](#tab/net-standard-sdk)

如需詳細資訊，請參閱下列 `PrefetchCount` 屬性：

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[Windowsazure.storage. 匯流排 SDK](#tab/net-framework-sdk)

如需詳細資訊，請參閱下列 `PrefetchCount` 屬性：

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>預先提取和 ReceiveBatch

> [!NOTE]
> 本節僅適用于 Windowsazure.storage，因為 Microsoft 不會公開批次函式。

雖然預先提取多個訊息的概念有類似的語義可以處理批次中的訊息（），但同時使用 `ReceiveBatch` 它們時，還有一些小差異必須牢記在心。

預先提取是用戶端（和）上的設定（或模式） `QueueClient` `SubscriptionClient` ，而且 `ReceiveBatch` 是一種作業（具有要求-回應的語義）。

同時使用這些案例時，請考慮下列情況：

* 預先提取應大於或等於您預期接收的訊息數目 `ReceiveBatch` 。
* 預先提取最多可以是每秒處理的訊息數目的 n/3 倍，其中 n 是預設的鎖定持續時間。

有一些因具有貪婪方法（也就是讓預先提取計數非常高）的挑戰，因為這表示訊息已鎖定至特定的接收者。 建議您在上面所述的閾值之間嘗試預先取值，並廣為人知且實證識別適合的值。

## <a name="multiple-queues"></a>多個佇列

如果預期的負載無法由單一佇列或主題處理，您必須使用多個訊息實體。 使用多個實體時，請針對每個實體建立專屬用戶端，而不是讓所有實體使用相同的用戶端。

## <a name="development-and-testing-features"></a>開發與測試功能

> [!NOTE]
> 這一節僅適用于 Windowsazure.storage，因為 Microsoft 不會公開此功能。

服務匯流排有一個專門用於開發的功能，但**絕不能用在生產環境設定中**： [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 。

將新的規則或篩選新增至主題時，您可以使用 [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 來驗證新的篩選條件運算式是否如預期般運作。

## <a name="scenarios"></a>案例

下列各節描述典型傳訊的案例，並簡述慣用的服務匯流排設定。 輸送量速率會分類為小型 (小於 1 則訊息/秒)、中型 (1 則訊息/秒或更多但小於 100 則訊息/秒) 和高型 (100 則訊息/秒或更多)。 用戶端數目可分類為小型 (5 個或更少)、中型 (5 個以上但小於或等於 20 個) 和大型 (超過 20 個)。

### <a name="high-throughput-queue"></a>高輸送量佇列

目標：最大化單一佇列的輸送量。 傳送者和接收者的數目很少。

* 若要增加傳送到佇列的整體速率，請使用多個訊息處理站來建立傳送者。 對每個傳送者使用非同步作業或多個執行緒。
* 若要增加從佇列接收的整體速率，請使用多個訊息處理站來建立接收者。
* 使用非同步作業來利用用戶端批次處理。
* 將批次處理間隔設為 50 毫秒，以減少服務匯流排用戶端通訊協定傳輸數目。 如果使用多個傳送者，批次處理間隔會增加到 100 毫秒。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至佇列的整體速率。
* 將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

### <a name="multiple-high-throughput-queues"></a>多個高輸送量佇列

目標：最大化多個佇列的整體輸送量。 個別佇列的輸送量為中或高。

若要取得跨多個佇列的最大輸送量，請使用簡述的設定以最大化單一佇列的輸送量。 此外，使用不同的處理站來建立傳送至或接收自不同佇列的用戶端。

### <a name="low-latency-queue"></a>低延遲性佇列

目標：最小化佇列或主題的端對端延遲。 傳送者和接收者的數目很少。 佇列的輸送量為小或中。

* 停用用戶端批次處理。 用戶端會立即傳送訊息。
* 停用批次處理的存放區存取。 服務會立即將訊息寫入至存放區中。
* 如果使用單一用戶端，請將預先擷取計數設為 20 乘以接收者的處理速率。 如果多個訊息同時抵達佇列，服務匯流排用戶端通訊協定會同時將它們全部傳輸。 當用戶端收到下一個訊息時，該訊息已經在本機快取中。 快取應該很小。
* 如果使用多個用戶端，請將預先擷取計數設為 0。 藉由設定該計數，第二個用戶端可以在第一個用戶端仍在處理第一個訊息時接收第二個訊息。

### <a name="queue-with-a-large-number-of-senders"></a>具有大量傳送者的佇列

目標：最大化具有大量傳送者之佇列或主題的輸送量。 每個傳送者都會以中等速率傳送訊息。 接收者的數目很少。

服務匯流排最多可為訊息實體提供1000的並行連接。 在命名空間層級強制執行這項限制，且佇列/主題/訂用帳戶的上限為每個命名空間的並行連線限制。 對佇列而言，這個數目是在傳送者和接收者之間共用的。 如果 1000 個連線全都為傳送者所需，請以主題和單一訂用帳戶取代此佇列。 主題會接受多達 1000 個來自傳送者的並行連線，而訂用帳戶可接受來自接收者的額外 1000 個並行連線。 如果需要超過 1000 個並行傳送者，傳送者必須透過 HTTP 將訊息傳送至服務匯流排通訊協定。

若要最大化輸送量，請執行下列步驟：

* 如果每個傳送者都位於不同的處理序中，每個處理序僅使用單一處理站。
* 使用非同步作業來利用用戶端批次處理。
* 使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至佇列或主題的整體速率。
* 將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

### <a name="queue-with-a-large-number-of-receivers"></a>具有大量接收者的佇列

目標：最大化具有大量接收者之佇列或訂用帳戶的接收速率。 每個接收者皆以中等速率接收訊息。 傳送者的數目很少。

服務匯流排可啟用多達 1000 個並行連線至實體。 如果佇列需要超過 1000 個接收者，請以主題和多個訂用帳戶取代此佇列。 每個訂用帳戶可支援最多 1000 個並行連線。 此外，接收者可以透過 HTTP 通訊協定存取佇列。

若要最大化輸送量，請執行下列作業：

* 如果每個接收者都位於不同的處理序中，每個處理序僅使用單一處理站。
* 接收者可以使用同步或非同步作業。 若指定個別接收者的中等接收速率，完全要求的用戶端批次處理不會影響接收者輸送量。
* 讓批次處理的存放區存取保持啟用。 此存取會減少實體的整體負載。 它也會減少訊息寫入至佇列或主題的整體速率。
* 將預先擷取計數設為較小的值 (例如，PrefetchCount = 10)。 此計數可以避免接收者在其他接收者具有大量快取的訊息時閒置。

### <a name="topic-with-a-small-number-of-subscriptions"></a>具有少量訂用帳戶的主題

目標：最大化具有少量訂用帳戶之主題的輸送量。 許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率高於傳送速率。 傳送者的數目很少。 每個訂用帳戶的接收者數目很少。

若要最大化輸送量，請執行下列作業：

* 若要增加傳送到主題的整體速率，請使用多個訊息處理站來建立傳送者。 對每個傳送者使用非同步作業或多個執行緒。
* 若要增加從訂用帳戶接收的整體速率，請使用多個訊息處理站來建立接收者。 對每個接收者使用非同步作業或多個執行緒。
* 使用非同步作業來利用用戶端批次處理。
* 使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至主題的整體速率。
* 將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

### <a name="topic-with-a-large-number-of-subscriptions"></a>具有大量訂用帳戶的主題

目標：最大化具有大量訂用帳戶之主題的輸送量。 許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率遠高於傳送速率。 傳送者的數目很少。 每個訂用帳戶的接收者數目很少。

如果所有訊息都路由傳送至所有訂用帳戶，具有大量訂用帳戶的主題通常會顯露較低的整體輸送量。 這樣較低的輸送量是因為每個訊息都被接收了許多次，而且包含在主題與其所有訂用帳戶中的所有訊息都儲存在相同的存放區中。 它會假設每個訂用帳戶的傳送者數目和接收者數目都很少。 服務匯流排支援每個主題最多 2,000 個訂用帳戶。

若要最大化輸送量，請嘗試下列步驟：

* 使用非同步作業來利用用戶端批次處理。
* 使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至主題的整體速率。
* 將預先擷取設為 20 乘以預期的接收速率 (以秒為單位)。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md