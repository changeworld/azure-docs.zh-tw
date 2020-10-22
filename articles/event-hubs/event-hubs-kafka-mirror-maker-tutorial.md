---
title: 使用 Apache Kafka MirrorMaker - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何使用 Kafka MirrorMaker 為「Azure 事件中樞」中的 Kafka 叢集建立鏡像的資訊。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: d1ec20a32ef27856483492212608e20e82725f58
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369517"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>使用 Kafka MirrorMaker 搭配適用於 Apache Kafka 的事件中樞

本教學課程說明如何使用 Kafka MirrorMaker，在事件中樞內鏡像 Kafka 訊息代理程式。

   ![具有事件中樞的 Kafka MirrorMaker](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) 上取得此範例


在本教學課程中，您會了解如何：
> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 複製範例專案
> * 設定 Kafka 叢集
> * 設定 Kafka MirrorMaker
> * 執行 Kafka MirrorMaker

## <a name="introduction"></a>簡介
新式雲端規模應用程式的一個主要考量在於能夠更新、改善，以及變更基礎結構，而不會中斷服務。 本教學課程說明事件中樞和 Kafka MirrorMaker 如何在事件中樞服務中「鏡像」 Kafka 輸入資料流程，以將現有的 Kafka 管線整合到 Azure。 

Azure 事件中樞 Kafka 端點可讓您使用 Kafka 通訊協定 (亦即 Kafka 用戶端) 連線到 Azure 事件中樞。 幾乎不需要變更 Kafka 應用程式，您就可以連線到 Azure 事件中樞，並享受 Azure 生態系統的優點。 事件中樞目前支援 Kafka 1.0 版和更新版本。

## <a name="prerequisites"></a>必要條件

若要完成本教學課程，請確定您具有下列項目︰

* 請參閱[適用於 Apache Kafka 的事件中樞](event-hubs-for-kafka-ecosystem-overview.md)一文。 
* Azure 訂用帳戶。 如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* [JAVA 開發工具組 (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * 在 Ubuntu 上，執行 `apt-get install default-jdk` 來安裝 JDK。
    * 務必設定 JAVA_HOME 環境變數，以指向 JDK 安裝所在的資料夾。
* [下載](https://maven.apache.org/download.cgi) 並 [安裝](https://maven.apache.org/install.html) Maven 二進位封存
    * 在 Ubuntu 上，您可以執行 `apt-get install maven` 來安裝 Maven。
* [Git](https://www.git-scm.com/downloads)
    * 在 Ubuntu 上，您可以執行 `sudo apt-get install git` 來安裝 Git。

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間

您需要事件中樞命名空間，才能從任何事件中樞服務傳送和接收。 請參閱 [建立事件中樞](event-hubs-create.md) ，以取得建立命名空間和事件中樞的指示。 請務必複製事件中樞連接字串以供稍後使用。

## <a name="clone-the-example-project"></a>複製範例專案

現在您已有事件中樞連接字串，請複製 Kafka 儲存機制的 Azure 事件中樞，然後流覽至 `mirror-maker` 子資料夾：

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>設定 Kafka 叢集

使用 [Kafka 快速入門指南](https://kafka.apache.org/quickstart) \(英文\) 使用所需的設定來設定叢集 (或使用現有的 Kafka 叢集)。

## <a name="configure-kafka-mirrormaker"></a>設定 Kafka MirrorMaker

Kafka MirrorMaker 可以「鏡像」資料流。 假設有來源和目的地 Kafka 叢集，MirrorMaker 可確保來源和目的地叢集都會收到傳送至來源叢集的所有訊息。 此範例說明如何使用目的地事件中樞來鏡像來源 Kafka 叢集。 此案例可用來將資料從現有的 Kafka 管線傳送至事件中樞，而不會中斷資料流程。 

如需有關 Kafka MirrorMaker 的詳細資訊，請參閱 [Kafka 鏡像/MirrorMaker 指南](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)。

若要設定 Kafka MirrorMaker，請為它提供一個 Kafka 叢集作為其取用者/來源，並提供一個事件中樞作為其生產者/目的地。

#### <a name="consumer-configuration"></a>取用者設定

更新取用者設定檔 `source-kafka.config`，此檔案會告訴 MirrorMaker 來源 Kafka 叢集的屬性。

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>生產者設定

現在更新生產者設定檔 `mirror-eventhub.config`，此檔案會告訴 MirrorMaker 將重複 (或「鏡像」) 的資料傳送至事件中樞服務。 具體來說，將 `bootstrap.servers` 和 `sasl.jaas.config` 變更為指向您的事件中樞 Kafka 端點。 事件中樞服務需要安全 (SASL) 通訊，在下列設定中設定最後三個屬性即可達到這個目的： 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> 將 `{YOUR.EVENTHUBS.CONNECTION.STRING}` 取代為事件中樞命名空間的連接字串。 如需取得連接字串的指示，請參閱 [取得事件中樞連接字串](event-hubs-get-connection-string.md)。 以下是範例設定： `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>執行 Kafka MirrorMaker

使用剛更新的設定檔，從 Kafka 根目錄中執行 Kafka MirrorMaker 指令碼。 請務必將設定檔複製到 Kafka 根目錄，或更新它在下列命令中的路徑。

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

若要確認事件會到達事件中樞，請參閱 [Azure 入口網站](https://azure.microsoft.com/features/azure-portal/)中的輸入統計資料，或對事件中樞執行取用者。

當 MirrorMaker 執行時，傳送至來源 Kafka 叢集的任何事件都會由 Kafka 叢集和鏡像事件中樞接收。 您可以使用 MirrorMaker 和事件中樞 Kafka 端點，將現有的 Kafka 管線移轉至受控 Azure 事件中樞服務，而不會變更現有的叢集或中斷任何進行中的資料流程。

## <a name="samples"></a>範例
請參閱 GitHub 上的下列範例：

- [在 GitHub 上進行本教學課程的範例程式碼](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [在 Azure 容器實例上執行的 Azure 事件中樞 Kafka MirrorMaker](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>後續步驟
若要深入瞭解適用于 Kafka 的事件中樞，請參閱下列文章：  

- [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md)
- [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md)
- [整合 Kafka Connect 與事件中樞](event-hubs-kafka-connect-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
- [將 Akka 串流連線到事件中樞](event-hubs-kafka-akka-streams-tutorial.md)
- [Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)