---
title: Azure 服務匯流排訊息-佇列、主題和訂用帳戶
description: 本文提供 Azure 服務匯流排訊息實體（佇列、主題和訂閱）的總覽。
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: deeebf56d6e2f4ccfac37c70170a0d1cb4d272a9
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119168"
---
# <a name="service-bus-queues-topics-and-subscriptions"></a>服務匯流排佇列、主題和訂用帳戶

Microsoft Azure 服務匯流排支援一組以雲端為基礎、訊息導向的中介軟體技術，包括可靠的訊息佇列和持久的發佈/訂閱訊息。 這些「代理」訊息功能可以視為低耦合訊息功能，使用服務匯流排訊息工作負載來支援發佈-訂閱、時態性分離和負載平衡案例。 低耦合通訊有許多優點︰例如，用戶端和伺服器可視需要連接並以非同步方式執行其作業。

構成「服務匯流排」中傳訊功能核心的傳訊實體是佇列、主題與訂用帳戶，以及規則/動作。

## <a name="queues"></a>佇列

如果有一或多個競爭取用者，佇列會採取「先進先出」**(FIFO) 訊息傳遞機制。 亦即，接收者通常依訊息加入佇列的順序來接收和處理訊息，而且每則訊息只能由一個訊息取用者接收和處理。 使用佇列的主要優點是達成應用程式元件的「時脈解離」。 換句話說，產生者 (傳送者) 和取用者 (接收者) 不必同時傳送和接收訊息，因為訊息會長期儲存在佇列中。 此外，產生者不必等待取用者的回覆，即可繼續處理及傳送訊息。

相關的優點是「負載調節」，這可讓產生者和取用者以不同的速率傳送和接收訊息。 在許多應用程式中，系統負載會隨時間改變；然而，處理每個工作單位所需的時間卻通常固定不變。 有佇列作為訊息生產者與取用者之間的中間者後，只需佈建取用方應用程式，就能夠處理平均負載而非尖峰負載。 佇列的深度會隨著連入負載的改變而增加和縮短。 就處理應用程式負載所需的基礎結構數量而言，這個功能可直接節省金錢。 當負載增加時，可以新增更多的背景工作角色程序來讀取佇列中的訊息。 每個訊息僅由其中一個背景工作程序處理。 此外，這個提取型負載平衡機制可讓背景工作電腦獲得最佳利用，即使背景工作電腦的處理能力有所不同也一樣，因為背景工作電腦會以自己的最大速率提取訊息。 此模式通常稱為「競爭取用者」模式。

使用佇列在訊息產生者與取用者之間居中協調，可提供元件之間的固有鬆散結合。 因為產生者和取用者不知道彼此的存在，所以取用者可以升級，而不會對產生者造成任何影響。

### <a name="create-queues"></a>建立佇列

您可以使用 [Azure 入口網站](service-bus-quickstart-portal.md)、[PowerShell](service-bus-quickstart-powershell.md)、[CLI](service-bus-quickstart-cli.md) 或 [Resource Manager 範本](service-bus-resource-manager-namespace-queue.md)來建立佇列。 然後使用 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 物件來傳送和接收訊息。

若要快速了解如何建立佇列，然後傳送和接收佇列的訊息，請參閱每個方法的[快速入門](service-bus-quickstart-portal.md)。 如需更深入的佇列使用方式教學課程，請參閱[開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)。

如需實用的範例，請參閱 GitHub 上的 [BasicSendReceiveUsingQueueClient 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingQueueClient)。

### <a name="receive-modes"></a>接收模式

您可以指定兩種不同的服務匯流排訊息接收模式：*ReceiveAndDelete* 或 *PeekLock*。 在[ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode)模式中，接收作業是一次性的;也就是說，當服務匯流排從取用者收到要求時，它會將訊息標示為已取用，並將它傳回到取用者應用程式。 **ReceiveAndDelete** 模式是最簡單的模型，且最適合可容許在發生失敗時不處理訊息的應用程式案例。 若要了解此案例，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 [PeekLock](/dotnet/api/microsoft.azure.servicebus.receivemode) 模式中，接收作業會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一個訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 在應用程式完成處理訊息 (或可靠地儲存此訊息以供未來處理) 之後，它可透過呼叫已接收訊息上的 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) 來完成接收程序的第二個階段。 當服務匯流排看到 **CompleteAsync** 呼叫時，它會將訊息標示為已取用。

如果應用程式因為某些原因無法處理訊息，它可以呼叫已接收訊息上的 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 方法 (而不是 [CompleteAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync))。 這個方法可讓服務匯流排將訊息解除鎖定，讓此訊息可被相同的取用者或其他競爭取用取再次接收。 其次，鎖定有相關聯的逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會將訊息解除鎖定，並讓訊息可以被重新接收 (根據預設，基本上會執行 [AbandonAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.abandonasync) 作業)。

如果應用程式在處理訊息之後，尚未發出 **CompleteAsync** 要求之前當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這個過程通常稱為「至少一次」** 處理；也就是說，會至少將每個訊息都處理一次。 不過，但在特定狀況下，可能會重新傳遞相同訊息。 如果此案例不容許重複處理，則應用程式中需要額外的邏輯才能根據訊息的 [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) 屬性偵測可達成的重複項目，而該屬性在各傳遞嘗試之間維持不變。 這個功能稱為「剛好一次」** 處理。

## <a name="topics-and-subscriptions"></a>主題和訂用帳戶

有別於佇列，佇列中的每個訊息只會由單一取用者處理，「主題」** 和「訂用帳戶」** 採用「發佈/訂閱」** 模式，提供一對多的通訊形式。 適合用於相應增加為非常大量的收件者，每個發佈的訊息都會提供給每個已註冊主題的訂用帳戶。 根據可依每個訂用帳戶設定的篩選規則，訊息會傳送至主題並傳遞給一或多個相關聯的訂用帳戶。 訂用帳戶可以使用其他篩選器來限制他們想要接收的訊息。 訊息會以其傳送至佇列的相同方式傳送至主題，但不會從主題直接接收訊息。 反而會從訂用帳戶接收。 主題訂用帳戶類似於虛擬佇列，同樣可接收已傳送到主題的訊息複本。 從佇列接收訊息的方式就像從訂用帳戶接收訊息一樣。

藉由比較，佇列的訊息傳送功能會直接對應至主題，而其訊息接收功能會對應至訂用帳戶。 除此之外，這個功能表示訂用帳戶支援本節前面所述有關佇列的相同模式︰競爭取用者、暫時分離、負載調節和負載平衡。

### <a name="create-topics-and-subscriptions"></a>建立主題和訂用帳戶

按照上一節所述，建立主題類似於建立佇列。 然後使用 [TopicClient](/dotnet/api/microsoft.azure.servicebus.topicclient) 類別來傳送訊息。 若要接收訊息，您可以建立主題的一或多個訂用帳戶。 與佇列類似，從訂用帳戶接收訊息是使用 [SubscriptionClient](/dotnet/api/microsoft.azure.servicebus.subscriptionclient) 物件，而非 [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) 物件。 建立訂用帳戶用戶端，並將主題名稱、訂用帳戶名稱及 (選擇性) 接收模式當作參數傳遞。

如需完整的實用範例，請參閱 GitHub 上的[BasicSendReceiveUsingTopicSubscriptionClient 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/BasicSendReceiveUsingTopicSubscriptionClient)。

### <a name="rules-and-actions"></a>執行和動作

在許多情況下，必須以不同的方式處理具有特定特性的訊息。 若要啟用這項處理，您可以設定訂用帳戶以尋找具有所需屬性的訊息，然後對這些屬性進行一些修改。 雖然服務匯流排訂用帳戶可看見所有傳送至主題的訊息，但您只可以將部分的訊息複製到虛擬訂用帳戶佇列。 使用訂用帳戶篩選器即可完成這個篩選。 這類修改稱之為「篩選器動作」**。 建立訂用帳戶時，您可以提供在訊息屬性上運作的篩選運算式，包括系統屬性（例如**標籤**）和自訂應用程式屬性（例如**StoreName**）。在此情況下，SQL 篩選運算式是選擇性的：如果沒有 SQL 篩選條件運算式，就會在訂用帳戶的所有訊息上執行任何定義于訂閱上的篩選器動作。

如需完整的實用範例，請參閱 GitHub 上的 [TopicSubscriptionWithRuleOperationsSample 範例](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/Microsoft.Azure.ServiceBus/TopicSubscriptionWithRuleOperationsSample)。

如需可能篩選值的詳細資訊，請參閱 [SqlFilter](/dotnet/api/microsoft.azure.servicebus.sqlfilter) 和 [SqlRuleAction](/dotnet/api/microsoft.azure.servicebus.sqlruleaction) 類別的文件。

## <a name="java-message-service-jms-20-entities-preview"></a>JAVA message service （JMS）2.0 實體（預覽）

連接到 Azure 服務匯流排 Premium 並利用[AZURE 服務匯流排 JMS 程式庫](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)的用戶端應用程式，可以利用以下的實體。

### <a name="queues"></a>佇列

JMS 中的佇列在語義上相當於先前所討論的傳統服務匯流排佇列。

若要建立佇列，請利用類別中的下列方法 `JMSContext` -

```java
Queue createQueue(String queueName)
```

### <a name="topics"></a>主題

JMS 中的主題在語義上與先前討論的傳統服務匯流排主題相當類似。

若要建立主題，請利用類別中的下列方法 `JMSContext` -

```java
Topic createTopic(String topicName)
```

### <a name="temporary-queues"></a>暫存佇列

當用戶端應用程式需要存在於應用程式存留期的暫存實體時，它可以使用暫存佇列。 這些是在[要求-回復](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html)模式中使用。

若要建立暫存佇列，請利用類別中的下列方法 `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

### <a name="temporary-topics"></a>暫存主題

就像暫存佇列一樣，有暫時的主題可透過存在於應用程式存留期的暫存實體來啟用發佈/訂閱。

若要建立暫存主題，請利用類別中的下列方法 `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

### <a name="java-message-service-jms-subscriptions"></a>JAVA 訊息服務（JMS）訂閱

雖然這些程式在語義上類似于上述的訂閱（也就是存在於主題上並啟用發行/訂閱語義），但 JAVA 訊息服務規格會針對指定的訂用帳戶，引進**共用**、未**共用**、**持久**和**非持久性**屬性的概念。

> [!NOTE]
> 下列訂用帳戶適用于預覽的 Azure 服務匯流排進階層，適用于使用[AZURE 服務匯流排 JMS 程式庫](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)連接到 Azure 服務匯流排的用戶端應用程式。
>
> 在公開預覽版本中，無法使用 Azure 入口網站建立這些訂用帳戶。
>

#### <a name="shared-durable-subscriptions"></a>共用的持久訂閱

當主題上發佈的所有訊息都是由應用程式接收和處理時，不論應用程式是否隨時從訂閱主動取用，都可以使用共用長期訂閱。

由於這是共用的訂用帳戶，已驗證要從服務匯流排接收的任何應用程式都可以從訂用帳戶接收。

若要建立共用的長期訂用帳戶，請在類別上使用下列方法 `JMSContext` ：

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

除非使用類別上的方法刪除，否則共用長期訂閱會繼續存在 `unsubscribe` `JMSContext` 。

```java
void unsubscribe(String name)
```

#### <a name="unshared-durable-subscriptions"></a>未共用的持久訂閱

就像共用長期訂用帳戶一樣，當所有發行于主題的訊息都是由應用程式接收和處理時，會使用非共用的長期訂閱，而不論應用程式是否隨時從訂用帳戶主動取用。

不過，由於這是不共用的訂用帳戶，因此只有建立訂閱的應用程式可以從它接收。

若要建立非共用的長期訂閱，請從類別使用下列方法 `JMSContext` - 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> 此 `noLocal` 功能目前不受支援且已忽略。
>

除非使用類別上的方法刪除，否則不共用的長期訂閱會繼續存在 `unsubscribe` `JMSContext` 。

```java
void unsubscribe(String name)
```

#### <a name="shared-non-durable-subscriptions"></a>共用的非持久訂閱

當多個用戶端應用程式需要接收和處理來自單一訂用帳戶的訊息時，才會使用共用的非持久性訂用帳戶，但必須等到它們主動取用/接收它為止。

由於訂用帳戶不是持久的，因此不會保存。 當此訂用帳戶沒有作用中的取用者時，不會收到訊息。

若要建立共用的非持久性訂用帳戶，請 `JmsConsumer` 從類別建立如下列方法所示的 `JMSContext` ：

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共用的非長期訂用帳戶會持續存在，直到有作用中的取用者從它接收為止。

#### <a name="unshared-non-durable-subscriptions"></a>不共用的非持久性訂閱

當用戶端應用程式需要接收和處理來自訂用帳戶的訊息時，才會使用不共用的非持久性訂閱，只有在它主動取用它的情況下。 此訂用帳戶上只能存在一個取用者，也就是建立訂用帳戶的用戶端。

由於訂用帳戶不是持久的，因此不會保存。 當此訂用帳戶沒有作用中的取用者時，不會收到訊息。

若要建立不共用的非持久性訂用帳戶，請 `JMSConsumer` 從 ' JMSCoNtext 類別建立如下列方法所示的： 

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> 此 `noLocal` 功能目前不受支援且已忽略。
>

未共用的非持久性訂用帳戶會持續存在，直到有作用中的取用者從它接收為止。

#### <a name="message-selectors"></a>訊息選取器

就像一般服務匯流排訂用帳戶的**篩選和動作**一樣，JMS 訂閱的**訊息選取器**也存在。

您可以在每個 JMS 訂閱上設定訊息選取器，並在訊息標頭屬性上以篩選準則的形式存在。 只會傳遞標頭屬性符合訊息選取器運算式的訊息。 Null 或空字串的值表示 JMS 訂閱/取用者沒有訊息選取器。

## <a name="next-steps"></a>後續步驟

如需使用服務匯流排傳訊的詳細資訊和範例，請參閱下列進階主題：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [快速入門：使用 Azure 入口網站和 .NET 傳送和接收訊息](service-bus-quickstart-portal.md)
* [教學課程：使用 Azure 入口網站和主題/訂用帳戶來更新庫存](service-bus-tutorial-topics-subscriptions-portal.md)


