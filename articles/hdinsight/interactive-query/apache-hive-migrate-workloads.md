---
title: 將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4.0
description: 瞭解如何將 HDInsight 3.6 上的 Apache Hive 工作負載遷移至 HDInsight 4.0。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/13/2019
ms.openlocfilehash: 313b6afb8bd96f8ae507118cd552110d5f07ff78
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86087511"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>將 Azure HDInsight 3.6 Hive 工作負載遷移至 HDInsight 4.0

本檔說明如何將 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作負載遷移至 HDInsight 4.0。 HDInsight 4.0 提供較新的 Hive 和 LLAP 功能，例如具體化視圖和查詢結果快取。 當您將工作負載遷移至 HDInsight 4.0 時，您可以使用在 HDInsight 3.6 上未提供的多個 Hive 3 新功能。

本文涵蓋下列主題：

* 將 Hive 元資料移轉至 HDInsight 4。0
* 安全地遷移 ACID 和非 ACID 資料表
* 跨 HDInsight 版本保留 Hive 安全性原則
* 從 HDInsight 3.6 到 HDInsight 4.0 的查詢執行和調試

Hive 的其中一個優點是能夠將中繼資料匯出到外部資料庫（稱為 Hive 中繼存放區）。 **Hive 中繼存放區**會負責儲存資料表統計資料，包括資料表儲存位置、資料行名稱和資料表索引資訊。 HDInsight 3.6 和 HDInsight 4.0 需要不同的中繼存放區架構，而且無法共用單一中繼存放區。 若要安全地升級 Hive 中繼存放區，建議的方式是在目前的生產環境中升級複本，而不是原始的。 這份檔需要原始和新的叢集，才能存取相同的儲存體帳戶。 因此，它不會涵蓋另一個區域的資料移轉。

## <a name="migrate-from-external-metastore"></a>從外部中繼存放區遷移

### <a name="1-run-major-compaction-on-acid-tables-in-hdinsight-36"></a>1. 在 HDInsight 3.6 中的 ACID 資料表上執行重大壓縮

HDInsight 3.6 和 HDInsight 4.0 ACID 資料表會以不同的方式瞭解 ACID 差異。 遷移前唯一需要的動作是針對3.6 叢集上的每個 ACID 資料表執行「主要」壓縮。 如需有關壓縮的詳細資訊，請參閱[Hive 語言手冊](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Compact)。

### <a name="2-copy-sql-database"></a>2. 複製 SQL database
建立外部中繼存放區的新複本。 如果您使用的是外部中繼存放區，建立中繼存放區複本的其中一個安全且簡單的方法，就是使用函式來還原具有不同名稱的[資料庫](../../azure-sql/database/recovery-using-backups.md#point-in-time-restore) `RESTORE` 。  請參閱[在 Azure HDInsight 中使用外部中繼資料存放區](../hdinsight-use-external-metadata-stores.md)，以深入瞭解如何將外部中繼存放區附加至 HDInsight 叢集。

### <a name="3-upgrade-metastore-schema"></a>3. 升級中繼存放區架構
中繼存放區**複製**完成後，請在現有 HDInsight 3.6 叢集上的[腳本動作](../hdinsight-hadoop-customize-cluster-linux.md)中執行架構升級腳本，將新的中繼存放區升級至 Hive 3 架構。 （此步驟不需要將新的中繼存放區連接到叢集）。這可讓資料庫附加為 HDInsight 4.0 中繼存放區。

使用下表中的值。 將取代為 `SQLSERVERNAME DATABASENAME USERNAME PASSWORD` Hive 中繼存放區**複製**的適當值，並以空格分隔。 指定 SQL server 名稱時，請勿包含 ". database.windows.net"。

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

### <a name="4-deploy-a-new-hdinsight-40-cluster"></a>4. 部署新的 HDInsight 4.0 叢集

1. 將升級的中繼存放區指定為新叢集的 Hive 中繼存放區。

1. 不過，在叢集可以存取必要的儲存體帳戶之前，資料表中的實際資料都無法存取。
請確定 HDInsight 3.6 叢集中的 Hive 資料表儲存體帳戶已指定為新 HDInsight 4.0 叢集的主要或次要儲存體帳戶。
如需有關將儲存體帳戶新增至 HDInsight 叢集的詳細資訊，請參閱[將其他儲存體帳戶新增至 hdinsight](../hdinsight-hadoop-add-storage.md)。

### <a name="5-complete-migration-with-a-post-upgrade-tool-in-hdinsight-40"></a>5. 在 HDInsight 4.0 中使用升級後工具完成遷移

根據預設，受控資料表在 HDInsight 4.0 上必須符合 ACID 規範。 完成中繼存放區遷移之後，請執行升級後工具，讓先前的非 ACID 受控資料表與 HDInsight 4.0 叢集相容。 此工具會套用下列轉換：

|3.6 |4.0 |
|---|---|
|外部資料表|外部資料表|
|非 ACID 受控資料表|具有屬性 ' external. table. 清除 ' = ' true ' 的外部資料表|
|ACID 受控資料表|ACID 受控資料表|

使用 SSH 命令介面，從 HDInsight 4.0 叢集執行 Hive 升級後工具：

1. 使用 SSH 連接到您的叢集前端節點。 如需指示，請參閱[使用 SSH 連接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 執行來以 Hive 使用者身分開啟登入命令介面`sudo su - hive`
1. 從 shell 執行下列命令。

    ```bash
    STACK_VERSION=$(hdp-select status hive-server2 | awk '{ print $3; }')
    /usr/hdp/$STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
    ```

在工具完成之後，您的 Hive 倉儲就已準備好可供 HDInsight 4.0。

## <a name="migrate-from-internal-metastore"></a>從內部中繼存放區遷移

如果您的 HDInsight 3.6 叢集使用內部 Hive 中繼存放區，請遵循下列步驟來執行腳本，這會產生 Hive 查詢以從中繼存放區匯出物件定義。

HDInsight 3.6 和4.0 叢集必須使用相同的儲存體帳戶。

> [!NOTE]
>
> * 在 ACID 資料表的情況下，將會建立資料表下的新資料複本。
>
> * 此腳本只支援 Hive 資料庫、資料表和資料分割的遷移。 其他中繼資料物件（例如，視圖、Udf 和資料表條件約束）則應該手動複製。
>
> * 此腳本完成後，就會假設舊叢集不再用來存取腳本中所參考的任何資料表或資料庫。
>
> * 所有受控資料表都會在 HDInsight 4.0 中變成交易式。 或者，將資料匯出至屬性為 ' external. table. 清除 ' = ' true ' 的外部資料表，以將資料表保持為非交易式。 例如，
>
>    ```SQL
>    create table tablename_backup like tablename;
>    insert overwrite table tablename_backup select * from tablename;
>    create external table tablename_tmp like tablename;
>    insert overwrite table tablename_tmp select * from tablename;
>    alter table tablename_tmp set tblproperties('external.table.purge'='true');
>    drop table tablename;
>    alter table tablename_tmp rename to tablename;
>    ```

1. 使用[安全殼層（SSH）用戶端](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到 HDInsight 3.6 叢集。

1. 從開啟的 SSH 會話，下載下列腳本檔案以產生名為**alltables.sql**的檔案。

    ```bash
    wget https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/exporthive_hdi_3_6.sh
    chmod 755 exporthive_hdi_3_6.sh
    ```

    * 若為一般 HDInsight 叢集（不含 ESP），只需執行即可 `exporthive_hdi_3_6.sh` 。

    * 針對具有 ESP 的叢集，請 kinit 並修改 beeline 的引數：執行下列程式，為具有完整 Hive 許可權的 Azure AD 使用者定義使用者和網域。

        ```bash
        USER="USER"  # replace USER
        DOMAIN="DOMAIN"  # replace DOMAIN
        DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
        kinit "$USER@$DOMAIN_UPPER"
        ```

        ```bash
        hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
        BEE_CMD="beeline -u 'jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http' -n "$USER@$DOMAIN" --showHeader=false --silent=true --outputformat=tsv2 -e"
        ./exporthive_hdi_3_6.sh "$BEE_CMD"
        ```

1. 結束您的 SSH 工作階段。 然後輸入 scp 命令以在本機下載**alltables.sql** 。

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. 將**alltables.sql**上傳至*新*的 HDInsight 叢集。

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然後使用 SSH 連接到*新*的 HDInsight 4.0 叢集。 從 SSH 會話對此叢集執行下列程式碼：

    不含 ESP 的：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -f alltables.hql
    ```

    含 ESP：

    ```bash
    USER="USER"  # replace USER
    DOMAIN="DOMAIN"  # replace DOMAIN
    DOMAIN_UPPER=$(printf "%s" "$DOMAIN" | awk '{ print toupper($0) }')
    kinit "$USER@$DOMAIN_UPPER"
    ```

    ```bash
    hn0=$(grep hn0- /etc/hosts | xargs | cut -d' ' -f4)
    beeline -u "jdbc:hive2://$hn0:10001/default;principal=hive/_HOST@$DOMAIN_UPPER;auth-kerberos;transportMode=http" -n "$USER@$DOMAIN" -f alltables.hql
    ```

外部中繼存放區遷移的升級後工具不適用於此處，因為 HDInsight 3.6 中的非 ACID 受控資料表會轉換成 HDInsight 4.0 中的 ACID 受控資料表。

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

等待10到15分鐘，然後使用此 URL 啟動資料分析 Studio： `https://CLUSTERNAME.azurehdinsight.net/das/` 。

在存取 DAS 之前，可能需要重新整理 Ambari UI 和/或重新開機所有 Ambari 元件。

安裝 DAS 之後，如果您沒有看到您在查詢檢視器中執行的查詢，請執行下列步驟：

1. 如本指南中所述，設定 Hive、Tez 和 DAS 的配置，以進行[DAS 安裝的疑難排解](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 請確定下列 Azure 儲存體目錄的設置是分頁 blob，而且它們列在下面 `fs.azure.page.blob.dirs` ：
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在兩個前端節點上重新開機 HDFS、Hive、Tez 和 DAS。

## <a name="next-steps"></a>後續步驟

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深入探討](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Hive 3 ACID 資料表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
