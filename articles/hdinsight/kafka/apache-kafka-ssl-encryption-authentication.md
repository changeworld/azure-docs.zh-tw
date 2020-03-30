---
title: 阿帕奇卡夫卡SSL加密&身份驗證 - Azure HDInsight
description: 為卡夫卡客戶和卡夫卡經紀人之間以及卡夫卡經紀人之間的通訊設定 SSL 加密。 設置用戶端的 SSL 身份驗證。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: hrasheed
ms.openlocfilehash: 4a363caf61046cf39c31ae2d5f35622b7b9109f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130017"
---
# <a name="set-up-secure-sockets-layer-ssl-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>在 Azure HDInsight 中為 Apache Kafka 設置安全通訊端層 （SSL） 加密和身份驗證

本文介紹如何在 Apache Kafka 用戶端和 Apache Kafka 代理之間設置 SSL 加密。 它還演示如何設置用戶端的身份驗證（有時稱為雙向 SSL）。

> [!Important]
> 有兩個用戶端可用於 Kafka 應用程式：JAVA 用戶端和主控台用戶端。 只有 JAVA`ProducerConsumer.java`用戶端可以使用 SSL 來生成和使用。 主控台產生器用戶端`console-producer.sh`不適用於 SSL。

> [!Note] 
> HDInsight Kafka 主控台製作器版本 1.1 不支援 SSL。
## <a name="apache-kafka-broker-setup"></a>阿帕奇卡夫卡經紀人設置

Kafka SSL 代理設置將採用以下方式使用四個 HDInsight 群集 VM：

* 頭節點 0 - 憑證授權單位 （CA）
* 輔助節點 0、1 和 2 - 代理

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

代理設置過程的摘要如下：

1. 以下步驟在三個輔助節點上重複：

    1. 產生憑證。
    1. 創建證書簽名請求。
    1. 將證書簽名要求傳送到憑證授權單位 （CA）。
    1. 登錄到 CA 並簽署請求。
    1. SCP 簽名的證書返回輔助角色節點。
    1. SCP CA 到輔助節點的公共證書。

1. 獲得所有證書後，將證書放入憑證存放區。
1. 轉到 Ambari 並更改配置。

使用以下詳細說明完成代理設置：

> [!Important]
> 在以下程式碼片段中，wnX 是三個輔助節點之一的縮寫，應替換為`wn0`或`wn1``wn2`根據需要替換。 `WorkerNode0_Name`並`HeadNode0_Name`應用相關機器的名稱替換。

1. 在頭節點 0 上執行初始設置，對於 HDInsight，這將填充憑證授權單位 （CA） 的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 對每個代理執行相同的初始設置（輔助節點 0、1 和 2）。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個輔助節點上，使用下面的程式碼片段執行以下步驟。
    1. 創建金鑰存儲，然後使用新的私有證書填充它。
    1. 創建證書簽名請求。
    1. SCP 向 CA（頭節點0）的證書簽名請求

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 電腦上運行以下命令以創建 ca-cert 和 ca 鍵檔：

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 更改為 CA 電腦並對所有已接收的證書簽名請求進行簽名：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 從 CA（頭節點0）將簽名的證書發送回輔助節點。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 將 CA 的公共證書發送到每個輔助節點。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 在每個輔助節點上，將 CA 公共證書添加到信任存儲和金鑰存儲。 然後將輔助節點自己的簽章憑證添加到金鑰存儲

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-ssl-and-restart-brokers"></a>更新 Kafka 設定以使用 SSL 並重新啟動訊息代理程式

現在，您已經使用金鑰庫和信任存儲為每個 Kafka 代理設置，並導入了正確的證書。 接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

若要完成設定修改，請執行以下步驟：

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板]**** 下方的 [Ambari 首頁]**** 來移至 Ambari UI。
1. 在 [Kafka Broker]**** \(Kafka 訊息代理程式\) 下方，將 [listeners]**** \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker]**** \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol]**** 屬性設定為 `SSL`

    ![在 Ambari 中編輯 Kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker]**** \(自訂 kafka-broker\) 下方，將 [ssl.client.auth]**** 屬性設定為 `required`。 僅當設置身份驗證和加密時，才需要此步驟。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 向伺服器.屬性檔添加新的配置屬性。

    ```bash
    # Configure Kafka to advertise IP addresses instead of FQDN
    IP_ADDRESS=$(hostname -i)
    echo advertised.listeners=$IP_ADDRESS
    sed -i.bak -e '/advertised/{/advertised@/!d;}' /usr/hdp/current/kafka-broker/conf/server.properties
    echo "advertised.listeners=PLAINTEXT://$IP_ADDRESS:9092,SSL://$IP_ADDRESS:9093" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.location=/home/sshuser/ssl/kafka.server.keystore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.keystore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.key.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.location=/home/sshuser/ssl/kafka.server.truststore.jks" >> /usr/hdp/current/kafka-broker/conf/server.properties
    echo "ssl.truststore.password=MyServerPassword123" >> /usr/hdp/current/kafka-broker/conf/server.properties
    ```

1. 轉到 Ambari 配置 UI 並驗證新屬性是否顯示在**高級 kafka-env**和**kafka-env 範本**屬性下。

    對於 HDI 版本 3.6：

    ![在安巴里編輯卡夫卡-恩夫範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    對於 HDI 版本 4.0：

     ![在 Ambari 四中編輯 kafka-env 範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)   

1. 重新啟動所有 Kafka 訊息代理程式。
1. 使用生產者和消費者選項啟動管理用戶端，以驗證生產者和消費者是否都在埠 9093 上工作。

## <a name="client-setup-without-authentication"></a>用戶端設定 (不搭配驗證)

如果不需要身份驗證，則僅設置 SSL 加密的步驟摘要包括：

1. 登錄到 CA（活動頭節點）。
1. 從 CA 電腦 （wn0） 將 CA 憑證複製到用戶端電腦。
1. 登錄到用戶端電腦 （hn1） 並導航到`~/ssl`資料夾。
1. 將 CA 憑證導入信任存儲。
1. 將 CA 憑證導入金鑰存儲。

這些步驟在以下程式碼片段中進行了詳細說明。

1. 登錄到 CA 節點。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. 將 ca-cert 複製到用戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 登錄到用戶端電腦（備用頭節點）。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 將 CA 憑證導入信任存儲。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 將 CA 憑證導入金鑰存儲。
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 建立檔案 `client-ssl-auth.properties`。 應該具有下列幾行：

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 下列步驟只有在您同時設定 SSL 加密 **和** 驗證的情況下才需要執行。 如果只是設置加密，請參閱[沒有身份驗證的用戶端設置](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)。

以下四個步驟總結了完成用戶端設置所需的任務：

1. 登錄到用戶端電腦（備用頭節點）。
1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。
1. 切換到 CA 電腦（活動頭節點）以對用戶端憑證進行簽名。
1. 轉到用戶端電腦（備用頭節點）並導航到`~/ssl`資料夾。 將已簽署的憑證複製到用戶端電腦。

下面給出了每個步驟的詳細資訊。

1. 登錄到用戶端電腦（備用頭節點）。

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. 刪除任何現有的 ssl 目錄。

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. 創建 java 金鑰庫並創建證書簽名請求。 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. 將證書簽名提取複寫到 CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. 切換到 CA 電腦（活動頭節點）並簽署用戶端憑證。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. 將簽名的用戶端憑證從 CA（活動頭節點）複製到用戶端電腦。

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. 將 ca-cert 複製到用戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 使用簽章憑證創建用戶端存儲，並將 ca 證書導入金鑰庫和信任存儲：

    ```bash
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass MyClientPassword123 -keypass MyClientPassword123 -noprompt
    ```

1. 創建檔`client-ssl-auth.properties`。 應該具有下列幾行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>驗證

> [!Note]
> 如果安裝了 HDInsight 4.0 和 Kafka 2.1，則可以使用主控台製作者/消費者來驗證您的設置。 如果沒有，請運行埠 9092 上的 Kafka 產生器並將消息發送到主題，然後在使用 SSL 的埠 9093 上使用 Kafka 消費者。

### <a name="kafka-21-or-above"></a>卡夫卡 2.1 或以上

1. 如果主題不存在，則創建主題。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  啟動主控台產生器，並提供作為`client-ssl-auth.properties`產生器的設定檔的路徑。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1.  打開另一個連接到用戶端電腦並啟動主控台消費者的 ssh 連接`client-ssl-auth.properties`，並為消費者提供作為設定檔的路徑。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>卡夫卡 1.1

1. 如果主題不存在，則創建主題。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1.  啟動主控台產生器，並提供用戶端-ssl-auth.屬性的路徑，作為生產者的設定檔。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

3.  打開另一個連接到用戶端電腦並啟動主控台消費者的 ssh 連接`client-ssl-auth.properties`，並為消費者提供作為設定檔的路徑。

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>後續步驟

* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
