---
title: 阿帕奇卡夫卡TLS加密&認證 - Azure HDInsight
description: 設置TLS加密,用於卡夫卡客戶與卡夫卡經紀人之間以及卡夫卡經紀人之間的通信。 設置用戶端的 SSL 身份驗證。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 02b64d77a4fb1af25e1022de3ac8e4775f916d9e
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261766"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>在 Azure HDInsight 中為 Apache Kafka 設定 TLS 加密與身分驗證

本文介紹如何在 Apache Kafka 用戶端和 Apache Kafka 代理之間設置傳輸層安全 (TLS) 加密(以前稱為安全套接字層 (SSL) 加密。 它還演示如何設置客戶端的身份驗證(有時稱為雙向 TLS)。

> [!Important]
> 有兩個用戶端可用於 Kafka 應用程式:JAVA 用戶端和主控台用戶端。 只有 Java`ProducerConsumer.java`用戶端可以使用 TLS 來生成和使用。 主控台產生器`console-producer.sh`客戶端 不適用於 TLS。

> [!Note]
> HDInsight Kafka 主控台製作器版本 1.1 不支援 SSL。

## <a name="apache-kafka-broker-setup"></a>阿帕奇卡夫卡經紀人設置

Kafka TLS 代理設定將採用以下方式使用四個 HDInsight 叢集 VM:

* 頭節點 0 - 憑證發行單位 (CA)
* 輔助節點 0、1 與 2 - 代理

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

代理設定過程的摘要如下:

1. 以下步驟在三個輔助節點上重複:

    1. 產生憑證。
    1. 建立證書簽名請求。
    1. 將證書簽名請求發送到證書頒發機構 (CA)。
    1. 登錄到 CA 並簽署請求。
    1. SCP 簽名的證書返回輔助角色節點。
    1. SCP CA 到輔助節點的公共證書。

1. 獲得所有證書後,將證書放入證書存儲。
1. 跳到 Ambari 並更改配置。

使用以下詳細說明完成代理設定:

> [!Important]
> 在以下代碼段中,wnX 是三個輔助節點之一的縮寫,應替換`wn0`為`wn1``wn2`或 根據需要替換。 `WorkerNode0_Name`並`HeadNode0_Name`應用相關機器的名稱替換。

1. 在頭節點 0 上執行初始設置,對於 HDInsight,這將填充證書頒發機構 (CA) 的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 對每個代理執行相同的初始設置(輔助節點 0、1 和 2)。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個輔助節點上,使用下面的代碼段執行以下步驟。
    1. 建立金鑰存儲,然後使用新的私有證書填充它。
    1. 建立證書簽名請求。
    1. SCP 向 CA(標頭節點0)的憑證簽章請求

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 電腦上執行以下指令以建立 ca-cert 和 ca 鍵檔:

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 變更為 CA 電腦並對所有已接收的憑證簽署要求簽署:

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 從 CA(頭節點0)將簽名的證書發送回輔助節點。

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

1. 在每個輔助節點上,將 CA 公共證書添加到信任存儲和密鑰存儲。 將輔助節點自己的簽署憑證到金鑰儲存

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>更新卡夫卡設定以使用 TLS 並重新啟動代理

現在,您已經使用金鑰庫和信任儲存為每個 Kafka 代理設置,並導入了正確的證書。 接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

若要完成設定修改，請執行以下步驟：

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板]**** 下方的 [Ambari 首頁]**** 來移至 Ambari UI。
1. 在 [Kafka Broker]**** \(Kafka 訊息代理程式\) 下方，將 [listeners]**** \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker]**** \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol]**** 屬性設定為 `SSL`

    ![在 Ambari 中編輯 Kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker]**** \(自訂 kafka-broker\) 下方，將 [ssl.client.auth]**** 屬性設定為 `required`。 僅當設置身份驗證和加密時,才需要此步驟。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 對於 HDI 版本 3.6,轉到 Ambari UI,並在**高級 kafka-env**和**kafka-env 範本**屬性下添加以下配置。

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

1. 下面是顯示 Ambari 配置 UI 的螢幕截圖,這些更改。

    對於 HDI 版本 3.6:

    ![在安巴里編輯卡夫卡-恩夫範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    對於 HDI 版本 4.0:

     ![在 Ambari 四中編輯 kafka-env 範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. 重新啟動所有 Kafka 訊息代理程式。

## <a name="client-setup-without-authentication"></a>用戶端設定 (不搭配驗證)

如果不需要身份驗證,則僅設置 TLS 加密的步驟摘要包括:

1. 登錄到 CA(活動頭節點)。
1. 從 CA 電腦 (wn0) 將 CA 憑證複製到用戶端電腦。
1. 登錄到用戶端電腦 (hn1) 並`~/ssl`瀏覽到 資料夾。
1. 將 CA 憑證匯入信任儲存。
1. 將 CA 憑證匯入金鑰儲存。

這些步驟在以下代碼段中進行了詳細說明。

1. 登錄到 CA 節點。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. 將 ca-cert 複製到客戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 登錄到用戶端計算機(備用頭節點)。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 將 CA 憑證匯入信任儲存。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 將 CA 憑證匯入金鑰儲存。
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 在用戶端電腦`client-ssl-auth.properties`(hn1) 上建立檔案。 應該具有下列幾行：

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. 使用生產者和使用者選項啟動管理用戶端,以驗證生產者和消費者是否都在埠 9093 上工作。 有關使用主控台生產者/使用者驗證設置所需的步驟,請參閱下面的[驗證](apache-kafka-ssl-encryption-authentication.md#verification)部分。

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 僅當同時設置 TLS 加密**和**身份驗證時,才需要以下步驟。 如果只是設定加密,請參閱[沒有身份認證的客戶端設定](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)。

以下四個步驟總結了完成用戶端設置所需的任務:

1. 登錄到用戶端計算機(備用頭節點)。
1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。
1. 切換到 CA 電腦(活動頭節點)以對用戶端證書進行簽名。
1. 到用戶端電腦(備用頭節點)並導航到`~/ssl`資料夾。 將已簽署的憑證複製到用戶端電腦。

下面給出了每個步驟的詳細資訊。

1. 登錄到用戶端計算機(備用頭節點)。

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. 刪除任何現有的 ssl 目錄。

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. 建立 java 金鑰庫並建立證書簽名請求。 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. 將憑證簽署要求複製到 CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. 切換到 CA 電腦(活動頭節點)並簽署客戶端證書。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. 將簽名的用戶端證書從 CA(活動頭節點)複製到用戶端電腦。

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. 將 ca-cert 複製到客戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. 登錄到用戶端計算機(備用頭節點)並導航到 ssl 目錄。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 使用簽署憑證建立客戶端儲存,並將 ca cert 匯入客戶端電腦上的金鑰庫和信任儲存 (hn1):

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 在用戶端電腦`client-ssl-auth.properties`(hn1) 上建立檔案。 應該具有下列幾行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>驗證

在用戶端計算機上運行這些步驟。

> [!Note]
> 如果安裝了 HDInsight 4.0 和 Kafka 2.1,則可以使用控制台製作者/消費者來驗證您的設置。 如果沒有,請運行埠 9092 上的 Kafka 生成器並將消息發送到主題,然後在使用 TLS 的埠 9093 上使用 Kafka 消費者。

### <a name="kafka-21-or-above"></a>卡夫卡 2.1 或以上

1. 如果主題不存在,則創建主題。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. 啟動主控台產生器,並提供`client-ssl-auth.properties`作為生成器的設定檔的路徑。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. 打開另一個連接到用戶端電腦並啟動主控台消費者的 ssh 連接`client-ssl-auth.properties`,並為消費者提供作為設定檔的路徑。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>卡夫卡 1.1

1. 如果主題不存在,則創建主題。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. 啟動主控台產生器,並提供用戶端-ssl-auth.屬性的路徑,作為生產者的配置檔。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. 打開另一個連接到用戶端電腦並啟動主控台消費者的 ssh 連接`client-ssl-auth.properties`,並為消費者提供作為設定檔的路徑。

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>後續步驟

* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
