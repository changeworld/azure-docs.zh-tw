---
title: 使用 AMQP 搭配 JAVA 訊息服務 API 和 Azure 服務匯流排
description: 使用 JAVA 訊息服務 (JMS) 搭配 Azure 服務匯流排和 Advanced Message 佇列通訊協定 (AMQP) 1.0。
ms.topic: article
ms.date: 06/23/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 97031abaedaa3e5595e290fa0292646feb744d47
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/15/2020
ms.locfileid: "90086686"
---
# <a name="use-the-java-message-service-with-azure-service-bus-and-amqp-10"></a>使用 JAVA 訊息服務搭配 Azure 服務匯流排和 AMQP 1。0

> [!WARNING]
> 本文已經考慮對 JAVA Message Service (JMS) 1.1 API 提供 *有限的支援* ，且僅適用于 Azure 服務匯流排標準層。
>
> JAVA 訊息服務 2.0 API 的完整支援僅適用于 [預覽中的 Azure 服務匯流排 premium 層](how-to-use-java-message-service-20.md)。 我們建議您使用這一層。
>

本文說明如何使用常用的 JMS API 標準，從 JAVA 應用程式使用服務匯流排訊息功能。 這些訊息功能包括佇列和發佈或訂閱主題。 [隨附的文章](service-bus-amqp-dotnet.md)說明如何使用 AZURE 服務匯流排 .net API 來進行相同的作業。 您可以同時使用這兩篇文章來瞭解如何使用 Advanced Message 佇列通訊協定的跨平臺訊息 (AMQP) 1.0。

AMQP 1.0 是有效率、可靠的有線等級訊息通訊協定，可讓您用來建立強大的跨平臺訊息應用程式。

服務匯流排中的 AMQP 1.0 支援表示您可以使用有效率的二進位通訊協定，從某個範圍的平臺使用佇列和發佈或訂閱代理訊息功能。 您也可以建立應用程式，這些應用程式是使用混合語言、架構和作業系統所建立的元件所組成。

## <a name="get-started-with-service-bus"></a>開始使用服務匯流排

本文假設您已經有服務匯流排命名空間，其中包含名為的佇列 `basicqueue` 。 如果沒有，您可以使用[Azure 入口網站](https://portal.azure.com)[建立命名空間和佇列](service-bus-create-namespace-portal.md)。 如需有關如何建立服務匯流排命名空間和佇列的相關詳細資訊，請參閱[開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)。

> [!NOTE]
> 分割的佇列和主題也支援 AMQP。 如需詳細資訊，請參閱[分割傳訊實體](service-bus-partitioning.md)及[服務匯流排分割佇列和主題的 AMQP 1.0 支援](./service-bus-amqp-protocol-guide.md)。
> 
> 

## <a name="download-the-amqp-10-jms-client-library"></a>下載 AMQP 1.0 JMS 用戶端程式庫

如需有關下載最新版 Apache Qpid JMS AMQP 1.0 用戶端程式庫的詳細資訊，請參閱 [Apache Qpid 下載網站](https://qpid.apache.org/download.html)。

當您使用服務匯流排來建立和執行 JMS 應用程式時，您必須將下列 JAR 檔案從 Apache Qpid JMS AMQP 1.0 散發封存新增至 JAVA 類別路徑環境變數：

* geronimo-jms\_1.1\_spec-1.0.jar
* qpid-jms-client-[版本].jar

> [!NOTE]
> JMS JAR 名稱和版本可能已經變更。 如需詳細資訊，請參閱 [QPID JMS AMQP 1.0](https://qpid.apache.org/maven.html#qpid-jms-amqp-10)。

## <a name="code-java-applications"></a>撰寫 JAVA 應用程式的程式碼

### <a name="java-naming-and-directory-interface"></a>JAVA 命名和目錄介面

JMS 使用 Java 命名及目錄介面 (JNDI) 建立邏輯名稱與實際名稱之間的區別。 使用 JNDI： **ConnectionFactory** 和 **Destination**來解析兩種類型的 JMS 物件。 JNDI 使用提供者模型，您可以在其中插入不同的目錄服務處理名稱解析作業。 Apache Qpid JMS AMQP 1.0 程式庫隨附簡單的屬性檔案型 JNDI 提供者，使用下列格式的屬性檔進行設定：

```TEXT
# servicebus.properties - sample JNDI configuration

# Register a ConnectionFactory in JNDI using the form:
# connectionfactory.[jndi_name] = [ConnectionURL]
connectionfactory.SBCF = amqps://[SASPolicyName]:[SASPolicyKey]@[namespace].servicebus.windows.net

# Register some queues in JNDI using the form
# queue.[jndi_name] = [physical_name]
# topic.[jndi_name] = [physical_name]
queue.QUEUE = queue1
```

#### <a name="set-up-jndi-context-and-configure-the-connectionfactory-object"></a>設定 JNDI 內容和設定 ConnectionFactory 物件

參考的連接字串是在**主要連接字串**底下[Azure 入口網站](https://portal.azure.com)的共用存取原則中可用的連接字串。

```java
// The connection string builder is the only part of the azure-servicebus SDK library
// we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
// connection string. 
ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");

// Look up queue
Destination queue = (Destination) context.lookup("QUEUE");
```

#### <a name="configure-producer-and-consumer-destination-queues"></a>設定生產者和取用者目的地佇列

在 Qpid 屬性檔 JNDI 提供者中用來定義目的地的專案使用下列格式。

若要建立生產者的目的地佇列：
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create producer
MessageProducer producer = session.createProducer(queue);
```

若要建立取用者的目的地佇列：
```java
String queueName = "queueName";
Destination queue = (Destination) queueName;

ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
Connection connection - cf.createConnection(csb.getSasKeyName(), csb.getSasKey());

Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

// Create consumer
MessageConsumer consumer = session.createConsumer(queue);
```

### <a name="write-the-jms-application"></a>撰寫 JMS 應用程式

當您搭配使用 JMS 與服務匯流排時，不需要任何特殊的 Api 或選項。 稍後將會討論一些限制。 和任何 JMS 應用程式一樣，第一件事是設定 JNDI 環境，以便能夠解析 **ConnectionFactory** 物件和目的地。

#### <a name="configure-the-jndi-initialcontext-object"></a>設定 JNDI InitialCoNtext 物件

JNDI 環境的設定方式，是將設定資訊的雜湊表傳遞到 javax.naming.InitialCoNtext 類別的函式。 雜湊表中的兩個必要元素是初始內容 Factory 和提供者 URL 的類別名稱。 下列程式碼會示範如何設定 JNDI 環境，以使用 Qpid 屬性檔案型 JNDI 提供者與名為「服務 **匯流排. 屬性**」的屬性檔。

```java
// Set up JNDI context
Hashtable<String, String> hashtable = new Hashtable<>();
hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + \
"?amqp.idleTimeout=120000&amqp.traceFrames=true");
hashtable.put("queue.QUEUE", "BasicQueue");
hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
Context context = new InitialContext(hashtable);
``` 

### <a name="a-simple-jms-application-that-uses-a-service-bus-queue"></a>使用服務匯流排佇列的簡單 JMS 應用程式

下列範例程式會使用佇列的 JNDI 邏輯名稱將 JMS 文字訊息傳送至服務匯流排佇列，並傳回訊息。

您可以從 [Azure 服務匯流排範例 JMS 佇列快速入門](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/qpid-jms-client/JmsQueueQuickstart)中，存取所有的原始程式碼和設定資訊。

```java
// Copyright (c) Microsoft. All rights reserved.
// Licensed under the MIT license. See LICENSE file in the project root for full license information.

package com.microsoft.azure.servicebus.samples.jmsqueuequickstart;

import com.microsoft.azure.servicebus.primitives.ConnectionStringBuilder;
import org.apache.commons.cli.*;
import org.apache.log4j.*;

import javax.jms.*;
import javax.naming.Context;
import javax.naming.InitialContext;
import java.util.Hashtable;
import java.util.concurrent.atomic.AtomicInteger;
import java.util.function.Function;

/**
 * This sample demonstrates how to send messages from a JMS queue producer into
 * an Azure Service Bus queue and receive them with a JMS message consumer.
 * JMS queue. 
 */
public class JmsQueueQuickstart {

    // Number of messages to send
    private static int totalSend = 10;
    //Tracking counter for how many messages have been received; used as termination condition
    private static AtomicInteger totalReceived = new AtomicInteger(0);
    // log4j logger 
    private static Logger logger = Logger.getRootLogger();

    public void run(String connectionString) throws Exception {

        // The connection string builder is the only part of the azure-servicebus SDK library
        // we use in this JMS sample and for the purpose of robustly parsing the Service Bus 
        // connection string. 
        ConnectionStringBuilder csb = new ConnectionStringBuilder(connectionString);
        
        // Set up JNDI context
        Hashtable<String, String> hashtable = new Hashtable<>();
        hashtable.put("connectionfactory.SBCF", "amqps://" + csb.getEndpoint().getHost() + "?amqp.idleTimeout=120000&amqp.traceFrames=true");
        hashtable.put("queue.QUEUE", "BasicQueue");
        hashtable.put(Context.INITIAL_CONTEXT_FACTORY, "org.apache.qpid.jms.jndi.JmsInitialContextFactory");
        Context context = new InitialContext(hashtable);
        ConnectionFactory cf = (ConnectionFactory) context.lookup("SBCF");
        
        // Look up queue
        Destination queue = (Destination) context.lookup("QUEUE");

        // We create a scope here so we can use the same set of local variables cleanly 
        // again to show the receive side separately with minimal clutter.
        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);

            // Create producer
            MessageProducer producer = session.createProducer(queue);

            // Send messages
            for (int i = 0; i < totalSend; i++) {
                BytesMessage message = session.createBytesMessage();
                message.writeBytes(String.valueOf(i).getBytes());
                producer.send(message);
                System.out.printf("Sent message %d.\n", i + 1);
            }

            producer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        {
            // Create connection
            Connection connection = cf.createConnection(csb.getSasKeyName(), csb.getSasKey());
            connection.start();
            // Create session, no transaction, client ack
            Session session = connection.createSession(false, Session.CLIENT_ACKNOWLEDGE);
            // Create consumer
            MessageConsumer consumer = session.createConsumer(queue);
            // Create a listener callback to receive the messages
            consumer.setMessageListener(message -> {
                try {
                    // Received message is passed to callback
                    System.out.printf("Received message %d with sq#: %s\n",
                            totalReceived.incrementAndGet(), // increments the tracking counter
                            message.getJMSMessageID());
                    message.acknowledge();
                } catch (Exception e) {
                    logger.error(e);
                }
            });

            // Wait on the main thread until all sent messages have been received
            while (totalReceived.get() < totalSend) {
                Thread.sleep(1000);
            }
            consumer.close();
            session.close();
            connection.stop();
            connection.close();
        }

        System.out.printf("Received all messages, exiting the sample.\n");
        System.out.printf("Closing queue client.\n");
    }

    public static void main(String[] args) {

        System.exit(runApp(args, (connectionString) -> {
            JmsQueueQuickstart app = new JmsQueueQuickstart();
            try {
                app.run(connectionString);
                return 0;
            } catch (Exception e) {
                System.out.printf("%s", e.toString());
                return 1;
            }
        }));
    }

    static final String SB_SAMPLES_CONNECTIONSTRING = "SB_SAMPLES_CONNECTIONSTRING";

    public static int runApp(String[] args, Function<String, Integer> run) {
        try {

            String connectionString = null;

            // Parse connection string from command line
            Options options = new Options();
            options.addOption(new Option("c", true, "Connection string"));
            CommandLineParser clp = new DefaultParser();
            CommandLine cl = clp.parse(options, args);
            if (cl.getOptionValue("c") != null) {
                connectionString = cl.getOptionValue("c");
            }

            // Get overrides from the environment
            String env = System.getenv(SB_SAMPLES_CONNECTIONSTRING);
            if (env != null) {
                connectionString = env;
            }

            if (connectionString == null) {
                HelpFormatter formatter = new HelpFormatter();
                formatter.printHelp("run jar with", "", options, "", true);
                return 2;
            }
            return run.apply(connectionString);
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
            return 3;
        }
    }
}
```

### <a name="run-the-application"></a>執行應用程式

傳遞來自 [共用存取原則] 的 [連接字串]****，以執行應用程式。
下列輸出是執行應用程式的表單：

```Output
> mvn clean package
>java -jar ./target/jmsqueuequickstart-1.0.0-jar-with-dependencies.jar -c "<CONNECTION_STRING>"

Sent message 1.
Sent message 2.
Sent message 3.
Sent message 4.
Sent message 5.
Sent message 6.
Sent message 7.
Sent message 8.
Sent message 9.
Sent message 10.
Received message 1 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-1
Received message 2 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-2
Received message 3 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-3
Received message 4 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-4
Received message 5 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-5
Received message 6 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-6
Received message 7 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-7
Received message 8 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-8
Received message 9 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-9
Received message 10 with sq#: ID:7f6a7659-bcdf-4af6-afc1-4011e2ddcb3c:1:1:1-10
Received all messages, exiting the sample.
Closing queue client.

```

## <a name="amqp-disposition-and-service-bus-operation-mapping"></a>AMQP 配置和服務匯流排作業對應

以下是 AMQP 配置轉譯為服務匯流排作業的方式：

```Output
ACCEPTED = 1; -> Complete()
REJECTED = 2; -> DeadLetter()
RELEASED = 3; (just unlock the message in service bus, will then get redelivered)
MODIFIED_FAILED = 4; -> Abandon() which increases delivery count
MODIFIED_FAILED_UNDELIVERABLE = 5; -> Defer()
```

## <a name="jms-topics-vs-service-bus-topics"></a>JMS 主題與服務匯流排主題

透過 JMS API 使用服務匯流排主題和訂用帳戶提供基本的傳送和接收功能。 當您從其他訊息代理程式使用符合 JMS 規範的 Api 來移植應用程式時，這是很方便的選擇，即使服務匯流排主題與 JMS 主題不同，也需要一些調整。

服務匯流排主題會將訊息路由至透過 Azure 資源管理介面、Azure 命令列工具或 Azure 入口網站管理的已命名、共用和持久的訂閱。 每個訂用帳戶最多允許2000個選取規則，每個都有篩選準則，而針對 SQL 篩選，也有一個中繼資料轉換動作。 每個篩選準則相符都會選取要複製到訂用帳戶中的輸入訊息。  

從訂閱接收訊息與從佇列接收訊息相同。 每個訂用帳戶都有相關聯的寄不出的信件佇列，以及自動將訊息轉送到另一個佇列或主題的能力。

JMS 主題可讓用戶端動態建立非持久性和持久的訂閱者，並選擇性地允許使用訊息選取器來篩選訊息。 服務匯流排不支援這些非共用的實體。 服務匯流排的 SQL 篩選規則語法類似于 JMS 所支援的訊息選取器語法。

JMS 主題發行者端與服務匯流排相容，如這個範例所示，但動態訂閱者則不是。 服務匯流排不支援下列與拓撲相關的 JMS Api。

## <a name="unsupported-features-and-restrictions"></a>不支援的功能和限制

當您搭配使用 JMS 與服務匯流排的 AMQP 1.0 時，會有下列限制，亦即：

* 每個會話僅允許一個 **MessageProducer** 或 **>messageconsumer** 物件。 如果您需要在應用程式中建立多個 **MessageProducer** 或 **>messageconsumer** 物件，請為每個物件建立專用的會話。
* 目前不支援暫時性主題訂用帳戶。
* 目前不支援**MessageSelector**物件。
* 不支援分散式交易，但支援交易會話。

服務匯流排會將控制平面從資料平面分割，因此不支援數個 JMS 動態拓撲函數。

| 不支援的方法          | 更換為                                                                             |
|-----------------------------|------------------------------------------------------------------------------------------|
| createDurableSubscriber     | 建立用來建立訊息選取器埠的主題訂用帳戶。                                |
| createDurableConsumer       | 建立用來建立訊息選取器埠的主題訂用帳戶。                                |
| createSharedConsumer        | 服務匯流排主題一律可共用。 請參閱「JMS 主題與服務匯流排主題」一節。                                    |
| createSharedDurableConsumer | 服務匯流排主題一律可共用。 請參閱「JMS 主題與服務匯流排主題」一節。                                      |
| createTemporaryTopic        | 透過管理 API、工具或入口網站建立主題，並將 *>autodeleteonidle* 設定為到期期限。 |
| createTopic                 | 透過管理 API、工具或入口網站建立主題。                                         |
| 取消訂閱                 | 刪除主題管理 API、工具或入口網站。                                            |
| createBrowser               | 不支援。 使用服務匯流排 API 的 Peek ( # A1 功能。                         |
| createQueue                 | 透過管理 API、工具或入口網站建立佇列。                                           | 
| createTemporaryQueue        | 透過管理 API、工具或入口網站建立佇列，並將 *>autodeleteonidle* 設定為到期期限。 |
| receiveNoWait               | 使用服務匯流排 SDK 提供的接收 ( # A1 方法，並指定非常低或零的超時。 |

## <a name="summary"></a>摘要

本文說明如何使用常用的 JMS API 和 AMQP 1.0，從 JAVA 使用服務匯流排代理訊息功能，例如佇列和發佈或訂閱主題。

您也可以使用來自其他語言的服務匯流排 AMQP 1.0，例如 .NET、C、Python 和 PHP。 使用這些不同語言所建立的元件可以使用服務匯流排中的 AMQP 1.0 支援，以可靠且完全精確地交換訊息。

## <a name="next-steps"></a>後續步驟

* [Azure 服務匯流排中的 AMQP 1.0 支援](service-bus-amqp-overview.md)
* [使用 AMQP 1.0 搭配服務匯流排 .NET API](./service-bus-amqp-dotnet.md)
* [Service Bus AMQP 1.0 開發人員指南](service-bus-amqp-dotnet.md)
* [開始使用服務匯流排佇列](service-bus-dotnet-get-started-with-queues.md)
* [JAVA 開發人員中心](https://azure.microsoft.com/develop/java/)