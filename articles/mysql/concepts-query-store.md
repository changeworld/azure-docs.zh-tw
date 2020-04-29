---
title: 查詢存放區-適用於 MySQL 的 Azure 資料庫
description: 瞭解適用於 MySQL 的 Azure 資料庫中的查詢存放區功能，以協助您追蹤一段時間的效能。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d138c2fb8ed667d5b3c961c9f567264fa40edaee
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79537035"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>使用查詢存放區監視適用於 MySQL 的 Azure 資料庫效能

**適用物件：** 適用於 MySQL 的 Azure 資料庫5。7

適用於 MySQL 的 Azure 資料庫中的查詢存放區功能可提供一段時間追蹤查詢效能的方式。 查詢存放區可協助您快速找到執行時間最長又最耗資源的查詢，簡化效能疑難排解。 查詢存放區會自動擷取查詢的歷程記錄和執行階段統計資料，並予以保留以供您檢閱。 依時間範圍區分資料，以便查看資料庫使用模式。 所有使用者、資料庫和查詢的資料都會儲存在適用於 MySQL 的 Azure 資料庫實例的**mysql**架構資料庫中。

## <a name="common-scenarios-for-using-query-store"></a>使用查詢存放區的常見案例

查詢存放區可以用於許多案例，包括下列各項：

- 偵測回歸查詢
- 判斷查詢在指定時間範圍內的執行次數
- 跨時間範圍比較查詢的平均值行時間，查看大幅差異

## <a name="enabling-query-store"></a>啟用查詢存放區

查詢存放區是選擇加入的功能，因此預設不會在伺服器上啟用。 針對指定伺服器上的所有資料庫，全域啟用或停用查詢存放區，且每個資料庫無法開啟或關閉。

### <a name="enable-query-store-using-the-azure-portal"></a>使用 Azure 入口網站啟用查詢存放區

1. 登入 Azure 入口網站並選取您的適用於 MySQL 的 Azure 資料庫伺服器。
1. 在功能表的 [設定]**** 區段中，選取 [伺服器參數]****。
1. 搜尋 query_store_capture_mode 參數。
1. 將 [值] 設定為 [全部] 並 [**儲存**]。

若要啟用查詢存放區中的等候統計資料：

1. 搜尋 query_store_wait_sampling_capture_mode 參數。
1. 將 [值] 設定為 [全部] 並 [**儲存**]。

最多允許20分鐘，讓第一批資料保存在 mysql 資料庫中。

## <a name="information-in-query-store"></a>查詢存放區中的資訊

查詢存放區有兩個存放區：

- 執行時間統計資料存放區，用於保存查詢執行統計資料資訊。
- 等候統計資料存放區，用於保存等候統計資料資訊。

為了將空間使用量降到最低，執行時間統計資料存放區中的執行時間執行統計資料是在固定且可設定的時間範圍內匯總。 查詢這些查詢存放區檢視，即可看到這些存放區中的資訊。

下列查詢會傳回查詢存放區中的相關資訊：

```sql
SELECT * FROM mysql.query_store;
```

或此查詢等候統計資料：

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>尋找等候查詢

> [!NOTE]
> 在尖峰工作負載期間內不應啟用等候統計資料，或針對機密工作負載無限期地開啟。 <br>針對在 CPU 使用率高或設定較低虛擬核心的伺服器上執行的工作負載，請在啟用等候統計資料時謹慎使用。 不應該無限期地開啟。 

等候事件類型會依相似性，將不同的等候事件結合成貯體。 查詢存放區提供等候事件類型、特定的等候事件名稱，以及有問題的查詢。 能夠將此等候資訊與查詢執行階段相互關聯，表示您可以更深入了解查詢效能特性從何而來。

以下是如何查詢存放區中的等候統計資料，以更多深入了解工作負載的一些範例：

| **觀測** | **動作** |
|---|---|
|高鎖定等候數 | 查看受影響查詢的查詢文字，並找出目標實體。 查看查詢存放區，針對經常執行和/或持續時間很長的實體，尋找修改同一實體的其他查詢。 找出這些查詢之後，請考慮變更應用程式邏輯，改善並行存取，或使用限制較少的隔離等級。 |
|高緩衝區 IO 等候數 | 在查詢存放區中尋找實體讀取次數高的查詢。 如果它們符合具有高 IO 等候的查詢，請考慮在基礎實體上引進索引，以執行搜尋而不是掃描。 這可將查詢的 IO 額外負荷降到最低。 請在入口網站檢查伺服器的**效能建議**，以查看是否有此伺服器的索引建議，可供將查詢最佳化。 |
|高記憶體等候數 | 找出查詢存放區中記憶體耗用量名列前茅的查詢。 這些查詢可能會進一步延遲受影響查詢的進度。 請在入口網站檢查伺服器的**效能建議**，以查看是否有索引建議，可供將這些查詢最佳化。|

## <a name="configuration-options"></a>設定選項

啟用查詢存放區時，會以每 15 分鐘的彙總時間範圍儲存資料一次，每個範圍內最多可有 500 個相異的查詢。

下列選項可用於設定查詢存放區參數。

| **參數** | **說明** | **預設值** | **格或** |
|---|---|---|---|
| query_store_capture_mode | 根據值開啟/關閉查詢存放區功能。 注意：如果 performance_schema 已關閉，開啟 query_store_capture_mode 將會開啟 performance_schema 以及此功能所需的效能架構儀器子集。 | ALL | 無、全部 |
| query_store_capture_interval | 查詢存放區捕獲間隔（以分鐘為單位）。 允許指定匯總查詢計量的間隔 | 15 | 5 - 60 |
| query_store_capture_utility_queries | 開啟或關閉以捕捉在系統中執行的所有公用程式查詢。 | 否 | 是，否 |
| query_store_retention_period_in_days | 在查詢存放區中保留資料的時間範圍（以天為單位）。 | 7 | 1 - 30 |

下列選項特別適用於等候統計資料。

| **參數** | **說明** | **預設值** | **格或** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | 允許開啟/關閉等候統計資料。 | 無 | 無、全部 |
| query_store_wait_sampling_frequency | 改變等候取樣的頻率（以秒為單位）。 5到300秒。 | 30 | 5-300 |

> [!NOTE]
> 目前**query_store_capture_mode**取代這項設定，這表示必須同時啟用**query_store_capture_mode**和**query_store_wait_sampling_capture_mode** ，等候統計資料才能正常執行。 如果**query_store_capture_mode**關閉，則等候統計資料也會關閉，因為等候統計資料會使用已啟用的 performance_schema，以及查詢存放區所捕獲的 query_text。

使用 [Azure 入口網站](howto-server-parameters.md) 或 [Azure CLI](howto-configure-server-parameters-using-cli.md) 來取得或設定參數的不同值。

## <a name="views-and-functions"></a>檢視和函式

使用下列檢視和函式來檢視和管理查詢存放區。 「[選取許可權」公用角色](howto-create-users.md#how-to-create-additional-admin-users-in-azure-database-for-mysql)中的任何人都可以使用這些視圖來查看查詢存放區中的資料。 這些視圖只能在**mysql**資料庫中使用。

移除常值和常數之後，查看查詢結構，其會呈現標準化。 如果兩個查詢完全相同 (但常值除外)，則兩者會有相同的雜湊碼。

### <a name="mysqlquery_store"></a>mysql. query_store

此檢視會傳回查詢存放區中的所有資料。 不同的資料庫識別碼、使用者識別碼及查詢識別碼都會自成一資料列。

| **Name** | **資料類型** | **IS_NullABLE** | **說明** |
|---|---|---|---|
| `schema_name`| Varchar （64） | 否 | 架構的名稱 |
| `query_id`| Bigint （20） | 否| 針對特定查詢產生的唯一識別碼，如果相同的查詢在不同的架構中執行，則會產生新的識別碼 |
| `timestamp_id` | timestamp| 否| 執行查詢的時間戳記。 這是以 query_store_interval 設定為基礎|
| `query_digest_text`| longtext| 否| 移除所有常值後的正規化查詢文字|
| `query_sample_text` | longtext| 否| 具有常值的實際查詢的第一個外觀|
| `query_digest_truncated` | bit| YES| 是否已截斷查詢文字。 如果查詢長度超過 1 KB，則值為 Yes。|
| `execution_count` | Bigint （20）| 否| 在設定的間隔期間內，查詢在此時間戳記識別碼/執行的次數|
| `warning_count` | Bigint （20）| 否| 此查詢在內部產生的警告數目|
| `error_count` | Bigint （20）| 否| 此查詢在間隔期間產生的錯誤數目|
| `sum_timer_wait` | double| YES| 此查詢在間隔期間的總執行時間|
| `avg_timer_wait` | double| YES| 此查詢在間隔期間的平均執行時間|
| `min_timer_wait` | double| YES| 此查詢的執行時間下限|
| `max_timer_wait` | double| YES| 執行時間上限|
| `sum_lock_time` | Bigint （20）| 否| 這個時間範圍內，此查詢執行的所有鎖定所花費的總時間量|
| `sum_rows_affected` | Bigint （20）| 否| 受影響的資料列數目|
| `sum_rows_sent` | Bigint （20）| 否| 傳送至用戶端的資料列數目|
| `sum_rows_examined` | Bigint （20）| 否| 檢查的資料列數目|
| `sum_select_full_join` | Bigint （20）| 否| 完整聯結的數目|
| `sum_select_scan` | Bigint （20）| 否| 選取掃描數目 |
| `sum_sort_rows` | Bigint （20）| 否| 已排序的資料列數目|
| `sum_no_index_used` | Bigint （20）| 否| 查詢未使用任何索引的次數|
| `sum_no_good_index_used` | Bigint （20）| 否| 查詢執行引擎未使用任何良好索引的次數|
| `sum_created_tmp_tables` | Bigint （20）| 否| 已建立的臨時表總數|
| `sum_created_tmp_disk_tables` | Bigint （20）| 否| 在磁片中建立的臨時表總數（產生 i/o）|
| `first_seen` | timestamp| 否| 匯總時間範圍內的第一次出現（UTC）查詢|
| `last_seen` | timestamp| 否| 此匯總時間範圍內的查詢最後一次出現（UTC）|

### <a name="mysqlquery_store_wait_stats"></a>mysql. query_store_wait_stats

此檢視會傳回查詢存放區中的等候事件資料。 不同的資料庫識別碼、使用者識別碼、查詢識別碼及事件都會自成一資料列。

| **Name**| **資料類型** | **IS_NullABLE** | **說明** |
|---|---|---|---|
| `interval_start` | timestamp | 否| 間隔開始時間（15分鐘增量）|
| `interval_end` | timestamp | 否| 間隔結束（15分鐘增量）|
| `query_id` | Bigint （20） | 否| 在正規化查詢上產生的唯一識別碼（從查詢存放區）|
| `query_digest_id` |  varchar(32) | 否| 移除所有常值之後的正規化查詢文字（從查詢存放區） |
| `query_digest_text` | longtext | 否| 具有常值的實際查詢的第一個外觀（從查詢存放區） |
| `event_type` |  varchar(32) | 否| 等待事件的類別 |
| `event_name` | varchar(128) | 否| 等待事件的名稱 |
| `count_star` | Bigint （20） | 否| 查詢間隔期間所取樣的等候事件數目 |
| `sum_timer_wait_ms` | double | 否| 此查詢在間隔期間的總等候時間（以毫秒為單位） |

### <a name="functions"></a>函式

| **名稱**| **描述** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | 在指定的時間戳記之前清除所有查詢存放區資料 |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | 清除指定時間戳記之前的所有等候事件資料 |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | 清除到期日早于指定時間戳記的建議 |

## <a name="limitations-and-known-issues"></a>限制與已知問題

- 如果 MySQL 伺服器上有參數`default_transaction_read_only` ，查詢存放區無法捕獲資料。
- 如果遇到長 Unicode 查詢（\>= 6000 個位元組），查詢存放區功能可能會中斷。
- 等候統計資料的保留期限為24小時。
- 等候統計資料會使用範例來捕獲部分事件。 您可以使用參數`query_store_wait_sampling_frequency`來修改頻率。

## <a name="next-steps"></a>後續步驟

- 深入瞭解[查詢效能深入](concepts-query-performance-insight.md)解析
