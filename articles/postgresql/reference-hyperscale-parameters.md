---
title: 伺服器參數-超大規模資料庫 (超大規模資料庫 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模資料庫中的參數) SQL API 中的 (Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 57258540f3cd7b4c47b662b0885453cedd188e5b
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136736"
---
# <a name="server-parameters"></a>伺服器參數

有各種伺服器參數會影響超大規模資料庫 (Citus) 的行為、來自標準于 postgresql 的參數，以及超大規模資料庫 (Citus) 特有的參數。 若要深入瞭解于 postgresql 設定參數，您可以造訪于 postgresql 檔的[執行時間](http://www.postgresql.org/docs/current/static/runtime-config.html)設定一節。

本參考的其餘部分旨在討論超大規模資料庫 (Citus) 特定的設定參數。 這些參數可以在超大規模資料庫 (Citus) 伺服器群組的 [**設定**] 下的 [背景**工作節點參數**] 底下設定 Azure 入口網站。

> [!NOTE]
>
> 執行舊版 Citus 引擎的超大規模資料庫伺服器群組可能不會提供以下所列的所有參數。

## <a name="general-configuration"></a>一般設定

### <a name="citususe_secondary_nodes-enum"></a>citus。使用 \_ \_ (列舉) 的次要節點

設定在選擇選取查詢的節點時，所要使用的原則。 如果設定為 [永遠]，則 planner 只會查詢[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中標示為 ' 次要 ' noderole 的節點。

此列舉支援的值為：

-   **永不：** (預設) 所有讀取都會發生在主要節點上。
-   **一律：** 系統會改為對次要節點執行讀取，並停用 insert/update 語句。

### <a name="cituscluster_name-text"></a>citus： \_ (文字) 的叢集名稱

通知協調器節點規劃程式所協調的叢集。 一旦 \_ 設定叢集名稱之後，planner 就只會查詢該叢集中的背景工作節點。

### <a name="citusenable_version_checks-boolean"></a>citus： \_ \_ (布林值啟用版本檢查) 

升級超大規模資料庫 (Citus) 版本需要重新開機伺服器 (才能挑選新的共用程式庫) ，後面接著 ALTER EXTENSION UPDATE 命令。  若無法執行這兩個步驟，可能會導致錯誤或當機。
超大規模資料庫 (Citus) 因此會驗證程式代碼的版本和延伸模組的相符項，並在不會發生錯誤 \' 。

此值預設為 true，並在協調器上生效。 在罕見的情況下，複雜的升級程式可能需要將此參數設定為 false，因此會停用檢查。

### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus \_ \_ \_)  (布林值的分散式鎖死偵測

是否要在伺服器記錄檔中記錄分散式的鎖死偵測相關處理。 預設值為 false。

### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus 分散式 \_ 鎖死 \_ 偵測 \_ 因素 (浮點) 

設定檢查分散式鎖死之前等待的時間。 特別的是，等候的時間會是此值乘以于 postgresql \' s[鎖死 \_ 超時](https://www.postgresql.org/docs/current/static/runtime-config-locks.html)設定。 預設值是 `2`。 值為會 `-1` 停用分散式鎖死偵測。

### <a name="citusnode_connection_timeout-integer"></a>citus \_ \_)  (整數的節點連接逾時

`citus.node_connection_timeout`GUC 會設定等候連線建立的最大持續時間（以毫秒為單位） () 。 如果已建立至少一個背景工作連接的時間，超大規模資料庫 (Citus) 會引發錯誤。 此 GUC 會影響從協調員到背景工作角色的連線，以及背景工作角色之間的連接。

-   預設值：五秒
-   最小值：10毫秒
-   最大值：一小時

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

## <a name="query-statistics"></a>查詢統計資料

### <a name="citusstat_statements_purge_interval-integer"></a>citus \_ \_ (整數) 的 stat 語句清除 \_ 間隔

設定維護背景工作從中不相符的[citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)中移除記錄的頻率 `pg_stat_statements` 。 此設定值會設定以秒為單位的清除之間的時間間隔，預設值為10。 值為0會停用清除。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

這個參數在協調器上有效，而且可以在執行時間變更。

## <a name="data-loading"></a>資料載入

### <a name="citusmulti_shard_commit_protocol-enum"></a>citus。多重 \_ 分區 \_ 認可 \_ 通訊協定 (列舉) 

設定在雜湊分散式資料表上執行複製時要使用的認可通訊協定。 在每個個別的分區位置上，會在交易區塊中執行複製，以確保在複製期間發生錯誤時不會內嵌任何資料。 不過，有一種特定的失敗情況是複本在所有位置上都成功，但是 (硬體) 失敗會在所有交易認可之前發生。 此參數可用來避免在此情況下，藉由選擇下列認可通訊協定來防止資料遺失：

-   **2pc：** (預設) 在分區位置上執行複製的交易，首先會使用於 postgresql 的 \' [兩階段認可](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html)，然後進行認可。 無法使用認可備妥或復原備妥來手動復原或中止失敗的認可。
    使用2pc 時，所有背景工作角色的[最大 \_ 準備 \_ 交易](http://www.postgresql.org/docs/current/static/runtime-config-resource.html)應該增加，通常與最大連接的值相同 \_ 。
-   **1pc：** 在分區放置上執行複製的交易會在單一迴圈中認可。 如果在所有位置的複製成功之後，如果認可失敗，資料可能會遺失 (罕見的) 。

### <a name="citusshard_replication_factor-integer"></a>citus. 分區 \_ replication \_ 因數 (integer) 

設定分區的複寫因素，也就是將放置分區的節點數目，並將預設為1。 這個參數可在執行時間設定，並且在協調器上生效。 這個參數的理想值取決於叢集的大小和節點失敗的速率。  例如，如果您執行大型叢集並更頻繁地觀察節點失敗，您可能會想要增加此複寫因素。

### <a name="citusshard_count-integer"></a>citus. 分區 \_ count (整數) 

設定雜湊分割資料表的分區計數，預設值為32。  建立雜湊分割資料表時， [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF 會使用這個值。 這個參數可在執行時間設定，並且在協調器上生效。

### <a name="citusshard_max_size-integer"></a>citus. 分區 \_ \_ 大小上限 (整數) 

設定分區在分割之前會成長的大小上限，並預設為 1 GB。 當原始程式檔的 \' 大小 (用於某個分區的暫存) 超過此設定值時，資料庫會確保會建立新的分區。 這個參數可在執行時間設定，並且在協調器上生效。

## <a name="planner-configuration"></a>Planner 設定

### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus \_ (整數) 的限制子句資料 \_ 列 \_ 提取 \_ 計數

設定限制子句優化的每個工作所要提取的資料列數目。
在某些情況下，select 查詢 with limit 子句可能需要從每個工作提取所有資料列，以產生結果。 在這些情況下，以及近似值會產生有意義的結果時，此設定值會設定要從每個分區提取的資料列數目。 限制近似值預設為停用，且此參數設定為-1。 此值可在執行時間設定，並在協調器上生效。

### <a name="cituscount_distinct_error_rate-floating-point"></a>citus。 count \_ 相異 \_ 錯誤率 \_ (浮點) 

超大規模資料庫 (Citus) 可以使用於 postgresql-hll 擴充功能來計算 (相異) 的計數。 此設定專案會在計算計數 (相異) 時，設定所需的錯誤率。 0.0 （這是預設值）會停用 (相異) 的計數近似值;和1.0 不保證結果的精確度。 我們建議將此參數設定為0.005，以獲得最佳結果。 此值可在執行時間設定，並在協調器上生效。

### <a name="citustask_assignment_policy-enum"></a>citus \_ \_ (列舉) 的工作指派原則

> [!NOTE]
> 只有當[shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer)大於1，或針對[reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)的查詢時，才適用此 GUC。

設定將工作指派給工作者時要使用的原則。 協調器會根據分區位置，將工作指派給工作者。 此設定值指定進行這些指派時要使用的原則。
目前有三種可能的工作指派原則可供使用。

-   **貪婪：**[貪婪原則] 是預設值，目的是要在背景工作角色之間平均分配工作。
-   迴圈配置資源 **：** 迴圈配置資源原則會以迴圈配置資源方式，在不同的複本之間交替指派工作給工作者。 相較于背景工作的數目，此原則可讓您更有效率地使用資料表的分區計數。
-   **第一個-複本：** 第一個複本原則會根據分區的插入順序來指派工作， (複本) 。 換句話說，分區的片段查詢會指派給具有該分區第一個複本的背景工作角色。
    這個方法可讓您對哪些分區將用於哪些節點 (亦即，更強的記憶體常駐保證) 的強大保證。

這個參數可在執行時間設定，並且在協調器上生效。

## <a name="intermediate-data-transfer"></a>中繼資料傳輸

### <a name="citusbinary_worker_copy_format-boolean"></a>citus \_ \_ \_ (布林值) 的二進位工作者複製格式

使用二進位複製格式來傳送背景工作角色之間的中繼資料。
在大型資料表聯結期間，超大規模資料庫 (Citus) 可能必須在不同的背景工作之間動態重新分割和亂數據。 根據預設，這項資料會以文字格式傳輸。 啟用此參數會指示資料庫使用於 postgresql 的二進位序列化格式來傳送此資料。 這個參數在背景工作角色上有效，而且需要在於 postgresql 檔案中變更。 編輯設定檔之後，使用者可以傳送 SIGHUP 信號或重新開機伺服器，讓此變更生效。

### <a name="citusbinary_master_copy_format-boolean"></a>citus 的二進位 \_ 主要 \_ 複製 \_ 格式 (布林值) 

使用二進位複製格式，在協調器和背景工作之間傳輸資料。 執行分散式查詢時，背景工作會將其中繼結果傳輸至協調器以進行最終匯總。 根據預設，這項資料會以文字格式傳輸。 啟用此參數會指示資料庫使用於 postgresql 的二進位序列化格式來傳送此資料。
此參數可在執行時間設定，並在協調器上生效。

### <a name="citusmax_intermediate_result_size-integer"></a>citus。 \_ \_ \_ (整數) 的中繼結果大小上限

Cte 的中繼結果大小上限（以 KB 為單位），無法向下推送至背景工作節點進行執行，以及用於複雜的子查詢。 預設為 1 GB，而值為-1 表示沒有限制。
超過此限制的查詢將會取消，並產生錯誤訊息。

## <a name="ddl"></a>DDL

### <a name="citusenable_ddl_propagation-boolean"></a>citus：啟用 \_ \_ (布林值) 的 ddl 傳播

指定是否要自動將 DDL 變更從協調器傳播到所有背景工作。 預設值為 true。 因為某些架構變更在資料表上需要存取獨佔鎖定，而且因為自動傳播會依序套用到所有的背景工作，所以它可以讓超大規模資料庫 (Citus) 叢集暫時不會有回應。 您可以選擇停用此設定，並手動傳播變更。

## <a name="executor-configuration"></a>執行程式設定

### <a name="general"></a>一般

#### <a name="citusall_modifications_commutative"></a>citus。所有 \_ 修改皆為 \_ 交換

超大規模資料庫 (Citus) 會強制執行運算交換性規則，並取得適當的修改作業鎖定，以保證行為的正確性。 例如，它會假設 INSERT 語句與另一個 INSERT 語句，而不是 UPDATE 或 DELETE 子句。 同樣地，它會假設 UPDATE 或 DELETE 子句不會以另一個 UPDATE 或 DELETE 子句來執行。 這種預防措施表示更新和刪除需要超大規模資料庫 (Citus) 才能取得更強的鎖定。

如果您有可與您的插入或其他更新交換的 UPDATE 語句，則可以將此參數設定為 true 來放寬這些運算交換性假設。 當此參數設定為 true 時，所有命令都會被視為可交換並宣告共用鎖定，以改善整體輸送量。 此參數可在執行時間設定，並在協調器上生效。

#### <a name="citusremote_task_check_interval-integer"></a>citus \_ \_ (整數) 的遠端工作檢查 \_ 間隔

設定超大規模資料庫 (Citus) 檢查工作追蹤程式執行器所管理之作業狀態的頻率。 預設值為10毫秒。 協調員會將工作指派給工作者，然後定期檢查是否有每個工作的 \' 進度。 此設定值會設定兩次後續檢查之間的時間間隔。 這個參數在協調器上有效，而且可以在執行時間設定。

#### <a name="citustask_executor_type-enum"></a>citus \_ (列舉) 的工作執行程式 \_ 類型

超大規模資料庫 (Citus) 有三個執行程式類型，可執行分散式 SELECT 查詢。  您可以藉由設定此 configuration 參數來選取所需的執行程式。 此參數接受的值包括：

-   調適型 **：** 預設值。 對於包含跨越多個分區之匯總和共置聯結聯結的查詢，這是最理想的做法。
-   工作**追蹤器：** 工作追蹤程式執行程式非常適合長期執行的複雜查詢，需要跨背景工作節點亂數據，以及有效率的資源管理。
-   **即時：** (已淘汰的) 與調適型執行程式的用途類似，但較不具彈性，而且可能會對背景工作節點造成更多的連接壓力。

這個參數可在執行時間設定，並且在協調器上生效。

#### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus 的多 \_ 任務 \_ 查詢 \_ 記錄 \_ 層級 (列舉) {#multi_task_logging}

為任何會產生多個工作 (的查詢設定記錄層級，也就是叫用一個以上的分區) 。 記錄在多租使用者應用程式遷移期間很有用，因為您可以選擇對這類查詢進行錯誤或警告，尋找它們並將租使用者 \_ 識別碼篩選器新增至其中。 此參數可在執行時間設定，並在協調器上生效。 此參數的預設值為 \' off \' 。

此列舉支援的值為：

-   **關閉：** 關閉產生多個工作的任何查詢的記錄， (也就是跨多個分區) 
-   **debug：** DEBUG 嚴重性層級的 Logs 語句。
-   **記錄檔：** 記錄嚴重性層級的記錄語句。 記錄行將包含已執行的 SQL 查詢。
-   **請注意：** 記錄語句嚴重性層級。
-   **警告：** WARNING 嚴重性層級的 Logs 語句。
-   **錯誤：** 記錄語句的錯誤嚴重性層級。

在 `error` 開發測試期間使用，以及在 `log` 實際生產環境部署期間等較低的記錄層級，可能會很有用。
選擇 `log` 會導致多工查詢出現在資料庫記錄中，查詢本身會顯示在語句之後 \" 。\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

#### <a name="citusenable_repartition_joins-boolean"></a>citus：啟用 \_ (布林值的重新分割聯結 \_) 

一般來說，嘗試使用調適型執行程式來執行重新分割聯結將會失敗，並出現錯誤訊息。  不過，將設定 `citus.enable_repartition_joins` 為 true 可讓超大規模資料庫 (Citus) 暫時切換到工作追蹤器執行程式以執行聯結。  預設值為 false。

### <a name="task-tracker-executor-configuration"></a>任務追蹤程式執行程式設定

#### <a name="citustask_tracker_delay-integer"></a>citus \_ (整數) 的工作追蹤器 \_ 延遲

這個參數會設定工作管理之間的工作追蹤器睡眠時間，並將預設值舍入為200毫秒。 工作追蹤程式會定期喚醒、引導所有指派給它的工作，以及排程和執行這些工作。  然後，工作追蹤器會睡眠一段時間，然後再次流覽這些工作。
此設定值會決定該休眠期間的長度。 這個參數在背景工作角色上有效，而且需要在於 postgresql 檔案中變更。 編輯設定檔之後，使用者可以傳送 SIGHUP 信號或重新開機伺服器，變更才會生效。

這個參數可以減少，以藉由減少管理四捨五入之間的時間間隔，來修剪因工作追蹤程式執行而造成的延遲。
當分區查詢很短，因而定期更新其狀態時，降低延遲會很有用。

#### <a name="citusmax_assign_task_batch_size-integer"></a>citus。 \_ 指派工作 \_ \_ 批次 \_ 大小上限 (整數) 

協調器上的工作追蹤程式執行程式會以同步方式，將工作分批指派給工作者上的背景程式。 這個參數會設定要在單一批次中指派的工作數目上限。 選擇較大的批次大小可加快工作指派的速度。 不過，如果背景工作角色的數目很大，則所有工作者可能需要較長的時間來取得工作。  此參數可在執行時間設定，並在協調器上生效。

#### <a name="citusmax_running_tasks_per_node-integer"></a>citus。每個節點的執行中工作 \_ \_ \_ \_ (整數) 的最大值

工作追蹤程式會視需要排程並執行指派給它的工作。 此設定值會設定在任何指定時間，在一個節點上同時執行的工作數目上限，預設為8。

此限制可確保您不 \' 會有許多工作同時遇到磁片的問題，並協助避免磁片 i/o 爭用。 如果您的查詢是從記憶體或 Ssd 提供，您可以在每個節點上增加執行中工作的最大值， \_ \_ \_ \_ 而不需要擔心。

#### <a name="cituspartition_buffer_size-integer"></a>citus 資料分割 \_ 緩衝區 \_ 大小 (整數) 

設定要用於資料分割作業的緩衝區大小，預設值為 8 MB。
超大規模資料庫 (Citus) 可在聯結兩個大型資料表時，將資料表資料重新分割成多個檔案。 此分割區緩衝區填滿之後，會將重新分割的資料排清到磁片上的檔案。  此設定專案可以在執行時間設定，並且在背景工作角色上生效。

### <a name="explain-output"></a>說明輸出

#### <a name="citusexplain_all_tasks-boolean"></a>citus：說明 \_ \_ (布林值的所有工作) 

根據預設，超大規模資料庫 (Citus) 會在執行分散式查詢的[說明](http://www.postgresql.org/docs/current/static/sql-explain.html)時，顯示單一的任意工作的輸出。 在大部分情況下，說明輸出在工作上會很相似。 有時候，部分工作會以不同的方式進行規劃，或有更高的執行時間。 在這些情況下，啟用此參數可能會有説明，在此之後，說明輸出將會包含所有工作。 說明所有工作可能會導致說明花費較長的時間。

## <a name="next-steps"></a>後續步驟

* 除了伺服器參數之外，另一種形式的設定是超大規模資料庫 (Citus) 伺服器群組中的資源設定[選項](concepts-hyperscale-configuration-options.md)。
* 基礎于 postgresql 資料基底也有設定[參數](http://www.postgresql.org/docs/current/static/runtime-config.html)。
