---
title: 阿帕奇卡夫卡活動中心開發人員指南
description: 本文提供了描述如何將 Kafka 應用程式與 Azure 事件中心集成的文章的連結。
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 8a72d20101aacaf59b4be5c4a231b132237113f3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633914"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Azure 活動中心 Apache Kafka 開發人員指南
本文提供了描述如何將 Apache Kafka 應用程式與 Azure 事件中心集成的文章的連結。 

## <a name="overview"></a>概觀
事件中樞提供的 Kafka 端點可讓您現有的 Kafka 型應用程式使用，作為執行您本身的 Kafka 叢集以外的另一項選擇。 事件中樞支援 Apache Kafka 通訊協定 1.0 和更新版本，並且可與您現有的 Kafka 應用程式搭配使用，包括 MirrorMaker。 有關詳細資訊,請參閱[Apache Kafka 的事件中心](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>快速入門
您可以在 GitHub 和此內容集中找到快速入門,説明您快速啟動 Kafka 的事件中心。

### <a name="quickstarts-in-github"></a>GitHub 的快速入門
請參考**azure 事件集線器 for kafka**回購中的以下快速啟動: 

| 用戶端語言/框架 | 描述 | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>此快速入門將演示如何使用使用 .NET Core 2.0 用 C# 編寫的範例生產者和使用者創建和連接到事件中心 Kafka 終結點。</p><p>此示例基於[康因的 Apache Kafka .NET 用戶端](https://github.com/confluentinc/confluent-kafka-dotnet),該用戶端已修改為卡夫卡的事件中心。</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | 此快速入門將演示如何使用 JAva 編寫的範例生成器和使用者創建和連接到事件中心 Kafka 終結點。 |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>此快速入門將演示如何使用 Node 中編寫的範例生成器和使用者創建和連接到事件中心 Kafka 終結點。</p><p>此示例使用節點[rdkafka](https://github.com/Blizzard/node-rdkafka)庫。 </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>此快速入門將演示如何使用用 python 編寫的範例生成器和使用者創建和連接到事件中心 Kafka 終結點。</p><p>此示例基於[康因的阿帕奇卡夫卡Python用戶端](https://github.com/confluentinc/confluent-kafka-python),修改用於卡夫卡的事件中心。</p>|
| [移至](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>此快速入門將演示如何使用 Go 編寫的範例生成器和使用者創建和連接到事件中心 Kafka 終結點。</p><p>此示例基於[康因的阿帕奇卡夫卡戈朗用戶端](https://github.com/confluentinc/confluent-kafka-go),修改用於卡夫卡的事件中心。</p>| 
| [薩拉瑪卡夫卡戈](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | 此快速入門將演示如何使用使用[Sarama Kafka 用戶端](https://github.com/Shopify/sarama)庫在 Go 中編寫的範例生產者和使用者創建和連接到事件中心 Kafka 終結點。 |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | 此快速入門將演示如何使用與 Apache Kafka 分佈捆綁的 CLI 創建和連接到事件中心 Kafka 終結點。| 
| [卡夫卡特](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat 是一種非 JVM 命令行消費者和生產商,基於利布卡夫卡,因其速度和佔地面積小而廣受歡迎。 此快速入門包含範例配置和幾個簡單示例 kakacat 命令。 | 
 
### <a name="quickstarts-in-docs"></a>DOCS 中快速入門
請參閱快速入門:使用此內容集中的[Kafka 協定使用事件中心進行數據流,該協定](event-hubs-quickstart-kafka-enabled-event-hubs.md)提供有關如何流式傳輸到事件中心的分步說明。 您學習如何使用生產者和消費者與事件中心交談,只需在應用程式中更改配置。 


## <a name="tutorials"></a>教學課程 

### <a name="tutorials-in-github"></a>GitHub 中的教學
請參閱 GitHub 上的以下教學:

| 教學課程 | 描述 | 
| ------------------------- | ----------- | 
| [阿卡](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | 本教學示範如何在不更改協定客戶端或運行自己的群集的情況下將 Akka 流連接到啟用 Kafka 的事件中心。 有兩個使用**JAVA**和**Scala**程式設計語言的單獨教程。 | 
| [連接](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | 本文件將引導您完成將 Kafka Connect 與 Azure 事件中心整合,以及部署基本檔流源和 FileStreamSink 連接器。 雖然這些連接器不用於生產,但它們演示了端到端 Kafka 連接方案,其中 Azure 事件中心偽裝成 Kafka 代理。| 
| [檔案](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | 本文件將引導您透過 Filebeat 的 Kafka 輸出整合 Filebeat 和事件中心。 | 
| [弗林克](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | 本教學將演示如何在不更改協定客戶端或運行自己的群集的情況下將 Apache Flink 連接到啟用 Kafka 的事件中心。 | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | 本文件將引導您使用 Fluentd`out_kafka`的 輸出外掛程式整合 Fluentd 和事件中心。 |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | 本教程介紹如何使用不同的協定在使用者和生產者之間交換事件。 |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | 本教學將引導您使用 Logstash Kafka 輸入/輸出外掛程式將 Logstash 與啟用卡夫卡的事件中心整合。 | 
| [鏡像製造商](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | 本教程演示如何通過鏡像事件中心服務中的 Kafka 輸入流,將現有的 Kafka 管道集成到 Azure 中。 |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | 本教學將演示如何將 Apache NiFi 連接到事件中心命名空間。 | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | 快速入門向您展示如何使用使用 Go 和 Java 程式設計語言編寫的範例生產者和使用者創建和連接到事件中心 Kafka 終結點。 |
| [匯能的架構註冊表](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | 本教學將引導您完成 Kafka 的架構註冊表和事件中心集成。 | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | 本教學將演示如何在不更改協定用戶端或運行自己的 Kafka 群集的情況下將 Spark 應用程式連接到事件中心。 | 

### <a name="tutorials-in-docs"></a>DOCS 中的教學
此外,請參閱教程:使用此內容集中[的流分析處理事件中心事件](event-hubs-kafka-stream-analytics.md),其中演示如何將數據流式傳輸到事件中心並使用 Azure 流分析處理。

## <a name="how-to-guides"></a>操作指南
請參考此文件中的以下操作指南:

| 發行項 | 描述 | 
| ------- | ----------- | 
| [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md) | 展示如何使用卡夫卡鏡像器在事件中心鏡像 Kafka 代理。 |
| [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md) | 引導您完成將Spark應用程式連接到事件中心,進行即時流式傳輸。 |
| [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md) | 演示如何在不更改協定客戶端或運行自己的群集的情況下將 Apache Flink 連接到事件中心。 |
| [將阿帕契卡夫卡連接與事件中心(預覽)](event-hubs-kafka-connect-tutorial.md) | 引導您完成將 Kafka Connect 與事件中心整合,並部署基本的 FileStreamSource 和 FileStreamSink 連接器。 |
| [將 Akka 串流連線到事件中心](event-hubs-kafka-akka-streams-tutorial.md) | 演示如何在不更改協定客戶端或運行自己的群集的情況下將 Akka 流連接到事件中心。 |
| [使用具有 Azure 活動中心的 Apache Kafka 的彈簧啟動器](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | 演示如何配置使用 Spring Boot 初始化器建立的基於 Java 的春雲流綁定器,以便將 Apache Kafka 與 Azure 事件中心一起使用。 |

## <a name="next-steps"></a>後續步驟
在快速入門和教程資料夾中查看 GitHub 儲存庫[Azure 事件中心(用於 kafka)](https://github.com/Azure/azure-event-hubs-for-kafka)中的範例。

