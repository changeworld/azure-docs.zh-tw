---
title: 搭配 Azure 服務匯流排 Premium 使用 JAVA Message Service 2.0 API
description: '如何搭配使用 JAVA 訊息服務與 Azure 服務匯流排 (JMS) '
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8363011187a4c2ef77681ece4bb8b1de73ec7a63
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/05/2020
ms.locfileid: "87801414"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium-preview"></a>搭配使用 JAVA Message Service 2.0 API 與 Azure 服務匯流排 Premium (Preview) 

本文說明如何使用常用的**JAVA 訊息服務 (JMS) 2.0** API，透過 Advanced Message 佇列通訊協定 (AMQP 1.0) Protocol 與 Azure 服務匯流排進行互動。

> [!NOTE]
> 支援 JAVA Message Service (JMS) 2.0 API 僅適用于**Azure 服務匯流排**進階層，且目前為**預覽**狀態。
>

## <a name="get-started-with-service-bus"></a>開始使用服務匯流排

本指南假設您已經有服務匯流排命名空間。 如果沒有，您可以使用[Azure 入口網站](https://portal.azure.com)[建立命名空間和佇列](service-bus-create-namespace-portal.md)。 

如需如何建立服務匯流排命名空間和佇列的詳細資訊，請參閱[透過 Azure 入口網站開始使用服務匯流排佇列](service-bus-quickstart-portal.md)。

## <a name="what-jms-features-are-supported"></a>支援哪些 JMS 功能？

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a> (JMS) 用戶端程式庫下載 JAVA 訊息服務

若要利用 Azure 服務匯流排進階層中可用的所有功能，您必須將下列程式庫新增至專案的組建路徑。

[Azure-層匯流排-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> 若要將[Azure-匯流排-jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)新增至組建路徑，請利用您的專案慣用的相依性管理工具，例如[Maven](https://maven.apache.org/)或[Gradle](https://gradle.org/)。
>

## <a name="coding-java-applications"></a>編寫 Java 應用程式

一旦匯入相依性，JAVA 應用程式就能以 JMS 提供者無從驗證的方式撰寫。

### <a name="connecting-to-azure-service-bus-using-jms"></a>使用 JMS 連接到 Azure 服務匯流排

若要使用 JMS 用戶端與 Azure 服務匯流排連線，您需要在 [**主要連接字串**] 下的 [ [Azure 入口網站](https://portal.azure.com)的 [共用存取原則] 中提供的**連接字串**。

1. 具現化`ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. `ServiceBusJmsConnectionFactory`使用適當的具現化 `ServiceBusConnectionString` 。

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. 使用 `ConnectionFactory` 建立 `Connection` ，然後選擇`Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    或 `JMSContext` JMS 2.0 用戶端的 () 

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

### <a name="write-the-jms-application"></a>撰寫 JMS 應用程式

一旦或已經具 `Session` `JMSContext` 現化，您的應用程式就可以使用熟悉的 JMS api 來執行管理和資料作業。

請參閱[支援的 JMS 功能](how-to-use-java-message-service-20.md#what-jms-features-are-supported)清單，以查看此預覽中支援的 api。

以下是開始使用 JMS 的一些範例程式碼片段-

#### <a name="sending-messages-to-a-queue-and-topic"></a>將訊息傳送至佇列和主題

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>從佇列接收訊息

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>從主題的共用長期訂用帳戶接收訊息

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>總結

本指南會展示使用 JAVA 訊息服務的 JAVA 用戶端應用程式如何 (透過 AMQP 1.0 的 JMS) 可以與 Azure 服務匯流排互動。

您也可以使用包括 .NET、C、Python 和 PHP 在內的其他語言所撰寫的 Service Bus AMQP 1.0。 使用這些不同的語言撰寫的元件可使用服務匯流排中的 AMQP 1.0 支援確實完整交換訊息。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 服務匯流排的詳細資訊以及 JAVA 訊息服務的詳細資料 (JMS) 實體，請參閱下列連結- 
* [服務匯流排-佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [服務匯流排-JAVA 訊息服務實體](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure 服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [JAVA 訊息服務 API (外部 Oracle doc) ](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [瞭解如何從 ActiveMQ 遷移至服務匯流排](migrate-jms-activemq-to-servicebus.md)
