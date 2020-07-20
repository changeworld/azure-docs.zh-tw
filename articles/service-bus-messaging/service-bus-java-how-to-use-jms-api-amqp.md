---
title: 使用 AMQP 搭配 JAVA Message Service API & Azure 服務匯流排
description: 如何搭配 Azure 服務匯流排和 Advanced Message Queuing Protodol (AMQP) 1.0 使用 Java Message Service (JMS)。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 04d2595951640b7fe878decfeb862863f06c17a2
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119152"
---
# <a name="use-the-java-message-service-jms-with-azure-service-bus-and-amqp-10"></a>搭配 Azure 服務匯流排和 AMQP 1.0 使用 JAVA 訊息服務（JMS）

Azure 服務匯流排中支援**先進訊息佇列通訊協定（AMQP） 1.0**通訊協定，表示您可以使用有效率的二進位通訊協定，從某個範圍的平臺使用佇列和發佈/訂閱代理訊息功能。 此外，您還可以建置使用混合語言、架構及作業系統所建置之元件所組成的應用程式。

本文說明如何在 AMQP 通訊協定上使用常用的**JAVA 訊息服務（JMS）** API，從 JAVA 應用程式使用 Azure 服務匯流排訊息功能（佇列和發佈/訂閱主題）。

## <a name="get-started-with-service-bus"></a>開始使用服務匯流排
本指南假設您已經有一個服務匯流排命名空間，其中包含名為的佇列 `basicqueue` 。 如果沒有，您可以使用[Azure 入口網站](https://portal.azure.com)[建立命名空間和佇列](service-bus-create-namespace-portal.md)。 如需有關如何建立服務匯流排命名空間和佇列的相關詳細資訊，請參閱[開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分割的佇列和主題也支援 AMQP。 如需詳細資訊，請參閱[分割傳訊實體](service-bus-partitioning.md)及[服務匯流排分割佇列和主題的 AMQP 1.0 支援](service-bus-partitioned-queues-and-topics-amqp-overview.md)。
> 
>

## <a name="what-jms-features-are-supported"></a>支援哪些 JMS 功能？

以下是 Azure 服務匯流排支援的 JMS 功能。

| 功能 | Azure 服務匯流排標準層-JMS 1。1 | Azure 服務匯流排 Premium 層-JMS 2.0 （預覽） |
|---|---|---|
| 透過 AMQP 自動建立實體 | 不支援 | **支援** |
| 佇列 | **支援** | **支援** |
| 主題 | **支援** | **支援** |
| 暫存佇列 | 不支援 <br/> （請改為建立已設定*AutoDeleteOnIdle*的一般佇列） | **支援** |
| 暫存主題 | 不支援 | **支援** |
| 訊息選取器 | 不支援 | **支援** |
| 佇列瀏覽器 | 不支援 <br/> （使用服務匯流排 API 的*查看*功能） | **支援** |
| 共用的持久訂閱 | **支援** | **支援**|
| 未共用的持久訂閱 | 不支援 | **支援** |
| 共用的非持久訂閱 | 不支援 | **支援** |
| 不共用的非持久性訂閱 | 不支援 | **支援** |
| 取消訂閱持久訂閱 | 不支援 | **支援** |
| ReceiveNoWait | 不支援 | **支援** |
| 分散式交易 | 不支援 | 不支援 |
| 持久終點 | 不支援 | 不支援 |

### <a name="additional-caveats-for-service-bus-standard-tier"></a>服務匯流排標準層的其他注意事項
對於各個**工作階段**僅允許一個 **MessageProducer** 或 **MessageConsumer**。 如果您需要在應用程式中建立多個 **MessageProducers** 或 **MessageConsumers**，請分別建立專用的**工作階段**。

## <a name="downloading-the-java-message-service-jms-client-library"></a>下載 JAVA Message Service （JMS）用戶端程式庫

若要與 Azure 服務匯流排連線並利用 AMQP 上的 JAVA 訊息服務（JMS） API，您必須利用下列程式庫。 這些必須使用專案的慣用相依性管理工具加入至組建路徑。

需要的用戶端程式庫取決於所使用的定價層。

### <a name="premium-tier---jms-20-over-amqp-preview"></a>高階層-透過 AMQP 的 JMS 2.0 （預覽）

若要利用 Azure 服務匯流排進階層上所有可用的預覽功能，請利用[Azure-匯流排 jms](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)程式庫。

### <a name="standard-tier---jms-11-over-amqp"></a>標準層-透過 AMQP 的 JMS 1。1

若要利用服務匯流排標準層所支援的 JMS 功能（請參閱[支援哪些 jms 功能？](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)），請利用下列程式庫-

* [Geronimo-jms JMS 1.1 規格](https://search.maven.org/artifact/org.apache.geronimo.specs/geronimo-jms_1.1_spec)
* [Qpid JMS 用戶端](https://search.maven.org/artifact/org.apache.qpid/qpid-jms-client)

> [!NOTE]
> JMS JAR 名稱和版本可能已經變更。 如需詳細資料，請參閱 [Qpid JMS - AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10) \(英文\)。
>

## <a name="coding-java-applications"></a>編寫 Java 應用程式

一旦匯入相依性，JAVA 應用程式就能以 JMS 提供者無從驗證的方式撰寫。

由於 Azure 服務匯流排 Standard 和 Premium 的相依性和其支援的 JMS 功能數目不同，這兩者的程式設計模型會稍有不同。

> [!IMPORTANT]
> 下列指南示範如何連接到具有簡單應用程式的 Azure 服務匯流排。
>
> 假設大部分的企業應用程式架構可能會有自訂的相依性和設定管理方式，請使用下列指南來瞭解所需的專案，並適當地調整您的應用程式。
>

### <a name="connecting-to-azure-service-bus-using-jms"></a>使用 JMS 連接到 Azure 服務匯流排

若要使用 JMS 用戶端與 Azure 服務匯流排連線，您需要在 [**主要連接字串**] 下的 [ [Azure 入口網站](https://portal.azure.com)的 [共用存取原則] 中提供**ConnectionString** 。


#### <a name="connecting-to-azure-service-bus-premium-over-jms-20-preview"></a>透過 JMS 2.0 連接到 Azure 服務匯流排 Premium （預覽）

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
    或 `JMSContext` （適用于 JMS 2.0 用戶端）

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

#### <a name="connecting-to-azure-service-bus-standard-over-jms-11"></a>透過 JMS 1.1 連接到 Azure 服務匯流排標準

1. 將 Azure 服務匯流排設定插入至 JNDI 屬性檔案，稱為「執行**匯流排. 屬性**」。
    ```properties
    # servicebus.properties - sample JNDI configuration
    
    # Register a ConnectionFactory in JNDI using the form:
    # connectionfactory.[jndi_name] = [ConnectionURL]
    connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net
    ```

2. 設定 JNDI 內容並設定 ConnectionFactory
    ```java
    ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);

    // set up JNDI context
    Hashtable<String, String> hashtable = new Hashtable<>();
    hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
    hashtable.put("queue.QUEUE", "BasicQueue");
    hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
    Context context = new InitialContext(hashtable);
    
    ConnectionFactory factory = (ConnectionFactory) context.lookup("SBCF");
    ```
3. 使用 `ConnectionFactory` 建立 `Connection` ，然後使用 `Session` 。
    ```java
    Connection connection - factory.createConnection(csb.getSasKeyName(), csb.getSasKey());
    Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
    ```

### <a name="write-the-jms-application"></a>撰寫 JMS 應用程式

一旦或已經具 `Session` `JMSContext` 現化，您的應用程式就可以利用熟悉的 JMS api 來執行管理和資料作業。

請參閱標準和進階層[支援的 JMS 功能](service-bus-java-how-to-use-jms-api-amqp.md#what-jms-features-are-supported)清單，以瞭解每個層級將支援哪些 api。

## <a name="summary"></a>摘要
本指南會展示使用 JAVA 訊息服務（JMS） over AMQP 1.0 的 JAVA 用戶端應用程式如何與 Azure 服務匯流排互動。

您也可以使用包括 .NET、C、Python 和 PHP 在內的其他語言所撰寫的 Service Bus AMQP 1.0。 使用這些不同的語言撰寫的元件可使用服務匯流排中的 AMQP 1.0 支援確實完整交換訊息。

## <a name="next-steps"></a>後續步驟

如需有關 Azure 服務匯流排的詳細資訊以及 JAVA Message Service （JMS）實體的詳細資訊，請參閱下列連結- 
* [服務匯流排-佇列、主題和訂用帳戶](service-bus-queues-topics-subscriptions.md)
* [服務匯流排-JAVA 訊息服務實體](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities-preview)
* [Azure 服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [服務匯流排 AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)

