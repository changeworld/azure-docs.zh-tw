---
title: 適用于事件中樞的 Apache Kafka 開發人員指南
description: 本文提供文章的連結，說明如何將您的 Kafka 應用程式與 Azure 事件中樞整合。
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: bbd173a7f81042c0839cec81838172b21f80d5cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85314235"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>適用于 Azure 事件中樞的 Apache Kafka 開發人員指南
本文提供文章的連結，說明如何將您的 Apache Kafka 應用程式與 Azure 事件中樞整合。 

## <a name="overview"></a>總覽
事件中樞提供的 Kafka 端點可讓您現有的 Kafka 型應用程式使用，作為執行您本身的 Kafka 叢集以外的另一項選擇。 事件中樞支援 Apache Kafka 通訊協定 1.0 和更新版本，並且可與您現有的 Kafka 應用程式搭配使用，包括 MirrorMaker。 如需詳細資訊，請參閱[Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>快速入門
您可以在 GitHub 和此內容集中找到快速入門，以協助您快速加速事件中樞 Kafka。

### <a name="quickstarts-in-github"></a>GitHub 中的快速入門
請參閱**azure kafka**存放庫中的下列快速入門： 

| 用戶端語言/架構 | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>本快速入門將示範如何使用 .NET Core 2.0，以 c # 撰寫的範例生產者和取用者來建立和連接到事件中樞 Kafka 端點。</p><p>這個範例是以[Confluent 的 Apache Kafka .net 用戶端](https://github.com/confluentinc/confluent-kafka-dotnet)為基礎，已修改以用於 Kafka 的事件中樞。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 本快速入門將示範如何使用以 JAVA 撰寫的範例生產者和取用者，建立並連接到事件中樞 Kafka 端點。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>本快速入門將示範如何使用以 Node 撰寫的範例生產者和取用者，建立並連接到事件中樞 Kafka 端點。</p><p>這個範例會使用[節點 rdkafka](https://github.com/Blizzard/node-rdkafka)程式庫。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>本快速入門將示範如何使用以 python 撰寫的範例生產者和取用者，建立並連接到事件中樞 Kafka 端點。</p><p>此範例是以[Confluent 的 Apache Kafka Python 用戶端](https://github.com/confluentinc/confluent-kafka-python)為基礎，已修改以用於 Kafka 的事件中樞。</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>本快速入門將示範如何使用以 Go 撰寫的範例生產者和取用者，建立並連接到事件中樞 Kafka 端點。</p><p>這個範例是以[Confluent 的 Apache Kafka Golang 用戶端](https://github.com/confluentinc/confluent-kafka-go)為基礎，已修改以與 Kafka 的事件中樞搭配使用。</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 本快速入門將示範如何使用[Sarama Kafka 用戶端](https://github.com/Shopify/sarama)程式庫來建立和連接到事件中樞 Kafka 端點，方法是使用以 Go 撰寫的範例生產者和取用者。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 本快速入門將示範如何使用與 Apache Kafka 散發套件配套的 CLI，來建立和連線至事件中樞 Kafka 端點。| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat 是一個非 JVM 的命令列取用者，而是以 librdkafka 為基礎，因為它的速度和小型使用量，所以很熱門。 本快速入門包含範例設定和數個簡單的範例 kafkacat 命令。 | 
 
### <a name="quickstarts-in-docs"></a>檔中的快速入門
請參閱快速入門：使用此內容集中[的 Kafka 通訊協定進行資料串流處理事件中樞](event-hubs-quickstart-kafka-enabled-event-hubs.md)，其中提供如何串流至事件中樞的逐步指示。 您將了解如何僅在應用程式中變更設定，就能讓產生者和取用者與事件中樞交談。 


## <a name="tutorials"></a>教學課程 

### <a name="tutorials-in-github"></a>GitHub 中的教學課程
請參閱 GitHub 上的下列教學課程：

| 教學課程 | 描述 | 
| ------------------------- | ----------- | 
| [Akka streams](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 本教學課程說明如何將 Akka streams 串流連線到已啟用 Kafka 的事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 有兩個不同的教學課程使用**JAVA**和**Scala**程式設計語言。 | 
| [[連接]](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 本檔將逐步引導您整合 Kafka Connect 與 Azure 事件中樞並部署基本的 FileStreamSource 和 FileStreamSink 連接器。 雖然這些連接器並非用於生產環境，但它們會示範端對端的 Kafka Connect 案例，其中 Azure 事件中樞 masquerades 為 Kafka 訊息代理程式。| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 本檔將逐步引導您透過 Filebeat 的 Kafka 輸出來整合 Filebeat 和事件中樞。 | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 本教學課程將示範如何將 Apache Flink 連線至已啟用 Kafka 的事件中樞，而不需要變更您的通訊協定用戶端或執行您自己的叢集。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 本檔將逐步引導您使用 `out_kafka` Fluentd 的輸出外掛程式來整合 Fluentd 和事件中樞。 |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 本教學課程會示範如何使用不同的通訊協定，在取用者與生產者之間交換事件。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 本教學課程將逐步引導您使用 Logstash Kafka 輸入/輸出外掛程式，將 Logstash 與已啟用 Kafka 的事件中樞整合。 | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 本教學課程說明事件中樞和 Kafka MirrorMaker 如何藉由鏡像事件中樞服務中的 Kafka 輸入資料流程，將現有的 Kafka 管線整合到 Azure 中。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 本教學課程將示範如何將 Apache 適用于 nifi 連線至事件中樞命名空間。 | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 快速入門示範如何使用以 Go 和 JAVA 程式語言撰寫的範例生產者和取用者，建立並連接到事件中樞 Kafka 端點。 |
| [Confluent 的架構登錄](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 本教學課程將逐步引導您整合架構登錄和事件中樞以進行 Kafka。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 本教學課程將示範如何將 Spark 應用程式連線至事件中樞，而不需要變更通訊協定用戶端或執行您自己的 Kafka 叢集。 | 

### <a name="tutorials-in-docs"></a>檔中的教學課程
另請參閱教學課程：使用此內容集中的[串流分析來處理事件中樞事件的 Apache Kafka](event-hubs-kafka-stream-analytics.md) ，其中顯示如何將資料串流至事件中樞，並使用 Azure 串流分析來處理。

## <a name="how-to-guides"></a>操作指南
請參閱檔中的下列操作指南：

| 文章 | 說明 | 
| ------- | ----------- | 
| [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md) | 示範如何使用 Kafka MirrorMaker，在事件中樞內鏡像 Kafka 訊息代理程式。 |
| [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md) | 逐步引導您將 Spark 應用程式連接到事件中樞進行即時串流。 |
| [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md) | 說明如何將 Apache Flink 連線至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 |
| [整合 Apache Kafka Connect 與事件中樞（預覽）](event-hubs-kafka-connect-tutorial.md) | 逐步引導您整合 Kafka Connect 與事件中樞，以及部署基本的 FileStreamSource 和 FileStreamSink 連接器。 |
| [將 Akka streams 串流連線至事件中樞](event-hubs-kafka-akka-streams-tutorial.md) | 說明如何將 Akka streams 串流連線至事件中樞，而不需要變更通訊協定用戶端或執行您自己的叢集。 |
| [使用適用于 Apache Kafka 的彈簧開機 Starter 搭配 Azure 事件中樞](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | 示範如何設定以「春季開機初始化運算式」建立的 JAVA 型春季雲端串流系結器，以搭配 Azure 事件中樞使用 Apache Kafka。 |

## <a name="next-steps"></a>後續步驟
在 [快速入門] 和 [教學課程] 資料夾底下，參閱 GitHub 存放庫中的範例[kafka](https://github.com/Azure/azure-event-hubs-for-kafka) 。

另請參閱下列文章：

- [適用于事件中樞的 Apache Kafka 疑難排解指南](apache-kafka-troubleshooting-guide.md)
- [常見問題-適用于 Apache Kafka 的事件中樞](apache-kafka-frequently-asked-questions.md)
- [適用于事件中樞的 Apache Kafka 遷移指南](apache-kafka-migration-guide.md)



