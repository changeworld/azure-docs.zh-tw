---
title: 使用 Azure 服務匯流排改善效能的最佳作法
description: 描述如何使用服務匯流排來在交換代理訊息時將效能最佳化。
ms.topic: article
ms.date: 11/11/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 6a0457537712ccb85191f320fd348446eed9b229
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97655623"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>使用服務匯流排傳訊的效能改進最佳作法

本文描述如何使用 Azure 服務匯流排來在交換代理的訊息時將效能最佳化。 本文的第一個部分說明可提高效能的不同機制。 第二個部分提供使用服務匯流排的指導方針，可在特定案例中提供最佳效能。

在本文中，「用戶端」一詞是指任何存取服務匯流排的實體。 用戶端可以擔任傳送者或接收者的角色。 「傳送者」一詞用於服務匯流排佇列用戶端或主題用戶端，可將訊息傳送至服務匯流排佇列或主題。 「接收者」一詞是指從服務匯流排佇列或訂用帳戶接收訊息的服務匯流排佇列用戶端或訂用帳戶用戶端。

## <a name="protocols"></a>通訊協定
服務匯流排可讓用戶端透過三種通訊協定的其中一種傳送和接收訊息：

1. 進階訊息佇列通訊協定 (AMQP)
2. 服務匯流排傳訊通訊協定 (SBMP)
3. 超文字傳輸通訊協定 (HTTP)

AMQP 是最有效率的，因為它會維護服務匯流排的連接。 它也會實作批次處理和預先擷取作業。 除非明確提到，否則本文中的所有內容都假設為使用 AMQP 和 SBMP。

> [!IMPORTANT]
> SBMP 僅適用于 .NET Framework。 AMQP 是 .NET Standard 的預設值。

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>選擇適當的服務匯流排 .NET SDK
有兩個支援的 .NET Sdk Azure 服務匯流排。 它們的 Api 很類似，而且可能會讓您覺得要選擇哪一種。 請參閱下表以協助引導您進行決策。 我們建議使用的是，因為它的現代化、效能更高，且可跨平臺相容。 此外，它支援透過 Websocket 的 AMQP，而且是開放原始碼專案的 Azure .NET SDK 集合的一部分。

| NuGet 封裝 | 主要命名空間 (s)  | 最低平臺 (s)  | 通訊協定 |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Azure。 <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5.4<br>Xamarin.iOS 10.14<br>Xamarin.Mac 3.8<br>Xamarin.Android 8.0<br>通用 Windows 平台 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure <span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

如需最小 .NET Standard 平臺支援的詳細資訊，請參閱 [.net 執行支援](/dotnet/standard/net-standard#net-implementation-support)。

## <a name="reusing-factories-and-clients"></a>重複使用處理站和用戶端

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

服務匯流排用戶端物件（例如或的 [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender] 執行）應該註冊為 singleton (或具現化一次並共用) 的相依性插入。 當您傳送訊息之後，建議您不要關閉訊息處理站、佇列、主題或訂用帳戶用戶端，然後在傳送下一個訊息時重新建立訊息處理站、佇列、主題或訂用帳戶用戶端。 關閉訊息 factory 會刪除服務匯流排服務的連接。 重新建立 factory 時，會建立新的連接。 建立連接是成本高昂的作業，您可以藉由重新使用多項作業的相同處理站和用戶端物件來避免此作業。 您可以安全地使用這些用戶端物件，從多個執行緒進行並行的非同步作業。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

服務匯流排用戶端物件（例如 `QueueClient` 或 `MessageSender` ）是透過 [MessagingFactory][MessagingFactory] 物件建立的，它也提供連線的內部管理。 當您傳送訊息之後，建議您不要關閉訊息處理站、佇列、主題或訂用帳戶用戶端，然後在傳送下一個訊息時重新建立訊息處理站、佇列、主題或訂用帳戶用戶端。 關閉傳訊處理站會刪除服務匯流排服務的連接，並在重新建立處理站時建立新的連接。 建立連接是成本高昂的作業，您可以藉由重新使用多項作業的相同處理站和用戶端物件來避免此作業。 您可以安全地使用這些用戶端物件，從多個執行緒進行並行的非同步作業。

---

## <a name="concurrent-operations"></a>並行作業
傳送、接收、刪除等作業需要一些時間。 這段時間包括服務匯流排服務處理作業所花費的時間，以及要求和回應的延遲時間。 若要增加每次的作業數目，就必須並行執行作業。

用戶端會透過執行 **非同步** 操作來排定並行作業。 下一個要求會在前一個要求完成之前啟動。 非同步傳送作業的程式碼片段範例如下：

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

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

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

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

`MessageReceiver`物件會使用連接字串、佇列名稱和查看查看接收模式來具現化。 接著， `receiver` 會使用此實例來註冊訊息處理常式。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

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

會 `MessagingFactory` `factory` 從連接字串建立物件。 使用 `factory` 實例 `MessageReceiver` 時，會具現化。 接著， `receiver` 會使用此實例來註冊訊息處理常式。

---

## <a name="receive-mode"></a>接收模式

建立佇列或訂用帳戶用戶端時，您可以指定接收模式：「查看鎖定」或「接收與刪除」。 預設接收模式是`PeekLock`。 以預設模式操作時，用戶端會傳送要求以接收來自服務匯流排的訊息。 用戶端收到訊息後，它會傳送要求以完成訊息。

若要將接收模式設定為`ReceiveAndDelete`，這兩個步驟會結合為單一要求。 這些步驟可減少整體的作業數目並可改善整體訊息輸送量。 此效能改善的風險為遺失訊息。

服務匯流排不支援接收和刪除作業的交易。 此外，如果用戶端想要延遲或寄不出的 [信件](service-bus-dead-letter-queues.md) 訊息，則需要查看鎖定語義。

## <a name="client-side-batching"></a>用戶端批次處理

用戶端批次處理可讓佇列或主題用戶端將訊息的傳送延遲一段時間。 如果用戶端在此期間傳送其他訊息，它將以單一批次傳輸訊息。 用戶端批次處理也會導致佇列或訂用帳戶用戶端將多個 **完成** 要求整批放入單一要求中處理。 批次處理僅適用於非同步 **傳送** 及 **完成** 作業。 同步作業會立即傳送至服務匯流排服務。 查看或接收作業不會進行批次處理，也不會在用戶端上進行批次處理。

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

.NET Standard SDK 的批次處理功能尚未公開要操作的屬性。

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

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

批次處理不會影響可計費的訊息作業數目，而且僅適用于使用 [Microsoft 的訊息](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) 程式庫的服務匯流排用戶端通訊協定。 HTTP 通訊協定不支援批次處理。

> [!NOTE]
> 設定 `BatchFlushInterval` 可確保批次處理是從應用程式的觀點來隱含的。 亦即：應用程式會進行 `SendAsync` 和 `CompleteAsync` 呼叫，且不會進行特定的批次呼叫。
>
> 明確的用戶端批次處理可以藉由使用下列方法呼叫來執行：
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> 在這裡，訊息的合併大小必須小於定價層所支援的大小上限。

---

## <a name="batching-store-access"></a>批次處理存放區存取

為了提高佇列、主題或訂用帳戶的輸送量，服務匯流排會在寫入至其內部存放區時批次處理多個訊息。 

- 當您對佇列啟用批次處理時，會將訊息寫入存放區，並從存放區中刪除訊息。 
- 當您在主題上啟用批次處理時，會批次處理將訊息寫入存放區。 
- 當您在訂用帳戶上啟用批次處理時，會批次處理從存放區刪除訊息。 
- 啟用實體的批次存放區存取時，服務匯流排會延遲該實體的存放區寫入作業，最多可達20毫秒。

> [!NOTE]
> 批次處理時沒有遺失訊息的風險，即使在 20ms 批次處理間隔結尾有服務匯流排失敗。

在此間隔期間進行的其他存放區作業都會加入至批次。 批次存放區存取只會影響 **傳送** 和 **完成** 作業;接收作業不受影響。 批次處理的存放區存取是實體上的屬性。 批次處理會在啟用批次處理存放區存取的所有實體進行。

建立新佇列、主題或訂用帳戶時，預設會啟用批次處理的存放區存取。

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

若要停用批次存放區存取，您將需要的實例 `ManagementClient` 。 從將屬性設為的佇列描述建立佇列 `EnableBatchedOperations` `false` 。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

如需詳細資訊，請參閱下列文章：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

若要停用批次存放區存取，您將需要的實例 `NamespaceManager` 。 從將屬性設為的佇列描述建立佇列 `EnableBatchedOperations` `false` 。

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

如需詳細資訊，請參閱下列文章：
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

批次存放區存取不會影響可計費的訊息作業數目。 它是佇列、主題或訂用帳戶的屬性。 它與在用戶端與服務匯流排服務之間使用的接收模式和通訊協定無關。

## <a name="prefetching"></a>預先擷取

預先[提取](service-bus-prefetch.md)可讓佇列或訂用帳戶用戶端在接收訊息時，從服務載入額外的訊息。 用戶端會將這些訊息儲存在本機快取中。 快取的大小取決於 `QueueClient.PrefetchCount` 或 `SubscriptionClient.PrefetchCount` 屬性。 啟用預先擷取的每個用戶端會維護自己的快取。 快取不會跨用戶端共用。 如果用戶端啟動接收作業，而且其快取是空的，則服務會傳輸一批訊息。 批次的大小等於快取的大小或 256 KB，以較小者為準。 如果用戶端啟動接收作業且快取包含訊息，則會從快取中取得訊息。

預先擷取訊息時，服務會鎖定預先擷取的訊息。 使用鎖定時，其他接收者就無法接收預先提取的訊息。 如果接收者無法在鎖定到期之前完成訊息，則訊息會變成可供其他接收者使用。 訊息的預先擷取副本會保留在快取中。 當取用過其快取複本的接收者嘗試完成該訊息時，他會收到例外狀況。 根據預設，訊息鎖定會在 60 秒之後到期。 此值可延長為 5 分鐘。 若要避免使用過期的訊息，請將快取大小設定為小於用戶端可在鎖定逾時間隔內使用的訊息數目。

使用 60 秒的預設鎖定到期時，`PrefetchCount` 的理想值是中心所有接收者處理速率上限的 20 倍。 例如，處理站建立三個接收者，而每個接收者每秒可以處理最多 10 則訊息。 預先提取計數不應超過 20 X 3 X 10 = 600。 依預設， `PrefetchCount` 會設定為0，這表示不會從服務提取任何額外的訊息。

預先擷取訊息會增加佇列或訂用帳戶的整體輸送量，因為此舉可減少訊息作業的整體數目或來回次數。 不過，提取第一個訊息將需要較長的 (，因為) 增加的訊息大小。 從快取接收預先提取的訊息將會更快，因為這些訊息已由用戶端下載。

伺服器會在它將訊息傳送至用戶端時，檢查訊息的存留時間 (TTL) 屬性。 用戶端不會在收到訊息時檢查訊息的 TTL 屬性。 相反地，即使在用戶端快取訊息時，訊息的 TTL 已經過，也可以接收訊息。

預先提取不會影響可計費的訊息作業數目，而且僅適用于服務匯流排用戶端通訊協定。 HTTP 通訊協定不支援預先提取。 同步和非同步接收作業皆可使用預先擷取。

# <a name="microsoftazureservicebus-sdk"></a>[Node.js SDK](#tab/net-standard-sdk)

如需詳細資訊，請參閱下列 `PrefetchCount` 屬性：

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure。](#tab/net-framework-sdk)

如需詳細資訊，請參閱下列 `PrefetchCount` 屬性：

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>預先提取和 ReceiveBatch

> [!NOTE]
> 本節僅適用于 WindowsAzure，因為 Azure 中的會將批次函數公開。

雖然預先提取多個訊息的概念具有類似的語法來處理批次 () 中的訊息 `ReceiveBatch` ，但在搭配使用這些方法時，必須記住一些小差異。

預先提取是用戶端上的設定 (或模式)  (`QueueClient` 和 `SubscriptionClient`) ，而且 `ReceiveBatch` 是具有要求-回應語義 (的作業) 。

搭配使用這些方法時，請考慮下列情況：

* 預先提取應大於或等於預期接收的訊息數目 `ReceiveBatch` 。
* 預先提取最多可達每秒處理的訊息數目 n/3 倍，其中 n 是預設的鎖定持續時間。

有一些具有貪婪方法的挑戰，也就是讓預先提取計數保持高，因為這表示訊息已鎖定到特定接收者。 建議您在上面所述的閾值與廣為人知且實證識別適合的閾值之間試用預先提取值。

## <a name="multiple-queues"></a>多個佇列

如果單一佇列或主題無法處理預期的，請使用多個訊息實體。 使用多個實體時，請針對每個實體建立專屬用戶端，而不是讓所有實體使用相同的用戶端。

## <a name="development-and-testing-features"></a>開發與測試功能

> [!NOTE]
> 本節僅適用于 WindowsAzure，因為 Azure 中的會不公開此功能。

服務匯流排有一項專門用於開發的功能， **不應在生產環境設定中使用**： [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 。

當主題中加入新的規則或篩選器時，您可以使用 [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] 來確認新的篩選運算式是否如預期般運作。

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

若要取得跨多個佇列的最大輸送量，請使用簡述的設定以最大化單一佇列的輸送量。 此外，使用不同的處理站來建立從不同佇列傳送或接收的用戶端。

### <a name="low-latency-queue"></a>低延遲性佇列

目標：將佇列或主題的延遲降至最低。 傳送者和接收者的數目很少。 佇列的輸送量為小或中。

* 停用用戶端批次處理。 用戶端會立即傳送訊息。
* 停用批次處理的存放區存取。 服務會立即將訊息寫入至存放區中。
* 如果使用單一用戶端，請將預先擷取計數設為 20 乘以接收者的處理速率。 如果多個訊息同時抵達佇列，服務匯流排用戶端通訊協定會同時將它們全部傳輸。 當用戶端收到下一個訊息時，該訊息已經在本機快取中。 快取應該很小。
* 如果使用多個用戶端，請將預先擷取計數設為 0。 藉由設定該計數，第二個用戶端可以在第一個用戶端仍在處理第一個訊息時接收第二個訊息。

### <a name="queue-with-a-large-number-of-senders"></a>具有大量傳送者的佇列

目標：最大化具有大量傳送者之佇列或主題的輸送量。 每個傳送者都會以中等速率傳送訊息。 接收者的數目很少。

服務匯流排可讓訊息實體使用最多1000的並行連接。 這項限制會在命名空間層級強制執行，佇列、主題或訂用帳戶會限制為每個命名空間的並行連線限制。 對佇列而言，這個數目是在傳送者和接收者之間共用的。 如果 1000 個連線全都為傳送者所需，請以主題和單一訂用帳戶取代此佇列。 主題最多可接受來自寄件者的1000個並行連接。 訂用帳戶可接受來自接收者的額外1000個並行連接。 如果需要超過 1000 個並行傳送者，傳送者必須透過 HTTP 將訊息傳送至服務匯流排通訊協定。

若要將輸送量最大化，請遵循下列步驟：

* 如果每個傳送者都在不同的進程中，每個進程只能使用一個 factory。
* 使用非同步作業來利用用戶端批次處理。
* 使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至佇列或主題的整體速率。
* 將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

### <a name="queue-with-a-large-number-of-receivers"></a>具有大量接收者的佇列

目標：最大化具有大量接收者之佇列或訂用帳戶的接收速率。 每個接收者皆以中等速率接收訊息。 傳送者的數目很少。

服務匯流排可啟用多達 1000 個並行連線至實體。 如果佇列需要超過 1000 個接收者，請以主題和多個訂用帳戶取代此佇列。 每個訂用帳戶可支援最多 1000 個並行連線。 此外，接收者可以透過 HTTP 通訊協定存取佇列。

若要將輸送量最大化，請遵循下列指導方針：

* 如果每個接收者都在不同的進程中，每個進程只能使用一個 factory。
* 接收者可以使用同步或非同步作業。 針對個別接收器的中等接收率，完整要求的用戶端批次處理不會影響接收者輸送量。
* 讓批次處理的存放區存取保持啟用。 此存取會減少實體的整體負載。 它也會減少訊息寫入至佇列或主題的整體速率。
* 將預先擷取計數設為較小的值 (例如，PrefetchCount = 10)。 此計數可以避免接收者在其他接收者具有大量快取的訊息時閒置。

### <a name="topic-with-a-small-number-of-subscriptions"></a>具有少量訂用帳戶的主題

目標：最大化具有少量訂用帳戶之主題的輸送量。 許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率高於傳送速率。 傳送者的數目很少。 每個訂用帳戶的接收者數目很少。

若要將輸送量最大化，請遵循下列指導方針：

* 若要增加傳送到主題的整體速率，請使用多個訊息處理站來建立傳送者。 對每個傳送者使用非同步作業或多個執行緒。
* 若要增加從訂用帳戶接收的整體速率，請使用多個訊息處理站來建立接收者。 對每個接收者使用非同步作業或多個執行緒。
* 使用非同步作業來利用用戶端批次處理。
* 使用預設的批次處理間隔 20 毫秒來減少服務匯流排用戶端通訊協定傳輸數目。
* 讓批次處理的存放區存取保持啟用。 此存取會增加訊息寫入至主題的整體速率。
* 將預先擷取計數設為 20 乘以處理站所有接收者的最高處理速率。 此計數會減少服務匯流排用戶端通訊協定傳輸數目。

### <a name="topic-with-a-large-number-of-subscriptions"></a>具有大量訂用帳戶的主題

目標：最大化具有大量訂用帳戶之主題的輸送量。 許多訂用帳戶收到一則訊息，表示所有訂用帳戶的合併接收速率遠高於傳送速率。 傳送者的數目很少。 每個訂用帳戶的接收者數目很少。

如果所有訊息都路由傳送至所有訂用帳戶，具有大量訂用帳戶的主題通常會顯露較低的整體輸送量。 這是因為每個訊息都會收到多次，而主題中的所有訊息和其所有訂閱都會儲存在相同的存放區中。 此處的假設是每個訂用帳戶的寄件者數目和接收者數目很小。 服務匯流排支援每個主題最多 2,000 個訂用帳戶。

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
