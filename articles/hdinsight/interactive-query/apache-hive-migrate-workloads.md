---
title: 將 Azure HDInsight 3.6 蜂巢工作負載遷移到 HDInsight 4.0
description: 瞭解如何在 HDInsight 3.6 上將 Apache Hive 工作負載遷移到 HDInsight 4.0。
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: ec96189185a06c1fcbd95eed6216ade47f3089c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214654"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>將 Azure HDInsight 3.6 蜂巢工作負載遷移到 HDInsight 4.0

本文檔演示如何將 HDInsight 3.6 上的 Apache Hive 和 LLAP 工作負載遷移到 HDInsight 4.0。 HDInsight 4.0 提供較新的 Hive 和 LLAP 功能，如具體化視圖和查詢結果緩存。 將工作負載遷移到 HDInsight 4.0 時，可以使用 HDInsight 3.6 上不可用的許多較新的 Hive 3 功能。

本文涵蓋以下主題：

* 將 Hive 元資料移轉到 HDInsight 4.0
* ACID 和非 ACID 表的安全遷移
* 跨 HDInsight 版本維護 Hive 安全性原則
* 從 HDInsight 3.6 到 HDInsight 4.0 的查詢執行和調試

Hive 的一個優點是能夠將中繼資料匯出到外部資料庫（稱為 Hive Metastore）。 **Hive Metastore**負責存儲表統計資訊，包括表存儲位置、列名稱和表索引資訊。 元存儲資料庫架構在 Hive 版本之間有所不同。 安全升級 Hive 元存儲的建議方法是棄置站台並升級副本，而不是當前生產環境。

## <a name="copy-metastore"></a>複製元存儲

HDInsight 3.6 和 HDInsight 4.0 需要不同的元存儲架構，不能共用單個元存儲。

### <a name="external-metastore"></a>外部元存儲

創建外部元存儲的新副本。 如果使用外部元存儲，則創建元存儲副本的安全簡便方法之一是使用 SQL 資料庫還原函數使用不同的名稱[還原資料庫](../../sql-database/sql-database-recovery-using-backups.md#point-in-time-restore)。  請參閱[使用 Azure HDInsight 中的外部中繼資料存儲](../hdinsight-use-external-metadata-stores.md)瞭解有關將外部元存儲附加到 HDInsight 群集的更多內容。

### <a name="internal-metastore"></a>內部元存儲

如果使用內部元存儲，則可以使用查詢匯出 Hive 元存儲中的物件定義，並將它們導入到新資料庫中。

完成此腳本後，假定舊群集將不再用於訪問腳本中引用的任何表或資料庫。

> [!NOTE]
> 在 ACID 表中，將創建表下方資料的新副本。

1. 使用[安全外殼 （SSH） 用戶端](../hdinsight-hadoop-linux-use-ssh-unix.md)連接到 HDInsight 群集。

1. 通過輸入以下命令，從打開的 SSH 會話與[蜂林用戶端](../hadoop/apache-hadoop-use-hive-beeline.md)連接到 HiveServer2：

    ```hiveql
    for d in `beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show databases;"`; 
    do
        echo "Scanning Database: $d"
        echo "create database if not exists $d; use $d;" >> alltables.hql; 
        for t in `beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show tables;"`;
        do
            echo "Copying Table: $t"
            ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;"`;

            echo "$ddl;" >> alltables.hql;
            lowerddl=$(echo $ddl | awk '{print tolower($0)}')
            if [[ $lowerddl == *"'transactional'='true'"* ]]; then
                if [[ $lowerddl == *"partitioned by"* ]]; then
                    # partitioned
                    raw_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "CREATE TABLE .*" | cut -d"(" -f2- | cut -f1 -d")" | sed 's/`//g');
                    ptn_cols=$(beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table $t;" | tr '\n' ' ' | grep -io "PARTITIONED BY .*" | cut -f1 -d")" | cut -d"(" -f2- | sed 's/`//g');
                    final_cols=$(echo "(" $raw_cols "," $ptn_cols ")")

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t $final_cols TBLPROPERTIES ('transactional'='false');";
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    parsed_ptn_cols=$(echo $ptn_cols| sed 's/ [a-z]*,/,/g' | sed '$s/\w*$//g');
                    echo "create table flattened_$t $final_cols;" >> alltables.hql;
                    echo "load data inpath '$dir' into table flattened_$t;" >> alltables.hql;
                    echo "insert into $t partition($parsed_ptn_cols) select * from flattened_$t;" >> alltables.hql;
                    echo "drop table flattened_$t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                else
                    # not partitioned
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "create external table ext_$t like $t TBLPROPERTIES ('transactional'='false');";
                    staging_ddl=`beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "show create table ext_$t;"`;
                    dir=$(echo $staging_ddl | grep -io " LOCATION .*" | grep -m1 -o "'.*" | sed "s/'[^-]*//2g" | cut -c2-);

                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "insert into ext_$t select * from $t;";
                    echo "load data inpath '$dir' into table $t;" >> alltables.hql;
                    beeline -u "jdbc:hive2://localhost:10001/$d;transportMode=http" --showHeader=false --silent=true --outputformat=tsv2 -e "drop table ext_$t";
                fi
            fi
            echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t;" >> alltables.hql;
        done;
    done
    ```

    此命令生成名為**alltables.hql**的檔。

1. 結束您的 SSH 工作階段。 然後輸入 scp 命令以在本地下載**所有表.hql。**

    ```bash
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:alltables.hql c:/hdi
    ```

1. 將所有**表.hql**上載到*新的*HDInsight 群集。

    ```bash
    scp c:/hdi/alltables.hql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

1. 然後使用 SSH 連接到*新的*HDInsight 群集。 從 SSH 會話運行以下代碼：

    ```bash
    beeline -u "jdbc:hive2://localhost:10001/;transportMode=http" -i alltables.hql
    ```


## <a name="upgrade-metastore"></a>升級元存儲

元存儲**副本**完成後，在現有 HDInsight 3.6 群集上運行[腳本操作](../hdinsight-hadoop-customize-cluster-linux.md)中的架構升級腳本，以將新的元存儲升級到 Hive 3 架構。 這允許將資料庫附加到 HDInsight 4.0 元存儲。

進一步使用下表中的值。 替換為`SQLSERVERNAME DATABASENAME USERNAME PASSWORD`**複製**的 Hive 元存儲的相應值，由空格分隔。 指定 SQL 伺服器名稱時，不要包含".database.windows.net"。

|屬性 | 值 |
|---|---|
|指令碼類型|- 自訂|
|名稱|蜂巢升級|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh`|
|節點類型|Head|
|參數|SQLServer 名稱 資料庫名稱使用者名密碼|

> [!Warning]  
> 無法逆轉將 HDInsight 3.6 中繼資料架構轉換為 HDInsight 4.0 架構的升級。

您可以通過針對資料庫運行以下 SQL 查詢來驗證升級：

```sql
select * from dbo.version
```

## <a name="migrate-hive-tables-to-hdinsight-40"></a>將 Hive 表遷移到 HDInsight 4.0

完成將 Hive Metastore 遷移到 HDInsight 4.0 的前幾組步驟後，通過執行`show tables`或`show databases`從群集內從 HDInsight 4.0 群集中記錄的表和資料庫將可見。 有關 HDInsight 4.0 群集中的查詢執行的資訊，請參閱[HDInsight 版本的查詢執行](#query-execution-across-hdinsight-versions)。

但是，在群集有權訪問必要的存儲帳戶之前，無法訪問表中的實際資料。 為了確保 HDInsight 4.0 群集可以訪問與舊 HDInsight 3.6 群集相同的資料，請完成以下步驟：

1. 確定表或資料庫的 Azure 存儲帳戶。

1. 如果 HDInsight 4.0 群集已在運行，則通過 Ambari 將 Azure 存儲帳戶附加到群集。 如果尚未創建 HDInsight 4.0 群集，請確保 Azure 存儲帳戶指定為主群集或輔助群集存儲帳戶。 有關將存儲帳戶添加到 HDInsight 群集的詳細資訊，請參閱[將其他存儲帳戶添加到 HDInsight](../hdinsight-hadoop-add-storage.md)。

## <a name="deploy-new-hdinsight-40-and-connect-to-the-new-metastore"></a>部署新的 HDInsight 4.0 並連接到新的元存儲

架構升級完成後，部署新的 HDInsight 4.0 群集並連接升級的元存儲。 如果已部署 4.0，請將其設置，以便可以從 Ambari 連接到元存儲。

## <a name="run-schema-migration-script-from-hdinsight-40"></a>從 HDInsight 4.0 運行架構遷移腳本

在 HDInsight 3.6 和 HDInsight 4.0 中，表的處理方式不同。 因此，不能為不同版本的群集共用相同的表。 如果要在 HDInsight 4.0 的同時使用 HDInsight 3.6，則必須為每個版本具有單獨的資料副本。

您的 Hive 工作負載可能包括 ACID 和非 ACID 表的組合。 HDInsight 3.6（Hive 2）上的 Hive 和 HDInsight 4.0（Hive 3）上的 Hive 之間的一個關鍵區別是表的 ACID 合規性。 在 HDInsight 3.6 中，啟用 Hive ACID 合規性需要額外的配置，但在 HDInsight 4.0 表中預設符合 ACID。 遷移前需要執行的唯一操作是針對 3.6 群集上的 ACID 表運行主要壓縮。 從 Hive 視圖或 Beeline 運行以下查詢：

```sql
alter table myacidtable compact 'major';
```

由於 HDInsight 3.6 和 HDInsight 4.0 ACID 表對 ACID 增量的理解不同，因此需要進行此壓縮。 壓縮強制執行一個乾淨的石板，保證一致性。 [Hive 遷移文檔](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html)的第 4 節包含有關 HDInsight 3.6 ACID 表批量壓縮的指導。

完成元存儲遷移和壓縮步驟後，可以遷移實際倉庫。 完成 Hive 倉庫遷移後，HDInsight 4.0 倉庫將具有以下屬性：

|3.6 |4.0 |
|---|---|
|外部資料表|外部資料表|
|非事務託管表|外部資料表|
|事務託管表|託管表|

在執行遷移之前，您可能需要調整倉庫的屬性。 例如，如果您希望某些表將由協力廠商（如 HDInsight 3.6 群集）訪問，則該表必須在遷移完成後是外部的。 在 HDInsight 4.0 中，所有託管表都是事務性的。 因此，HDInsight 4.0 中的託管表只能由 HDInsight 4.0 群集訪問。

正確設置表屬性後，使用 SSH shell 從其中一個群集頭節點執行 Hive 倉庫遷移工具：

1. 使用 SSH 連接到群集頭節點。 有關說明，請參閱[使用 SSH 連接到 HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. 通過運行以 Hive 使用者身份打開登錄外殼`sudo su - hive`
1. 通過執行確定資料平臺堆疊版本`ls /usr/hdp`。 這將顯示應在下一個命令中使用的版本字串。
1. 從 shell 執行以下命令。 替換為`STACK_VERSION`上一步驟中的版本字串：

```bash
/usr/hdp/STACK_VERSION/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true -m automatic --modifyManagedTables
```

遷移工具完成後，您的 Hive 倉庫將準備好用於 HDInsight 4.0。

> [!Important]  
> HDInsight 4.0 中的託管表（包括從 3.6 遷移的表）不應由其他服務或應用程式（包括 HDInsight 3.6 群集）訪問。

## <a name="secure-hive-across-hdinsight-versions"></a>跨 HDInsight 版本保護蜂巢

自 HDInsight 3.6 以來，HDInsight 使用 HDInsight 企業安全包 （ESP） 與 Azure 活動目錄集成。 ESP 使用 Kerberos 和 Apache Ranger 管理群集中特定資源的許可權。 通過以下步驟，可以在 HDInsight 3.6 中針對 Hive 部署的遊俠策略遷移到 HDInsight 4.0：

1. 導航到 HDInsight 3.6 群集中的遊俠服務管理員面板。
2. 導航到名為**HIVE**的策略，並將策略匯出到 json 檔。
3. 確保匯出的策略 json 中引用的所有使用者都存在於新群集中。 如果在策略 json 中引用使用者，但新群集中不存在，請將使用者添加到新群集或從策略中刪除引用。
4. 導航到 HDInsight 4.0 群集中的**遊俠服務管理員**面板。
5. 導航到名為**HIVE**的策略，並從步驟 2 導入遊俠策略 json。

## <a name="check-compatibility-and-modify-codes-as-needed-in-test-app"></a>檢查相容性並根據需要修改測試應用中的代碼

遷移工作負荷（如現有程式和查詢）時，請檢查版本資訊和文檔中的更改，並根據需要應用更改。 如果您的 HDInsight 3.6 群集使用共用 Spark 和 Hive 元存儲，則需要[使用 Hive 倉庫連接器進行其他配置](./apache-hive-warehouse-connector.md)。

## <a name="deploy-new-app-for-production"></a>部署用於生產的新應用

要切換到新群集，例如，您可以安裝新的用戶端應用程式並將其用作新的生產環境，也可以升級現有用戶端應用程式並切換到 HDInsight 4.0。

## <a name="switch-hdinsight-40-to-the-production"></a>將 HDInsight 4.0 切換到生產

如果在測試時在元存儲中創建了差異，則需要在切換之前更新更改。 在這種情況下，您可以匯出&導入元存儲，然後再次升級。

## <a name="remove-the-old-production"></a>刪除舊生產

確認版本已完成且完全正常運行後，可以刪除版本 3.6 和以前的元存儲。 在刪除環境之前，請確保遷移所有內容。

## <a name="query-execution-across-hdinsight-versions"></a>跨 HDInsight 版本的查詢執行

在 HDInsight 3.6 群集中執行和調試 Hive/LLAP 查詢有兩種方法。 HiveCLI 提供命令列體驗，Tez 視圖/Hive 視圖提供基於 GUI 的工作流。

在 HDInsight 4.0 中，HiveCLI 已替換為 Beeline。 HiveCLI 是 Hiveserver 1 的節儉用戶端，而 Beeline 是 JDBC 用戶端，提供對 Hiveserver 2 的訪問。 Beeline 還可用於連接到任何其他與 JDBC 相容的資料庫終結點。 Beeline 可在 HDInsight 4.0 上開箱即用，無需安裝任何裝置。

在 HDInsight 3.6 中，用於與 Hive 伺服器交互的 GUI 用戶端是 Ambari Hive 視圖。 HDInsight 4.0 不隨安巴里視圖一起發貨。 我們為我們的客戶提供了一種使用資料分析工作室 （DAS） 的方式，該工作室不是核心的 HDInsight 服務。 DAS 不會附帶 HDInsight 群集開箱即用，也不是官方支援的套裝軟體。 但是，可以使用[腳本操作](../hdinsight-hadoop-customize-cluster-linux.md)在群集上安裝 DAS，如下所示：

|屬性 | 值 |
|---|---|
|指令碼類型|- 自訂|
|名稱|Das|
|Bash 指令碼 URI|`https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh`|
|節點類型|Head|

等待 10 到 15 分鐘，然後使用此 URL 啟動`https://CLUSTERNAME.azurehdinsight.net/das/`資料分析工作室： 。

在訪問 DAS 之前，可能需要刷新 Ambari UI 和/或重新開機所有 Ambari 元件。

安裝 DAS 後，如果在查詢檢視器中看不到已運行的查詢，請執行以下步驟：

1. 設置 Hive、Tez 和 DAS 的配置，如[本指南中所述，用於對 DAS 安裝進行故障排除](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html)。
2. 確保以下 Azure 存儲目錄配置是頁面 blob，並且它們列在 ： `fs.azure.page.blob.dirs`
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. 在兩個頭節點上重新開機 HDFS、蜂巢、Tez 和 DAS。

## <a name="next-steps"></a>後續步驟

* [HDInsight 4.0 公告](../hdinsight-version-release.md)
* [HDInsight 4.0 深潛](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [蜂巢 3 ACID 表](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
