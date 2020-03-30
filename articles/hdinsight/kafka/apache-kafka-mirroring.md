---
title: 鏡像 Apache Kafka 主題 - Azure HDInsight
description: 了解如何使用 Apache Kafka 的鏡像功能，藉由將主題鏡像處理至次要叢集來維護 HDInsight 叢集上的 Kafka 複本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 45977f52226fac0a3e23455ce9457a721947a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425879"
---
# <a name="use-mirrormaker-to-replicate-apache-kafka-topics-with-kafka-on-hdinsight"></a>使用 MirrorMaker，透過 HDInsight 上的 Kafka 來複寫 Apache Kafka 主題

了解如何使用 Apache Kafka 的鏡像功能，將主題複寫至次要叢集。 鏡像功能可以當作連續程序執行，或間歇地做為在叢集間移轉資料的方法。

在此範例中，會使用鏡像來複寫兩個 HDInsight 叢集之間的主題。 兩個群集位於不同資料中心的不同虛擬網路中。

> [!WARNING]  
> 但不能將鏡像功能視為達成容錯的方法。 主題中的項偏移在主群集和輔助群集之間不同，因此用戶端不能互換使用這兩個。
>
> 如果您很擔心容錯，您應該為叢集內的主題設定複寫。 如需詳細資訊，請參閱 [開始使用 HDInsight 上的 Apache Kafka](apache-kafka-get-started.md)。

## <a name="how-apache-kafka-mirroring-works"></a>Apache Kafka 鏡像的運作方式

鏡像的工作原理是使用[鏡像製造商](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330)工具（Apache Kafka 的一部分）使用主群集上主題的記錄，然後在輔助群集上創建本機複本。 MirrorMaker 使用一個（或多個）從主群集讀取*的消費者*，以及寫入本地（輔助）群集的*生產者*。

災害復原最有用的鏡像設置使用不同的 Azure 區域中的 Kafka 群集。 為此，群集所在的虛擬網路將一起對等。

下圖說明瞭鏡像過程以及群集之間的通信流動方式：

![鏡像程序圖表](./media/apache-kafka-mirroring/kafka-mirroring-vnets2.png)

主群集和輔助群集在節點和分區的數量上可能不同，主題中的偏移量也不同。 鏡像功能會維護用於資料分割的金鑰值，因此會根據每個金鑰保留記錄順序。

### <a name="mirroring-across-network-boundaries"></a>跨網路界限鏡像

如果您需要在不同網路中的 Kafka 叢集之間進行鏡像處理，有下列額外考量︰

* **閘道**：網路必須能夠在 TCP/IP 級別進行通信。

* **伺服器定址**：您可以選擇使用叢集節點的 IP 位址或完全限定的功能變數名稱定址。

    * **IP 位址**：如果將 Kafka 群集配置為使用 IP 位址廣告，則可以使用代理節點和動物園管理員節點的 IP 位址繼續進行鏡像設置。
    
    * **功能變數名稱**：如果您未為 IP 位址廣告配置 Kafka 群集，群集必須能夠使用完全限定功能變數名稱 （FQDN） 相互連接。 這需要每個網路中的網域名稱系統 （DNS） 伺服器配置為將請求轉發到其他網路。 建立 Azure 虛擬網路 (而不是使用網路提供的自動 DNS) 時，您必須指定自訂 DNS 伺服器和伺服器的 IP 位址。 建立虛擬網路之後，您就必須建立使用該 IP 位址的 Azure 虛擬機器，然後在其上安裝和設定 DNS 軟體。

    > [!WARNING]  
    > 先建立和設定自訂 DNS 伺服器，然後再將 HDInsight 安裝到虛擬網路中。 HDInsight 不需要進行其他設定，即可使用針對虛擬網路設定的 DNS 伺服器。

如需有關如何連接兩個 Azure 虛擬網路的詳細資訊，請參閱[設定 VNet 對 VNet 連線](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

## <a name="mirroring-architecture"></a>鏡像體系結構

此體系結構具有不同資源組和虛擬網路中的兩個群集：**主**群集和**輔助**網路。

### <a name="creation-steps"></a>創建步驟

1. 創建兩個新的資源組：

    |資源群組 | Location |
    |---|---|
    | 卡夫卡-初級-rg | 美國中部 |
    | 卡夫卡-中學 | 美國中北部 |

1. 在**卡夫卡-主-rg**中創建新的虛擬網路**kafka-主 vnet。** 保留預設設置。
1. 在**kafka-輔助 rg**中創建新的虛擬網路**kafka-輔助 vnet，** 也使用預設設置。

1. 創建兩個新的卡夫卡集群：

    | 叢集名稱 | 資源群組 | 虛擬網路 | 儲存體帳戶 |
    |---|---|---|---|
    | 卡夫卡主群集 | 卡夫卡-初級-rg | 卡夫卡-主vnet | 卡夫卡主要存儲 |
    | 卡夫卡-輔助集群 | 卡夫卡-中學 | 卡夫卡-二級vnet | 卡夫卡二次存儲 |

1. 創建虛擬網路對等互連。 此步驟將創建兩個對等：一個從**卡夫卡-主vnet**到**卡夫卡-輔助vnet，** 一個從**卡夫卡-輔助vnet**返回卡夫**卡-主vnet。**
    1. 選擇**卡夫卡主 vnet**虛擬網路。
    1. 在 **"設置**"下選擇**對等。**
    1. 選取 [加入]****。
    1. 在 **"添加對等"** 螢幕上，輸入如下螢幕截圖所示的詳細資訊。

        ![HDInsight 卡夫卡添加 vnet 對等互連](./media/apache-kafka-mirroring/hdi-add-vnet-peering.png)

### <a name="configure-ip-advertising"></a>配置 IP 廣告

配置 IP 廣告以使用戶端能夠使用代理 IP 位址而不是功能變數名稱進行連接。

1. 轉到主群集的 Ambari 儀表板： `https://PRIMARYCLUSTERNAME.azurehdinsight.net`。
1. 選擇**服務** > **卡夫卡**。 CliSelectck"**配置"** 選項卡。
1. 將以下配置行添加到底部**kafka-env 範本**部分。 選取 [儲存]****。

    ```
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 在 **"保存配置**"螢幕上輸入注釋，然後按一下"**保存**"。
1. 如果提示您出現配置警告，請按一下"**繼續"。**
1. 在 **"保存配置更改**"上選擇 **"確定**"。
1. 選擇 **"重新開機** > **重新開機所有受影響的"****通知。** 選擇 **"確認全部重新開機**"。

    ![阿帕奇·安巴里重啟所有受影響的](./media/apache-kafka-mirroring/ambari-restart-notification.png)

### <a name="configure-kafka-to-listen-on-all-network-interfaces"></a>配置 Kafka 以偵聽所有網路介面。
    
1. 保持在**服務** > **卡夫卡**下的 **"康菲克"** 選項卡上。 在**Kafka Broker**部分中，**攔截器**屬性設置為`PLAINTEXT://0.0.0.0:9092`。
1. 選取 [儲存]****。
1. 選擇 **"重新開機**"，然後**確認全部重新開機**。

### <a name="record-broker-ip-addresses-and-zookeeper-addresses-for-primary-cluster"></a>記錄主群集的代理 IP 位址和 Zookeeper 位址。

1. 在 Ambari 儀表板上選擇**主機**。
1. 記下經紀人和動物園管理員的 IP 位址。 代理**節點作為主機**名的前兩個字母，Zookeeper 節點的**zk**作為主機名稱的前兩個字母。

    ![阿帕奇·安巴里視圖節點 ip 位址](./media/apache-kafka-mirroring/view-node-ip-addresses2.png)

1. 重複第二個群集**kafka 輔助群集**的前三個步驟：配置 IP 廣告、設置攔截器並記下 Broker 和 Zookeeper IP 位址。

## <a name="create-topics"></a>建立主題

1. 使用 SSH 連接到**主**群集：

    ```bash
    ssh sshuser@PRIMARYCLUSTER-ssh.azurehdinsight.net
    ```

    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **"主集"** 替換為創建群集時使用的基本名稱。

    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 使用以下命令為主群集創建具有 Apache Zookeeper 主機的變數。 喜歡的`ZOOKEEPER_IP_ADDRESS1`字串必須替換為前面記錄的實際 IP 位址，如`10.23.0.11`和`10.23.0.7`。 如果您使用 FQDN 解析與自訂 DNS 伺服器，請按照[以下步驟](apache-kafka-get-started.md#getkafkainfo)獲取代理和動物園管理員名稱：

    ```bash
    # get the zookeeper hosts for the primary cluster
    export PRIMARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181, ZOOKEEPER_IP_ADDRESS2:2181, ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. 若要建立名為 `testtopic` 的主題，請使用下列命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $PRIMARY_ZKHOSTS
    ```

1. 使用下列命令確認已建立主題：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $PRIMARY_ZKHOSTS
    ```

    回應包含 `testtopic`。

1. 使用以下功能查看此（**主**） 群集的 Zookeeper 主機資訊：

    ```bash
    echo $PRIMARY_ZKHOSTS
    ```

    此命令會傳回類似以下文字的資訊：

    `10.23.0.11:2181,10.23.0.7:2181,10.23.0.9:2181`

    請儲存此資訊。 它用於下一節。

## <a name="configure-mirroring"></a>設定鏡像功能

1. 使用其他 SSH 會話連接到**輔助**群集：

    ```bash
    ssh sshuser@SECONDARYCLUSTER-ssh.azurehdinsight.net
    ```

    將 **sshuser** 替換為建立叢集時所使用的 SSH 使用者名稱。 將 **"輔助群集**"替換為創建群集時使用的名稱。

    如需相關資訊，請參閱[搭配 HDInsight 使用 SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)。

1. 檔`consumer.properties`用於配置與**主**群集的通信。 若要建立檔案，請使用下列命令：

    ```bash
    nano consumer.properties
    ```

    使用下列文字做為 `consumer.properties` 檔案的內容：

    ```yaml
    zookeeper.connect=PRIMARY_ZKHOSTS
    group.id=mirrorgroup
    ```

    將**PRIMARY_ZKHOSTS**替換為**主**群集中的 Zookeeper IP 位址。

    此檔描述從主 Kafka 群集讀取時要使用的消費者資訊。 如需取用者組態詳細資訊，請參閱 kafka.apache.org 上的[取用者組態](https://kafka.apache.org/documentation#consumerconfigs)。

    若要儲存檔案，請使用 **Ctrl + X**、**Y** 和 **Enter** 鍵。

1. 在配置與輔助群集通信的生產者之前，為**輔助**群集的代理 IP 位址設置變數。 使用以下命令創建此變數：

    ```bash
    export SECONDARY_BROKERHOSTS='BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092'
    ```

    該命令`echo $SECONDARY_BROKERHOSTS`應返回類似于以下文本的資訊：

    `10.23.0.14:9092,10.23.0.4:9092,10.23.0.12:9092`

1. 檔`producer.properties`用於通信**輔助**群集。 若要建立檔案，請使用下列命令：

    ```bash
    nano producer.properties
    ```

    使用下列文字做為 `producer.properties` 檔案的內容：

    ```yaml
    bootstrap.servers=SECONDARY_BROKERHOSTS
    compression.type=none
    ```

    將**SECONDARY_BROKERHOSTS**替換為上一步驟中使用的代理 IP 位址。

    如需產生者組態詳細資訊，請參閱 kafka.apache.org 上的[者組態](https://kafka.apache.org/documentation#producerconfigs)。

1. 使用以下命令創建具有輔助群集 Zookeeper 主機 IP 位址的環境變數：

    ```bash
    # get the zookeeper hosts for the secondary cluster
    export SECONDARY_ZKHOSTS='ZOOKEEPER_IP_ADDRESS1:2181,ZOOKEEPER_IP_ADDRESS2:2181,ZOOKEEPER_IP_ADDRESS3:2181'
    ```

1. HDInsight 上的 Kafka 的預設配置不允許自動創建主題。 您必須先使用下列其中一個選項，才能啟動鏡像程序：

    * **在輔助群集上創建主題**：此選項還允許您設置分區數和複製因數。

        您可以使用下列命令提前建立主題：

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 2 --partitions 8 --topic testtopic --zookeeper $SECONDARY_ZKHOSTS
        ```

        將 `testtopic` 取代為要建立的主題名稱。

    * **為自動主題創建配置群集**：此選項允許 MirrorMaker 自動創建主題，但它可能會使用與主主題不同的分區或複製因數創建主題。

        要將輔助群集配置為自動創建主題，請執行以下步驟：

        1. 轉到輔助群集的 Ambari 儀表板： `https://SECONDARYCLUSTERNAME.azurehdinsight.net`。
        1. 按一下**服務** > **卡夫卡**。 按一下 [Configs (設定)] **** 索引標籤。
        1. 在 [篩選]____ 欄位中，輸入 `auto.create` 的值。 這會篩選屬性清單並顯示 `auto.create.topics.enable` 設定。
        1. 將 `auto.create.topics.enable` 的值變更為 true，然後選取 [儲存]____。 新增附註，然後再次選取 [儲存]____。
        1. 依序選取 [Kafka]____ 服務、[重新啟動]____ 和 [重新啟動所有受影響的]____。 當出現提示時，選擇 __"確認全部重新開機__"。

        ![卡夫卡啟用自動創建主題](./media/apache-kafka-mirroring/kafka-enable-auto-create-topics.png)

## <a name="start-mirrormaker"></a>啟動 MirrorMaker

1. 從 SSH 連接到**輔助**群集，使用以下命令啟動鏡像製造商進程：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-run-class.sh kafka.tools.MirrorMaker --consumer.config consumer.properties --producer.config producer.properties --whitelist testtopic --num.streams 4
    ```

    此範例中使用的參數：

    |參數 |描述 |
    |---|---|
    |--消費者.配置|指定包含取用者屬性的檔案。 這些屬性用於創建從*主*Kafka 群集讀取的消費者。|
    |--製作人.配置|指定包含產生者屬性的檔案。 這些屬性用於創建寫入*輔助*Kafka 群集的生產者。|
    |--白名單|鏡像 Maker 從主群集複製到輔助群集的主題清單。|
    |--num.流|要建立的取用者執行緒數目。|

    輔助節點上的消費者現在正在等待接收消息。

2. 從 SSH 連接到**主**群集，使用以下命令啟動生產者並向主題發送消息：

    ```bash
    export PRIMARY_BROKERHOSTS=BROKER_IP_ADDRESS1:9092,BROKER_IP_ADDRESS2:9092,BROKER_IP_ADDRESS2:9092
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $SOURCE_BROKERHOSTS --topic testtopic
    ```

     當您抵達有游標的空白行時，請輸入一些文字訊息。 消息將發送到**主**群集上的主題。 完成後，使用 **Ctrl + C** 結束產生者程序。

3. 從 SSH 連接到**輔助**群集，使用**Ctrl + C**結束鏡像製造商過程。 這可能需要數秒鐘的時間才能完成程序。 要驗證消息是否複製到次要資料庫，請使用以下命令：

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $SECONDARY_ZKHOSTS --topic testtopic --from-beginning
    ```

    主題清單現在包括`testtopic`，這是在 MirrorMaster 將主題從主群集鏡像到輔助群集時創建的。 從主題檢索到的消息與您在主群集上輸入的消息相同。

## <a name="delete-the-cluster"></a>選取叢集

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

本文檔中的步驟在不同的 Azure 資源組中創建了群集。 要刪除創建的所有資源，可以刪除創建的兩個資源組 **：kafka-主資料庫**和**kafka-secondary_rg**。 刪除資源組將刪除遵循本文檔創建的所有資源，包括群集、虛擬網路和存儲帳戶。

## <a name="next-steps"></a>後續步驟

在本文件中，您已學會如何使用 [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) 建立 [Apache Kafka](https://kafka.apache.org/) 叢集的複本。 使用下列連結來探索使用 Kafka 的其他方式︰

* [Apache Kafka MirrorMaker 文件](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) (網址為 cwiki.apache.org)。
* [卡夫卡鏡製造商最佳實踐](https://community.cloudera.com/t5/Community-Articles/Kafka-Mirror-Maker-Best-Practices/ta-p/249269)
* [開始使用 Apache Kafka on HDInsight](apache-kafka-get-started.md)
* [在 HDInsight 上使用阿帕奇火花與阿帕奇卡夫卡](../hdinsight-apache-spark-with-kafka.md)
* [透過 Azure 虛擬網路連線到 Apache Kafka](apache-kafka-connect-vpn-gateway.md)
