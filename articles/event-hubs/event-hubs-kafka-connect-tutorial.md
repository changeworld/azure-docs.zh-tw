---
title: 與 Apache Kafka Connect 整合 - Azure 事件中樞 | Microsoft Docs
description: 本文提供有關如何將 Apache Spark 與「適用於 Kafka 的 Azure 事件中樞」搭配使用的資訊。
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 2e7a6b406b6d33c94c6fddea2f73b70c24f45f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85320167"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>在 Azure 事件中樞上整合 Apache Kafka Connect 支援 (預覽)
隨著擷取商務需求的情況增加，對於擷取各種外部來源和接收的需求也隨之增加。 [Apache Kafka Connect](https://kafka.apache.org/documentation/#connect) 會提供這類架構，以便在任何外部系統 (例如，MySQL、HDFS 以及透過 Kafka 叢集的檔案系統) 往返連線和匯入/匯出資料。 本教學課程會逐步引導您使用 Kafka Connect framework 與事件中樞。

本教學課程會逐步引導您將 Kafka Connect 與事件中樞整合，以及部署基本的 FileStreamSource 和 FileStreamSink 連接器。 此功能目前為預覽狀態。 雖然這些連接器並非用於生產環境，但可示範 Azure 事件中樞會作為 Kafka 訊息代理程式的端對端 Kafka Connect 案例。

> [!NOTE]
> 您可在 [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) 上取得此範例。

在本教學課程中，您會執行下列步驟：

> [!div class="checklist"]
> * 建立事件中樞命名空間
> * 複製範例專案
> * 設定事件中樞的 Kafka Connect
> * 執行 Kafka Connect
> * 建立連接器

## <a name="prerequisites"></a>Prerequisites
若要完成本逐步解說，請確定您具有下列必要條件：

- Azure 訂用帳戶。 如果您沒有訂用帳戶，請[建立免費帳戶](https://azure.microsoft.com/free/)。
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Kafka 版本 (1.1.1 版，Scala 2.11 版)，取得來源為 [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- 請參閱[適用於 Apache Kafka 的事件中樞](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview)簡介文章

## <a name="create-an-event-hubs-namespace"></a>建立事件中樞命名空間
您需要事件中樞命名空間，才能從任何事件中樞服務傳送和接收。 如需建立命名空間和事件中樞的指示，請參閱[建立事件中樞](event-hubs-create.md)。 請取得事件中樞連接字串和完整網域名稱 (FQDN) 以供稍後使用。 如需相關指示，請參閱[取得事件中樞連接字串](event-hubs-get-connection-string.md)。 

## <a name="clone-the-example-project"></a>複製範例專案
請複製 Azure 事件中樞存放庫，並瀏覽至 tutorials/connect 子資料夾： 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>設定事件中樞的 Kafka Connect
將 Kafka Connect 輸送量從 Kafka 重新導向至事件中樞時，需要稍微重新設定。  下列 `connect-distributed.properties` 範例說明如何設定 Connect，以向事件中樞上的 Kafka 端點進行驗證與通訊：

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>執行 Kafka Connect

在此步驟中，Kafka Connect 背景工作角色會以分散模式在本機啟動，使用事件中樞來維持叢集的狀態。

1. 將上述 `connect-distributed.properties` 檔案儲存在本機。  請務必取代括號中的所有值。
2. 瀏覽至機器上的 Kafka 版本位置。
4. 執行 `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`。  當您看到 `'INFO Finished starting connectors and tasks'` 時，Connect 背景工作角色 REST API 便可供互動。 

> [!NOTE]
> Kafka Connect 會使用 Kafka AdminClient API，以使用建議的設定 (包括壓縮) 自動建立主題。 在 Azure 入口網站中快速檢查命名空間，會顯露出 Connect 背景工作角色的內部主題已自動建立。
>
>Kafka Connect 內部主題**必須使用壓縮**。  若未正確設定 [內部連線] 主題，事件中樞小組將不會負責修正不當的設定。

### <a name="create-connectors"></a>建立連接器
本節會逐步引導您啟動 FileStreamSource 和 FileStreamSink 連接器。 

1. 建立輸入和輸出資料檔案的目錄。
    ```bash
    mkdir ~/connect-quickstart
    ```

2. 建立兩個檔案：一個檔案具有種子資料可供 FileStreamSource 連接器讀取，另一個檔案則可供 FileStreamSink 連接器寫入。
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. 建立 FileStreamSource 連接器。  請務必將大括號取代為主目錄路徑。
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    執行上述命令之後，您應該就會在事件中樞執行個體上看到事件中樞的 `connect-quickstart`。
4. 檢查來源連接器的狀態。
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    或者，您也可以使用[服務匯流排總管](https://github.com/paolosalvatori/ServiceBusExplorer/releases)來驗證事件已抵達 `connect-quickstart` 主題。

5. 建立 FileStreamSink 連接器。  同樣地，請務必將大括號取代為主目錄路徑。
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. 檢查接收連接器的狀態。
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. 確認已在檔案之間複寫資料，且該資料在這兩個檔案是相同的。
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>清理
Kafka Connect 會建立事件中樞主題，以儲存即使在 Connect 叢集關閉後仍會保存下來的設定、位移及狀態。 除非需要此持續性，否則建議您刪除這些主題。 您也可以刪除在本逐步解說進行過程中所建立的 `connect-quickstart` 事件中樞。

## <a name="next-steps"></a>後續步驟

若要深入瞭解 Kafka 的事件中樞，請參閱下列文章：  

- [在事件中樞中鏡像 Kafka 訊息代理程式](event-hubs-kafka-mirror-maker-tutorial.md)
- [將 Apache Spark 連線到事件中樞](event-hubs-kafka-spark-tutorial.md)
- [將 Apache Flink 連線到事件中樞](event-hubs-kafka-flink-tutorial.md)
- [在 GitHub 上探索範例](https://github.com/Azure/azure-event-hubs-for-kafka) \(英文\)
- [將 Akka streams 串流連線至事件中樞](event-hubs-kafka-akka-streams-tutorial.md)
- [適用于 Azure 事件中樞的 Apache Kafka 開發人員指南](apache-kafka-developer-guide.md)
