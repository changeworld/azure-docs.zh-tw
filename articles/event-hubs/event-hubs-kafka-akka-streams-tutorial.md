---
title: 使用適用於 Apache Kafka 的 Akka Streams - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何將 Akka streams 串流連接至 Azure 事件中樞的資訊。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1fbbeef37c4cbdd52d2127c5242474ac46e42d25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90061694"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>使用 Akka Streams 搭配適用於 Apache Kafka 的事件中樞

本教學課程說明如何透過適用于 Apache Kafka 的事件中樞支援來連接 Akka streams 串流，而不需要變更通訊協定用戶端或執行您自己的叢集。 

在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 複製範例專案
> * 執行 Akka Streams 生產者 
> * 執行 Akka Streams 取用者

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) 上取得此範例

## <a name="prerequisites"></a>Prerequisites

若要完成本教學課程，請確定您具有下列必要條件：

* 請參閱[適用於 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)一文。 
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks) \(英文\)
    * 在 Ubuntu 上，執行 `apt-get install default-jdk` 來安裝 JDK。
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](https://maven.apache.org/download.cgi) 並 [安裝](https://maven.apache.org/install.html) Maven 二進位封存
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

您需要事件中樞命名空間，才能從任何事件中樞服務傳送或接收。 如需詳細資訊，請參閱 [建立事件中樞](event-hubs-create.md) 。 請務必複製事件中樞連接字串以供稍後使用。

## <a name="clone-the-example-project"></a>複製範例專案

現在您已有事件中樞連接字串，請複製 Kafka 儲存機制的 Azure 事件中樞，然後流覽至 `akka` 子資料夾：

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>執行 Akka Streams 生產者

使用提供的 Akka Streams 生產者範例，傳送訊息到事件中樞服務。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中樞 Kafka 端點

#### <a name="producer-applicationconf"></a>生產者 application.conf

更新 `producer/src/main/resources/application.conf` 中 `bootstrap.servers` 和 `sasl.jaas.config` 的值，以使用正確的驗證將生產者導向至事件中樞 Kafka 端點。

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>從命令列執行生產者

若要從命令列執行生產者，請從 Maven 中產生 JAR 然後從中執行 (或是使用 Maven 產生 JAR，然後將必要的 Kafka JAR 加入至 classpath，在 Java 中執行)：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

生產者開始在主題中將事件傳送至事件中樞 `test` ，並將事件列印至 stdout。

## <a name="run-akka-streams-consumer"></a>執行 Akka Streams 取用者

使用提供的取用者範例，接收來自事件中樞的訊息。

### <a name="provide-an-event-hubs-kafka-endpoint"></a>提供事件中樞 Kafka 端點

#### <a name="consumer-applicationconf"></a>取用者 application.conf

更新 `consumer/src/main/resources/application.conf` 中 `bootstrap.servers` 和 `sasl.jaas.config` 的值，以使用正確的驗證將取用者導向至事件中樞 Kafka 端點。

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>從命令列執行取用者

若要從命令列執行取用者，請從 Maven 中產生 JAR 然後從中執行 (或是使用 Maven 產生 JAR，然後將必要的 Kafka JAR 加入至 classpath，以在 Java 中執行)：

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

如果事件中樞有 (的事件，則如果您的生產者也正在執行) ，則取用者會開始接收主題的事件 `test` 。 

如需有關 Akka Streams 的詳細資訊，請參閱 [Akka Streams Kafka 指南](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) \(英文\)。

## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于 Kafka 的事件中樞，請參閱下列文章：  

- [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md)
- [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md)
- [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md)
- [整合 Kafka Connect 與事件中樞](event-hubs-kafka-connect-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
- [Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
