---
title: 快速入門：Apache HBase 與 Apache Phoenix - Azure HDInsight
description: 在本快速入門中，您學會如何在 HDInsight 中使用 Apache Phoenix。 也了解如何在電腦上安裝並設定 SQLLine，以連線到在 HDInsight 中的 HBase 叢集。
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: quickstart
ms.date: 06/12/2019
ms.author: hrasheed
ms.openlocfilehash: 1c400e41c4c10023d2595bde8c0d62e26184cf05
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79370316"
---
# <a name="quickstart-query-apache-hbase-in-azure-hdinsight-with-apache-phoenix"></a>快速入門：搭配 Apache Phoenix 在 Azure HDInsight 中查詢 Apache HBase

在此快速入門中，您將會了解如何使用 Apache Phoenix 在 Azure HDInsight 中執行 HBase 查詢。 Apache Phoenix 是適用於 Apache HBase 的 SQL 查詢引擎。 其以 JDBC 驅動程式的形式進行存取，並可使用 SQL 查詢和管理 HBase 資料表。 [SQLLine](http://sqlline.sourceforge.net/) \(英文\) 是執行 SQL 的命令列公用程式。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

* Apache HBase 叢集。 請參閱[建立叢集](../hadoop/apache-hadoop-linux-tutorial-get-started.md)以建立 HDInsight 叢集。  請確定您選擇的是 [HBase]  叢集類型。

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="identify-a-zookeeper-node"></a>識別 ZooKeeper 節點

連線到 HBase 叢集時，您必須連線到其中一個 Apache ZooKeeper 節點。 每個 HDInsight 叢集都會有三個 ZooKeeper 節點。 可以使用 Curl 來快速識別 ZooKeeper 節點。 編輯下列 curl 命令並將 `PASSWORD` 和 `CLUSTERNAME` 取代為相關的值，然後在命令提示字元中輸入該命令：

```cmd
curl -u admin:PASSWORD -sS -G https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER
```

部分輸出將會類似如下範例：

```output
    {
      "href" : "http://hn1-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net:8080/api/v1/clusters/myCluster/hosts/zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net/host_components/ZOOKEEPER_SERVER",
      "HostRoles" : {
        "cluster_name" : "myCluster",
        "component_name" : "ZOOKEEPER_SERVER",
        "host_name" : "zk0-brim.432dc3rlshou3ocf251eycoapa.bx.internal.cloudapp.net"
      }
```

請記下 `host_name` 的值以供稍後使用。

## <a name="create-a-table-and-manipulate-data"></a>建立資料表並操作資料

您可以使用 SSH 來連線到 HBase 叢集，然後使用 Apache Phoenix 來建立 HBase 資料表、插入資料，以及查詢資料。

1. 使用 `ssh` 命令來連線至您的 HBase 叢集。 編輯以下命令並將 `CLUSTERNAME` 取代為您叢集的名稱，然後輸入命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. 將目錄變更為 Phoenix 用戶端。 輸入下列命令：

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

3. 啟動 [SQLLine](http://sqlline.sourceforge.net/)(英文\)。 編輯下列命令並將 `ZOOKEEPER` 取代為先前所識別的 ZooKeeper 節點，然後輸入該命令：

    ```bash
    ./sqlline.py ZOOKEEPER:2181:/hbase-unsecure
    ```

4. 建立 HBase 資料表。 輸入下列命令：

    ```sql
    CREATE TABLE Company (company_id INTEGER PRIMARY KEY, name VARCHAR(225));
    ```

5. 使用 SQLLine `!tables` 命令來列出 HBase 中的所有資料表。 輸入下列命令：

    ```sqlline
    !tables
    ```

6. 將值插入資料表。 輸入下列命令：

    ```sql
    UPSERT INTO Company VALUES(1, 'Microsoft');
    UPSERT INTO Company VALUES(2, 'Apache');
    ```

7. 查詢資料表。 輸入下列命令：

    ```sql
    SELECT * FROM Company;
    ```

8. 刪除記錄。 輸入下列命令：

    ```sql
    DELETE FROM Company WHERE COMPANY_ID=1;
    ```

9. 卸除資料表。 輸入下列命令：

    ```hbase
    DROP TABLE Company;
    ```

10. 使用 SQLLine `!quit` 命令來結束 SQLLine。 輸入下列命令：

    ```sqlline
    !quit
    ```

## <a name="clean-up-resources"></a>清除資源

完成此快速入門之後，您可以刪除叢集。 利用 HDInsight，您的資料會儲存在 Azure 儲存體中，以便您在未使用叢集時安全地進行刪除。 您也需支付 HDInsight 叢集的費用 (即使未使用)。 由於叢集費用是儲存體費用的許多倍，所以刪除未使用的叢集符合經濟效益。

若要刪除叢集，請參閱[使用您的瀏覽器、PowerShell 或 Azure CLI 刪除 HDInsight 叢集](../hdinsight-delete-cluster.md)。

## <a name="next-steps"></a>後續步驟

在此快速入門中，您已了解如何使用 Apache Phoenix 在 Azure HDInsight 中執行 HBase 查詢。 若要深入了解 Apache Phoenix，下一篇文章將會提供更深入的探討。

> [!div class="nextstepaction"]
> [HDInsight 中的 Apache Phoenix](../hdinsight-phoenix-in-hdinsight.md)
