---
title: 伺服器參數-適用於 MySQL 的 Azure 資料庫
description: 本主題提供在適用於 MySQL 的 Azure 資料庫中設定伺服器參數的指導方針。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 5415446e0211618cfbee917d0df91213d68b7097
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91627341"
---
# <a name="server-parameters-in-azure-database-for-mysql"></a>適用於 MySQL 的 Azure 資料庫中的伺服器參數

本文提供在適用於 MySQL 的 Azure 資料庫中設定伺服器參數的考慮和指導方針。

## <a name="what-are-server-parameters"></a>什麼是伺服器參數？ 

MySQL 引擎提供許多不同的伺服器變數/參數，可用來設定和微調引擎行為。 某些參數可在執行時間期間動態設定，而其他參數則是「靜態」，需要重新開機伺服器才能套用。

適用於 MySQL 的 Azure 資料庫公開使用 [Azure 入口網站](./howto-server-parameters.md)、 [Azure CLI](./howto-configure-server-parameters-using-cli.md)和 [PowerShell](./howto-configure-server-parameters-using-powershell.md) 來變更各種 MySQL 伺服器參數的值，以符合您的工作負載需求。

## <a name="configurable-server-parameters"></a>可設定的伺服器參數

支援的伺服器參數清單會不斷成長。 使用 Azure 入口網站中的 [伺服器參數] 索引標籤，即可查看完整清單及設定伺服器參數值。

請參閱下列各節，以深入瞭解數個經常更新之伺服器參數的限制。 這些限制取決於伺服器的定價層和虛擬核心。

### <a name="thread-pools"></a>執行緒集區

MySQL 通常會為每個用戶端連接指派一個執行緒。 當並行使用者數目增加時，效能會有相對應的下降。 許多作用中線程可能會因為內容切換增加、執行緒爭用，以及 CPU 快取的錯誤位置而大幅影響效能。

執行緒集區是伺服器端功能，與連接共用不同，它會引進背景工作執行緒的動態集區，以限制伺服器上執行的使用中線程數目，並將執行緒變換降至最低。 這有助於確保連接的高載不會導致伺服器用盡資源或當機，並出現記憶體不足的錯誤。 執行緒集區最有效率，適用于簡短的查詢和需要大量 CPU 的工作負載，例如 OLTP 工作負載。

若要深入瞭解執行緒集區，請參閱[適用於 MySQL 的 Azure 資料庫中的執行緒](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-thread-pools-in-azure-database-for-mysql-service/ba-p/1504173)集區簡介

> [!NOTE]
> MySQL 5.6 版本不支援執行緒集區功能。 

### <a name="configuring-the-thread-pool"></a>設定執行緒集區
若要啟用執行緒集區，請將 `thread_handling` 伺服器參數更新為「執行緒集區」。 根據預設，此參數設定為 `one-thread-per-connection` ，這表示 MySQL 會為每個新的連接建立新的執行緒。 請注意，這是靜態參數，需要重新開機伺服器才能套用。

您也可以藉由設定下列伺服器參數，設定集區中的執行緒數目上限和下限： 
- `thread_pool_max_threads`：這個值可確保集區中不會有超過此數目的執行緒。
- `thread_pool_min_threads`：這個值會設定即使在關閉連接之後仍會保留的執行緒數目。

為了改善執行緒集區上的簡短查詢效能問題，適用於 MySQL 的 Azure 資料庫可讓您啟用批次執行，其中不會在執行查詢後立即返回執行緒集區，執行緒將會保持使用中的時間，以等候下一個查詢通過此連接。 執行緒接著會快速執行查詢，並在完成後等候下一個查詢，直到此進程的整體時間耗用量超過閾值為止。 批次執行行為是使用下列伺服器參數來決定：  

-  `thread_pool_batch_wait_timeout`：這個值會指定執行緒等候另一個查詢處理的時間。
- `thread_pool_batch_max_time`：此值會決定執行緒將重複執行查詢並等候下一個查詢的時間上限。

> [!IMPORTANT]
> 請先測試執行緒集區，再于生產環境中開啟它。 

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

在適用於 MySQL 的 Azure 資料庫中，二進位記錄一律會啟用 (`log_bin` 也就是在) 上設定為。 如果您想要使用觸發程式，將會收到類似于 *您未啟用超級許可權和二進位記錄的錯誤 (您可能會想要使用較不安全的 `log_bin_trust_function_creators` 變數) *。 

二進位記錄格式一律是 **row** ，而伺服器的所有連接 **一律** 會使用以資料列為基礎的二進位記錄。 使用以資料列為基礎的二進位記錄時，不會有安全性問題，而且二進位記錄無法中斷，因此您可以安全地設定 [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) 為 **TRUE**。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_buffer_pool_size) \(英文\)，以深入了解此參數。

#### <a name="servers-supporting-up-to-4-tb-storage"></a>最多可支援 4 TB 儲存體的伺服器

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|872415232|134217728|872415232|
|基本|2|2684354560|134217728|2684354560|
|一般用途|2|3758096384|134217728|3758096384|
|一般用途|4|8053063680|134217728|8053063680|
|一般用途|8|16106127360|134217728|16106127360|
|一般用途|16|32749125632|134217728|32749125632|
|一般用途|32|66035122176|134217728|66035122176|
|一般用途|64|132070244352|134217728|132070244352|
|記憶體最佳化|2|7516192768|134217728|7516192768|
|記憶體最佳化|4|16106127360|134217728|16106127360|
|記憶體最佳化|8|32212254720|134217728|32212254720|
|記憶體最佳化|16|65498251264|134217728|65498251264|
|記憶體最佳化|32|132070244352|134217728|132070244352|

#### <a name="servers-support-up-to-16-tb-storage"></a>伺服器最多支援 16 TB 的儲存體

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|872415232|134217728|872415232|
|基本|2|2684354560|134217728|2684354560|
|一般用途|2|7516192768|134217728|7516192768|
|一般用途|4|16106127360|134217728|16106127360|
|一般用途|8|32212254720|134217728|32212254720|
|一般用途|16|65498251264|134217728|65498251264|
|一般用途|32|132070244352|134217728|132070244352|
|一般用途|64|264140488704|134217728|264140488704|
|記憶體最佳化|2|15032385536|134217728|15032385536|
|記憶體最佳化|4|32212254720|134217728|32212254720|
|記憶體最佳化|8|64424509440|134217728|64424509440|
|記憶體最佳化|16|130996502528|134217728|130996502528|
|記憶體最佳化|32|264140488704|134217728|264140488704|

### <a name="innodb_file_per_table"></a>innodb_file_per_table

> [!NOTE]
> `innodb_file_per_table` 只能在一般用途和記憶體優化定價層中更新。

MySQL 會根據在建立資料表期間所提供的設定，將 InnoDB 資料表儲存在不同的資料表空間中。 [系統資料表空間](https://dev.mysql.com/doc/refman/5.7/en/innodb-system-tablespace.html) \(英文\) 是 InnoDB 資料字典的儲存區域。 [file-per-table 資料表空間](https://dev.mysql.com/doc/refman/5.7/en/innodb-file-per-table-tablespaces.html) \(英文\) 包含單一 InnoDB 資料表的資料和索引，且會獨自儲存在檔案系統的資料檔中。 這個行為是由 `innodb_file_per_table` 伺服器參數所控制。 將 `innodb_file_per_table` 設定為 `OFF` 會導致 InnoDB 在系統資料表空間中建立資料表。 否則，InnoDB 會在 file-per-table 資料表空間中建立資料表。

適用於 MySQL 的 Azure 資料庫在單一資料檔中支援最大達到 **1 TB**。 如果資料庫大小大於 1 TB，則應該在 [innodb_file_per_table](https://dev.mysql.com/doc/refman/5.7/en/innodb-parameters.html#sysvar_innodb_file_per_table) \(英文\) 資料表空間中建立資料表。 如果單一資料表大小大於 1 TB，則應該使用分割區資料表。

### <a name="join_buffer_size"></a>join_buffer_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_join_buffer_size) \(英文\)，以深入了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|無法在基本層中設定|N/A|N/A|
|基本|2|無法在基本層中設定|N/A|N/A|
|一般用途|2|262144|128|268435455|
|一般用途|4|262144|128|536870912|
|一般用途|8|262144|128|1073741824|
|一般用途|16|262144|128|2147483648|
|一般用途|32|262144|128|4294967295|
|一般用途|64|262144|128|4294967295|
|記憶體最佳化|2|262144|128|536870912|
|記憶體最佳化|4|262144|128|1073741824|
|記憶體最佳化|8|262144|128|2147483648|
|記憶體最佳化|16|262144|128|4294967295|
|記憶體最佳化|32|262144|128|4294967295|

### <a name="max_connections"></a>max_connections

|定價層|**vCore(s)**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|50|10|50|
|基本|2|100|10|100|
|一般用途|2|300|10|600|
|一般用途|4|625|10|1250|
|一般用途|8|1250|10|2500|
|一般用途|16|2500|10|5000|
|一般用途|32|5000|10|10000|
|一般用途|64|10000|10|20000|
|記憶體最佳化|2|625|10|1250|
|記憶體最佳化|4|1250|10|2500|
|記憶體最佳化|8|2500|10|5000|
|記憶體最佳化|16|5000|10|10000|
|記憶體最佳化|32|10000|10|20000|

當連線超過限制時，則可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多連線

> [!IMPORTANT]
> 為了獲得最佳體驗，建議您使用連線共用器 (例如 ProxySQL)，有效率地管理連線。

建立 MySQL 的新用戶端連線需要一段時間，且在建立之後，這些連線會佔用資料庫資源，即使閒置時也一樣。 大部分應用程式會要求許多短期連線，這會加重這種情況。 結果會減少實際工作負載的可用資源，因而導致效能降低。 減少閒置連線並重複使用現有連線的連接共用器，有助於避免這種情況。 若要了解如何設定 ProxySQL，請前往[部落格文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) \(英文\)。

>[!Note]
>ProxySQL 是開放原始碼的社區工具。 Microsoft 會以最大的努力來支援它。 為了取得具有授權指導的生產環境支援，您可以評估並與 [ProxySQL 產品支援人員](https://proxysql.com/services/support/)聯繫。

### <a name="max_heap_table_size"></a>max_heap_table_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_max_heap_table_size) \(英文\)，以深入了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|無法在基本層中設定|N/A|N/A|
|基本|2|無法在基本層中設定|N/A|N/A|
|一般用途|2|16777216|16384|268435455|
|一般用途|4|16777216|16384|536870912|
|一般用途|8|16777216|16384|1073741824|
|一般用途|16|16777216|16384|2147483648|
|一般用途|32|16777216|16384|4294967295|
|一般用途|64|16777216|16384|4294967295|
|記憶體最佳化|2|16777216|16384|536870912|
|記憶體最佳化|4|16777216|16384|1073741824|
|記憶體最佳化|8|16777216|16384|2147483648|
|記憶體最佳化|16|16777216|16384|4294967295|
|記憶體最佳化|32|16777216|16384|4294967295|

### <a name="query_cache_size"></a>query_cache_size

查詢快取依預設為關閉。 若要啟用查詢快取，請設定 `query_cache_type` 參數。 

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_query_cache_size) \(英文\)，以深入了解此參數。

> [!NOTE]
> 查詢快取從 MySQL 5.7.20 開始就已過時，而且已在 MySQL 8.0 中移除

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|* * 最大值 * *|
|---|---|---|---|---|
|基本|1|無法在基本層中設定|N/A|N/A|
|基本|2|無法在基本層中設定|N/A|N/A|
|一般用途|2|0|0|16777216|
|一般用途|4|0|0|33554432|
|一般用途|8|0|0|67108864|
|一般用途|16|0|0|134217728|
|一般用途|32|0|0|134217728|
|一般用途|64|0|0|134217728|
|記憶體最佳化|2|0|0|33554432|
|記憶體最佳化|4|0|0|67108864|
|記憶體最佳化|8|0|0|134217728|
|記憶體最佳化|16|0|0|134217728|
|記憶體最佳化|32|0|0|134217728|

### <a name="lower_case_table_names"></a>lower_case_table_names

根據預設，lower_case_table_name 會設定為1，而您可以在 MySQL 5.6 和 MySQL 5.7 中更新此參數。

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_lower_case_table_names) \(英文\)，以深入了解此參數。

> [!NOTE]
> 在 MySQL 8.0 中，預設會將 lower_case_table_name 設定為1，且您無法加以變更。

### <a name="innodb_strict_mode"></a>innodb_strict_mode

如果您收到類似「資料列大小太大 ( # A0 8126) 」的錯誤，則您可能會想要關閉參數 **innodb_strict_mode**。 不允許在伺服器層級全域修改伺服器參數 **innodb_strict_mode** ，因為如果資料列資料大小大於8k，資料將會被截斷而不會造成資料遺失的錯誤。 建議您修改架構，以符合頁面大小的限制。 

您可以使用，在工作階段層級設定這個參數 `init_connect` 。 若要在工作階段層級設定 **innodb_strict_mode** ，請參閱 [未列出的設定參數](https://docs.microsoft.com/azure/mysql/howto-server-parameters#setting-parameters-not-listed)。

> [!NOTE]
> 如果您有讀取複本伺服器，請在來源伺服器的工作階段層級將 **innodb_strict_mode** 設定為 OFF，將會中斷複寫。 如果您有讀取的複本，建議您將參數設定為 [關閉]。

### <a name="sort_buffer_size"></a>sort_buffer_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_sort_buffer_size) \(英文\)，以深入了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|無法在基本層中設定|N/A|N/A|
|基本|2|無法在基本層中設定|N/A|N/A|
|一般用途|2|524288|32768|4194304|
|一般用途|4|524288|32768|8388608|
|一般用途|8|524288|32768|16777216|
|一般用途|16|524288|32768|33554432|
|一般用途|32|524288|32768|33554432|
|一般用途|64|524288|32768|33554432|
|記憶體最佳化|2|524288|32768|8388608|
|記憶體最佳化|4|524288|32768|16777216|
|記憶體最佳化|8|524288|32768|33554432|
|記憶體最佳化|16|524288|32768|33554432|
|記憶體最佳化|32|524288|32768|33554432|

### <a name="tmp_table_size"></a>tmp_table_size

請檢閱 [MySQL 文件](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_tmp_table_size) \(英文\)，以深入了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組) **|**最小值 (位元組) **|**最大值 (位元組) **|
|---|---|---|---|---|
|基本|1|無法在基本層中設定|N/A|N/A|
|基本|2|無法在基本層中設定|N/A|N/A|
|一般用途|2|16777216|1024|67108864|
|一般用途|4|16777216|1024|134217728|
|一般用途|8|16777216|1024|268435456|
|一般用途|16|16777216|1024|536870912|
|一般用途|32|16777216|1024|1073741824|
|一般用途|64|16777216|1024|1073741824|
|記憶體最佳化|2|16777216|1024|134217728|
|記憶體最佳化|4|16777216|1024|268435456|
|記憶體最佳化|8|16777216|1024|536870912|
|記憶體最佳化|16|16777216|1024|1073741824|
|記憶體最佳化|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

在初始部署時，適用于 MySQL 的 Azure 伺服器會包含適用于時區資訊的系統資料表，但不會填入這些資料表。 時區資料表可藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。 請參閱 [Azure 入口網站](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-using-cli.md#working-with-the-time-zone-parameter) 文章，以了解如何呼叫預存程序，以及設定全域或工作階段層級的時區。

## <a name="non-configurable-server-parameters"></a>無法設定的伺服器參數

下列伺服器參數無法在服務中設定：

|**參數**|**固定值**|
| :------------------------ | :-------- |
|基本層中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

此處未列出的其他變數會設定為預設 MySQL 現成的值。 如需預設值的 [8.0](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html)、 [5.7](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)和 [5.6](https://dev.mysql.com/doc/refman/5.6/en/server-system-variables.html) 版本，請參閱 MySQL 檔。 

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [使用 Azure 入口網站設定伺服器參數](./howto-server-parameters.md)
- 瞭解如何 [使用 Azure CLI 設定伺服器參數](./howto-configure-server-parameters-using-cli.md)
- 瞭解如何 [使用 PowerShell 設定伺服器參數](./howto-configure-server-parameters-using-powershell.md)
