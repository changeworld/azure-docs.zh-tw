---
title: 針對複寫延遲進行疑難排解-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用適用於 MySQL 的 Azure 資料庫讀取複本針對複寫延遲進行疑難排解
keywords: mysql，疑難排解，複寫延遲（秒）
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.openlocfilehash: 16a502a53b4441faf68ea342e0bc865731d38b1a
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876991"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>針對適用於 MySQL 的 Azure 資料庫中的複寫延遲進行疑難排解

[讀取複本](concepts-read-replicas.md)功能可讓您從適用於 MySQL 的 Azure 資料庫伺服器將資料複寫至唯讀複本伺服器。 讀取複本可用來將讀取/報告查詢從應用程式路由傳送至複本伺服器，以相應放大工作負載。 這可降低主伺服器的壓力，並改善應用程式在調整規模時的整體效能和延遲。 複本會使用 MySQL 引擎的原生二進位記錄 (binlog) 檔案位置型複寫技術來進行非同步更新。 若要深入了解 binlog 複寫，請參閱 [MySQL binlog 複寫概觀](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html) \(英文\)。 

次要讀取複本上的複寫延遲取決於一些因素，包括但不限於 

- 網路延遲
- 來源伺服器上的交易磁片區
- 來源和次要讀取複本伺服器的計算層
- 在主要和次要伺服器上執行的查詢。 

在本檔中，您將瞭解如何針對適用於 MySQL 的 Azure 資料庫中的複寫延遲進行疑難排解。 此外，您也將瞭解複本伺服器上較高複寫延遲的一些常見原因。

## <a name="replication-concepts"></a>複寫概念

當啟用二進位記錄檔時，來源伺服器會將認可的交易寫入二進位記錄檔，以用於複寫。 針對最多支援 16 TB 儲存體的所有新布建伺服器，預設會開啟二進位記錄檔。 在複本伺服器上，每個複本伺服器都有兩個執行中的執行緒，一個稱為 IO 執行緒，另一個稱為「SQL 執行緒」。

- **IO 執行緒**會連接到來源伺服器，並要求更新的二進位記錄檔。 在這個執行緒收到二進位記錄檔更新之後，就會將它們儲存在複本伺服器上，稱為轉送記錄檔的本機記錄檔中。
- **SQL 執行緒**會讀取轉送記錄，並將資料變更套用到複本伺服器上 (的) 。

## <a name="monitoring-replication-latency"></a>監視複寫延遲

適用於 MySQL 的 Azure 資料庫在 [Azure 監視器](concepts-monitoring.md)中提供複寫延遲（以秒為單位）。 此計量僅適用于讀取複本伺服器。 此計量會使用 MySQL 中可用的 seconds_behind_master 計量來計算。 若要瞭解增加複寫延遲的根本原因，請使用 [MySQL 工作臺](connect-workbench.md) 或 [Azure Cloud shell](https://shell.azure.com) 連線到複本伺服器，然後執行下列命令：

 將值取代為您實際的複本伺服器名稱和系統管理員使用者登入名稱。 系統管理員使用者名稱需要適用於 MySQL 的 Azure 資料庫的 ' @ \<servername> '：

  ```azurecli-interactive
  mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  以下是 Cloud Shell 終端機中體驗顯示的樣子
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h myreplicademoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
  在相同的 Azure Cloud Shell 終端機中，執行下列命令

  ```
  mysql> SHOW SLAVE STATUS;
  ```

  一般的輸出看起來會像這樣：
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="監視複寫延遲":::


輸出包含許多資訊，但通常只需專注于下列資料行：

|計量|描述|
|---|---|
|Slave_IO_State| IO 執行緒的目前狀態。 一般來說，如果正在進行同步處理，則狀態會是「正在等候 master to send 事件」。 但是，如果您看到「正在連接到主要」之類的狀態，表示複本已失去與主伺服器的連線。 檢查主機是否正在執行，或防火牆是否封鎖連接。|
|Master_Log_File| 主要寫入的二進位記錄檔。|
|Read_Master_Log_Pos| 表示在上述二進位記錄檔中，主要寫入的位置。|
|Relay_Master_Log_File| 表示複本伺服器正在從主伺服器讀取的二進位記錄檔。|
|Slave_IO_Running| 指出 IO 執行緒是否正在執行。 它應該是 "Yes"。 如果是「否」，則很有可能是複寫中斷。|
|Slave_SQL_Running| 指出 SQL 執行緒是否正在執行。 它應該是 "Yes"。 如果是「否」，則很有可能是複寫中斷。|
|Exec_Master_Log_Pos| 顯示覆本套用 Relay_Master_Log_File 的位置。 如果有延遲，此位置序列應小於 Read_Master_Log_Pos。|
|Relay_Log_Space|顯示轉送記錄檔大小的上限。 您可以藉由查詢顯示全域變數（例如 "relay_log_space_limit"）來檢查大小。|
|Seconds_Behind_Master| 顯示覆寫延遲（以秒為單位）。|
|Last_IO_Errno|顯示 IO 執行緒錯誤碼（如果有的話）。 如需這些程式碼的詳細資訊，請參閱 [MySQL 檔](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)。|
|Last_IO_Error| 顯示 IO 執行緒錯誤訊息（如果有的話）。|
|Last_SQL_Errno|顯示 SQL 執行緒錯誤碼（如果有的話）。 如需這些程式碼的詳細資訊，請參閱 [MySQL 檔](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)。|
|Last_SQL_Error|顯示 SQL 執行緒錯誤訊息（如果有的話）。|
|Slave_SQL_Running_State| 指出目前的 SQL 執行緒狀態。 請注意，在此狀態下顯示的「系統鎖定」是正常行為。 如果看到狀態為「正在等候相依交易認可」，則是正常的。 這表示複本正在等候 master 更新認可的交易。|

如果 Slave_IO_Running 為 [是]，且 Slave_SQL_Running 為 [是]，則複寫會正常運作。 

接下來，您必須檢查 Last_IO_Errno、Last_IO_Error Last_SQL_Errno 和 Last_SQL_Error。  這些欄位會保存導致 SQL 執行緒停止的最新錯誤的錯誤號碼和錯誤訊息。 錯誤號碼0和空白訊息表示沒有任何錯誤。 錯誤中的非零值必須進一步調查，方法是查閱 [MySQL 檔](https://dev.mysql.com/doc/refman/5.7/en/server-error-reference.html)中的錯誤碼。

## <a name="common-scenarios-for-high-replication-latency"></a>高複寫延遲的常見案例

### <a name="network-latency-or-high-cpu-on-source-server"></a>來源伺服器上的網路延遲或高 cpu

如果您觀察到下列值，最常見的複寫延遲原因是來源伺服器上的網路延遲過高或高 cpu 耗用量。 在此情況下，IO 執行緒正在執行，並在主伺服器上等候。 主要 (來源伺服器) 已寫入二進位記錄檔 #20，而複本只接收到檔案 #10。 在此案例中，高複寫延遲的主要因素是來源伺服器上的網路速度或高 cpu 使用率。  在 Azure 中，區域內的網路延遲通常是以毫秒為單位，而跨區域最多可達幾秒鐘。 在大部分的情況下，由於來源伺服器上的 cpu 使用率很高，導致 IO 執行緒速度變慢，因此連接到來源伺服器的 IO 執行緒延遲會造成延遲。 您可以藉由監視 cpu 使用率，並觀察來源伺服器上使用 Azure 監視器的並行連線數目來偵測此狀況。

如果您在來源伺服器上看不到高 cpu 使用率，可能的原因可能是網路延遲。 如果您突然看到很高的網路延遲，則建議您檢查 [Azure 狀態頁面](https://status.azure.com/status) ，以確定沒有已知的問題或中斷。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

### <a name="heavy-burst-of-transactions-on-source-server"></a>來源伺服器上大量的交易高載

如果您觀察到下列值，最常見的複寫延遲原因就是在來源伺服器上大量高載交易。 在下列輸出中，雖然複本可以取出 master 後方的二進位記錄檔，但是複本 IO 執行緒指出轉送記錄空間已滿。 因此，網路速度不會造成延遲，因為複本已經嘗試以最快的速度來趕上。 相反地，更新的二進位記錄檔大小超過轉送記錄空間的上限。 若要進一步針對此問題進行疑難排解，請在主伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) 。 慢速查詢記錄檔可讓您識別來源伺服器上長時間執行的交易。 已識別的查詢需要微調以減少伺服器上的延遲。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

以下是此類別中延遲的常見原因：

#### <a name="replication-latency-due-to-data-load-on-source-server"></a>由於來源伺服器上的資料載入而產生的複寫延遲
在某些情況下，來源伺服器上會有每週或每月的資料載入。 可惜的是，在此情況下，複寫延遲是不可避免的。 在此案例中，複本伺服器最後會在來源伺服器上的資料載入完成之後趕上。


### <a name="slowness-on-the-replica-server"></a>複本伺服器上的緩慢

如果您觀察到下列值，最常見的原因可能是複本伺服器上需要進一步調查的問題。 在此情況下，如輸出所示，IO 和 SQL 執行緒都正常執行，而且複本會讀取與主要寫入相同的二進位記錄檔。 不過，複本伺服器上會發生一些延遲，以反映來自來源伺服器的相同交易。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: The binary log file sequence equals to Relay_Master_Log_File, e.g. mysql-bin.000191
Read_Master_Log_Pos: The position of master server written to the above file is larger than Relay_Log_Pos, e.g. 103978138
Relay_Master_Log_File: mysql-bin.000191
Slave_IO_Running: Yes
Slave_SQL_Running: Yes
Exec_Master_Log_Pos: The position of slave reads from master binary log file is smaller than Read_Master_Log_Pos, e.g. 13468882
Seconds_Behind_Master: There is latency and the value here is greater than 0
```

以下是此類別中延遲的常見原因：

#### <a name="no-primary-or-unique-key-on-a-table"></a>資料表上沒有主要或唯一索引鍵

適用於 MySQL 的 Azure 資料庫使用以資料列為基礎的複寫。 使用以資料列為基礎的複寫時，主伺服器會將事件寫入有關個別資料表資料列變更的二進位記錄檔。 SQL 執行緒接著會在複本伺服器上對對應的資料表資料列執行這些變更。 資料表上沒有主要或唯一索引鍵是複寫延遲的常見原因之一。 缺少主要或唯一索引鍵會導致 SQL 執行緒掃描目標資料表中的所有資料列，以套用變更。

在 MySQL 中，主要索引鍵是相關聯的索引，可確保快速查詢效能，因為它不能包含 Null 值。 使用 InnoDB 儲存引擎，資料表資料會以實體方式組織，以進行非常快速的查閱，並根據主要索引鍵進行排序。 因此，建議您在建立複本伺服器之前，先在來源伺服器的資料表上新增主鍵。 在此案例中，您需要在來源伺服器上新增主鍵並重新建立讀取複本，以協助改善複寫延遲。

您可以使用下列查詢來判斷來源伺服器上遺漏主鍵的資料表：

```sql 
select tab.table_schema as database_name, tab.table_name 
from information_schema.tables tab left join 
information_schema.table_constraints tco 
on tab.table_schema = tco.table_schema 
and tab.table_name = tco.table_name 
and tco.constraint_type = 'PRIMARY KEY' 
where tco.constraint_type is null 
and tab.table_schema not in('mysql', 'information_schema', 'performance_schema', 'sys') 
and tab.table_type = 'BASE TABLE' 
order by tab.table_schema, tab.table_name;

```

#### <a name="replication-latency-due-to-long-running-queries-on-replica-server"></a>複本伺服器上長時間執行的查詢導致複寫延遲

複本伺服器上的工作負載可能會讓 SQL 執行緒無法與 IO 執行緒保持在一起。 如果複本伺服器上有長時間執行的查詢，這就是高複寫延遲的常見原因之一。 在此情況下，應該在複本伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) ，以協助針對問題進行疑難排解。 緩慢的查詢可能會增加資源耗用量或減緩伺服器的速度，因此複本將無法與主伺服器趕上。 在此案例中，您需要微調查詢速度。 更快速的查詢可防止封鎖 SQL 執行緒，並大幅改善複寫延遲。


#### <a name="replication-latency-due-to-ddl-queries-on-source-server"></a>由於來源伺服器上的 DDL 查詢而產生的複寫延遲
如果有長時間執行的 DDL 命令，像是在來源伺服器上執行 [ALTER TABLE](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) ，而且假設花了1小時執行。 在這段期間內，來源伺服器上可能會同時執行上千個其他查詢。 當 DDL 複寫到複本時，若要確保資料庫的一致性，MySQL 引擎必須在單一複寫執行緒中執行 DDL。 因此，所有其他複寫的查詢都將遭到封鎖，而且需要等候一小時或更多時間，直到複本伺服器上的 DDL 作業完成為止。 無論線上 DDL 作業為何，都是如此。 使用 DDL 作業時，複寫應該會看到複寫延遲增加。

如果您在來源伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) 檔，您可以查看慢速查詢記錄，查看是否已在來源伺服器上執行 DDL 命令，以偵測到此情況。 雖然索引卸載、重新命名和建立應該針對可能牽涉到複製資料表資料的 ALTER TABLE 使用就地演算法，然後重建資料表。 通常支援就地演算法並行 DML，但資料表上的獨佔中繼資料鎖定可能會在作業的準備和執行階段短暫進行。 如此一來，在 CREATE INDEX 語句中，子句演算法和鎖定可能會用來影響資料表複製方法和平行存取層級，以進行讀取和寫入，但是加入全文檢索或空間索引仍會防止 DML 作業。 請參閱下列使用演算法和鎖定子句建立索引的範例：

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

可惜的是，對於需要鎖定的 DDL 語句，無法避免複寫延遲，而是在離峰時段執行這些類型的 DDL 作業，例如在夜間期間減少潛在的影響。

#### <a name="replication-latency-due-to-replica-server-lower-sku"></a>由於複本伺服器較低的 SKU 而產生的複寫延遲

在適用於 MySQL 的 Azure 資料庫讀取複本是使用與主伺服器相同的伺服器設定所建立。 複本伺服器設定在建立後可以變更。 但是，如果複本伺服器將會降級，工作負載可能會導致較高的資源耗用量，進而導致複寫延遲。 您可以從 Azure 監視器監視複本伺服器的 CPU 和記憶體耗用量，以觀察這一點。 在此案例中，建議將複本伺服器的設定保留為等於或大於來源的值，以確保複本能夠跟上主伺服器。

#### <a name="improving-replication-latency-using-server-parameter-tuning-on-source-server"></a>使用來源伺服器上的伺服器參數微調來改善複寫延遲

在適用於 MySQL 的 Azure 資料庫中，會根據預設，將複寫優化為使用平行線程在複本上執行。 針對複本伺服器無法趕上的來源伺服器上的高並行工作負載，可以藉由設定來源伺服器上的參數 binlog_group_commit_sync_delay 來改善複寫延遲。 此參數會控制在同步處理二進位記錄檔之前，二進位記錄檔認可等候的毫秒數。 好處是，主伺服器會大量傳送二進位記錄檔更新，而不是立即套用每個認可的交易。 這會減少複本上的 IO，並有助於改善效能。 在此案例中，將 binlog_group_commit_sync_delay 設定為1000或監視複寫延遲可能會很有用。 此參數應謹慎設定，並只運用於高並行工作負載。 針對具有許多單一交易的低並行案例，設定 binlog_group_commit_sync_delay 可能會增加延遲，因為 IO 執行緒正在等候大量二進位記錄檔更新，但只有少數交易可認可。 

## <a name="next-steps"></a>後續步驟
深入瞭解 [MySQL binlog 複製總覽](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。
