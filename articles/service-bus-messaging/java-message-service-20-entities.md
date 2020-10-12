---
title: 'Azure 服務匯流排訊息-JAVA 訊息服務實體 (預覽) '
description: 本文概述可透過 JAVA 訊息服務 API 存取的 Azure 服務匯流排訊息實體。
ms.topic: article
ms.date: 07/20/2020
ms.openlocfilehash: 1a7fe3d6355146ccf0fce50266a6f3b8da5231b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87801454"
---
# <a name="java-message-service-jms-20-entities-preview"></a>JAVA 訊息服務 (JMS) 2.0 實體 (預覽) 

連接到 Azure 服務匯流排 Premium 並利用 [AZURE 服務匯流排 JMS 程式庫](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) 的用戶端應用程式，可以利用下列實體。

## <a name="queues"></a>佇列

JMS 中的佇列在語義上與傳統的 [服務匯流排佇列](service-bus-queues-topics-subscriptions.md#queues)相當類似。

若要建立佇列，請利用類別中的下列方法 `JMSContext` -

```java
Queue createQueue(String queueName)
```

## <a name="topics"></a>主題

JMS 中的主題在語義上與傳統的 [服務匯流排主題](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)相當類似。

若要建立主題，請利用類別中的下列方法 `JMSContext` -

```java
Topic createTopic(String topicName)
```

## <a name="temporary-queues"></a>暫存佇列

當用戶端應用程式需要在應用程式存留期存在的暫時實體時，可以使用暫存佇列。 這些會在 [要求-回復](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RequestReply.html) 模式中使用。

若要建立暫存佇列，請利用類別中的下列方法 `JMSContext` -

```java
TemporaryQueue createTemporaryQueue()
```

## <a name="temporary-topics"></a>暫存主題

就像暫存佇列一樣，有暫時的主題可讓您透過存在於應用程式存留期的暫時實體啟用發佈/訂閱。

若要建立暫存主題，請利用類別中的下列方法 `JMSContext` -

```java
TemporaryTopic createTemporaryTopic()
```

## <a name="java-message-service-jms-subscriptions"></a>JAVA 訊息服務 (JMS) 訂閱

雖然這些在語義上類似于訂用 [帳戶 (也](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) 就是存在於主題上，並啟用發佈/訂閱語義) ，JAVA 訊息服務規格會為指定的訂用帳戶引進 **共用**、未 **共用**、 **持久** 和 **非持久性** 屬性的概念。

> [!NOTE]
> 下列訂用帳戶可在 Azure 服務匯流排 Premium 層中取得，以供使用 [AZURE 服務匯流排 JMS 程式庫](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)連接到 Azure 服務匯流排的用戶端應用程式預覽。
>
> 若為公開預覽版本，則無法使用 Azure 入口網站建立這些訂用帳戶。
>

### <a name="shared-durable-subscriptions"></a>共用持久訂閱

當應用程式中發佈的所有訊息都是由應用程式接收和處理時，無論應用程式是否隨時主動從訂用帳戶取用，都會使用共用的長期訂用帳戶。

由於這是共用的訂用帳戶，因此任何已驗證可從服務匯流排接收的應用程式都可以從訂用帳戶接收。

若要建立共用的長期訂用帳戶，請在類別上使用下列方法 `JMSContext` ：

```java
JMSConsumer createSharedDurableConsumer(Topic topic, String name)

JMSConsumer createSharedDurableConsumer(Topic topic, String name, String messageSelector)
```

除非使用類別上的方法刪除，否則共用的長期訂閱會繼續存在 `unsubscribe` `JMSContext` 。

```java
void unsubscribe(String name)
```

### <a name="unshared-durable-subscriptions"></a>未共用的持久訂閱

就像共用的長期訂用帳戶一樣，在主題上發佈的所有訊息都是由應用程式接收和處理時，會使用未共用的長期訂用帳戶，而不論應用程式是否隨時都在使用訂用帳戶。

不過，由於這是不共用的訂用帳戶，因此只有建立訂用帳戶的應用程式可以接收該訂用帳戶。

若要建立非共用的長期訂用帳戶，請使用下列類別中的方法 `JMSContext` - 

```java
JMSConsumer createDurableConsumer(Topic topic, String name)

JMSConsumer createDurableConsumer(Topic topic, String name, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`這項功能目前不受支援且已略過。
>

除非使用類別上的方法刪除非共用的長期訂閱，否則會繼續存在 `unsubscribe` `JMSContext` 。

```java
void unsubscribe(String name)
```

### <a name="shared-non-durable-subscriptions"></a>共用的非持久訂閱

當多個用戶端應用程式需要接收和處理來自單一訂用帳戶的訊息時，才會使用共用的非長期訂用帳戶，但要等到它們主動取用/接收訊息為止。

因為訂用帳戶不是持久的，所以不會保存。 此訂用帳戶沒有任何作用中的取用者時，不會收到訊息。

若要建立共用的非長期訂用帳戶，請建立， `JmsConsumer` 如下所示：類別中的方法 `JMSContext` ：

```java
JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName)

JMSConsumer createSharedConsumer(Topic topic, String sharedSubscriptionName, String messageSelector)
```

共用的非長期訂用帳戶會持續存在，直到有使用中的取用者收到為止。

### <a name="unshared-non-durable-subscriptions"></a>未共用的非持久訂閱

當用戶端應用程式必須從訂用帳戶接收和處理訊息時，才會使用未共用的非長期訂閱，但要等到它主動取用為止。 此訂用帳戶中只能存在一個取用者，也就是建立訂用帳戶的用戶端。

因為訂用帳戶不是持久的，所以不會保存。 當此訂用帳戶沒有使用中的取用者時，此訂用帳戶不會收到訊息。

若要建立非共用的非長期訂用帳戶，請建立， `JMSConsumer` 如下列類別中的方法所示 `JMSContext` -

```java
JMSConsumer createConsumer(Destination destination)

JMSConsumer createConsumer(Destination destination, String messageSelector)

JMSConsumer createConsumer(Destination destination, String messageSelector, boolean noLocal)
```

> [!NOTE]
> `noLocal`這項功能目前不受支援且已略過。
>

未共用的非長期訂用帳戶會持續存在，直到有使用中的取用者收到為止。

### <a name="message-selectors"></a>訊息選取器

如同一般服務匯流排訂閱的 **篩選準則和動作** 一樣，JMS 訂用帳戶的 **訊息選取器** 也是存在的。

您可以在每個 JMS 訂用帳戶上設定訊息選取器，並以篩選準則的形式存在於訊息標頭屬性。 只會傳遞標頭屬性符合訊息選取器運算式的訊息。 Null 或空字串的值表示 JMS 訂閱/取用者沒有訊息選取器。

## <a name="next-steps"></a>後續步驟

如需使用服務匯流排傳訊的詳細資訊和範例，請參閱下列進階主題：

* [服務匯流排訊息概觀](service-bus-messaging-overview.md)
* [使用 JAVA Message Service 2.0 API 搭配 Azure 服務匯流排 Premium (Preview) ](how-to-use-java-message-service-20.md)



