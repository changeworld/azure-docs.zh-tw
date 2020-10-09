---
title: 如何在 Azure HDInsight 叢集中使用 Apache Hive 複寫
description: 瞭解如何在 HDInsight 叢集中使用 Hive 複寫，以複寫 Hive 中繼存放區和 Azure Data Lake Storage Gen 2 Data Lake。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: b6a7b9d4e4b8a7caf79e2dbcc1059adc3c9122d7
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843787"
---
# <a name="how-to-use-apache-hive-replication-in-azure-hdinsight-clusters"></a>如何在 Azure HDInsight 叢集中使用 Apache Hive 複寫

在資料庫和倉儲的內容中，複寫是將實體從某個倉儲複製到另一個倉儲的程式。 複製可以套用到整個資料庫或較小的層級，例如資料表或資料分割。 目標是讓複本在基底實體變更時變更。 Apache Hive 上的複寫著重于嚴重損壞修復，並提供單向的主要複製複寫。 在 HDInsight 叢集中，您可以使用 Hive 複寫來 unidirectionally 在 Azure Data Lake Storage Gen2 上複寫 Hive 中繼存放區和相關聯的基礎 data lake。  

以較新版本提供更好的功能，且更快且更不需要耗用大量資源的情況，Hive 複寫已發展多年。 在本文中，我們將討論在 HDInsight 3.6 和 HDInsight 4.0 叢集類型中支援的 Hive 複寫 (Replv2) 。

## <a name="advantages-of-replv2"></a>Replv2 的優點

[Hive ReplicationV2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development) 或 (Replv2) 在使用 Hive 匯 [入-匯出](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ImportExport)的 hive 複寫的第一個版本上有下列優點：

- 以事件為基礎的累加式複寫
- 時間點複寫  
- 減少頻寬需求  
- 減少中繼複本的數目  
- 複寫狀態為維護狀態
- 限制複寫  
- 支援中樞和輪輻模型  
- 支援在 HDInsight 4.0 中 (ACID 資料表) 

## <a name="replication-phases"></a>複寫階段

以 Hive 事件為基礎的複寫是在主要與次要叢集之間進行設定。 這項複寫是由兩個不同的階段所組成：啟動載入和增量執行。

### <a name="bootstrapping"></a>引導

啟動載入的目的是要執行一次，以將資料庫的基礎狀態從主要複本複寫至次要複本。 您可以設定啟動載入（如有需要），以在必須啟用複寫的目標資料庫中包含資料表的子集。

### <a name="incremental-runs"></a>增量執行

在啟動載入之後，會在主要叢集上自動執行增量執行，而且在這些增量執行期間產生的事件會在次要叢集上播放回來。 當次要叢集與主要叢集捕捉時，次要叢集會與主要的事件一致。

## <a name="replication-commands"></a>複寫命令

Hive 提供一組複寫命令– `DUMP` 、 `LOAD` 和 `STATUS` -以協調事件的流程。 此 `DUMP` 命令會產生主要叢集上所有 DDL/DML 事件的本機記錄檔。 此 `LOAD` 命令是一種延遲將中繼資料和記錄的資料複製到已解壓縮之複寫傾印輸出，並在目標叢集上執行的方法。 此 `STATUS` 命令會從目標叢集執行，以提供最新的事件識別碼，即最新的複寫負載已成功複寫。

### <a name="set-replication-source"></a>設定複寫來源

開始複寫之前，請確定要複寫的資料庫已設定為複寫來源。 您可以使用 `DESC DATABASE EXTENDED <db_name>` 命令來判斷參數 `repl.source.for` 是否使用原則名稱進行設定。

如果已排程原則且 `repl.source.for` 未設定參數，則您必須先使用設定此參數 `ALTER DATABASE <db_name> SET DBPROPERTIES ('repl.source.for'='<policy_name>')` 。

```sql
ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.for'='replpolicy1') 
```

### <a name="dump-metadata-to-the-data-lake"></a>將中繼資料傾印至 data lake

此 `REPL DUMP [database name]. => location / event_id` 命令會在啟動程式階段中用來將相關的中繼資料傾印至 Azure Data Lake Storage Gen2。 `event_id`指定將相關的中繼資料放入 Azure Data Lake Storage Gen2 的最小事件。 
 
```sql
repl dump tpcds_orc; 
```
範例輸出︰

| dump_dir|last_repl_id
|-|-|
|/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0|2925|

### <a name="load-data-to-the-target-cluster"></a>將資料載入目標叢集

此 `REPL LOAD [database name] FROM [ location ] { WITH ( ‘key1’=‘value1’{, ‘key2’=‘value2’} ) }` 命令可用來將資料載入到目標叢集中，以供啟動程式和增量的複寫階段使用。 `[database name]`可以與來源或目標叢集上的不同名稱相同。 `[location]`表示來自先前命令輸出的位置 `REPL DUMP` 。 這表示目標叢集應該能夠與來源叢集交談。 此 `WITH` 子句主要是加入來防止重新開機目標叢集，以允許複寫。

```sql
repl load tpcds_orc from '/tmp/hive/repl/38896729-67d5-41b2-90dc-46eeed4c5dd0'; 
```

### <a name="output-the-last-replicated-event-id"></a>輸出上一個複寫的事件識別碼

此 `REPL STATUS [database name]` 命令會在目標叢集上執行，並輸出最後一次複寫 `event_id` 。 此命令也可讓使用者知道其目標叢集已複寫到的狀態。 您可以使用此命令的輸出來為累加式複寫建立下一個 `REPL DUMP` 命令。

```sql
repl status tpcds_orc;
```

範例輸出︰

|last_repl_id|
|-|
|2925|

### <a name="dump-relevant-data-and-metadata-to-the-data-lake"></a>將相關的資料和中繼資料傾印至 data lake

此 `REPL DUMP [database name] FROM [event-id] { TO [event-id] } { LIMIT [number of events] }` 命令可用來將相關的中繼資料和資料傾印至 Azure Data Lake Storage。 此命令會用於累加階段，並且會在來源倉儲上執行。 `FROM [event-id]`遞增階段需要，而且的值 `event-id` 可以藉由在 `REPL STATUS [database name]` 目標倉儲上執行命令來衍生。

```sql
repl dump tpcds_orc from 2925;
```

範例輸出︰

|dump_dir|last_repl_id|
|-|-|
| /tmp/hive/repl/38896729-67d5-41b2-90dc-466466agadd0 | 2960|

## <a name="hive-replication-process"></a>Hive 複寫進程

下列步驟是在 Hive 複寫過程中發生的連續事件。

1. 確定要複寫的資料表已設定為特定原則的複寫來源。

1. 此 `REPL_DUMP` 命令會以相關聯的條件約束（例如資料庫名稱、事件識別碼範圍和 Azure Data Lake Storage Gen2 儲存體 URL）發出至主要叢集。

1. 系統會將中繼存放區中所有追蹤事件的傾印序列化至最新的事件。 此傾印會儲存在主要叢集上的 Azure Data Lake Storage Gen2 儲存體帳戶中，位於所指定的 URL `REPL_DUMP` 。  

1. 主要叢集會將複寫中繼資料保存到主要叢集的 Azure Data Lake Storage Gen2 儲存體。 在 Ambari 的 Hive 設定 UI 中可設定路徑。 此程式會提供儲存中繼資料的路徑，以及最新追蹤的 DML/DDL 事件的識別碼。

1. 此 `REPL_LOAD` 命令會從次要叢集發出。 此命令會指向步驟3中所設定的路徑。

1. 次要叢集會使用在步驟3中建立的追蹤事件來讀取中繼資料檔案。 確定次要叢集具有與主要叢集的 Azure Data Lake Storage Gen2 儲存體之間的網路連線，而這些儲存追蹤的事件 `REPL_DUMP` 。  

1. 次要叢集 () 計算產生分散式複製 `DistCP` 。

1. 次要叢集會從主要叢集的儲存體複製資料。  

1. 次要叢集上的中繼存放區已更新。  

1. 最後追蹤的事件識別碼會儲存在主要中繼存放區中。

增量複寫會遵循相同的進程，而且需要最後一個複寫的事件識別碼作為輸入。 這會導致自上次複寫事件之後的增量複製。 增量複寫通常會以預先決定的頻率自動化，以達到 (RPO) 所需的復原點目標。

:::image type="content" source="media/apache-hive-replication/hive-replication-diagram.png" alt-text="Hive 複寫圖表":::

## <a name="replication-patterns"></a>複寫模式  

複寫通常會在主要與次要之間以單向方式設定，其中主要已經考慮可讀取和寫入要求。 次要叢集已經考慮只會讀取要求。 如果發生損毀，則允許在次要資料庫上進行寫入，但是反向複寫必須設定回主要複本。

:::image type="content" source="media/apache-hive-replication/replication-pattern.png" alt-text="Hive 複寫圖表":::

有許多適用于 Hive 複寫的模式，包括主要-次要、中樞和輪輻，以及轉送。

在 HDInsight 主動主要-待命次要資料庫中，常見的商務持續性和嚴重損壞修復 (BCDR) 模式，而 HiveReplicationV2 可以搭配使用此模式與使用 VNet 對等互連的地區分隔 HDInsight Hadoop 叢集。 對等互連到這兩個叢集的一般虛擬機器都可以用來裝載複寫自動化腳本。 如需可能的 HDInsight BCDR 模式的詳細資訊，請參閱 [hdinsight 商務持續性檔](../hdinsight-business-continuity.md)。  

### <a name="hive-replication-with-enterprise-security-package"></a>使用企業安全性套件的 Hive 複寫  

在使用企業安全性套件在 HDInsight Hadoop 叢集上規劃 Hive 複寫的情況下，您必須考慮 Ranger 中繼存放區和 Azure Active Directory Domain Services (AD DS) 的複寫機制。  

使用 Azure AD DS 複本集功能，在多個區域中為每個 Azure AD 租使用者建立多個 Azure AD DS 複本集。 每個個別的複本集都必須在其各自的區域中與 HDInsight Vnet 對等互連。 在此設定中，Azure AD DS 的變更，包括設定、使用者身分識別和認證、群組、群組原則物件、電腦物件和其他變更，都會套用至受控網域中的所有複本集（使用 Azure AD DS 複寫）。
  
您可以使用 Ranger Import-Export 功能，定期將 Ranger 原則備份並複寫至次要複本。 您可以根據您想要在次要叢集上執行的授權層級，選擇複寫所有或 Ranger 原則的子集。  

## <a name="sample-code"></a>範例程式碼  

下列程式碼順序會提供一個範例，說明如何在稱為的範例資料表上，執行啟動載入和累加式複寫 `tpcds_orc` 。  

1. 將資料表設定為複寫原則的來源。

   ```sql
   ALTER DATABASE tpcds_orc SET DBPROPERTIES ('repl.source.   for'='replpolicy1');
   ```

1. 在主要叢集上啟動程式傾印。

   ```sql
   repl dump tpcds_orc with ('hive.repl.rootdir'='/tmpag/hiveag/replag'); 
   ```
   
   範例輸出︰
   
   |dump_dir|last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/675d1bea-2361-4cad-bcbf-8680d305a27a|2925|
 
1. 次要叢集上的啟動載入器。 

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag 675d1bea-2361-4cad-bcbf-8680d305a27a'; 
   ```

1. 檢查次要叢集的 `REPL` 狀態。

   ```sql
   repl status tpcds_orc; 
   ```
 
   |last_repl_id|
   |-|
   |2925|

1. 在主要叢集上的累加式傾印。

   ```sql
   repl dump tpcds_orc from 2925 with ('hive.repl.rootdir'='/tmpag/hiveag/ replag');
   ```

   範例輸出︰
   
   |dump_dir | last_repl_id|
   |-|-|
   |/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31|2960|

1. 次要叢集上的累加式載入。  

   ```sql
   repl load tpcds_orc from '/tmpag/hiveag/replag/31177ff7-a40f-4f67-a613-3b64ebe3bb31';
   ```

1. 檢查 `REPL` 次要叢集的狀態。

   ```sql
   repl status tpcds_orc;
   ```

   |last_repl_id|
   |-|
   |2960|

## <a name="next-steps"></a>後續步驟

若要深入瞭解本文中討論的專案，請參閱：

- [Apache Ambari REST 參考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)
- [安裝和設定 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
- [安裝和設定 Azure PowerShell 模組 Az](/powershell/azure/)
- [使用 Apache Ambari 來管理 HDInsight](../hdinsight-hadoop-manage-ambari.md)
- [佈建以 Linux 為基礎的 HDInsight 叢集](../hdinsight-hadoop-provision-linux-clusters.md)