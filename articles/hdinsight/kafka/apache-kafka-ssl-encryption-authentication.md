---
title: Apache Kafka TLS 加密 & 驗證-Azure HDInsight
description: 針對 Kafka 用戶端和 Kafka 訊息代理程式之間的通訊設定 TLS 加密，以及在 Kafka 代理程式之間進行通訊。 設定用戶端的 SSL 驗證。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/01/2019
ms.openlocfilehash: 02b64d77a4fb1af25e1022de3ac8e4775f916d9e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261766"
---
# <a name="set-up-tls-encryption-and-authentication-for-apache-kafka-in-azure-hdinsight"></a>在 Azure HDInsight 中設定 Apache Kafka 的 TLS 加密和驗證

本文說明如何設定 Apache Kafka 用戶端與 Apache Kafka 訊息代理程式之間的傳輸層安全性（TLS）加密，先前稱為安全通訊端層（SSL）加密。 它也會說明如何設定用戶端的驗證（有時稱為雙向 TLS）。

> [!Important]
> 有兩個用戶端可供您用來 Kafka 應用程式： JAVA 用戶端和主控台用戶端。 只有 JAVA 用戶端`ProducerConsumer.java`可以使用 TLS 來產生和使用。 主控台產生者客戶`console-producer.sh`端無法使用 TLS。

> [!Note]
> 具有1.1 版的 HDInsight Kafka 主控台產生者不支援 SSL。

## <a name="apache-kafka-broker-setup"></a>Apache Kafka broker 安裝程式

Kafka TLS broker 安裝程式會以下列方式使用四個 HDInsight 叢集 Vm：

* 前端節點 0-憑證授權單位單位（CA）
* 背景工作節點0、1和 2-訊息代理程式

> [!Note] 
> 本指南將會使用自我簽署憑證，但最安全的作法是使用由受信任的 CA 所核發的憑證。

Broker 安裝程式的摘要如下所示：

1. 下列步驟會在三個背景工作角色節點上重複執行：

    1. 產生憑證。
    1. 建立憑證簽署要求。
    1. 將憑證簽署要求傳送給憑證授權單位單位（CA）。
    1. 登入 CA 並簽署要求。
    1. 將已簽署的憑證傳回背景工作角色節點的 SCP。
    1. SCP CA 的公開憑證到背景工作節點。

1. 擁有所有憑證之後，請將憑證放入憑證存放區中。
1. 請移至 Ambari 並變更設定。

使用下列詳細指示來完成 broker 安裝程式：

> [!Important]
> 在下列程式碼片段中，wnX 是三個背景工作角色節點其中之一的縮寫，而且應該`wn0`視`wn1`需要`wn2`取代或。 `WorkerNode0_Name`和`HeadNode0_Name`應該以各自電腦的名稱取代。

1. 在前端節點0上執行初始安裝，HDInsight 將會填入憑證授權單位單位（CA）的角色。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個訊息代理程式（背景工作節點0、1和2）上執行相同的初始設定。

    ```bash
    # Create a new directory 'ssl' and change into it
    mkdir ssl
    cd ssl
    ```

1. 在每個背景工作節點上，使用下面的程式碼片段執行下列步驟。
    1. 建立金鑰存放區，並將新的私人憑證填入其中。
    1. 建立憑證簽署要求。
    1. SCP 對 CA 的憑證簽署要求（headnode0）

    ```bash
    keytool -genkey -keystore kafka.server.keystore.jks -validity 365 -storepass "MyServerPassword123" -keypass "MyServerPassword123" -dname "CN=FQDN_WORKER_NODE" -storetype pkcs12
    keytool -keystore kafka.server.keystore.jks -certreq -file cert-file -storepass "MyServerPassword123" -keypass "MyServerPassword123"
    scp cert-file sshuser@HeadNode0_Name:~/ssl/wnX-cert-sign-request
    ```

1. 在 CA 電腦上，執行下列命令以建立 ca 憑證和 ca 金鑰檔案：

    ```bash
    openssl req -new -newkey rsa:4096 -days 365 -x509 -subj "/CN=Kafka-Security-CA" -keyout ca-key -out ca-cert -nodes
    ```

1. 變更為 CA 電腦，並簽署所有收到的憑證簽署要求：

    ```bash
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn0-cert-sign-request -out wn0-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn1-cert-sign-request -out wn1-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    openssl x509 -req -CA ca-cert -CAkey ca-key -in wn2-cert-sign-request -out wn2-cert-signed -days 365 -CAcreateserial -passin pass:"MyServerPassword123"
    ```

1. 將已簽署的憑證傳回給 CA 的背景工作節點（headnode0）。

    ```bash
    scp wn0-cert-signed sshuser@WorkerNode0_Name:~/ssl/cert-signed
    scp wn1-cert-signed sshuser@WorkerNode1_Name:~/ssl/cert-signed
    scp wn2-cert-signed sshuser@WorkerNode2_Name:~/ssl/cert-signed
    ```

1. 將 CA 的公開憑證傳送給每個背景工作節點。

    ```bash
    scp ca-cert sshuser@WorkerNode0_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode1_Name:~/ssl/ca-cert
    scp ca-cert sshuser@WorkerNode2_Name:~/ssl/ca-cert
    ```

1. 在每個背景工作節點上，將 CAs 公開憑證新增至 truststore 和金鑰儲存區。 然後將背景工作角色節點的已簽署憑證新增至金鑰儲存區

    ```bash
    keytool -keystore kafka.server.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt
    keytool -keystore kafka.server.keystore.jks -import -file cert-signed -storepass "MyServerPassword123" -keypass "MyServerPassword123" -noprompt

    ```

## <a name="update-kafka-configuration-to-use-tls-and-restart-brokers"></a>將 Kafka 設定更新為使用 TLS 並重新啟動訊息代理程式

您現在已使用金鑰存放區和 truststore 來設定每個 Kafka 訊息代理程式，並已匯入正確的憑證。 接下來，請使用 Ambari 修改相關的 Kafka 設定屬性，然後重新啟動 Kafka 訊息代理程式。

若要完成設定修改，請執行以下步驟：

1. 登入 Azure 入口網站，然後選取您的 Azure HDInsight Apache Kafka 叢集。
1. 按一下 [叢集儀表板]**** 下方的 [Ambari 首頁]**** 來移至 Ambari UI。
1. 在 [Kafka Broker]**** \(Kafka 訊息代理程式\) 下方，將 [listeners]**** \(接聽程式\) 屬性設定為 `PLAINTEXT://localhost:9092,SSL://localhost:9093`
1. 在 [Advanced kafka-broker]**** \(進階 kafka-broker\) 下方，將 [security.inter.broker.protocol]**** 屬性設定為 `SSL`

    ![在 Ambari 中編輯 Kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari.png)

1. 在 [Custom kafka-broker]**** \(自訂 kafka-broker\) 下方，將 [ssl.client.auth]**** 屬性設定為 `required`。 只有當您要設定驗證和加密時，才需要執行此步驟。

    ![在 Ambari 中編輯 kafka ssl 設定屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-ambari2.png)

1. 針對 HDI 3.6 版，請移至 Ambari UI，並在**Advanced kafka-env**和**kafka-env template**屬性底下新增下列設定。

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

1. 以下螢幕擷取畫面顯示具有這些變更的 Ambari 設定 UI。

    針對 HDI 版本3.6：

    ![編輯 Ambari 中的 kafka-env 範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env.png)

    針對 HDI 版本4.0：

     ![編輯 Ambari 四中的 kafka-env 範本屬性](./media/apache-kafka-ssl-encryption-authentication/editing-configuration-kafka-env-four.png)

1. 重新啟動所有 Kafka 訊息代理程式。

## <a name="client-setup-without-authentication"></a>用戶端設定 (不搭配驗證)

如果您不需要驗證，只設定 TLS 加密的步驟摘要如下：

1. 登入 CA （使用中的前端節點）。
1. 從 CA 電腦（wn0）將 CA 憑證複製到用戶端電腦。
1. 登入用戶端電腦（hn1）並流覽至`~/ssl`資料夾。
1. 將 CA 憑證匯入 truststore。
1. 將 CA 憑證匯入金鑰儲存區。

下列程式碼片段會詳細說明這些步驟。

1. 登入 CA 節點。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    ```

1. 將 ca 憑證複製到用戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

1. 登入用戶端電腦（待命前端節點）。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 將 CA 憑證匯入 truststore。

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. 將 CA 憑證匯入金鑰儲存區。
    
    ```bash
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. `client-ssl-auth.properties`在用戶端電腦上建立檔案（hn1）。 應該具有下列幾行：

    ```config
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ```

1. 啟動具有生產者和取用者選項的系統管理用戶端，以確認生產者和取用者都在通訊埠9093上運作。 請參閱下面的[驗證](apache-kafka-ssl-encryption-authentication.md#verification)一節，以瞭解使用主控台產生者/消費者驗證設定所需的步驟。

## <a name="client-setup-with-authentication"></a>用戶端設定 (搭配驗證)

> [!Note]
> 只有當您同時設定 TLS 加密**和**驗證時，才需要執行下列步驟。 如果您只是要設定加密，請參閱[未驗證的用戶端設定](apache-kafka-ssl-encryption-authentication.md#client-setup-without-authentication)。

下列四個步驟摘要說明完成用戶端安裝所需的工作：

1. 登入用戶端電腦（待命前端節點）。
1. 建立 java 金鑰儲存區並取得訊息代理程式的已簽署憑證。 然後將憑證複製到執行該 CA 的 VM。
1. 切換至 CA 電腦（作用中前端節點）以簽署用戶端憑證。
1. 移至用戶端電腦（待命前端節點），然後流覽至`~/ssl`資料夾。 將已簽署的憑證複製到用戶端電腦。

以下提供每個步驟的詳細資料。

1. 登入用戶端電腦（待命前端節點）。

    ```bash
    ssh sshuser@HeadNode1_Name
    ```

1. 移除任何現有的 ssl 目錄。

    ```bash
    rm -R ~/ssl
    mkdir ssl
    cd ssl
    ```

1. 建立 java 金鑰儲存區，並建立憑證簽署要求。 

    ```bash
    keytool -genkey -keystore kafka.client.keystore.jks -validity 365 -storepass "MyClientPassword123" -keypass "MyClientPassword123" -dname "CN=HEADNODE1_FQDN" -storetype pkcs12
    
    keytool -keystore kafka.client.keystore.jks -certreq -file client-cert-sign-request -storepass "MyClientPassword123" -keypass "MyClientPassword123"
    ```

1. 將憑證簽署要求複製到 CA

    ```bash
    scp client-cert-sign-request sshuser@HeadNode0_Name:~/ssl/client-cert-sign-request
    ```

1. 切換至 CA 電腦（作用中的前端節點）並簽署用戶端憑證。

    ```bash
    ssh sshuser@HeadNode0_Name
    cd ssl
    openssl x509 -req -CA ca-cert -CAkey ca-key -in ~/ssl/client-cert-sign-request -out ~/ssl/client-cert-signed -days 365 -CAcreateserial -passin pass:MyClientPassword123
    ```

1. 從 CA （作用中前端節點）將已簽署的用戶端憑證複製到用戶端電腦。

    ```bash
    scp client-cert-signed sshuser@HeadNode1_Name:~/ssl/client-signed-cert
    ```

1. 將 ca 憑證複製到用戶端電腦

    ```bash
    scp ca-cert sshuser@HeadNode1_Name:~/ssl/ca-cert
    ```

    1. 登入用戶端電腦（待命前端節點），然後流覽至 [ssl 目錄]。

    ```bash
    ssh sshuser@HeadNode1_Name
    cd ssl
    ```

1. 使用已簽署的憑證建立用戶端存放區，並將 ca 憑證匯入至用戶端電腦上的金鑰儲存區和 truststore （hn1）：

    ```bash
    keytool -keystore kafka.client.truststore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -alias CARoot -import -file ca-cert -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    
    keytool -keystore kafka.client.keystore.jks -import -file client-cert-signed -storepass "MyClientPassword123" -keypass "MyClientPassword123" -noprompt
    ```

1. `client-ssl-auth.properties`在用戶端電腦上建立檔案（hn1）。 應該具有下列幾行：

    ```bash
    security.protocol=SSL
    ssl.truststore.location=/home/sshuser/ssl/kafka.client.truststore.jks
    ssl.truststore.password=MyClientPassword123
    ssl.keystore.location=/home/sshuser/ssl/kafka.client.keystore.jks
    ssl.keystore.password=MyClientPassword123
    ssl.key.password=MyClientPassword123
    ```

## <a name="verification"></a>驗證

在用戶端電腦上執行這些步驟。

> [!Note]
> 如果已安裝 HDInsight 4.0 和 Kafka 2.1，您可以使用主控台生產者/取用者來驗證您的設定。 如果不是，請在埠9092上執行 Kafka 生產者並將訊息傳送至主題，然後在使用 TLS 的埠9093上使用 Kafka 取用者。

### <a name="kafka-21-or-above"></a>Kafka 2.1 或更新版本

1. 建立主題（如果尚未存在）。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. 啟動主控台產生者，並提供的`client-ssl-auth.properties`路徑做為產生者的設定檔。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9093 --topic topic1 --producer.config ~/ssl/client-ssl-auth.properties
    ```

1. 開啟另一個與用戶端電腦的 ssh 連線，並啟動主控台取用者`client-ssl-auth.properties` ，並將路徑提供給取用者的設定檔。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

### <a name="kafka-11"></a>Kafka 1。1

1. 建立主題（如果尚未存在）。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --zookeeper <ZOOKEEPER_NODE_0>:2181 --create --topic topic1 --partitions 2 --replication-factor 2
    ```

1. 啟動主控台產生者，並提供用戶端-ssl-驗證的路徑，做為生產者的設定檔。

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list <FQDN_WORKER_NODE>:9092 --topic topic1 
    ```

1. 開啟另一個與用戶端電腦的 ssh 連線，並啟動主控台取用者`client-ssl-auth.properties` ，並將路徑提供給取用者的設定檔。

    ```bash
    $ /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server <FQDN_WORKER_NODE>:9093 --topic topic1 --consumer.config ~/ssl/client-ssl-auth.properties --from-beginning
    ```

## <a name="next-steps"></a>後續步驟

* [什麼是 HDInsight 上的 Apache Kafka？](apache-kafka-introduction.md)
