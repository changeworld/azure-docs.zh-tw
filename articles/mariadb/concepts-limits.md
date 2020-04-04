---
title: 限制 - MariaDB 的 Azure 資料庫
description: 本文說明適用於 MariaDB 的 Azure 資料庫有何限制，例如連線數量和儲存引擎選項。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632331"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>適用於 MariaDB 的 Azure 資料庫相關限制
下列各節說明資料庫服務中的容量、儲存引擎支援、權限支援、資料操作陳述式支援，以及功能限制。

## <a name="server-parameters"></a>伺服器參數

幾個常用伺服器參數的最小值和最大值由定價層和 vCore 決定。 有關限制,請參閱下表。

### <a name="max_connections"></a>max_connections

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|50|10|50|
|基本|2|100|10|100|
|一般用途|2|300|10|600|
|一般用途|4|625|10|1250|
|一般用途|8|1250|10|2500|
|一般用途|16|2500|10|5000|
|一般用途|32|5000|10|10000|
|一般用途|64|10000|10|20000|
|記憶體最佳化|2|600|10|800|
|記憶體最佳化|4|1250|10|2500|
|記憶體最佳化|8|2500|10|5000|
|記憶體最佳化|16|5000|10|10000|
|記憶體最佳化|32|10000|10|20000|

當連線超過限制時，則可能會收到下列錯誤：
> 錯誤 1040 (08004)：太多的連接

> [!IMPORTANT]
> 為了獲得最佳體驗,我們建議您使用代理SQL等連接池程式來高效地管理連接。

創建到 MariaDB 的新用戶端連接需要時間,一旦建立,這些連接將佔用資料庫資源,即使處於空閒狀態也是如此。 大多數應用程式都要求許多短壽命連接,這加劇了這種情況。 結果是實際工作負載可用的資源更少,導致性能下降。 減少空閒連接並重用現有連接的連接池將有助於避免這種情況。 要瞭解如何設定 ProxySQL,請造訪我們的[部落格文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

### <a name="query_cache_size"></a>query_cache_size

默認情況下,查詢緩存處於關閉狀態。 要啟用查詢快取,請設定參數`query_cache_type`。 

查看[MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)以瞭解有關此參數的更多詳細資訊。

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|基本層中無法設定|N/A|N/A|
|基本|2|基本層中無法設定|N/A|N/A|
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

查看[MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)以瞭解有關此參數的更多詳細資訊。

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|基本層中無法設定|N/A|N/A|
|基本|2|基本層中無法設定|N/A|N/A|
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

### <a name="join_buffer_size"></a>join_buffer_size

查看[MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)以瞭解有關此參數的更多詳細資訊。

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|基本層中無法設定|N/A|N/A|
|基本|2|基本層中無法設定|N/A|N/A|
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

### <a name="max_heap_table_size"></a>max_heap_table_size

查看[MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)以瞭解有關此參數的更多詳細資訊。

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|基本層中無法設定|N/A|N/A|
|基本|2|基本層中無法設定|N/A|N/A|
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

### <a name="tmp_table_size"></a>tmp_table_size

查看[MariaDB 文件](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)以瞭解有關此參數的更多詳細資訊。

|**定價層**|**虛擬核心**|**預設值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|基本層中無法設定|N/A|N/A|
|基本|2|基本層中無法設定|N/A|N/A|
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

可以通過從 MySQL`mysql.az_load_timezone`命令行或 MySQL 工作台等工具調用存儲過程來填充時區表。 有關如何調用存儲過程並設置全域或會話級時區,請參閱[Azure 門戶](howto-server-parameters.md#working-with-the-time-zone-parameter)或[Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter)文章。

## <a name="storage-engine-support"></a>儲存引擎支援

### <a name="supported"></a>支援
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/) \(英文\)
- [記憶](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>不支援
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/) \(英文\)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/) \(英文\)
- [檔案](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>權限支援

### <a name="unsupported"></a>不支援
- DBA 角色：許多伺服器參數與設定可能會在無意中造成伺服器效能降級，或是取消 DBMS 的 ACID 屬性。 因此，為了維護產品層級的服務完整性與 SLA，此服務並不會公開 DBA 角色。 在建立新資料庫行個體時所建構的預設使用者帳戶，可讓使用者在受管理的資料庫執行個體中執行大部分的 DDL 與 DML 陳述式。
- 超級特權:同樣[超級特權](https://mariadb.com/kb/en/library/grant/#global-privileges)也受到限制。
- 定義:需要超級許可權才能創建並且受到限制。 如果使用備份匯入資料，執行 mysqldump 時以手動方式或使用 `--skip-definer` 命令移除 `CREATE DEFINER` 命令。

## <a name="data-manipulation-statement-support"></a>資料操作陳述式支援

### <a name="supported"></a>支援
- 支援 `LOAD DATA INFILE`，但必須指定 `[LOCAL]` 參數並導向至 UNC 路徑 (透過 SMB 掛接的 Azure 儲存體)。

### <a name="unsupported"></a>不支援
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>功能限制：

### <a name="scale-operations"></a>調整作業
- 目前不支援基本定價層的雙向動態調整。
- 不支援減少伺服器儲存體大小。

### <a name="server-version-upgrades"></a>伺服器版本升級
- 目前不支援在主要資料庫引擎版本之間進行自動轉換。

### <a name="point-in-time-restore"></a>還原時間點
- 使用 PITR 功能時，所建立新伺服器的設定會與作為新伺服器基礎的伺服器設定相同。
- 不支援還原已刪除的伺服器。

### <a name="subscription-management"></a>訂用帳戶管理
- 目前不支援跨訂用帳戶和資源群組動態移動預先建立的伺服器。

### <a name="vnet-service-endpoints"></a>VNet 服務端點
- VNet 服務端點的支援僅適用於一般用途伺服器和記憶體最佳化伺服器。

### <a name="storage-size"></a>儲存體大小
- 有關每個定價層的儲存大小限制,請參閱[定價層](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>目前已知問題
- MariaDB 伺服器執行個體於建立連線後會顯示不正確的伺服器版本。 若要取得正確的伺服器執行個體引擎版本，請使用 `select version();` 命令。

## <a name="next-steps"></a>後續步驟
- [每個服務層中提供的內容](concepts-pricing-tiers.md)
- [支援的 MariaDB 資料庫版本](concepts-supported-versions.md)
