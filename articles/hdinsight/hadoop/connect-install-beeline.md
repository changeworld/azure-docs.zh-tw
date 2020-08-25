---
title: 連接或安裝 Apache Beeline-Azure HDInsight
description: 瞭解如何連線到 Apache Beeline 用戶端，以使用 HDInsight 上的 Hadoop 來執行 Hive 查詢。 Beeline 是透過 JDBC 與 HiveServer2 搭配作業的公用程式。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: contperfq1
ms.date: 05/27/2020
ms.openlocfilehash: 5495e6c6392ba2e824a0a70717bd19747db9b754
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/23/2020
ms.locfileid: "88754951"
---
# <a name="connect-to-apache-beeline-on-hdinsight-or-install-it-locally"></a>連線到 Apache Beeline on HDInsight 或將其安裝在本機

[Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) 是一種 Hive 用戶端，包含在 HDInsight 叢集的前端節點上。 本文說明如何連線至您的 HDInsight 叢集上安裝的 Beeline 用戶端，以跨越不同類型的連接。 它也會討論如何 [在本機安裝 Beeline 用戶端](#install-beeline-client)。 

## <a name="types-of-connections"></a>連線類型

### <a name="from-an-ssh-session"></a>從 SSH 會話

從 SSH 會話連線到叢集前端節點時，您可以連接到 `headnodehost` 埠上的位址 `10001` ：

```bash
beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
```

### <a name="over-an-azure-virtual-network"></a>透過 Azure 虛擬網路

從用戶端透過 Azure 虛擬網路連線到 HDInsight 時，您必須提供叢集前端節點 (FQDN) 的完整功能變數名稱。 由於此連線是直接連線到叢集節點，因此會使用連接埠 `10001` 進行連線：

```bash
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

取代 `<headnode-FQDN>` 為叢集前端節點的完整功能變數名稱。 若要找出前端節點的完整網域名稱，請利用[使用 Apache Ambari REST API 管理 HDInsight](../hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes) 文件中的資訊。

### <a name="to-hdinsight-enterprise-security-package-esp-cluster-using-kerberos"></a>使用 Kerberos 企業安全性套件 (ESP) 叢集的 HDInsight

從用戶端連線至企業安全性套件 (ESP) 叢集加入至叢集相同領域的電腦上 Azure Active Directory (AAD) -DS 時，您也必須指定具有叢集存取權的網域 `<AAD-Domain>` 使用者帳戶的功能變數名稱和名稱 `<username>` ：

```bash
kinit <username>
beeline -u 'jdbc:hive2://<headnode-FQDN>:10001/default;principal=hive/_HOST@<AAD-Domain>;auth-kerberos;transportMode=http' -n <username>
```

以網域上具備叢集存取權限的帳戶名稱取代 `<username>`。 將 `<AAD-DOMAIN>` 取代為叢集所加入 Azure Active Directory (AAD) 的名稱。 請針對 `<AAD-DOMAIN>` 值使用大寫字串，否則會找不到認證。 如有需要，請檢查 `/etc/krb5.conf` 以尋找領域名稱。

若要從 Ambari 尋找 JDBC URL：

1. 從網頁瀏覽器瀏覽至 `https://CLUSTERNAME.azurehdinsight.net/#/main/services/HIVE/summary`，其中 `CLUSTERNAME` 是叢集的名稱。 確定 HiveServer2 正在執行。

1. 使用剪貼簿複製 HiveServer2 JDBC URL。

### <a name="over-public-or-private-endpoints"></a>透過公用或私人端點

使用公用或私人端點連接到叢集時，您必須提供叢集登入帳戶名稱 (預設 `admin`) 和密碼。 例如，使用 Beeline 從用戶端系統連線到 `clustername.azurehdinsight.net` 位址。 此連線是透過埠進行 `443` ，且會使用 TLS/SSL 進行加密。

將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 針對 ESP 叢集，請使用完整的 UPN (例如 user@domain.com) 。 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

或私人端點：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2' -n admin -p 'password'
```

私人端點指向基本負載平衡器，只能從相同區域中的 Vnet 對等互連存取。 如需詳細資訊，請參閱 [全域 VNet 對等互連與負載平衡器的條件約束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 。 在使用 beeline 之前，您可以使用 `curl` 命令搭配 `-v` 選項來針對公用或私人端點的任何連線問題進行疑難排解。

### <a name="use-beeline-with-apache-spark"></a>使用 Beeline 搭配 Apache Spark

Apache Spark 提供自己的 HiveServer2 (有時稱為 Spark Thrift 伺服器) 實作。 這項服務會使用 Spark SQL 來解析查詢，而不是 Hive。 根據您的查詢，可能會提供更好的效能。

#### <a name="through-public-or-private-endpoints"></a>透過公用或私人端點

使用的連接字串稍有不同。 而不包含 `httpPath=/hive2` 它使用 `httpPath/sparkhive2` 。 將 `clustername` 替換為 HDInsight 叢集的名稱。 將 `admin` 取代為叢集的叢集登入帳戶。 針對 ESP 叢集，請使用完整的 UPN (例如 user@domain.com) 。 將 `password` 取代為叢集登入帳戶的密碼。

```bash
beeline -u 'jdbc:hive2://clustername.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

或私人端點：

```bash
beeline -u 'jdbc:hive2://clustername-int.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/sparkhive2' -n admin -p 'password'
```

私人端點指向基本負載平衡器，只能從相同區域中的 Vnet 對等互連存取。 如需詳細資訊，請參閱 [全域 VNet 對等互連與負載平衡器的條件約束](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 。 在使用 beeline 之前，您可以使用 `curl` 命令搭配 `-v` 選項來針對公用或私人端點的任何連線問題進行疑難排解。

#### <a name="from-cluster-head-or-inside-azure-virtual-network-with-apache-spark"></a>從叢集前端或 Azure 虛擬網路內部搭配 Apache Spark

直接從叢集前端節點，或是從 Azure 虛擬網路 (與 HDInsight 叢集相同) 內的資源進行連線時，應對 Spark Thrift 伺服器使用連接埠 `10002`，而非 `10001`。 下列範例顯示如何直接連接到前端節點：

```bash
/usr/hdp/current/spark2-client/bin/beeline -u 'jdbc:hive2://headnodehost:10002/;transportMode=http'
```

## <a name="install-beeline-client"></a>安裝 Beeline 用戶端

雖然 Beeline 包含在前端節點上，但是您可能會想要在本機進行安裝。  本機電腦的安裝步驟是以 [Windows 子系統 Linux 版](https://docs.microsoft.com/windows/wsl/install-win10)為基礎。

1. 更新套件清單。 在 bash shell 中輸入下列命令：

    ```bash
    sudo apt-get update
    ```

1. 如果未安裝，請安裝 JAVA。 您可以使用命令來檢查 `which java` 。

    1. 如果未安裝 java 封裝，請輸入下列命令：

        ```bash
        sudo apt install openjdk-11-jre-headless
        ```

    1. 開啟 >.bashrc 檔案 (通常會在 ~/.bashrc) ：中找到 `nano ~/.bashrc` 。

    1. 修改 >.bashrc 檔案。 在檔案結尾處加入下列這一行︰

        ```bash
        export JAVA_HOME=/usr/lib/jvm/java-1.11.0-openjdk-amd64
        ```

        然後按下 **Ctrl + X**、 **Y**，然後輸入。

1. 下載 Hadoop 和 Beeline 封存，輸入下列命令：

    ```bash
    wget https://archive.apache.org/dist/hadoop/core/hadoop-2.7.3/hadoop-2.7.3.tar.gz
    wget https://archive.apache.org/dist/hive/hive-1.2.1/apache-hive-1.2.1-bin.tar.gz
    ```

1. 將封存解壓縮之後，輸入下列命令：

    ```bash
    tar -xvzf hadoop-2.7.3.tar.gz
    tar -xvzf apache-hive-1.2.1-bin.tar.gz
    ```

1. 進一步修改 >.bashrc 檔。 您將需要識別封存解壓縮的目的地路徑。 如果使用 [Windows 子系統 Linux 版](https://docs.microsoft.com/windows/wsl/install-win10)，且您完全遵循步驟，則路徑會是 `/mnt/c/Users/user/` ，其中 `user` 是您的使用者名稱。

    1. 開啟檔案： `nano ~/.bashrc`

    1. 使用適當的路徑修改下列命令，然後在 >.bashrc 檔案的結尾輸入這些命令：

        ```bash
        export HADOOP_HOME=/path_where_the_archives_were_unpacked/hadoop-2.7.3
        export HIVE_HOME=/path_where_the_archives_were_unpacked/apache-hive-1.2.1-bin
        PATH=$PATH:$HIVE_HOME/bin
        ```

    1. 然後按下 **Ctrl + X**、 **Y**，然後輸入。

1. 關閉並重新開啟 bash 會話。

1. 測試您的連接。 使用上述的連接格式，透過 [公用或私人端點](#over-public-or-private-endpoints)。

## <a name="next-steps"></a>後續步驟

* 如需搭配 Apache Hive 使用 Beeline 用戶端的範例，請參閱搭配 [使用 Apache Beeline 與 Apache Hive](apache-hadoop-use-hive-beeline.md)
* 如需 HDInsight 中 Hive 的一般資訊，請參閱 [在 hdinsight 上搭配使用 Apache Hive 與 Apache Hadoop](hdinsight-use-hive.md)
