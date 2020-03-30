---
title: 查詢存儲 - MariaDB 的 Azure 資料庫
description: 瞭解 MariaDB Azure 資料庫中的查詢存儲功能，以説明您跟蹤隨時間的變化性能。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527804"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>使用查詢存儲監視 Azure 資料庫以顯示 MariaDB 的性能

**適用于：** MariaDB 的 Azure 資料庫 10.2

Mariadb Azure 資料庫中的查詢存儲功能提供了一種跟蹤查詢性能的方法。 查詢存放區可協助您快速找到執行時間最長又最耗資源的查詢，簡化效能疑難排解。 查詢存放區會自動擷取查詢的歷程記錄和執行階段統計資料，並予以保留以供您檢閱。 依時間範圍區分資料，以便查看資料庫使用模式。 所有使用者、資料庫和查詢的資料都存儲在 MariaDB 實例的 Azure 資料庫中**的 mysql**架構資料庫中。

## <a name="common-scenarios-for-using-query-store"></a>使用查詢存儲的常見方案

查詢存儲可用於多種方案，包括：

- 檢測回歸查詢
- 判斷查詢在指定時間範圍內的執行次數
- 跨時間範圍比較查詢的平均值行時間，查看大幅差異

## <a name="enabling-query-store"></a>啟用查詢存放區

查詢存放區是選擇加入的功能，因此預設不會在伺服器上啟用。 對於給定伺服器上的所有資料庫，查詢存儲全域啟用或禁用，並且不能為每個資料庫打開或關閉。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 入口網站啟用查詢存放區

1. 登錄到 Azure 門戶並為 MariaDB 伺服器選擇 Azure 資料庫。
1. 在功能表的 [設定]**** 區段中，選取 [伺服器參數]****。
1. 搜索query_store_capture_mode參數。
1. 將值設置為"全部"並**保存**。

要在查詢存儲中啟用等待統計資訊，請：

1. 搜索query_store_wait_sampling_capture_mode參數。
1. 將值設置為"全部"並**保存**。

第一批資料最多等待 20 分鐘才能保留在 mysql 資料庫中。

## <a name="information-in-query-store"></a>查詢存放區中的資訊

查詢存放區有兩個存放區：

- 用於持久化查詢執行統計資訊的運行時統計資訊存儲。
- 等待統計資訊存儲，用於持久等待統計資訊。

為了最小化空間使用，運行時統計資訊存儲中的運行時執行統計資訊將聚合到固定的可配置時間視窗上。 查詢這些查詢存放區檢視，即可看到這些存放區中的資訊。

下列查詢會傳回查詢存放區中的相關資訊：

```sql
SELECT * FROM mysql.query_store;
```

或此查詢的等待統計資訊：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>尋找等候查詢

> [!NOTE]
> 不應在工作負荷高峰時間啟用等待統計資訊，也不應針對敏感工作負載無限期打開。 <br>對於 CPU 利用率高的工作負載或配置的 vCore 較低的伺服器上，啟用等待統計資訊時要小心。 不應無限期地打開它。 

等候事件類型會依相似性，將不同的等候事件結合成貯體。 查詢存放區提供等候事件類型、特定的等候事件名稱，以及有問題的查詢。 能夠將此等候資訊與查詢執行階段相互關聯，表示您可以更深入了解查詢效能特性從何而來。

以下是如何查詢存放區中的等候統計資料，以更多深入了解工作負載的一些範例：

| **觀測** | **動作** |
|---|---|
|高鎖定等候數 | 查看受影響查詢的查詢文字，並找出目標實體。 查看查詢存放區，針對經常執行和/或持續時間很長的實體，尋找修改同一實體的其他查詢。 找出這些查詢之後，請考慮變更應用程式邏輯，改善並行存取，或使用限制較少的隔離等級。 |
|高緩衝區 IO 等候數 | 在查詢存放區中尋找實體讀取次數高的查詢。 如果查詢與較高的 IO 等待匹配，請考慮在基礎實體上引入索引，以執行查找而不是掃描。 這可將查詢的 IO 額外負荷降到最低。 請在入口網站檢查伺服器的**效能建議**，以查看是否有此伺服器的索引建議，可供將查詢最佳化。 |
|高記憶體等候數 | 找出查詢存放區中記憶體耗用量名列前茅的查詢。 這些查詢可能會進一步延遲受影響查詢的進度。 請在入口網站檢查伺服器的**效能建議**，以查看是否有索引建議，可供將這些查詢最佳化。|

## <a name="configuration-options"></a>設定選項

啟用查詢存放區時，會以每 15 分鐘的彙總時間範圍儲存資料一次，每個範圍內最多可有 500 個相異的查詢。

下列選項可用於設定查詢存放區參數。

| **參數** | **描述** | **預設** | **範圍** |
|---|---|---|---|
| query_store_capture_mode | 根據值打開/關閉查詢存儲區功能。 注意：如果performance_schema處於關閉程度，則打開query_store_capture_mode將打開performance_schema以及此功能所需的性能架構工具子集。 | ALL | 無，全部 |
| query_store_capture_interval | 查詢存儲捕獲間隔（以分鐘為單位）。 允許指定彙總查詢指標的時間間隔 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 打開或關閉以捕獲系統中正在執行的所有實用程式查詢。 | 否 | 是，否 |
| query_store_retention_period_in_days | 在查詢存儲中保留資料的時間視窗（以天數表示）。 | 7 | 1 - 30 |

下列選項特別適用於等候統計資料。

| **參數** | **描述** | **預設** | **範圍** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 允許打開/關閉等待統計資訊。 | 無 | 無，全部 |
| query_store_wait_sampling_frequency | 以秒為單位更改等待採樣的頻率。 5 到 300 秒。 | 30 | 5-300 |

> [!NOTE]
> 當前**query_store_capture_mode**取代此配置，這意味著**必須啟用query_store_capture_mode**和**query_store_wait_sampling_capture_mode**以等待統計資訊才能正常工作。 如果**query_store_capture_mode**關閉，則等待統計資訊也關閉，因為等待統計資訊利用了啟用performance_schema以及查詢存儲捕獲query_text。

使用[Azure 門戶](howto-server-parameters.md)獲取或設置參數的不同值。

## <a name="views-and-functions"></a>檢視和函式

使用下列檢視和函式來檢視和管理查詢存放區。 [選擇特權公共角色的](howto-create-users.md#create-additional-admin-users)任何人都可以使用這些視圖查看查詢存儲中的資料。 這些視圖僅在**mysql**資料庫中可用。

移除常值和常數之後，查看查詢結構，其會呈現標準化。 如果兩個查詢完全相同 (但常值除外)，則兩者會有相同的雜湊碼。

### <a name="mysqlquery_store"></a>mysql.query_store

此檢視會傳回查詢存放區中的所有資料。 不同的資料庫識別碼、使用者識別碼及查詢識別碼都會自成一資料列。

| **名稱** | **資料類型** | **IS_NullABLE** | **描述** |
|---|---|---|---|
| `schema_name`| 瓦爾查爾 （64） | 否 | 架構的名稱 |
| `query_id`| 大金 （20） | 否| 為特定查詢生成的唯一 ID，如果同一查詢在不同的架構中執行，將生成新的 ID |
| `timestamp_id` | timestamp| 否| 執行查詢的時間戳記。 這是基於query_store_interval配置|
| `query_digest_text`| longtext| 否| 刪除所有文本後正常化查詢文本|
| `query_sample_text` | longtext| 否| 使用文本的實際查詢的第一次外觀|
| `query_digest_truncated` | bit| YES| 查詢文本是否已截斷。 如果查詢長於 1 KB，則值為"是"|
| `execution_count` | 大金 （20）| 否| 此時間戳記 ID / 在配置間隔期間執行查詢的次數|
| `warning_count` | 大金 （20）| 否| 此查詢在內部期間生成的警告數|
| `error_count` | 大金 （20）| 否| 此查詢在間隔內生成的錯誤數|
| `sum_timer_wait` | double| YES| 在間隔內此查詢的總執行時間|
| `avg_timer_wait` | double| YES| 在間隔內此查詢的平均執行時間|
| `min_timer_wait` | double| YES| 此查詢的最短執行時間|
| `max_timer_wait` | double| YES| 最長執行時間|
| `sum_lock_time` | 大金 （20）| 否| 在此時間視窗期間，此查詢執行的所有鎖所花費的總時間|
| `sum_rows_affected` | 大金 （20）| 否| 受影響的資料列數目|
| `sum_rows_sent` | 大金 （20）| 否| 發送到用戶端的行數|
| `sum_rows_examined` | 大金 （20）| 否| 檢查的資料列數目|
| `sum_select_full_join` | 大金 （20）| 否| 完整聯接數|
| `sum_select_scan` | 大金 （20）| 否| 選擇掃描數 |
| `sum_sort_rows` | 大金 （20）| 否| 已排序的行數|
| `sum_no_index_used` | 大金 （20）| 否| 查詢不使用任何索引的次數|
| `sum_no_good_index_used` | 大金 （20）| 否| 查詢執行引擎不使用任何好索引的次數|
| `sum_created_tmp_tables` | 大金 （20）| 否| 創建臨時表的總數|
| `sum_created_tmp_disk_tables` | 大金 （20）| 否| 在磁片中創建的臨時表總數（生成 I/O）|
| `first_seen` | timestamp| 否| 聚合視窗期間查詢的第一次出現 （UTC）|
| `last_seen` | timestamp| 否| 在此聚合視窗期間查詢的最後一次出現 （UTC）|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

此檢視會傳回查詢存放區中的等候事件資料。 不同的資料庫識別碼、使用者識別碼、查詢識別碼及事件都會自成一資料列。

| **名稱**| **資料類型** | **IS_NullABLE** | **描述** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 間隔的開始（15 分鐘增量）|
| `interval_end` | timestamp | 否| 間隔結束（15 分鐘增量）|
| `query_id` | 大金 （20） | 否| 在正常化查詢上生成唯一 ID（從查詢存儲）|
| `query_digest_id` |  varchar(32) | 否| 刪除所有文本（從查詢存儲）後正常化查詢文本 |
| `query_digest_text` | longtext | 否| 使用文本首次出現實際查詢（從查詢存儲） |
| `event_type` |  varchar(32) | 否| 等待事件的類別 |
| `event_name` | varchar(128) | 否| 等待事件的名稱 |
| `count_star` | 大金 （20） | 否| 查詢間隔內採樣的等待事件數 |
| `sum_timer_wait_ms` | double | 否| 在間隔內此查詢的總等待時間（以毫秒為單位） |

### <a name="functions"></a>函式

| **名稱**| **描述** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 在給定時間戳記之前清除所有查詢存儲資料 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 在給定時間戳記之前清除所有等待事件資料 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 清除過期時間戳記之前的建議 |

## <a name="limitations-and-known-issues"></a>限制與已知問題

- 如果 MariaDB 伺服器已打開`default_transaction_read_only`參數，則查詢存儲無法捕獲資料。
- 如果查詢存儲功能遇到長 Unicode 查詢 （=\>6000 位元組），則可以中斷。
- 等待統計資訊的保留期為 24 小時。
- 等待統計資訊使用示例 ti 捕獲事件的一小部分。 可以使用 參數`query_store_wait_sampling_frequency`修改頻率。

## <a name="next-steps"></a>後續步驟

- 瞭解有關[查詢性能見解](concepts-query-performance-insight.md)的更多
