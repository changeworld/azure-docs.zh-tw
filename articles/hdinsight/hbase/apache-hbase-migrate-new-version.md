---
title: 將 HBase 叢集移轉至新的版本 - Azure HDInsight
description: 如何將 Apache HBase 群集遷移到 Azure HDInsight 中的較新版本。
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/02/2020
ms.openlocfilehash: 30cda7a83feddaeb41385252a61d1dc68a881a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646501"
---
# <a name="migrate-an-apache-hbase-cluster-to-a-new-version"></a>將 Apache HBase 叢集移轉至新的版本

本文討論將 Azure HDInsight 上的 Apache HBase 群集更新到較新版本所需的步驟。

升級時的停機時間不應太長，為大約幾分鐘。 這個停機時間是因排清所有記憶體內部資料的步驟，接著在新叢集上設定及重新啟動服務的時間所造成。 您的結果會依節點數目、資料量和其他變數而有所不同。

## <a name="review-apache-hbase-compatibility"></a>檢閱 Apache HBase 相容性

在升級 Apache HBase 之前，請確認來源和目的地叢集上的 HBase 版本相容。 如需詳細資訊，請參閱[可搭配 HDInsight 使用的 Apache Hadoop 元件和版本](../hdinsight-component-versioning.md)。

> [!NOTE]  
> 強烈建議您檢閱 [HBase 書籍](https://hbase.apache.org/book.html#upgrading)中的版本相容性矩陣。 任何重大的不相容都需在 HBase 版本的版本資訊中加以說明。

下面是一個示例版本相容性矩陣。 Y 表示相容性，N 表示潛在的不相容：

| 相容性類型 | 主要版本| 次要版本 | Patch |
| --- | --- | --- | --- |
| 用戶端-伺服器連線相容性 | N | Y | Y |
| 伺服器-伺服器相容性 | N | Y | Y |
| 檔案格式相容性 | N | Y | Y |
| 用戶端 API 相容性 | N | Y | Y |
| 用戶端二進位相容性 | N | N | Y |
| **伺服器端有限的 API 相容性** |  |  |  |
| 穩定 | N | Y | Y |
| 發展中 | N | N | Y |
| 不穩定 | N | N | N |
| 相依性相容性 | N | Y | Y |
| 作業相容性 | N | N | Y |

## <a name="upgrade-with-same-apache-hbase-major-version"></a>以相同的 Apache HBase 主要版本進行升級

要在 Azure HDInsight 上升級 Apache HBase 群集，請完成以下步驟：

1. 請確定您的應用程式與新的版本相容，如 HBase 相容性矩陣和版本資訊中所示。 在執行 HDInsight 和 HBase 目標版本的叢集中測試您的應用程式。

1. 使用包含不同容器名稱的相同儲存體帳戶來[設定新的目的地 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)：

    ![使用相同的儲存體帳戶，但建立不同的容器](./media/apache-hbase-migrate-new-version/same-storage-different-container.png)

1. 刷新源 HBase 群集，這是要升級的群集。 HBase 會將內送資料寫入名為 _memstore_ 的記憶體內部存放區中。 在 memstore 達到特定大小後，HBase 將其刷新到磁片，以便群集的存儲帳戶中長期存儲。 將舊的叢集刪除時，會回收 memstores，可能還會遺失資料。 若要將每個資料表的 memstore 手動排清至磁碟，請執行下列指令碼。 此指令碼的最新版本位於 Azure 的 [GitHub](https://raw.githubusercontent.com/Azure/hbase-utils/master/scripts/flush_all_tables.sh)。

    ```bash
    #!/bin/bash
    
    #-------------------------------------------------------------------------------#
    # SCRIPT TO FLUSH ALL HBASE TABLES.
    #-------------------------------------------------------------------------------#
    
    LIST_OF_TABLES=/tmp/tables.txt
    HBASE_SCRIPT=/tmp/hbase_script.txt
    TARGET_HOST=$1
    
    usage ()
    {
        if [[ "$1" == "-h" ]] || [[ "$1" == "--help" ]]
        then
            cat << ...
    
    Usage: 
    
    $0 [hostname]
    
    Providing hostname is optional and not required when the script is executed within HDInsight cluster with access to 'hbase shell'.
    
    However hostname should be provided when executing the script as a script-action from HDInsight portal.
    
    For Example:
    
        1.  Executing script inside HDInsight cluster (where 'hbase shell' is 
            accessible):
    
            $0 
    
            [No need to provide hostname]
    
        2.  Executing script from HDinsight Azure portal:
    
            Provide Script URL.
    
            Provide hostname as a parameter (i.e. hn0, hn1, hn2.. or wn2 etc.).
    ...
            exit
        fi
    }
    
    validate_machine ()
    {
        THIS_HOST=`hostname`
    
        if [[ ! -z "$TARGET_HOST" ]] && [[ $THIS_HOST  != $TARGET_HOST* ]]
        then
            echo "[INFO] This machine '$THIS_HOST' is not the right machine ($TARGET_HOST) to execute the script."
            exit 0
        fi
    }
    
    get_tables_list ()
    {
    hbase shell << ... > $LIST_OF_TABLES 2> /dev/null
        list
        exit
    ...
    }
    
    add_table_for_flush ()
    {
        TABLE_NAME=$1
        echo "[INFO] Adding table '$TABLE_NAME' to flush list..."
        cat << ... >> $HBASE_SCRIPT
            flush '$TABLE_NAME'
    ...
    }
    
    clean_up ()
    {
        rm -f $LIST_OF_TABLES
        rm -f $HBASE_SCRIPT
    }
    
    ########
    # MAIN #
    ########
    
    usage $1
    
    validate_machine
    
    clean_up
    
    get_tables_list
    
    START=false
    
    while read LINE 
    do 
        if [[ $LINE == TABLE ]] 
        then
            START=true
            continue
        elif [[ $LINE == *row*in*seconds ]]
        then
            break
        elif [[ $START == true ]]
        then
            add_table_for_flush $LINE
        fi
    
    done < $LIST_OF_TABLES
    
    cat $HBASE_SCRIPT
    
    hbase shell $HBASE_SCRIPT << ... 2> /dev/null
    exit
    ...
    
    ```

1. 停止擷取舊的 HBase 叢集。

1. 若要確定系統已排清 memstore 中的任何新資料，請再次執行上述指令碼。

1. 登錄到舊群集上的[Apache Ambari](https://ambari.apache.org/) ，`https://OLDCLUSTERNAME.azurehdidnsight.net`並停止 HBase 服務。 當您提示確認要停止服務時，選中此框可打開 HBase 的維護模式。 如需連線至 Ambari 及使用 Ambari 的詳細資訊，請參閱[使用 Ambari Web UI 管理 HDInsight 叢集](../hdinsight-hadoop-manage-ambari.md)。

    ![在 Ambari 中，按一下"服務> HBase >"在服務操作下停止"](./media/apache-hbase-migrate-new-version/stop-hbase-services1.png)

    ![勾選 [開啟 HBase 的維護模式] 核取方塊，然後確認](./media/apache-hbase-migrate-new-version/turn-on-maintenance-mode.png)

1. 在新的 HDInsight 群集上登錄到 Ambari。 變更 `fs.defaultFS` HDFS 設定，以指向原始叢集所使用的容器名稱。 此設定位於 [HDFS] > [設定] > [進階] > [進階核心網站]**** 之下。

    ![在 Ambari 中，按一下"服務> HDFS >配置>高級](./media/apache-hbase-migrate-new-version/hdfs-advanced-settings.png)

    ![在 Ambari 中，變更容器名稱](./media/apache-hbase-migrate-new-version/change-container-name.png)

1. 如果不將 HBase 群集與增強寫入功能一起使用，請跳過此步驟。 它僅需要具有增強寫入功能的 HBase 群集。

   更改路徑`hbase.rootdir`以指向原始群集的容器。

    ![在 Ambari 中，更改 HBase 根迪爾的容器名稱](./media/apache-hbase-migrate-new-version/change-container-name-for-hbase-rootdir.png)

1. 如果要將 HDInsight 3.6 升級到 4.0，請按照以下步驟操作，否則請跳到步驟 10：
    1. 通過選擇"**全部必需的服務重新開機****"，** > 重新開機 Ambari 中的所有必需服務。
    1. 停止 HBase 服務。
    1. SSH 到動物園管理員節點，並執行[zkCli](https://github.com/go-zkcli/zkcli) `rmr /hbase-unsecure`命令，從 Zookeeper 中刪除 HBase 根 znode。
    1. 重新開機 HBase。

1. 如果要升級到除 4.0 之外的任何其他 HDInsight 版本，請按照以下步驟操作：
    1. 儲存您的變更。
    1. 如 Ambari 所示，重新啟動所有必要的服務。

1. 將您的應用程式指向新的叢集。

    > [!NOTE]  
    > 您應用程式的靜態 DNS 會在升級時變更。 您不必將此 DNS 寫入程式碼，而是可以在網域名稱的 DNS 設定中設定 CNAME，以指向叢集的名稱。 另一個選項是使用您應用程式的組態檔，以便您無須重新部署即可進行更新。

1. 開始擷取，以查看一切是否皆如預期運作。

1. 如果新的叢集令人滿意，請將原始叢集刪除。

## <a name="next-steps"></a>後續步驟

若要深入了解 [Apache HBase](https://hbase.apache.org/) 和升級 HDInsight 叢集，請參閱下列文章：

* [將 HDInsight 叢集升級為更新的版本](../hdinsight-upgrade-cluster.md)
* [使用 Apache Ambari Web UI 監視和管理 Azure HDInsight](../hdinsight-hadoop-manage-ambari.md)
* [Apache Hadoop 元件和版本](../hdinsight-component-versioning.md)
* [使用 Apache Ambari 將組態最佳化](../hdinsight-changing-configs-via-ambari.md#apache-hbase-optimization-with-the-ambari-web-ui)
