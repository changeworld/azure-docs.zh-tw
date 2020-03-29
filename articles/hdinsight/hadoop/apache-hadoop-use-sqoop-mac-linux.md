---
title: 採用 Apache Hadoop 的 Apache Sqoop - Azure HDInsight
description: 了解如何使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 Azure SQL Database 之間進行匯入和匯出。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 21bc903349876a76576fb742840e9899f9d94bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74769382"
---
# <a name="use-apache-sqoop-to-import-and-export-data-between-apache-hadoop-on-hdinsight-and-sql-database"></a>使用 Apache Sqoop 在 Apache Hadoop on HDInsight 與 SQL Database 之間匯入及匯出資料

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

了解如何使用 Apache Sqoop，在 Azure HDInsight 中的 Apache Hadoop 叢集與 Azure SQL Database 或 Microsoft SQL Server 資料庫之間進行匯入和匯出。 本文件中的步驟直接從 Hadoop 叢集的前端節點使用 `sqoop` 命令。 您可以使用 SSH 連接至前端節點，並執行本文件中的命令。 本文是[使用阿帕奇奇普與哈多普在HDInsight](./hdinsight-use-sqoop.md)的延續。

## <a name="prerequisites"></a>Prerequisites

* 完成[設置測試環境](./hdinsight-use-sqoop.md#create-cluster-and-sql-database)從[使用阿帕奇與Hadoop在HDInsight。](./hdinsight-use-sqoop.md)

* SSH 用戶端。 如需詳細資訊，請參閱[使用 SSH 連線至 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

* 熟悉 Sqoop。 有關詳細資訊，請參閱[Sqoop 使用者指南](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html)。

## <a name="set-up"></a>設定

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到群集。 通過將 CLUSTERNAME 替換為群集的名稱來編輯下面的命令，然後輸入以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 為便於使用，設置變數。 用`PASSWORD`相關`MYSQLSERVER`值替換`MYDATABASE`和 ，然後輸入以下命令：

    ```bash
    export password='PASSWORD'
    export sqlserver="MYSQLSERVER"
    export database="MYDATABASE"


    export serverConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password"
    export serverDbConnect="jdbc:sqlserver://$sqlserver.database.windows.net:1433;user=sqluser;password=$password;database=$database"
    ```

## <a name="sqoop-export"></a>Sqoop export

從蜂巢到 SQL 伺服器。

1. 要驗證 Sqoop 是否可以看到 SQL 資料庫，請在打開的 SSH 連接中輸入下面的命令。 此命令返回資料庫清單。

    ```bash
    sqoop list-databases --connect $serverConnect
    ```

1. 輸入以下命令以查看指定資料庫的表清單：

    ```bash
    sqoop list-tables --connect $serverDbConnect
    ```

1. 要將資料從 Hive`hivesampletable`表匯出到`mobiledata`SQL 資料庫中的表，請在打開的 SSH 連接中輸入下面的命令：

    ```bash
    sqoop export --connect $serverDbConnect \
    -table mobiledata \
    --hcatalog-table hivesampletable
    ```

1. 要驗證資料是否匯出，請使用 SSH 連接中的以下查詢來查看匯出的資料：

    ```bash
    sqoop eval --connect $serverDbConnect \
    --query "SELECT COUNT(*) from dbo.mobiledata WITH (NOLOCK)"


    sqoop eval --connect $serverDbConnect \
    --query "SELECT TOP(10) * from dbo.mobiledata WITH (NOLOCK)"
    ```

## <a name="sqoop-import"></a>Sqoop import

從 SQL 伺服器到 Azure 存儲。

1. 在打開的 SSH 連接中輸入以下命令，將資料從`mobiledata`SQL 資料庫中的表導入`wasbs:///tutorials/usesqoop/importeddata`到 HDInsight 上的目錄。 資料中的欄位是以定位字元分隔，行是以換行字元終止。

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' -m 1
    ```

1. 或者，您還可以指定 Hive 表：

    ```bash
    sqoop import --connect $serverDbConnect \
    --table mobiledata \
    --target-dir 'wasb:///tutorials/usesqoop/importeddata2' \
    --fields-terminated-by '\t' \
    --lines-terminated-by '\n' \
    --create-hive-table \
    --hive-table mobiledata_imported2 \
    --hive-import -m 1
    ```

1. 導入完成後，在打開的 SSH 連接中輸入以下命令以列出新目錄中的資料：

    ```bash
    hadoop fs -tail /tutorials/usesqoop/importeddata/part-m-00000
    ```

1. 使用[蜂線](./apache-hadoop-use-hive-beeline.md)驗證表是否已在 Hive 中創建。

    1. 連線

        ```bash
        beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
        ```

    1. 一次執行以下每個查詢並查看輸出：

        ```hql
        show tables;
        describe mobiledata_imported2;
        SELECT COUNT(*) FROM mobiledata_imported2;
        SELECT * FROM mobiledata_imported2 LIMIT 10;
        ```

    1. 退出蜜蜂線與`!exit`。

## <a name="limitations"></a>限制

* 批量匯出 - 使用基於 Linux 的 HDInsight，用於將資料匯出到 Microsoft SQL Server 或 Azure SQL 資料庫的 Sqoop 連接器不支援批量插入。

* 批次處理 - 使用 Linux 型 HDInsight，執行插入時若使用 `-batch` 參數，Sqoop 將會執行多個插入，而不是批次處理插入作業。

## <a name="important-considerations"></a>重要考量︰

* HDInsight 與 SQL Server 必須位於相同的 Azure 虛擬網路。

    如需範例，請參閱[將 HDInsight 連線至內部部署網路](./../connect-on-premises-network.md)文件。

    如需使用 HDInsight 搭配 Azure 虛擬網路的詳細資訊，請參閱[使用 Azure 虛擬網路擴充 HDInsight](../hdinsight-plan-virtual-network-deployment.md)文件。 如需 Azure 虛擬網路的詳細資訊，請參閱[虛擬網路概觀](../../virtual-network/virtual-networks-overview.md)文件。

* SQL Server 也必須設定為允許 SQL 驗證。 如需詳細資訊，請參閱[選擇驗證模式](https://msdn.microsoft.com/ms144284.aspx)文件。

* 您可能必須設定 SQL Server 以接受遠端連線。 如需詳細資訊，請參閱[如何疑難排解 SQL Server Database Engine 連線](https://social.technet.microsoft.com/wiki/contents/articles/2102.how-to-troubleshoot-connecting-to-the-sql-server-database-engine.aspx)文件 (英文)。

## <a name="next-steps"></a>後續步驟

現在，您已經學會了如何使用 Sqoop。 若要深入了解，請參閱：

* [將 Apache Oozie 與 HDInsight](../hdinsight-use-oozie-linux-mac.md)一起使用 ： 在 Oozie 工作流中使用 Sqoop 操作。
* [使用 HDInsight 分析航班延誤資料](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)：使用互動式查詢分析航班延誤資料，然後使用 Sqoop 將資料匯出到 Azure SQL 資料庫。
* [將資料上傳至 HDInsight](../hdinsight-upload-data.md)：尋找可將資料上傳至 HDInsight/Azure Blob 儲存體的其他方法。
