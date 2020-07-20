---
title: 查詢存放區-適用於 MariaDB 的 Azure 資料庫
description: 瞭解適用於 MariaDB 的 Azure 資料庫中的查詢存放區功能，以協助您追蹤一段時間的效能。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: a502638744009fc34a7f0a27f8034b89d2c8fa26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "79527804"
---
# <a name="monitor-azure-database-for-mariadb-performance-with-query-store"></a>使用查詢存放區監視適用於 MariaDB 的 Azure 資料庫效能

**適用物件：** 適用於 MariaDB 的 Azure 資料庫10。2

適用于適用于 mariadb 的 Azure 資料庫中的查詢存放區功能提供一段時間追蹤查詢效能的方式。 查詢存放區可協助您快速找到執行時間最長又最耗資源的查詢，簡化效能疑難排解。 查詢存放區會自動擷取查詢的歷程記錄和執行階段統計資料，並予以保留以供您檢閱。 依時間範圍區分資料，以便查看資料庫使用模式。 所有使用者、資料庫和查詢的資料都會儲存在適用於 MariaDB 的 Azure 資料庫實例的**mysql**架構資料庫中。

## <a name="common-scenarios-for-using-query-store"></a>使用查詢存放區的常見案例

查詢存放區可以用於許多案例，包括下列各項：

- 偵測迴歸查詢
- 判斷查詢在指定時間範圍內的執行次數
- 跨時間範圍比較查詢的平均值行時間，查看大幅差異

## <a name="enabling-query-store"></a>啟用查詢存放區

查詢存放區是選擇加入的功能，因此預設不會在伺服器上啟用。 查詢存放區是針對指定伺服器上的所有資料庫全域啟用或停用的，並無法個別開啟或關閉資料庫。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 入口網站啟用查詢存放區

1. 登入 Azure 入口網站並選取您的適用於 MariaDB 的 Azure 資料庫伺服器。
1. 在功能表的 [設定] 區段中，選取 [伺服器參數]。
1. 搜尋 query_store_capture_mode 參數。
1. 將值設為 [ALL]，然後**儲存**。

若要啟用查詢存放區中的等候統計資料：

1. 搜尋 query_store_wait_sampling_capture_mode 參數。
1. 將值設為 [ALL]，然後**儲存**。

請等候 20 分鐘，以讓第一批資料保存在 mysql 資料庫中。

## <a name="information-in-query-store"></a>查詢存放區中的資訊

查詢存放區有兩個存放區：

- 執行時間統計資料存放區，用於保存查詢執行統計資料資訊。
- 等候統計資料存放區，用於保存等候統計資料資訊。

為了讓空間使用量降到最低，會經過一段固定且可設定的時間範圍，才彙總執行階段統計資料存放區的執行階段執行統計資料。 查詢這些查詢存放區檢視，即可看到這些存放區中的資訊。

下列查詢會傳回查詢存放區中的相關資訊：

```sql
SELECT * FROM mysql.query_store;
```

或者，此查詢可取得等候統計資料：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>尋找等候查詢

> [!NOTE]
> 等候統計資料不應在尖峰工作負載期間內啟用，或針對機密工作負載無限期地開啟。 <br>如果工作負載以高 CPU 使用率執行，或在設定較少虛擬核心的伺服器上執行，請在啟用等候統計資料時特別小心。 不應無限期地開啟等候統計資料。 

等候事件類型會依相似性，將不同的等候事件結合成貯體。 查詢存放區提供等候事件類型、特定的等候事件名稱，以及有問題的查詢。 能夠將此等候資訊與查詢執行階段相互關聯，表示您可以更深入了解查詢效能特性從何而來。

以下是如何查詢存放區中的等候統計資料，以更多深入了解工作負載的一些範例：

| **觀測** | **動作** |
|---|---|
|高鎖定等候數 | 查看受影響查詢的查詢文字，並找出目標實體。 查看查詢存放區，針對經常執行和/或持續時間很長的實體，尋找修改同一實體的其他查詢。 找出這些查詢之後，請考慮變更應用程式邏輯，改善並行存取，或使用限制較少的隔離等級。 |
|高緩衝區 IO 等候數 | 在查詢存放區中尋找實體讀取次數高的查詢。 如果與高 IO 等候數的查詢相符，請考慮對基礎實體引進索引，以執行搜尋，而不是掃描。 這可將查詢的 IO 額外負荷降到最低。 請在入口網站檢查伺服器的**效能建議**，以查看是否有此伺服器的索引建議，可供將查詢最佳化。 |
|高記憶體等候數 | 找出查詢存放區中記憶體耗用量名列前茅的查詢。 這些查詢可能會進一步延遲受影響查詢的進度。 請在入口網站檢查伺服器的**效能建議**，以查看是否有索引建議，可供將這些查詢最佳化。|

## <a name="configuration-options"></a>設定選項

啟用查詢存放區時，會以每 15 分鐘的彙總時間範圍儲存資料一次，每個範圍內最多可有 500 個相異的查詢。

下列選項可用於設定查詢存放區參數。

| **參數** | **說明** | **預設值** | **Range** |
|---|---|---|---|
| query_store_capture_mode | 根據該值來開啟/關閉查詢存放區功能。 注意:如果 performance_schema 為 OFF，則開啟 query_store_capture_mode 時將會開啟 performance_schema 和此功能所需的效能結構描述檢測子集。 | ALL | NONE、ALL |
| query_store_capture_interval | 查詢存放區擷取間隔 (以分鐘為單位)。 允許指定彙總查詢計量的間隔 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 開啟或關閉，以擷取在系統中執行的所有公用程式查詢。 | 否 | YES、NO |
| query_store_retention_period_in_days | 查詢存放區中的資料保留期間 (以天為單位)。 | 7 | 1 - 30 |

下列選項特別適用於等候統計資料。

| **參數** | **說明** | **預設值** | **Range** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 允許開啟/關閉等候統計資料。 | 無 | NONE、ALL |
| query_store_wait_sampling_frequency | 改變等候取樣的頻率 (以秒為單位)。 5 到 300 秒。 | 30 | 5-300 |

> [!NOTE]
> 目前，**query_store_capture_mode** 會取代此設定，這表示 **query_store_capture_mode** 和 **query_store_wait_sampling_capture_mode** 必須啟用為 ALL，等待統計資料才能正常執行。 如果關閉 **query_store_capture_mode**，則等候統計資料也會關閉，因為等候統計資料會利用啟用的 performance_schema，以及查詢存放區所擷取的 query_text。

使用[Azure 入口網站](howto-server-parameters.md)來取得或設定參數的不同值。

## <a name="views-and-functions"></a>檢視和函式

使用下列檢視和函式來檢視和管理查詢存放區。 [選取權限公用角色](howto-create-users.md#create-additional-admin-users)中的任何人都可以使用這些檢視，查看查詢存放區中的資料。 這些檢視僅適用於 **mysql** 資料庫。

移除常值和常數之後，查看查詢結構，其會呈現標準化。 如果兩個查詢完全相同 (但常值除外)，則兩者會有相同的雜湊碼。

### <a name="mysqlquery_store"></a>mysql.query_store

此檢視會傳回查詢存放區中的所有資料。 不同的資料庫識別碼、使用者識別碼及查詢識別碼都會自成一資料列。

| **名稱** | **資料類型** | **IS_NULLABLE** | **說明** |
|---|---|---|---|
| `schema_name`| varchar(64) | 否 | 結構描述的名稱 |
| `query_id`| bigint(20) | 否| 針對特定查詢產生的唯一識別碼，如果在不同的架構中執行相同查詢，則會產生新的識別碼 |
| `timestamp_id` | timestamp| 否| 查詢執行時的時間戳記。 這會以 query_store_interval 設定為基礎|
| `query_digest_text`| longtext| 否| 移除所有常值後的正規化查詢文字|
| `query_sample_text` | longtext| 否| 第一個具有常值的實際查詢外觀|
| `query_digest_truncated` | bit| YES| 是否截斷查詢文字。 如果查詢長度超過 1 KB，則值為 [是]|
| `execution_count` | bigint(20)| 否| 此時間戳記識別碼/所設定間隔期間內的查詢執行次數|
| `warning_count` | bigint(20)| 否| 此查詢在間隔期間產生的警告數目|
| `error_count` | bigint(20)| 否| 此查詢在間隔期間產生的錯誤數目|
| `sum_timer_wait` | double| YES| 此查詢在間隔期間的總執行時間|
| `avg_timer_wait` | double| YES| 此查詢在間隔期間的平均執行時間|
| `min_timer_wait` | double| YES| 此查詢的最短執行時間|
| `max_timer_wait` | double| YES| 最長執行時間|
| `sum_lock_time` | bigint(20)| 否| 在此時間範圍內，此查詢執行上所有鎖定的時間總計|
| `sum_rows_affected` | bigint(20)| 否| 受影響的資料列數目|
| `sum_rows_sent` | bigint(20)| 否| 傳送至用戶端的資料列數目|
| `sum_rows_examined` | bigint(20)| 否| 檢查的資料列數目|
| `sum_select_full_join` | bigint(20)| 否| 完整聯結的次數|
| `sum_select_scan` | bigint(20)| 否| 掃描選取項目的次數 |
| `sum_sort_rows` | bigint(20)| 否| 已排序的資料列數目|
| `sum_no_index_used` | bigint(20)| 否| 查詢未使用任何索引的次數|
| `sum_no_good_index_used` | bigint(20)| 否| 查詢執行引擎未使用任何良好索引的次數|
| `sum_created_tmp_tables` | bigint(20)| 否| 已建立的暫存資料表總數|
| `sum_created_tmp_disk_tables` | bigint(20)| 否| 在磁碟中建立的暫存資料表總數 (產生 I/O)|
| `first_seen` | timestamp| 否| 彙總時間範圍內的第一次出現查詢的時間 (UTC)|
| `last_seen` | timestamp| 否| 此彙總時間範圍內最後一次出現查詢的時間 (UTC)|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

此檢視會傳回查詢存放區中的等候事件資料。 不同的資料庫識別碼、使用者識別碼、查詢識別碼及事件都會自成一資料列。

| **名稱**| **資料類型** | **IS_NULLABLE** | **說明** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 間隔開始時間 (依 15 分鐘遞增)|
| `interval_end` | timestamp | 否| 間隔結束時間 (依 15 分鐘遞增)|
| `query_id` | bigint(20) | 否| 在正規化查詢上產生的唯一識別碼 (從查詢存放區中)|
| `query_digest_id` | varchar(32) | 否| 移除所有常值之後的正規化查詢文字 (從查詢存放區中) |
| `query_digest_text` | longtext | 否| 第一個具有常值的實際查詢外觀 (從查詢存放區中) |
| `event_type` | varchar(32) | 否| 等候事件的類別 |
| `event_name` | varchar(128) | 否| 等候事件的名稱 |
| `count_star` | bigint(20) | 否| 查詢間隔期間所取樣的等候事件數目 |
| `sum_timer_wait_ms` | double | 否| 此查詢在間隔期間的總等候時間 (以毫秒為單位) |

### <a name="functions"></a>函式

| **名稱**| **說明** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 清除指定時間戳記之前的所有查詢存放區資料 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 清除指定時間戳記之前的所有等候事件資料 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 清除到期日早於指定時間戳記的建議 |

## <a name="limitations-and-known-issues"></a>限制與已知問題

- 如果適用于 mariadb 伺服器上有參數 `default_transaction_read_only` ，查詢存放區就無法捕獲資料。
- 如果遇到長時間的 Unicode 查詢 (\> = 6000 個位元組)，查詢存放區功能可能會中斷。
- 等候統計資料的保留期限為 24 小時。
- 等候統計資料會使用範例 ti 來捕捉事件的一小部分。 您可以使用參數 `query_store_wait_sampling_frequency` 來修改頻率。

## <a name="next-steps"></a>後續步驟

- 深入了解[查詢效能深入解析](concepts-query-performance-insight.md)
