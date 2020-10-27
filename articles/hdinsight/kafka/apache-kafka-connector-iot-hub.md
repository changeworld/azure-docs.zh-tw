---
title: 搭配使用 HDInsight 上的 Apache Kafka 與 Azure IoT 中樞
description: 了解如何搭配使用 HDInsight 上的 Apache Kafka 與 Azure IoT 中樞。 Kafka Connect Azure IoT 中樞專案提供 Kafka 的來源和接收連接器。 來源連接器可從 IoT 中樞讀取資料，接收連接器則可寫入 IoT 中樞。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: c4efb8b4835b7dc828caa49eaf6013a2f58bb081
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/26/2020
ms.locfileid: "92534544"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>搭配使用 HDInsight 上的 Apache Kafka 與 Azure IoT 中樞

了解如何使用 [Apache Kafka Connect Azure IoT 中樞](https://github.com/Azure/toketi-kafka-connect-iothub)連接器在 HDInsight 上的 Apache Kafka 與 Azure IoT 中樞之間移動資料。 在本文件中，您將了解如何從叢集中的邊緣節點執行 IoT 中樞連接器。

Kafka Connect API 可讓您實作持續將資料提取到 Kafka 中，或將資料從 Kafka 推送至另一個系統的連接器。 [Apache Kafka Connect Azure IoT 中樞](https://github.com/Azure/toketi-kafka-connect-iothub)是會將資料從 Azure IoT 中樞提取到 Kafka 中的連接器。 它也可以將資料從 Kafka 推送到 IoT 中樞。

從 IoT 中樞提取時，您會使用 __來源__ 連接器。 推送至 IoT 中樞時，您會使用 __接收__ 連接器。 IoT 中樞連接器同時提供來源和接收連接器。

下圖顯示在使用連接器時，Azure IoT 中樞與 HDInsight 上的 Kafka 之間的資料流程。

![顯示資料透過連接器從 IoT 中樞流入 Kafka 的影像](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

如需 Connect API 的詳細資訊，請參閱 [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) 。

## <a name="prerequisites"></a>必要條件

* HDInsight 上的 Apache Kafka 叢集。 如需詳細資訊，請參閱 [HDInsight 上的 Kafka 快速入門](apache-kafka-get-started.md)文件。

* Kafka 叢集中的邊緣節點。 如需詳細資訊，請參閱[搭配使用邊緣節點與 HDInsight](../hdinsight-apps-use-edge-node.md) 文件。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* Azure IoT 中樞和裝置。 在本文中，請考慮使用 [Connect Raspberry Pi 線上模擬器來 Azure IoT 中樞](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md)。

* [Scala build tool](https://www.scala-sbt.org/)。

## <a name="build-the-connector"></a>建立連接器

1. 從將連接器的來源下載 [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) 到您的本機環境。

2. 從命令提示字元中，流覽至 `toketi-kafka-connect-iothub-master` 目錄。 然後使用下列命令來建立並封裝專案：

    ```cmd
    sbt assembly
    ```

    組建需要幾分鐘的時間才能完成。 此命令會 `kafka-connect-iothub-assembly_2.11-0.7.0.jar` 在專案的目錄中建立名為的檔案 `toketi-kafka-connect-iothub-master\target\scala-2.11` 。

## <a name="install-the-connector"></a>安裝連接器

1. 將 .jar 檔案上傳至 Kafka on HDInsight 叢集的邊緣節點。 `CLUSTERNAME`以您的叢集實際名稱取代，以編輯下列命令。 SSH 使用者帳戶的預設值和 [邊緣節點](../hdinsight-apps-use-edge-node.md#access-an-edge-node) 的名稱會在下面使用，視需要進行修改。

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. 檔案複製完成後，請使用 SSH 連線至邊緣節點：

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 若要將連接器安裝到 Kafka`libs` 目錄中，請使用下列命令：

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

讓 SSH 連線保持在作用中狀態，以進行其餘步驟。

## <a name="configure-apache-kafka"></a>設定 Apache Kafka

從 SSH 連線到邊緣節點，使用下列步驟來設定 Kafka 以獨立模式執行連接器：

1. 設定密碼變數。 以叢集登入密碼取代密碼，然後輸入下列命令：

    ```bash
    export password='PASSWORD'
    ```

1. 安裝 [jq](https://stedolan.github.io/jq/) 公用程式。 jq 可讓您更輕鬆地處理從 Ambari 查詢傳回的 JSON 檔。 輸入下列命令：

    ```bash
    sudo apt -y install jq
    ```

1. 取得 Kafka 訊息代理程式的位址。 您的叢集中可能會有許多訊息代理程式，但您只需要參考其中一或兩個。 若要取得兩個訊息代理程式主機的位址，請使用下列命令：

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    複製值以供稍後使用。 傳回的值類似下列文字︰

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. 取得 Apache Zookeeper 節點的位址。 叢集中可能會有數個 Zookeeper 節點，但您只需要參考其中一或兩個。 使用下列命令將位址儲存在變數中 `KAFKAZKHOSTS` ：

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. 在獨立模式中執行連接器時，會使用 `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` 檔案與 Kafka 訊息代理程式通訊。 若要編輯 `connect-standalone.properties` 檔案，請使用下列命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. 進行下列編輯：

    |目前的值 |新值 | 註解 |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|將值取代為 `localhost:9092` 上一個步驟中的訊息代理程式主機|設定邊緣節點的獨立設定，以尋找 Kafka 訊息代理程式。|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|此變更可讓您使用隨附於 Kafka 的主控台產生者進行測試。 對於其他產生者和取用者，您可能需要不同的轉換器。 如需使用其他轉換器值的詳細資訊，請參閱 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) 。|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|同上。|
    |不適用|`consumer.max.poll.records=10`|新增至檔案結尾。 此變更可將接收連接器限定為一次 10 筆記錄以內，以防止連接器逾時。 如需詳細資訊，請參閱 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。|

1. 若要儲存檔案，請使用 __Ctrl + X__ 、 __Y__ 和 __Enter__ 鍵。

1. 若要建立連接器所使用的主題，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    若要確認 `iotin` 和 `iotout` 主題存在，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` 主題可用來接收來自 IoT 中樞的訊息。 `iotout` 主題可用來將訊息傳送至 IoT 中樞。

## <a name="get-iot-hub-connection-information"></a>取得 IoT 中樞連線資訊

若要擷取連接器所使用 IoT 中樞資訊，請使用下列步驟：

1. 取得 IoT 中樞的事件中樞相容端點和事件中樞相容端點名稱。 若要取得這項資訊，請使用下列其中一個方法：

   * __在 [Azure 入口網站](https://portal.azure.com/)中__ ，使用下列步驟：

     1. 瀏覽至您的 IoT 中樞，並選取 [端點]  。
     2. 在 [內建端點]  中，選取 [事件]  。
     3. 在 [屬性]  中，複製下列欄位的值：

         * __事件中樞相容名稱__
         * __事件中樞相容端點__
         * __資料分割__

        > [!IMPORTANT]  
        > 入口網站中的端點值可能包含在此範例中不需要的多餘文字。 請擷取符合 `sb://<randomnamespace>.servicebus.windows.net/` 模式的文字。

   * __在 [Azure CLI](/cli/azure/get-started-with-azure-cli) 中__ ，使用下列命令：

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       請將 `myhubname` 取代為您的 IoT 中樞名稱。 回應會類似於下列文字：

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. 取得 __共用存取原則__ 和 __金鑰__ 。 在此範例中，請使用 __服務__ 金鑰。 若要取得這項資訊，請使用下列其中一個方法：

    * __在 [Azure 入口網站](https://portal.azure.com/)中__ ，使用下列步驟：

        1. 選取 [共用存取原則]  ，然後選取 [服務]  。
        2. 複製 __主要金鑰__ 值。
        3. 複製 [連接字串 – 主要金鑰]  的值。

    * __在 [Azure CLI](/cli/azure/get-started-with-azure-cli) 中__ ，使用下列命令：

        1. 若要取得主要金鑰值，請使用下列命令：

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            請將 `myhubname` 取代為您的 IoT 中樞名稱。 回應是此中樞之 `service` 原則的主要金鑰。

        2. 若要取得 `service` 原則的連接字串，請使用下列命令：

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            請將 `myhubname` 取代為您的 IoT 中樞名稱。 回應是 `service` 原則的連接字串。

## <a name="configure-the-source-connection"></a>設定來源連線

若要設定使用 IoT 中樞時的來源，請透過邊緣節點的 SSH 連線執行下列動作：

1. 在 `/usr/hdp/current/kafka-broker/config/` 目錄中建立 `connect-iot-source.properties` 檔案的複本。 若要從 toketi-kafka-connect-iothub 專案下載檔案，請使用下列命令：

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. 若要編輯 `connect-iot-source.properties` 檔案並新增 IoT 中樞資訊，請使用下列命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. 在編輯器中找出下列項目，並加以變更：

    |目前的值 |編輯|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 `iotin`。 從 IoT 中樞接收到的訊息會放在 `iotin` 主題中。|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|將 `PLACEHOLDER` 取代為事件中樞相容名稱。|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|將 `PLACEHOLDER` 取代為事件中樞相容端點。|
    |`IotHub.AccessKeyName=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 `service`。|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 `service` 原則的主要金鑰。|
    |`IotHub.Partitions=PLACEHOLDER`|將 `PLACEHOLDER` 取代為前述步驟中的分割區數目。|
    |`IotHub.StartTime=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 UTC 日期。 此日期是連接器開始檢查訊息的時間。 日期格式為 `yyyy-mm-ddThh:mm:ssZ`。|
    |`BatchSize=100`|將 `100` 取代為 `5`。 此變更會使連接器在 IoT 中樞內有五個新訊息之後，將訊息讀取到 Kafka 中。|

    如需範例設定，請參閱 [Azure IoT 中樞的 Kafka Connect 來源連接器](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)。

1. 若要儲存變更，請使用 __Ctrl + X__ 、 __Y__ 和 __Enter__ 鍵。

如需設定連接器來源的詳細資訊，請參閱 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) 。

## <a name="configure-the-sink-connection"></a>設定接收連線

若要設定使用 IoT 中樞時的接收連線，請透過邊緣節點的 SSH 連線執行下列動作：

1. 在 `/usr/hdp/current/kafka-broker/config/` 目錄中建立 `connect-iothub-sink.properties` 檔案的複本。 若要從 toketi-kafka-connect-iothub 專案下載檔案，請使用下列命令：

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. 若要編輯 `connect-iothub-sink.properties` 檔案並新增 IoT 中樞資訊，請使用下列命令：

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. 在編輯器中找出下列項目，並加以變更：

    |目前的值 |編輯|
    |---|---|
    |`topics=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 `iotout`。 寫入 `iotout` 主題的訊息會轉送至 IoT 中樞。|
    |`IotHub.ConnectionString=PLACEHOLDER`|將 `PLACEHOLDER` 取代為 `service` 原則的連接字串。|

    如需範例設定，請參閱 [Azure IoT 中樞的 Kafka Connect 接收器連接器](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)。

1. 若要儲存變更，請使用 __Ctrl + X__ 、 __Y__ 和 __Enter__ 鍵。

如需設定連接器接收的詳細資訊，請參閱 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) 。

## <a name="start-the-source-connector"></a>啟動來源連接器

1. 若要啟動來源連接器，請透過邊緣節點的 SSH 連線使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    連接器啟動後，請從您的裝置將訊息傳送至 IoT 中樞。 連接器從 IoT 中樞讀取訊息並將其儲存於 Kafka 主題時，會將資訊記錄至主控台：

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > 當連接器啟動時，您可能會看到幾則警告。 這些警告不會導致從 IoT 中樞接收訊息的問題。

1. 使用 **Ctrl + C** 兩次，在幾分鐘後停止連接器。 連接器會需要幾分鐘的時間才會停止。

## <a name="start-the-sink-connector"></a>啟動接收連接器

透過邊緣節點的 SSH 連線，使用下列命令在獨立模式中啟動接收連接器：

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

連接器執行時，會顯示類似下列文字的資訊：

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> 當連接器啟動時，您可能會看到幾則警告。 您可以放心地忽略這些警告。

## <a name="send-messages"></a>傳送訊息

若要透過連接器傳送訊息，請使用下列步驟：

1. 開啟 Kafka 叢集 *的第二個* SSH 會話：

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 取得新 ssh 會話的 Kafka 訊息代理程式位址。 以叢集登入密碼取代密碼，然後輸入下列命令：

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. 若要將訊息傳送至 `iotout` 主題，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    此命令不會讓您返回一般 Bash 提示字元。 它會將鍵盤輸入傳送至 `iotout` 主題。

1. 若要將訊息傳送至您的裝置，請將 JSON 文件貼到 `kafka-console-producer` 的 SSH 工作階段中。

    > [!IMPORTANT]  
    > 您必須將 `"deviceId"` 項目的值設定為您的裝置識別碼。 在下列範例中，裝置會命名為 `myDeviceId`：

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    此 JSON 檔的架構在中有更詳細的說明 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) 。

    如果您正在使用模擬的 Raspberry Pi 裝置，而且正在執行中，則裝置會記錄下列訊息：

    ```output
    Receive message: Turn On
    ```

    重新傳送 JSON 文件，但變更 `"message"` 項目的值。 裝置會記錄新的值。

如需使用接收器連接器的詳細資訊，請參閱 [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) 。

## <a name="next-steps"></a>下一步

在本文件中，您已了解如何使用 Apache Kafka Connect API 在 HDInsight 上啟動 IoT Kafka Connector。 使用下列連結來探索使用 Kafka 的其他方式︰

* [在 HDInsight 上搭配使用 Apache Spark 與 Apache Kafka](../hdinsight-apache-spark-with-kafka.md)
* [在 HDInsight 上搭配使用 Apache Storm 與 Apache Kafka](../hdinsight-apache-storm-with-kafka.md)