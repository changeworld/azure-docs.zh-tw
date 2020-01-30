---
title: 將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4。0
description: 瞭解如何將 HDInsight 3.6 上的 Apache Hive 工作負載遷移至 HDInsight 4.0。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: eceb4b312476d701ec8ce4eb0ce4886621824b3a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841586"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4。0

本檔說明如何將 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作負載遷移至 HDInsight 4.0。 HDInsight 4.0 提供較新的 Hive 和 LLAP 功能，例如具體化視圖和查詢結果快取。 當您將工作負載遷移至 HDInsight 4.0 時，您可以使用在 HDInsight 3.6 上未提供的多個 Hive 3 新功能。

本文涵蓋下列主題：

* 將 Hive 元資料移轉至 HDInsight 4。0
* 安全地遷移 ACID 和非 ACID 資料表
* 跨 HDInsight 版本保留 Hive 安全性原則
* 從 HDInsight 3.6 到 HDInsight 4.0 的查詢執行和調試

Hive 的其中一個優點是能夠將中繼資料匯出到外部資料庫（稱為 Hive 中繼存放區）。 **Hive 中繼存放區**會負責儲存資料表統計資料，包括資料表儲存位置、資料行名稱和資料表索引資訊。 中繼存放區資料庫架構在 Hive 版本之間有所不同。 若要安全地升級 Hive 中繼存放區，建議的方式是建立複本並升級複本，而不是目前的生產環境。

## <a name="copy-metastore"></a>複製中繼存放區

HDInsight 3.6 和 HDInsight 4.0 需要不同的中繼存放區架構，而且無法共用單一中繼存放區。

### <a name="external-metastore"></a>外部中繼存放區

建立外部中繼存放區的新複本。 如果您使用的是外部中繼存放區，建立中繼存放區複本的其中一個安全且簡單的方法，就是使用 SQL Database restore 函數來還原具有不同名稱的[資料庫](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore)。  請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](../hdinsight-use-external-metadata-stores.md)，以深入瞭解如何將外部中繼存放區附加至 HDInsight 叢集。

### <a name="internal-metastore"></a>內部中繼存放區

如果您使用內部中繼存放區，您可以使用查詢來匯出 Hive 中繼存放區中的物件定義，並將其匯入至新的資料庫。

1. 使用[安全殼層（SSH）用戶端](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到 HDInsight 叢集。

1. 輸入下列命令，從開啟的 SSH 會話連接到 HiveServer2 與您的[Beeline 用戶端](../hadoop/apache-hadoop-use-hive-beeline.md)：

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; do echo "create database $d; use $d;" >> alltables.sql; for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"` ; do ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

    此命令會產生名為**alltables.sql**的檔案。 因為無法刪除/重新建立預設資料庫，請移除**alltables.sql**中的 `create database default;` 語句。

1. 結束您的 SSH 會話。 然後輸入 scp 命令，以在本機下載**alltables.sql** 。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.sql c:/hdi
    ```

1. 將**alltables.sql**上傳至*新*的 HDInsight 叢集。

    ```bash
    scp c:/hdi/alltables.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然後使用 SSH 連接到*新*的 HDInsight 叢集。 從 SSH 會話執行下列程式碼：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.sql
    ```

## <a name="upgrade-metastore"></a>升級中繼存放區

中繼存放區**複製**完成後，請在現有 HDInsight 3.6 叢集上的[腳本動作](../hdinsight-hadoop-customize-cluster-linux.md)中執行架構升級腳本，將新的中繼存放區升級至 Hive 3 架構。 這可讓資料庫附加為 HDInsight 4.0 中繼存放區。

使用下表中的值。 將 `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` 取代為**複製**之 Hive 中繼存放區的適當值，並以空格分隔。 指定 SQL server 名稱時，請勿包含 ". database.windows.net"。

|屬性 | 值 |
|---|---|
|指令碼類型|- 自訂|
|名稱|Hive 升級|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|節點類型|Head|
|參數|SQLSERVERNAME DATABASENAME 使用者名稱密碼|

> [!Warning]  
> 無法反轉將 HDInsight 3.6 中繼資料架構轉換成 HDInsight 4.0 架構的升級。

您可以對資料庫執行下列 SQL 查詢，以確認升級：

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>將 Hive 資料表遷移至 HDInsight 4。0

完成先前的步驟集以將 Hive 中繼存放區遷移至 HDInsight 4.0 之後，從叢集中執行 `show tables` 或 `show databases`，即可從 HDInsight 4.0 叢集內看到記錄在中繼存放區中的資料表和資料庫。 如需 HDInsight 4.0 叢集中查詢執行的相關資訊，請參閱[跨 hdinsight 版本查詢執行](#query-execution-across-hdinsight-versions)。

不過，在叢集可以存取必要的儲存體帳戶之前，資料表中的實際資料都無法存取。 若要確保您的 HDInsight 4.0 叢集可以存取與舊的 HDInsight 3.6 叢集相同的資料，請完成下列步驟：

1. 判斷資料表或資料庫的 Azure 儲存體帳戶。

1. 如果您的 HDInsight 4.0 叢集已在執行，請透過 Ambari 將 Azure 儲存體帳戶連結至叢集。 如果您尚未建立 HDInsight 4.0 叢集，請確定 Azure 儲存體帳戶已指定為主要或次要叢集儲存體帳戶。 如需有關將儲存體帳戶新增至 HDInsight 叢集的詳細資訊，請參閱[將其他儲存體帳戶新增至 hdinsight](../hdinsight-hadoop-add-storage.md)。

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>部署新的 HDInsight 4.0 並連接到新的中繼存放區

架構升級完成後，部署新的 HDInsight 4.0 叢集並連接升級後的中繼存放區。 如果您已部署4.0，請加以設定，以便從 Ambari 連接到中繼存放區。

## <a name="run-schema-migration-script-from-hdinsight-40"></a>從 HDInsight 4.0 執行架構遷移腳本

HDInsight 3.6 和 HDInsight 4.0 中的資料表會以不同方式處理。 基於這個理由，您不能針對不同版本的叢集共用相同的資料表。 如果您想要與 HDInsight 4.0 同時使用 HDInsight 3.6，您必須針對每個版本各有不同的資料複本。

您的 Hive 工作負載可能包含 ACID 和非 ACID 資料表的混合。 HDInsight 3.6 （Hive 2）上的 Hive 和 HDInsight 4.0 （Hive 3）上的 Hive 之間有一個主要差異，就是資料表的 ACID 合規性。 在 HDInsight 3.6 中，啟用 Hive ACID 合規性需要額外的設定，但在 HDInsight 4.0 資料表中，預設為符合 ACID 規範。 遷移前唯一需要的動作是針對3.6 叢集上的 ACID 資料表執行主要壓縮。 從 Hive 視圖或從 Beeline 執行下列查詢：

```sql
alter table myacidtable compact 'major';
```

這是必要的壓縮，因為 HDInsight 3.6 和 HDInsight 4.0 ACID 資料表會以不同的方式瞭解 ACID 差異。 壓縮會強制執行可確保一致性的全新平板電腦。 [Hive 遷移檔](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第4節包含 HDINSIGHT 3.6 ACID 資料表的大量壓縮指引。

完成中繼存放區的遷移和壓縮步驟之後，您就可以遷移實際的倉儲。 完成 Hive 倉儲遷移之後，HDInsight 4.0 倉儲會有下列屬性：

|3.6 |4.0 |
|---|---|
|外部資料表|外部資料表|
|非交易式受控資料表|外部資料表|
|交易式 managed 資料表|受控資料表|

在執行遷移之前，您可能需要調整倉儲的屬性。 例如，如果您預期某些資料表會由協力廠商（例如 HDInsight 3.6 叢集）存取，則在完成遷移之後，該資料表必須是外部的。 在 HDInsight 4.0 中，所有受控資料表都是交易式。 因此，HDInsight 4.0 中的受控資料表只能由 HDInsight 4.0 叢集存取。

正確設定資料表屬性後，請使用 SSH 命令介面，從其中一個叢集前端節點執行 Hive 倉儲遷移工具：

1. 使用 SSH 連接到您的叢集前端節點。 如需指示，請參閱[使用 SSH 連接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 藉由執行 `sudo su - hive`，將登入命令介面開啟為 Hive 使用者
1. 藉由執行 `ls /usr/hdp`來判斷資料平臺堆疊版本。 這會顯示您在下一個命令中應該使用的版本字串。
1. 從 shell 執行下列命令。 將 `STACK_VERSION` 取代為上一個步驟中的版本字串：

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

在遷移工具完成之後，您的 Hive 倉儲就已準備好可供 HDInsight 4.0。

> [!Important]  
> HDInsight 4.0 中的受控資料表（包括從3.6 遷移的資料表）不應由其他服務或應用程式（包括 HDInsight 3.6 叢集）存取。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保護 Hive

從 HDInsight 3.6 開始，HDInsight 會與使用 HDInsight 企業安全性套件（ESP）的 Azure Active Directory 整合。 ESP 會使用 Kerberos 和 Apache Ranger 來管理叢集中特定資源的許可權。 針對 HDInsight 3.6 中的 Hive 部署的 Ranger 原則，可以使用下列步驟遷移至 HDInsight 4.0：

1. 流覽至 HDInsight 3.6 叢集中的 [Ranger Service Manager] 面板。
2. 流覽至名為**HIVE**的原則，並將原則匯出至 json 檔案。
3. 請確定匯出的原則 json 中所參考的所有使用者都存在於新的叢集中。 如果使用者在原則 json 中被參考，但不存在於新的叢集中，請將使用者新增至新叢集，或從原則中移除參考。
4. 流覽至 HDInsight 4.0 叢集中的 [ **Ranger Service Manager** ] 面板。
5. 流覽至名為**HIVE**的原則，並匯入步驟2中的 ranger 原則 json。

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>視需要在測試應用程式中檢查相容性和修改代碼

當遷移工作負載（例如現有的程式和查詢）時，請查看版本資訊和檔中的變更，並視需要套用變更。 如果您的 HDInsight 3.6 叢集使用共用 Spark 和 Hive 中繼存放區，則需要[使用 Hive 倉儲連接器的其他](./apache-hive-warehouse-connector.md)設定。

## <a name="deploy-new-app-for-production"></a>部署適用于生產環境的新應用程式

若要切換到新的叢集，例如您可以安裝新的用戶端應用程式，並將其當做新的生產環境使用，或者您可以升級現有的用戶端應用程式，並切換至 HDInsight 4.0。

## <a name="switch-hdinsight-40-to-the-production"></a>將 HDInsight 4.0 切換到生產環境

如果在測試期間，在中繼存放區中建立了差異，您就必須在切換之前更新變更。 在此情況下，您可以匯出 & 匯入中繼存放區，然後再升級一次。

## <a name="remove-the-old-production"></a>移除舊的生產環境

確認發行已完成且完全正常運作之後，您就可以移除3.6 版和先前的中繼存放區。 請先確認所有專案都已遷移，再刪除環境。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本的查詢執行

有兩種方式可在 HDInsight 3.6 叢集中執行和偵測 Hive/LLAP 查詢。 HiveCLI 提供命令列體驗，而 Tez view/Hive view 則提供以 GUI 為基礎的工作流程。

在 HDInsight 4.0 中，HiveCLI 已由 Beeline 取代。 HiveCLI 是 Hiveserver 1 的 thrift 用戶端，而 Beeline 是提供存取 Hiveserver 2 的 JDBC 用戶端。 Beeline 也可以用來連接到任何其他與 JDBC 相容的資料庫端點。 Beeline 在 HDInsight 4.0 上是現成可用的，不需要任何安裝。

在 HDInsight 3.6 中，與 Hive 伺服器互動的 GUI 用戶端是 Ambari Hive 視圖。 HDInsight 4.0 不會隨附 Ambari View。 我們提供了一種方法，讓我們的客戶使用資料分析 Studio （DAS），這不是核心的 HDInsight 服務。 DAS 不會隨附現成的 HDInsight 叢集，且不是正式支援的套件。 不過，您可以使用[腳本動作](../hdinsight-hadoop-customize-cluster-linux.md)，在叢集上安裝 DAS，如下所示：

|屬性 | 值 |
|---|---|
|指令碼類型|- 自訂|
|名稱|轉移|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|節點類型|Head|

等待10到15分鐘，然後使用此 URL 啟動資料分析 Studio： `https://CLUSTERNAME.azurehdinsight.net/das/`。

在存取 DAS 之前，可能需要重新整理 Ambari UI 和/或重新開機所有 Ambari 元件。

安裝 DAS 之後，如果您沒有看到您在查詢檢視器中執行的查詢，請執行下列步驟：

1. 如本指南中所述，設定 Hive、Tez 和 DAS 的配置，以進行[DAS 安裝的疑難排解](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 請確定下列 Azure 儲存體目錄的設置是分頁 blob，而且它們列在 `fs.azure.page.blob.dirs`：
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在兩個前端節點上重新開機 HDFS、Hive、Tez 和 DAS。

## <a name="next-steps"></a>後續步驟

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探討](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 資料表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
