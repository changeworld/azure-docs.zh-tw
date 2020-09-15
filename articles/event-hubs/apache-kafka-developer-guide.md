---
title: 適用于事件中樞的 Apache Kafka 開發人員指南
description: 本文提供文章的連結，說明如何將您的 Kafka 應用程式與 Azure 事件中樞整合。
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 51df05f27adb8f3ce67ad11d75cb0ee0b016dc5d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/14/2020
ms.locfileid: "90061728"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure 事件中樞的 Apache Kafka 開發人員指南
本文提供文章的連結，說明如何將您的 Apache Kafka 應用程式與 Azure 事件中樞整合。 

## <a name="overview"></a>概觀
事件中樞提供的 Kafka 端點可讓您現有的 Kafka 型應用程式使用，作為執行您本身的 Kafka 叢集以外的另一項選擇。 事件中樞適用于許多現有的 Kafka 應用程式。 如需詳細資訊，請參閱 [適用于 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>快速入門
您可以在 GitHub 和此內容集中找到快速入門，協助您快速提升事件中樞的 Kafka。

### <a name="quickstarts-in-github"></a>GitHub 中的快速入門
請參閱 **azure kafka** 存放庫中的下列快速入門： 

| 用戶端語言/架構 | 描述 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>本快速入門將說明如何使用以 c # 撰寫的範例生產者和取用者（使用 .NET Core 2.0）來建立和連線至事件中樞 Kafka 端點。</p><p>此範例是以 [Confluent 的 Apache Kafka .net 用戶端](https://github.com/confluentinc/confluent-kafka-dotnet)為基礎，已修改為與 Kafka 的事件中樞搭配使用。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 本快速入門將說明如何使用以 JAVA 撰寫的範例生產者和取用者來建立和連線至事件中樞 Kafka 端點。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>本快速入門將說明如何使用以 Node 撰寫的範例生產者和取用者來建立和連線至事件中樞 Kafka 端點。</p><p>此範例會使用 [節點 rdkafka](https://github.com/Blizzard/node-rdkafka) 程式庫。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>本快速入門將說明如何使用以 python 撰寫的範例生產者和取用者來建立和連線至事件中樞 Kafka 端點。</p><p>此範例是以 [Confluent 的 Apache Kafka Python 用戶端](https://github.com/confluentinc/confluent-kafka-python)為基礎，經過修改後可搭配適用于 Kafka 的事件中樞使用。</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>本快速入門將說明如何使用以 Go 撰寫的範例生產者和取用者來建立和連線至事件中樞 Kafka 端點。</p><p>此範例是以 [Confluent 的 Apache Kafka Golang 用戶端](https://github.com/confluentinc/confluent-kafka-go)為基礎，經過修改後可搭配適用于 Kafka 的事件中樞使用。</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 本快速入門將說明如何使用 [Sarama Kafka 用戶端](https://github.com/Shopify/sarama) 程式庫，來建立並聯機至事件中樞 Kafka 端點，方法是使用以 Go 撰寫的範例生產者和取用者。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 本快速入門將說明如何使用 Apache Kafka 散發套件隨附的 CLI 來建立和連線至事件中樞 Kafka 端點。| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat 是一種非 JVM 命令列取用者和產生者，以 librdkafka 為基礎，因為它的速度和磁片使用量很低。 本快速入門包含範例設定和數個簡單的範例 kafkacat 命令。 | 
 
### <a name="quickstarts-in-docs"></a>檔中的快速入門
請參閱快速入門：使用此內容集中 [的 Kafka 通訊協定與事件中樞進行資料串流處理](event-hubs-quickstart-kafka-enabled-event-hubs.md) ，以提供如何串流至事件中樞的逐步指示。 您將了解如何僅在應用程式中變更設定，就能讓產生者和取用者與事件中樞交談。 


## <a name="tutorials"></a>教學課程 

### <a name="tutorials-in-github"></a>GitHub 中的教學課程
請參閱 GitHub 上的下列教學課程：

| 教學課程 | 描述 | 
| ------------------------- | ----------- | 
| [Akka streams](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 本教學課程說明如何將 Akka streams 串流連線到已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 有兩個使用 **JAVA** 和 **Scala** 程式設計語言的不同教學課程。 | 
| [[連接]](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 本檔將逐步引導您整合 Kafka Connect 與 Azure 事件中樞及部署基本 FileStreamSource 和 FileStreamSink 連接器。 雖然這些連接器並非用於生產環境，但它們會示範 Azure 事件中樞 masquerades 做為 Kafka broker 的端對端 Kafka 連接案例。| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 本檔將逐步引導您透過 Filebeat 的 Kafka 輸出整合 Filebeat 和事件中樞。 | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 本教學課程將說明如何將 Apache Flink 連線到已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 本檔將逐步引導您使用 `out_kafka` Fluentd 的輸出外掛程式來整合 Fluentd 和事件中樞。 |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 本教學課程說明如何使用不同的通訊協定，在取用者和產生者之間交換事件。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 本教學課程將逐步引導您使用 Logstash Kafka 輸入/輸出外掛程式，將 Logstash 與啟用 Kafka 的事件中樞整合。 | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 本教學課程說明事件中樞和 Kafka MirrorMaker 如何在事件中樞服務中鏡像 Kafka 輸入資料流程，以將現有的 Kafka 管線整合到 Azure。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 本教學課程將示範如何將 Apache 適用于 nifi 連線至事件中樞命名空間。 | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 快速入門會示範如何使用以 Go 和 JAVA 程式設計語言撰寫的範例生產者和取用者來建立和連線至事件中樞 Kafka 端點。 |
| [Confluent 的架構登錄](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 本教學課程將逐步引導您整合適用于 Kafka 的架構登錄和事件中樞。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 本教學課程將示範如何將 Spark 應用程式連線到事件中樞，而不需要變更通訊協定用戶端或執行您自己的 Kafka 叢集。 | 

### <a name="tutorials-in-docs"></a>檔中的教學課程
另請參閱教學課程：在此內容集中 [使用串流分析處理適用于事件中樞事件的 Apache Kafka](event-hubs-kafka-stream-analytics.md) ，其中顯示如何將資料串流至事件中樞，並使用 Azure 串流分析進行處理。

## <a name="how-to-guides"></a>操作指南
請參閱檔中的下列操作指南：

| 文章 | 描述 | 
| ------- | ----------- | 
| [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md) | 說明如何使用 Kafka MirrorMaker 在事件中樞中鏡像 Kafka 訊息代理程式。 |
| [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md) | 引導您將 Spark 應用程式連線到事件中樞，以進行即時串流。 |
| [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md) | 說明如何將 Apache Flink 連接到事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 |
| [整合 Apache Kafka Connect 與事件中樞 (預覽版) ](event-hubs-kafka-connect-tutorial.md) | 引導您整合 Kafka Connect 與事件中樞，以及部署基本 FileStreamSource 和 FileStreamSink 連接器。 |
| [將 Akka 串流連線到事件中樞](event-hubs-kafka-akka-streams-tutorial.md) | 說明如何將 Akka streams 串流連線至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 |
| [使用適用于 Apache Kafka 的彈簧開機 Starter 搭配 Azure 事件中樞](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | 示範如何設定以「彈簧開機初始化運算式」建立的 JAVA 型春季 Cloud Stream 系結器，以搭配 Azure 事件中樞使用 Apache Kafka。 |

## <a name="next-steps"></a>後續步驟
在 [快速入門] 和 [教學課程] 資料夾底下的 GitHub 存放庫中，查看 [kafka](https://github.com/Azure/azure-event-hubs-for-kafka) 的範例。

此外，請參閱下列文章：

- [適用于事件中樞的 Apache Kafka 疑難排解指南](apache-kafka-troubleshooting-guide.md)
- [常見問題-Apache Kafka 的事件中樞](apache-kafka-frequently-asked-questions.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)



