---
title: 針對複寫延遲進行疑難排解-適用於 MySQL 的 Azure 資料庫
description: 瞭解如何使用適用於 MySQL 的 Azure 資料庫讀取複本進行複寫延遲的疑難排解。
keywords: mysql，疑難排解，複寫延遲（秒）
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 10/25/2020
ms.openlocfilehash: af82b9e2feee3e03d2a0703d771c68b67ddd08c9
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92791574"
---
# <a name="troubleshoot-replication-latency-in-azure-database-for-mysql"></a>對適用於 MySQL 的 Azure 資料庫中的複寫延遲進行疑難排解

[!INCLUDE[applies-to-single-flexible-server](./includes/applies-to-single-flexible-server.md)]

[讀取複本](concepts-read-replicas.md)功能可讓您從適用於 MySQL 的 Azure 資料庫伺服器將資料複寫至唯讀複本伺服器。 您可以將應用程式的讀取和報告查詢路由傳送至複本伺服器，以相應放大工作負載。 此設定可減少來源伺服器的壓力。 它也可改善應用程式調整規模時的整體效能和延遲。 

複本會使用 MySQL 引擎的原生二進位記錄檔 (binlog) 檔案位置型複寫技術，以非同步方式更新。 如需詳細資訊，請參閱 [MySQL binlog 以位置為基礎的](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)複寫設定總覽。 

次要讀取複本上的複寫延遲取決於數個因素。 這些因素包括但不限於： 

- 網路延遲。
- 來源伺服器上的交易磁片區。
- 來源伺服器和次要讀取複本伺服器的計算層。
- 在來源伺服器和次要伺服器上執行的查詢。 

在本文中，您將瞭解如何針對適用於 MySQL 的 Azure 資料庫中的複寫延遲進行疑難排解。 您也將瞭解複本伺服器上增加複寫延遲的一些常見原因。

## <a name="replication-concepts"></a>複寫概念

當二進位記錄檔啟用時，來源伺服器會將認可的交易寫入二進位記錄檔中。 二進位記錄檔用於複寫。 它預設會針對最多支援 16 TB 儲存體的所有新布建伺服器開啟。 在複本伺服器上，兩個執行緒會在每個複本伺服器上執行。 其中一個執行緒是 *IO 執行緒* ，另一個則是 *SQL 執行緒* ：

- IO 執行緒會連接到來源伺服器，並要求更新的二進位記錄檔。 此執行緒會接收二進位記錄檔更新。 這些更新會儲存在複本伺服器上，名為「 *轉送」記錄* 檔的本機記錄檔中。
- SQL 執行緒會讀取轉送記錄，然後將資料變更套用到複本伺服器上。

## <a name="monitoring-replication-latency"></a>監視複寫延遲

適用於 MySQL 的 Azure 資料庫在 [Azure 監視器](concepts-monitoring.md)中提供複寫延遲的度量（以秒為單位）。 此計量僅適用于讀取複本伺服器。 它是以 MySQL 中可用的 seconds_behind_master 計量來計算。 

若要瞭解增加複寫延遲的原因，請使用 [MySQL 工作臺](connect-workbench.md) 或 [Azure Cloud Shell](https://shell.azure.com)連接到複本伺服器。 然後執行下列命令。

>[!NOTE] 
> 在您的程式碼中，將範例值取代為您的複本伺服器名稱和系統管理員使用者名稱。 系統管理員使用者名稱需要 `@\<servername>` 適用於 MySQL 的 Azure 資料庫。

```azurecli-interactive
mysql --host=myreplicademoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
```

以下是 Cloud Shell 終端機中的體驗外觀：

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

在相同的 Cloud Shell 終端機中，執行下列命令：

```
mysql> SHOW SLAVE STATUS;
```

以下是一般輸出：
  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/howto-troubleshoot-replication-latency/show-status.png" alt-text="監視複寫延遲":::


輸出包含許多資訊。 一般來說，您只需要將焦點放在下表所描述的資料列。

|計量|描述|
|---|---|
|Slave_IO_State| 代表 IO 執行緒的目前狀態。 一般來說，如果來源 (主要) 伺服器正在進行同步處理，則狀態會是「正在等候 master 傳送事件」。 狀態（例如「連線到主要」）表示複本遺失與來源伺服器的連線。 請確定來源伺服器正在執行，或查看防火牆是否封鎖了連接。|
|Master_Log_File| 表示來源伺服器所寫入的二進位記錄檔。|
|Read_Master_Log_Pos| 指出來源伺服器在二進位記錄檔中的寫入位置。|
|Relay_Master_Log_File| 代表複本伺服器正在從來源伺服器讀取的二進位記錄檔。|
|Slave_IO_Running| 指出 IO 執行緒是否正在執行。 值應為 `Yes` 。 如果值為，則複寫 `NO` 可能會中斷。|
|Slave_SQL_Running| 指出 SQL 執行緒是否正在執行。 值應為 `Yes` 。 如果值為，則複寫 `NO` 可能會中斷。|
|Exec_Master_Log_Pos| 指出複本所套用 Relay_Master_Log_File 的位置。 如果有延遲，則這個位置序列應小於 Read_Master_Log_Pos。|
|Relay_Log_Space|指出轉送記錄檔大小的上限。 您可以查詢 like 來檢查大小 `SHOW GLOBAL VARIABLES` `relay_log_space_limit` 。|
|Seconds_Behind_Master| 顯示覆寫延遲（以秒為單位）。|
|Last_IO_Errno|顯示 IO 執行緒錯誤碼（如果有的話）。 如需這些程式碼的詳細資訊，請參閱 [MySQL server 錯誤訊息參考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)。|
|Last_IO_Error| 顯示 IO 執行緒錯誤訊息（如果有的話）。|
|Last_SQL_Errno|顯示 SQL 執行緒錯誤碼（如果有的話）。 如需這些程式碼的詳細資訊，請參閱 [MySQL server 錯誤訊息參考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)。|
|Last_SQL_Error|顯示 SQL 執行緒錯誤訊息（如果有的話）。|
|Slave_SQL_Running_State| 指出目前的 SQL 執行緒狀態。 在此狀態下， `System lock` 是正常的。 查看狀態也是正常的 `Waiting for dependent transaction to commit` 。 此狀態表示複本正在等候來源伺服器更新認可的交易。|

如果 Slave_IO_Running 為 `Yes` ，且 Slave_SQL_Running 是，則複寫 `Yes` 會正常運作。 

接下來，請檢查 Last_IO_Errno、Last_IO_Error、Last_SQL_Errno 和 Last_SQL_Error。  這些欄位會顯示導致 SQL 執行緒停止的最新錯誤的錯誤號碼和錯誤訊息。 的錯誤號碼 `0` 和空白訊息表示沒有任何錯誤。 檢查 [MySQL 伺服器錯誤訊息參考](https://dev.mysql.com/doc/mysql-errors/5.7/en/server-error-reference.html)中的錯誤碼，以調查任何非零的錯誤值。

## <a name="common-scenarios-for-high-replication-latency"></a>高複寫延遲的常見案例

下列各節說明高複寫延遲很常見的情況。

### <a name="network-latency-or-high-cpu-consumption-on-the-source-server"></a>來源伺服器上的網路延遲或高 CPU 耗用量

如果您看到下列值，則複寫延遲可能是因為來源伺服器上的網路延遲過高或高 CPU 耗用量所造成。 

```
Slave_IO_State: Waiting for master to send event
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller than Master_Log_File, e.g. mysql-bin.00010
```

在此情況下，IO 執行緒正在執行，且正在等候來源伺服器。 來源伺服器已寫入二進位記錄檔編號20。 複本最多隻接收到檔案號碼10。 在此案例中，高複寫延遲的主要因素是來源伺服器上的網路速度或高 CPU 使用率。  

在 Azure 中，區域內的網路延遲通常可以測量毫秒。 跨區域，延遲範圍從毫秒到秒。 

在大部分的情況下，IO 執行緒和來源伺服器之間的連接延遲是因為來源伺服器上的高 CPU 使用率所造成。 IO 執行緒的處理速度很慢。 您可以使用 Azure 監視器檢查來源伺服器上的 CPU 使用率和並行連接數目，來偵測此問題。

如果您在來源伺服器上看不到高 CPU 使用率，問題可能是網路延遲。 如果網路延遲突然偏高，請檢查 [Azure 狀態頁面](https://status.azure.com/status) 中的已知問題或中斷情形。 

### <a name="heavy-bursts-of-transactions-on-the-source-server"></a>來源伺服器上的大量高載交易

如果您看到下列值，則來源伺服器上大量的交易可能會導致複寫延遲。 

```
Slave_IO_State: Waiting for the slave SQL thread to free enough relay log space
Master_Log_File: the binary file sequence is larger then Relay_Master_Log_File, e.g. mysql-bin.00020
Relay_Master_Log_File: the file sequence is smaller then Master_Log_File, e.g. mysql-bin.00010
```

輸出顯示覆本可以取出來源伺服器背後的二進位記錄檔。 但是複本 IO 執行緒指出轉送記錄空間已滿。 

網路速度不會造成延遲。 複本正在嘗試趕上。 但更新的二進位記錄檔大小超過轉送記錄空間的上限。 

若要針對此問題進行疑難排解，請在來源伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) 檔。 使用慢速查詢記錄來識別來源伺服器上長時間執行的交易。 然後調整已識別的查詢，以減少伺服器上的延遲。 

這種排序的複寫延遲通常是來源伺服器上的資料負載所造成。 當來源伺服器有每週或每月的資料載入時，複寫延遲很可惜無法避免。 複本伺服器最後會在來源伺服器上的資料載入完成之後趕上。


### <a name="slowness-on-the-replica-server"></a>複本伺服器上的緩慢

如果您觀察到下列值，則問題可能是在複本伺服器上。 

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

在此案例中，輸出顯示 IO 執行緒和 SQL 執行緒都正常執行。 複本會讀取來源伺服器所寫入的相同二進位記錄檔。 不過，複本伺服器上的某些延遲會反映來自來源伺服器的相同交易。 

下列各節說明這種延遲的常見原因。

#### <a name="no-primary-key-or-unique-key-on-a-table"></a>資料表上沒有 primary key 或 unique 索引鍵

適用於 MySQL 的 Azure 資料庫使用以資料列為基礎的複寫。 來源伺服器會將事件寫入二進位記錄檔中，記錄個別資料表資料列中的變更。 然後，SQL 執行緒會將這些變更複寫到複本伺服器上對應的資料表資料列。 當資料表缺少 primary key 或 unique 索引鍵時，SQL 執行緒會掃描目標資料表中的所有資料列，以套用變更。 這種掃描可能會導致複寫延遲。

在 MySQL 中，主鍵是相關聯的索引，可確保快速查詢效能，因為它不能包含 Null 值。 如果您使用 InnoDB 儲存引擎，則會以實體方式組織資料表資料，以進行非常快速的查閱，並根據主要索引鍵進行排序。 

建議您先在來源伺服器的資料表上新增主鍵，然後再建立複本伺服器。 在來源伺服器上新增主鍵，然後重新建立讀取複本，以協助改善複寫延遲。

您可以使用下列查詢，找出來源伺服器上的哪些資料表缺少主鍵：

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

#### <a name="long-running-queries-on-the-replica-server"></a>複本伺服器上長時間執行的查詢

複本伺服器上的工作負載可以讓 SQL 執行緒延遲在 IO 執行緒後方。 複本伺服器上長時間執行的查詢是高複寫延遲的其中一個常見原因。 若要針對此問題進行疑難排解，請在複本伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) 檔。 

緩慢的查詢可能會增加資源耗用量或降低伺服器的速度，讓複本無法趕上來源伺服器。 在此案例中，請調整緩慢查詢。 更快速的查詢能防止 SQL 執行緒的瓶頸，並大幅改善複寫延遲。


#### <a name="ddl-queries-on-the-source-server"></a>來源伺服器上的 DDL 查詢
在來源伺服器上，類似的資料定義語言 (DDL) 命令 [`ALTER TABLE`](https://dev.mysql.com/doc/refman/5.7/en/alter-table.html) 可能需要很長的時間。 執行 DDL 命令時，可能會在來源伺服器上平行執行數以千計的其他查詢。 

複寫 DDL 時，為了確保資料庫的一致性，MySQL 引擎會在單一複寫執行緒中執行 DDL。 在此工作期間，所有其他複寫的查詢都會被封鎖，而且必須等到 DDL 作業在複本伺服器上完成為止。 即使是線上 DDL 作業也會造成此延遲。 DDL 作業會增加複寫延遲。

如果您已在來源伺服器上啟用 [慢速查詢記錄](concepts-server-logs.md) ，您可以藉由檢查在來源伺服器上執行的 DDL 命令來偵測此延遲問題。 透過索引卸載、重新命名和建立，您可以針對 ALTER TABLE 使用「就地演算法」。 您可能需要複製資料表資料並重建資料表。 

一般情況下，並存演算法支援並行 DML。 但是，當您準備並執行作業時，您可以簡單地在資料表上取得專屬的中繼資料鎖定。 因此，在 CREATE INDEX 語句中，您可以使用子句演算法和鎖定來影響資料表複製的方法，以及用於讀取和寫入的平行存取層級。 您仍然可以藉由加入全文檢索索引或空間索引來防止 DML 作業。 

下列範例會使用演算法和鎖定子句來建立索引。

```sql
ALTER TABLE table_name ADD INDEX index_name (column), ALGORITHM=INPLACE, LOCK=NONE;
```

可惜的是，對於需要鎖定的 DDL 語句而言，您無法避免複寫延遲。 若要減少潛在的效果，請在離峰時段執行這些類型的 DDL 作業，例如，在夜間進行。

#### <a name="downgraded-replica-server"></a>降級複本伺服器

在適用於 MySQL 的 Azure 資料庫中，讀取複本會使用與來源伺服器相同的伺服器設定。 您可以在建立複本伺服器之後變更它。 

如果複本伺服器已降級，工作負載可能會耗用更多資源，而這可能會導致複寫延遲。 若要偵測這個問題，請使用 Azure 監視器檢查複本伺服器的 CPU 和記憶體耗用量。 

在此案例中，建議您將複本伺服器的設定保留在等於或大於來源伺服器值的值。 此設定可讓複本跟上來源伺服器。

#### <a name="improving-replication-latency-by-tuning-the-source-server-parameters"></a>藉由微調來源伺服器參數來改善複寫延遲

在適用於 MySQL 的 Azure 資料庫中，根據預設，複寫會針對複本上的平行線程執行優化。 如果來源伺服器上的高並行工作負載導致複本伺服器落後，您可以在來源伺服器上設定 binlog_group_commit_sync_delay 的參數，以改善複寫延遲。 

Binlog_group_commit_sync_delay 參數會控制在同步處理二進位記錄檔之前，二進位記錄檔認可等候的毫秒數。 此參數的優點是，它不會立即套用每個認可的交易，而是讓來源伺服器大量傳送二進位記錄檔更新。 此延遲會減少複本上的 IO，並協助提升效能。 

將 binlog_group_commit_sync_delay 參數設定為1000或這樣可能會很有用。 然後監視複寫延遲。 請小心設定此參數，並只將其用於高平行存取工作負載。 

針對包含許多單一交易的低並行工作負載，binlog_group_commit_sync_delay 設定可能會增加延遲。 延遲可能會增加，因為 IO 執行緒會等待大量二進位記錄檔更新，即使只有少數交易才會認可。 

## <a name="next-steps"></a>後續步驟
查看 [MySQL binlog 複製總覽](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html)。
