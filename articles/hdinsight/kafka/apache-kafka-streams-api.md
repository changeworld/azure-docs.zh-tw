---
title: '教學課程：使用 Apache Kafka 串流 API - Azure HDInsight '
description: 教學課程 - 了解如何搭配使用 Apache Kafka 串流 API 與 HDInsight 上的 Kafka。 此 API 可讓您在 Kafka 主題之間執行串流處理。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 0174c40a0fada0f78cc8d52f5c45b991c3851da0
ms.sourcegitcommit: cec9676ec235ff798d2a5cad6ee45f98a421837b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85850553"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>教學課程：在 Azure HDInsight 中使用 Apache Kafka 串流 API

了解如何建立應用程式，該應用程式會使用 Apache Kafka 串流 API，並且與 HDInsight 上的 Kafka 搭配使用。

本教學課程中使用的應用程式是串流字數統計程式。 它會讀取 Kafka 主題中的文字資料、擷取個別文字，然後將文字和字數儲存到另一個 Kafka 主題中。

Kafka 串流處理通常會使用 Apache Spark 或 Apache Storm 來完成。 Kafka 1.1.0 版 (在 HDInsight 3.5 和 3.6 中) 導入了 Kafka 串流 API。 此 API 可讓您轉換輸入和輸出主題之間的資料流。 在某些情況下，這可能是建立 Spark 或 Storm 串流解決方案的替代方案。

如需有關 Kafka 串流的詳細資訊，請參閱 Apache.org 上的[串流簡介](https://kafka.apache.org/10/documentation/streams/)文件。

在本教學課程中，您會了解如何：

> [!div class="checklist"]
> * 了解程式碼
> * 建置並部署應用程式
> * 設定 Kafka 主題
> * 執行程式碼

## <a name="prerequisites"></a>Prerequisites

* HDInsight 3.6 叢集上的 Kafka。 若要深入了解如何建立 HDInsight 上的 Apache Kafka 叢集，請參閱[開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md) 文件。

* 完成 [Apache Kafka Consumer 和 Producer API](apache-kafka-producer-consumer-api.md) 文件中的步驟。 此文件中的步驟會在本教學課程中建立的範例應用程式和主題。

* [Java Developer Kit (JDK) 第 8 版](https://aka.ms/azure-jdks)或同等版本，例如 OpenJDK。

* 根據 Apache 正確[安裝](https://maven.apache.org/install.html)的 [Apache Maven](https://maven.apache.org/download.cgi)。  Maven 是適用於 Java 專案的專案建置系統。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="understand-the-code"></a>了解程式碼

範例應用程式位於 [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) 的 `Streaming` 子目錄中。 該應用程式包含兩個檔案：

* `pom.xml`:此檔案會定義專案相依性、Java 版本和封裝方法。
* `Stream.java`:此檔案會實作串流邏輯。

### <a name="pomxml"></a>Pom.xml

在 `pom.xml` 檔案中務必要了解的事項包括：

* 相依性：此專案依存於 `kafka-clients` 套件所提供的「Kafka 串流 API」。 下列 XML 程式碼會定義此相依性：

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}` 項目會在 `pom.xml` 的 `<properties>..</properties>` 區段中進行宣告，並設定為 HDInsight 叢集的 Kafka 版本。

* 外掛程式：Maven 外掛程式可提供多種功能。 在此專案中，會使用下列外掛程式：

    * `maven-compiler-plugin`:用來將專案所使用的 Java 版本設為 8。 HDInsight 3.6 需要 Java 8。
    * `maven-shade-plugin`:用來產生包含此應用程式及任何相依性的 uber jar。 它也可用來設定應用程式的進入點，如此您即可直接執行 Jar 檔案，而不需要指定主要類別。

### <a name="streamjava"></a>Stream.java

[Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) 檔案會使用串流 API 來實作字數統計應用程式。 它會從名為 `test` 的 Kafka 主題中讀取資料，並將字數統計寫入名為 `wordcounts` 的主題中。

下列程式碼會定義字數統計應用程式：

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>建置並部署範例

若要建置專案，並將其部署至 HDInsight 叢集上的 Kafka，請使用下列步驟：

1. 將目前的目錄設定為 `hdinsight-kafka-java-get-started-master\Streaming` 目錄的位置，然後使用下列命令來建立 jar 套件︰

    ```cmd
    mvn clean package
    ```

    此命令會在 `target/kafka-streaming-1.0-SNAPSHOT.jar` 上建立套件。

2. 將 `sshuser` 取代為叢集的 SSH 使用者，並將 `clustername` 取代為叢集的名稱。 使用下列命令將 `kafka-streaming-1.0-SNAPSHOT.jar` 檔案複製到 HDInsight 叢集。 出現提示時，請輸入 SSH 使用者帳戶的密碼。

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>建立 Apache Kafka 主題

1. 將 `sshuser` 取代為叢集的 SSH 使用者，並將 `CLUSTERNAME` 取代為叢集的名稱。 輸入下列命令，開啟與叢集的 SSH 連線。 出現提示時，請輸入 SSH 使用者帳戶的密碼。

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 安裝 [jq](https://stedolan.github.io/jq/)，這是命令列 JSON 處理器。 從開啟的 SSH 連線，輸入下列命令來安裝 `jq`：

    ```bash
    sudo apt -y install jq
    ```

3. 設定密碼變數。 請將 `PASSWORD` 取代為叢集登入密碼，然後輸入下列命令：

    ```bash
    export password='PASSWORD'
    ```

4. 擷取正確大小寫的叢集名稱。 視叢集的建立方式而定，叢集名稱的實際大小寫可能與您預期的不同。 此命令會取得實際的大小寫，然後將其儲存在變數中。 輸入下列命令：

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > 如果您是從叢集外部執行此程序，則應以不同的程序儲存叢集名稱。 請從 Azure 入口網站取得小寫的叢集名稱。 然後，在下列命令中，以叢集名稱取代 `<clustername>`，並執行命令：`export clusterName='<clustername>'`。  

5. 若要取得 Kafka 代理程式主機和 Apache Zookeeper 主機，請使用下列命令。 出現提示時，輸入叢集登入 (admin) 帳戶的密碼。

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > 這些命令需要存取 Ambari。 如果您的叢集位於 NSG 後方，請從可存取 Ambari 的機器執行這些命令。

6. 若要建立串流作業所使用的主題，請使用下列命令：

    > [!NOTE]  
    > 您可能會收到指出 `test` 主題已存在的錯誤。 這是正常的，因為該主題可能已在「Producer 和 Consumer API」教學課程中建立。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    這些主題的用途如下：

   * `test`:此主題是接收記錄之處。 串流應用程式會從中進行讀取。
   * `wordcounts`:此主題是串流應用程式儲存其輸出之處。
   * `RekeyedIntermediateTopic`:此主題可在 `countByKey` 運算子更新計數時用來重新分割資料。
   * `wordcount-example-Counts-changelog`:此主題是 `countByKey` 作業所使用的狀態存放區

    HDInsight 上的 Kafka 也可設定為自動建立主題。 如需詳細資訊，請參閱[設定自動建立主題功能](apache-kafka-auto-create-topics.md)文件。

## <a name="run-the-code"></a>執行程式碼

1. 若要以背景程序的形式啟動串流應用程式，請使用下列命令：

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    您可能會收到關於 Apache log4j 的警告。 您可以忽略此警告。

2. 若要將記錄傳送至 `test` 主題，請使用下列命令啟動產生器應用程式：

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. 產生器執行完成後，請使用下列命令檢視 `wordcounts` 主題中儲存的資訊：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    `--property` 參數會告知主控台取用者列印索引鍵 (字組) 和計數 (值)。 這個參數也會設定從 Kafka 讀取這些值時要使用的還原序列化。

    輸出大致如下：

    ```output
    dwarfs  13635
    ago     13664
    snow    13636
    dwarfs  13636
    ago     13665
    a       13803
    ago     13666
    a       13804
    ago     13667
    ago     13668
    jumped  13640
    jumped  13641
    ```

    參數 `--from-beginning` 會設定讓取用者從主題中儲存的記錄開頭處開始執行。 每遇到一個字，字數就會增加一個，因此主題中會包含每個字的多個項目，且計數會遞增。

4. 使用 __Ctrl + C__ 來結束產生者。 繼續使用 __Ctrl + C__ 來結束應用程式和取用者。

5. 若要刪除串流作業所使用的主題，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>清除資源

若要清除本教學課程所建立的資源，您可以刪除資源群組。 刪除資源群組也會刪除相關聯的 HDInsight 叢集，以及與資源群組相關聯的任何其他資源。

若要使用 Azure 入口網站移除資源群組：

1. 在 Azure 入口網站中展開左側功能表，以開啟服務的功能表，然後選擇 [資源群組]  以顯示資源群組的清單。
2. 找出要刪除的資源群組，然後以滑鼠右鍵按一下清單右側的 [更多]  按鈕 (...)。
3. 選取 [刪除資源群組]  ，並加以確認。

## <a name="next-steps"></a>後續步驟

在本文件中，您會了解如何搭配 HDInsight 上的 Kafka 使用 Apache Kafka 串流 API。 使用下列各項來深入了解 Kafka 的使用方式。

> [!div class="nextstepaction"]
> [分析 Apache Kafka 記錄](apache-kafka-log-analytics-operations-management.md)
