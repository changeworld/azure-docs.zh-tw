---
title: 伺服器參數-適用於 MariaDB 的 Azure 資料庫
description: 本主題提供在適用於 MariaDB 的 Azure 資料庫中設定伺服器參數的指導方針。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 6/25/2020
ms.openlocfilehash: 7797ee9d20b33a25c1b51289036651c7ad9f22a1
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247493"
---
# <a name="server-parameters-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫中的伺服器參數

本文提供在適用於 MariaDB 的 Azure 資料庫中設定伺服器參數的考慮和指導方針。

## <a name="what-are-server-parameters"></a>什麼是伺服器參數？ 

適用于 mariadb 引擎提供許多不同的伺服器變數/參數，可用來設定和微調引擎行為。 某些參數可在執行時間期間動態設定，而其他參數則是「靜態」，需要重新開機伺服器才能套用。

適用於 MariaDB 的 Azure 資料庫公開使用 [Azure 入口網站](./howto-server-parameters.md)、 [Azure CLI](./howto-configure-server-parameters-cli.md)和 [PowerShell](./howto-configure-server-parameters-using-powershell.md) 來變更各種適用于 mariadb 伺服器參數的值，以符合您工作負載需求的能力。

## <a name="configurable-server-parameters"></a>可設定的伺服器參數

支援的伺服器參數清單會不斷成長。 使用 Azure 入口網站中的 [伺服器參數] 索引標籤，即可查看完整清單及設定伺服器參數值。

請參閱下列各節，以深入瞭解數個經常更新之伺服器參數的限制。 這些限制取決於伺服器的定價層和虛擬核心。

### <a name="log_bin_trust_function_creators"></a>log_bin_trust_function_creators

在適用於 MariaDB 的 Azure 資料庫中，二進位記錄一律會啟用 (`log_bin` 也就是在) 上設定為。 如果您想要使用觸發程式，將會收到類似于 *您未啟用超級許可權和二進位記錄的錯誤 (您可能會想要使用較不安全的 `log_bin_trust_function_creators` 變數)*。

二進位記錄格式一律是 **row** ，而伺服器的所有連接 **一律** 會使用以資料列為基礎的二進位記錄。 使用以資料列為基礎的二進位記錄時，不會有安全性問題，而且二進位記錄無法中斷，因此您可以安全地設定 [`log_bin_trust_function_creators`](https://mariadb.com/docs/reference/mdb/system-variables/log_bin_trust_function_creators/) 為 **TRUE**。

### <a name="innodb_buffer_pool_size"></a>innodb_buffer_pool_size

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/innodb-system-variables/#innodb_buffer_pool_size)，進一步了解此參數。

#### <a name="servers-supporting-up-to-4-tb-storage"></a>最多可支援 4 TB 儲存體的伺服器

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

MariaDB 會根據在建立資料表期間所提供的設定，將 InnoDB 資料表儲存在不同的資料表空間中。 [系統資料表空間](https://mariadb.com/kb/en/innodb-system-tablespaces/)是 InnoDB 資料字典的儲存區域。 [file-per-table 資料表空間](https://mariadb.com/kb/en/innodb-file-per-table-tablespaces/) \(英文\) 包含單一 InnoDB 資料表的資料和索引，且會獨自儲存在檔案系統的資料檔中。 這個行為是由 `innodb_file_per_table` 伺服器參數所控制。 將 `innodb_file_per_table` 設定為 `OFF` 會導致 InnoDB 在系統資料表空間中建立資料表。 否則，InnoDB 會在 file-per-table 資料表空間中建立資料表。

適用於 MariaDB 的 Azure 資料庫在單一資料檔中支援最大達到 **1 TB**。 如果資料庫大小大於 1 TB，則應該在 [innodb_file_per_table](https://mariadb.com/kb/en/innodb-system-variables/#innodb_file_per_table) 資料表空間中建立資料表。 如果單一資料表大小大於 1 TB，則應該使用分割區資料表。

### <a name="join_buffer_size"></a>join_buffer_size

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)，進一步了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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
> 為了獲得最佳體驗，建議使用連接共用器 (例如 ProxySQL)，有效率地管理連線。

建立 MariaDB 的新用戶端連線需要一段時間，且在建立之後，這些連線會佔用資料庫資源，即使閒置時也一樣。 大部分應用程式會要求許多短期連線，這會加重這種情況。 結果會減少實際工作負載的可用資源，因而導致效能降低。 減少閒置連線並重複使用現有連線的連接共用器，有助於避免這種情況。 若要了解如何設定 ProxySQL，請前往[部落格文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) \(英文\)。

>[!Note]
>ProxySQL 是開放原始碼的社區工具。 Microsoft 會以最大的努力來支援它。 為了取得具有授權指導的生產環境支援，您可以評估並與 [ProxySQL 產品支援人員](https://proxysql.com/services/support/)聯繫。

### <a name="max_heap_table_size"></a>max_heap_table_size

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)，進一步了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

使用參數，適用于 mariadb 中預設會啟用查詢快取 `have_query_cache` 。 

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)，進一步了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

### <a name="sort_buffer_size"></a>sort_buffer_size

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)，進一步了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

請參閱 [MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)，進一步了解此參數。

|定價層|**vCore(s)**|**預設值 (位元組)**|**最小值 (位元組)**|**最大值 (位元組)**|
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

在初始部署時，Azure for 適用于 mariadb 伺服器包含時區資訊的系統資料表，但不會填入這些資料表。 時區資料表可藉由從 MySQL 命令列或 MySQL Workbench 等工具呼叫 `mysql.az_load_timezone` 預存程序來填入。 請參閱 [Azure 入口網站](howto-server-parameters.md#working-with-the-time-zone-parameter)或 [Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) 文章，以了解如何呼叫預存程序，以及設定全域或工作階段層級的時區。

## <a name="non-configurable-server-parameters"></a>無法設定的伺服器參數

下列伺服器參數無法在服務中設定：

|**參數**|**固定值**|
| :------------------------ | :-------- |
|基本層中的 innodb_file_per_table|OFF|
|innodb_flush_log_at_trx_commit|1|
|sync_binlog|1|
|innodb_log_file_size|256MB|
|innodb_log_files_in_group|2|

未列在此的其他伺服器參數會設定為其 [MariaDB](https://mariadb.com/kb/en/server-system-variables/) 的 MariaDB 預設值。

## <a name="next-steps"></a>後續步驟

- 瞭解如何 [使用 Azure 入口網站設定伺服器參數](./howto-server-parameters.md)
- 瞭解如何 [使用 Azure CLI 設定伺服器參數](./howto-configure-server-parameters-cli.md)
- 瞭解如何 [使用 PowerShell 設定伺服器參數](./howto-configure-server-parameters-using-powershell.md)
