---
title: 在 Azure HDInsight 中使用 Confluent 架構登錄的 Apache Kafka
description: 使用虛擬網路內的邊緣節點來部署 HDInsight 受控 Kafka 叢集，然後在邊緣節點上安裝 Confluent 架構登錄。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: bb46bc18469638416ff76f84516498e0076c85fd
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/23/2020
ms.locfileid: "95500317"
---
# <a name="apache-kafka-with-confluent-schema-registry-in-azure-hdinsight"></a>在 Azure HDInsight 中使用 Confluent 架構登錄的 Apache Kafka

Kafka Schema Registry 提供的序列化程式，會插入 Apache Kafka 用戶端來處理訊息架構儲存，以及抓取以 Avro 格式傳送的 Kafka 訊息。 它是 Confluent 的 OSS 專案，但現在是在 [Confluent 的社區授權](https://www.confluent.io/blog/license-changes-confluent-platform/)下。 架構登錄另外提供下列用途：

* 儲存和取出生產者和取用者的架構。
* 在主題上強制執行回溯/向前/full 相容性。
* 減少傳送至 Kafka 的承載大小。

在 HDInsight 受控 Kafka 叢集中，架構登錄通常會部署在邊緣節點上，以允許來自前端節點的計算分隔。

以下是在 HDInsight 叢集上部署 Schema Registry 的代表性架構。 架構登錄會以原生方式公開作業的 REST API。  生產者和取用者可以從 VNet 內或使用 [KAFKA REST Proxy](rest-proxy.md)，與架構登錄互動。

![HDInsight Kafka 架構登錄](./media/schema-registry/pic1.png)

## <a name="deploy-an-hdinsight-managed-kafka-with-confluent-schema-registry"></a>使用 Confluent Schema Registry 部署 HDInsight 受控 Kafka

在本節中，我們會在虛擬網路內部署具有邊緣節點的 HDInsight 受控 Kafka 叢集，然後在邊緣節點上安裝 Confluent Schema Registry。  

1. 選取下方的 [部署至 Azure]  按鈕來登入 Azure，並開啟 Resource Manager 範本。

    [![部署至 Azure](./media/schema-registry/hdi-deploy-to-azure1.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Farnabganguly%2FKafkaschemaregistry%2Fmaster%2Fazuredeploy.json)

1. 在 [自訂部署] 範本中，填入欄位，如下所述：

    |屬性 |描述 |
    |---|---|
    |訂用帳戶|從下拉式清單中，選取用於此叢集的 Azure 訂用帳戶。|
    |資源群組|從下拉式清單中選取現有資源群組，或選取 [新建]。|
    |區域|從下拉式清單中，選取要在其中建立叢集的區域。|
    |叢集名稱|輸入全域唯一名稱。 或保持原狀以使用預設名稱。|
    |叢集登入使用者名稱|提供使用者名稱，預設值為 **admin**。|
    |叢集登入密碼|請提供密碼。|
    |SSH 使用者名稱|提供使用者名稱，預設值為 **sshuser**。|
    |SSH 密碼|請提供密碼。|

    將其他欄位保留原狀。 選取 [檢閱 + 建立] 以繼續執行。

1. 檢查部署詳細資料，然後選取 [ **建立** ] 以初始化部署。 部署可能需要45分鐘的時間才能完成。

## <a name="configure-the-confluent-schema-registry"></a>設定 Confluent Schema Registry

在本節中，我們會設定在邊緣節點上安裝的 Confluent Kafka Schema Registry。 Confluent 架構登錄位於  `/etc/schema-registry/schema-registry.properties` ，以及啟動和停止服務可執行檔的機制位於  `/usr/bin/` 資料夾。

架構註冊必須知道 Zookeeper 服務可以與 HDInsight Kafka 叢集互動。 請遵循下列步驟來取得 Zookeeper 仲裁的詳細資料。

1. 使用 [ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md) 連接到您的叢集邊緣節點。 編輯以下命令並將 CLUSTERNAME 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@schema-registry.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 設定密碼變數。 以叢集登入密碼取代密碼，然後輸入下列命令：

    ```bash
    export password='PASSWORD'
    ```

1. 擷取正確大小寫的叢集名稱。 執行下列命令：

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

1. 將 Kafka Zookeeper 主機解壓縮。 執行下列命令：

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKAZKHOSTS
    ```

    請記下此值，因為稍後將會用到此值。

1. 將 Kafka 訊息代理程式主機解壓縮。 執行下列命令：

    ```bash
    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);

    echo $KAFKABROKERS
    ```

1. 在編輯模式中開啟架構登錄屬性檔。 執行下列命令：

    ```bash
    sudo nano /etc/schema-registry/schema-registry.properties
    ```

    1. `kafkastore.connection.url`使用稍早所識別的 Zookeeper 字串來更新的值。
    1. 將的值更新 `debug` 為 `true` 。

    屬性檔現在看起來像這樣：  

    ```
    listeners=http://0.0.0.0:8081
    kafkastore.connection.url=zk1-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181,zk2-ag4kaf.q2hwzr1xkxjuvobkaagmjjkhta.gx.internal.cloudapp.net:2181
    kafkastore.topic=_schemas
    debug=true
    ```

1. 若要儲存檔案，請使用 **Ctrl + X**、**Y** 和 **Enter** 鍵。

1. 啟動架構登錄，並將其指向使用更新的架構登錄屬性檔。 執行下列命令：

    ```bash
    cd /bin
    sudo schema-registry-start /etc/schema-registry/schema-registry.properties
    ```

1. 在一個 SSH 會話中執行架構登錄之後，啟動另一個 SSH 視窗。  在主體「Kafka 金鑰」下註冊新版本的架構，並記下輸出：

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}'
    ```

    ```output
       HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:33:04 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. 在主體「Kafka-值」下註冊新版本的架構，並記下輸出：

    ```cmd
    curl -X POST -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        --data '{"schema": "{\"type\": \"string\"}"}' \
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:18 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 9
    Server: Jetty(9.2.24.v20180105)
    ```

1. 列出所有主體並檢查輸出：

    ```cmd
    curl -X GET -i -H "Content-Type: application/vnd.schemaregistry.v1+json" \
        http://localhost:8081/subjects
    ```

    ```output
    HTTP/1.1 200 OK
    Date: Sun, 22 Mar 2020 16:34:39 GMT
    Content-Type: application/vnd.schemaregistry.v1+json
    Content-Length: 27
    Server: Jetty(9.2.24.v20180105)

    ["Kafka-value","Kafka-key"]
    ```

您可能會想要嘗試此處列出的一些其他 [advanced 命令](https://docs.confluent.io/1.0/schema-registry/docs/intro.html#quickstart)。

## <a name="send-and-consume-avro-data-from-kafka-using-schema-registry"></a>使用 schema registry 從 Kafka 傳送和取用 Avro 資料

在本節中，我們將從標準輸入讀取資料，並將其以格式寫入至 Kafka 主題。 接著，我們將使用具有 Avro 格式器的取用者，從 Kafka 主題讀取資料，以將該資料轉換為可讀取的格式。

1. 建立 Kafka 主題 `agkafkaschemareg` 。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic agkafkaschemareg --zookeeper $KAFKAZKHOSTS
    ```

1. 您可以使用 **Kafka Avro 主控台** 產生者來建立架構、將架構指派給主題，以及開始將資料傳送至主題的 Avro 格式。 確定已成功建立上一個步驟中的 Kafka 主題，且 **$KAFKABROKERS** 有其中的值。

    我們要傳送的架構是一組索引鍵：值配對

    ```
    Value
    {
      "type": "record",
      "name": "example_schema",
      "namespace": "com.example",
      "fields": [
        {
          "name": "cust_id",
          "type": "int",
          "doc": "Id of the customer account"
        },
        {
          "name": "year",
          "type": "int",
          "doc": "year of expense"
        },
        {
          "name": "expenses",
          "type": {
            "type": "array",
            "items": "float"
          },
          "doc": "Expenses for the year"
        }
      ],
      "doc:": "A basic schema for storing messages"
    }
    ```

    使用下列命令來啟動 **Kafka Avro 主控台生產者**：

    ```bash
    /usr/bin/kafka-avro-console-producer     --broker-list $KAFKABROKERS     --topic agkafkaschemareg     --property parse.key=true --property key.schema='{"type" : "int", "name" : "id"}'     --property value.schema='{ "type" : "record", "name" : "example_schema", "namespace" : "com.example", "fields" : [ { "name" : "cust_id", "type" : "int", "doc" : "Id of the customer account" }, { "name" : "year", "type" : "int", "doc" : "year of expense" }, { "name" : "expenses", "type" : {"type": "array", "items": "float"}, "doc" : "Expenses for the year" } ], "doc:" : "A basic schema for storing messages" }'
    ```

1. 當生產者準備接受訊息時，請以預先定義的 Avro 架構格式開始傳送訊息。 使用 Tab 鍵來建立索引鍵和值之間的間距。

    ```
    1 TAB {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2 TAB {"cust_id":3535353, "year":2011, "expenses":[761.35, 92.18, 14.41]}
    3 TAB {"cust_id":7979797, "year":2011, "expenses":[4489.00]}
    ```

1. 嘗試在主控台生產者中輸入隨機的非架構資料，以查看生產者現在如何允許不符合預先定義 Avro 架構的任何資料。

    ```
    1       {"cust_id":1313131, "year":2012, "expenses":[1313.13, 2424.24]}
    2       {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]}
    org.apache.kafka.common.errors.SerializationException: Error deserializing json {"cust_id":1313131,"cust_age":34 "year":2012, "expenses":[1313.13, 2424.24,34.212]} to Avro of schema {"type":"record","name":"example_schema","namespace":"com.example","fields":[{"name":"cust_id","type":"int","doc":"Id of the customer account"},{"name":"year","type":"int","doc":"year of expense"},{"name":"expenses","type":{"type":"array","items":"float"},"doc":"Expenses for the year"}],"doc:":"A basic schema for storing messages"}
    Caused by: org.codehaus.jackson.JsonParseException: Unexpected character ('"' (code 34)): was expecting comma to separate OBJECT entries
     at [Source: java.io.StringReader@3561c410; line: 1, column: 35]
            at org.codehaus.jackson.JsonParser._constructError(JsonParser.java:1433)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportError(JsonParserMinimalBase.java:521)
            at org.codehaus.jackson.impl.JsonParserMinimalBase._reportUnexpectedChar(JsonParserMinimalBase.java:442)
            at org.codehaus.jackson.impl.ReaderBasedParser.nextToken(ReaderBasedParser.java:406)
            at org.apache.avro.io.JsonDecoder.getVaueAsTree(JsonDecoder.java:549)
            at org.apache.avro.io.JsonDecoder.doAction(JsonDecoder.java:474)
            at org.apache.avro.io.parsing.Parser.advance(Parser.java:88)
            at org.apache.avro.io.JsonDecoder.advance(JsonDecoder.java:139)
            at org.apache.avro.io.JsonDecoder.readInt(JsonDecoder.java:166)
            at org.apache.avro.io.ValidatingDecoder.readInt(ValidatingDecoder.java:83)
            at org.apache.avro.generic.GenericDatumReader.readInt(GenericDatumReader.java:511)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:182)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.readField(GenericDatumReader.java:240)
            at org.apache.avro.generic.GenericDatumReader.readRecord(GenericDatumReader.java:230)
            at org.apache.avro.generic.GenericDatumReader.readWithoutConversion(GenericDatumReader.java:174)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:152)
            at org.apache.avro.generic.GenericDatumReader.read(GenericDatumReader.java:144)
            at io.confluent.kafka.formatter.AvroMessageReader.jsonToAvro(AvroMessageReader.java:213)
            at io.confluent.kafka.formatter.AvroMessageReader.readMessage(AvroMessageReader.java:200)
            at kafka.tools.ConsoleProducer$.main(ConsoleProducer.scala:59)
            at kafka.tools.ConsoleProducer.main(ConsoleProducer.scala)
    
    ```

1. 在不同的畫面中，啟動 Kafka Avro 主控台取用者

    ```bash
    sudo /usr/bin/kafka-avro-console-consumer --bootstrap-server $KAFKABROKERS --topic agkafkaschemareg --from-beginning
    ```

    您應該會開始看到下列輸出：

    ```output
    {"cust_id":1313131,"year":2012,"expenses":[1313.13,2424.24]}
    {"cust_id":7979797,"year":2011,"expenses":[4489.0]}
    {"cust_id":3535353,"year":2011,"expenses":[761.35,92.18,14.41]}
    ```

## <a name="next-steps"></a>後續步驟

* [使用 Apache Kafka 生產者和取用者 Api](apache-kafka-producer-consumer-api.md)