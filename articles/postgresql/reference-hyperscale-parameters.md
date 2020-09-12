---
title: 伺服器參數-超大規模 (超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模 (Citus 中的參數) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 2b4d3d44cb4e78f52ab03f31e58df5e66118ae25
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441505"
---
# <a name="server-parameters"></a>伺服器參數

有各種伺服器參數會影響超大規模 (Citus) 的行為，兩者都是來自標準于 postgresql，而特定于超大規模 (Citus) 。
您可以在超大規模 (Citus) 伺服器群組的 Azure 入口網站中設定這些參數。 在 [ **設定** ] 類別下，選擇 [背景 **工作節點參數** ] 或 [ **協調器節點參數**]。 這些頁面可讓您設定所有背景工作節點的參數，或僅針對協調器節點設定參數。

## <a name="hyperscale-parameters"></a>超大規模參數

> [!NOTE]
>
> 執行舊版 Citus 引擎的超大規模伺服器群組可能無法提供下列所有參數。

### <a name="general-configuration"></a>一般設定

#### <a name="citususe_secondary_nodes-enum"></a>citus：使用 \_ \_ (列舉) 的次要節點

設定為選取查詢選擇節點時所要使用的原則。 如果設定為 [永遠]，則 planner 只會查詢 [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中標示為「次要」 noderole 的節點。

此列舉支援的值為：

-   **永不：** (預設) 在主要節點上發生所有讀取。
-   **一律：** 會改為針對次要節點執行讀取，並停用 insert/update 語句。

#### <a name="cituscluster_name-text"></a>citus \_ (文字) 的叢集名稱

通知協調器 node planner 它會協調哪個叢集。 一旦 \_ 設定叢集名稱，planner 就只會查詢該叢集中的背景工作節點。

#### <a name="citusenable_version_checks-boolean"></a>citus：啟用 \_ \_ (布林值的版本檢查) 

升級超大規模 (Citus) 版本需要重新開機伺服器 (才能挑選新的共用程式庫) ，後面接著 ALTER EXTENSION UPDATE 命令。  執行這兩個步驟的失敗可能會造成錯誤或損毀。
超大規模 (Citus) 因此會驗證程式代碼的版本和延伸模組相符的版本，並在不會時發生錯誤 \' 。

此值預設為 true，而且在協調器上有效。 在罕見的情況下，複雜的升級程式可能需要將此參數設定為 false，進而停用檢查。

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus \_ \_ (布林值的記錄分散式鎖死 \_ 偵測) 

是否要在伺服器記錄檔中記錄分散式鎖死偵測相關的處理。 預設值為 false。

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus \_ \_ \_ (浮點數) 的分散式鎖死偵測因數

設定檢查分散式鎖死之前要等候的時間。 尤其是，等候時間會是此值乘以于 postgresql \' s [鎖死 \_ 超時](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) 設定。 預設值是 `2`。 的值會 `-1` 停用分散式鎖死偵測。

#### <a name="citusnode_connection_timeout-integer"></a>citus \_ \_ (整數) 的節點連接逾時

`citus.node_connection_timeout`GUC 會將最大持續時間 (以毫秒為單位，) 以等候連接建立。 超大規模 (Citus) 如果在至少一個背景工作連接建立之前經過超時時間，就會引發錯誤。 此 GUC 會影響來自協調器與背景工作角色的連線，以及背景工作角色之間的連接。

-   預設值：五秒
-   最小值：10毫秒
-   最大值：一小時

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>查詢統計資料

#### <a name="citusstat_statements_purge_interval-integer"></a>citus \_ \_ (整數) 的 stat 語句清除 \_ 間隔

設定維護背景工作從中不相符的 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) 移除記錄的頻率 `pg_stat_statements` 。 此設定值會設定以秒為單位的清除間隔時間間隔，預設值為10。 值為0會停用清除。

```psql
SET citus.stat_statements_purge_interval TO 5;
```

這個參數在協調器上是有效的，而且可以在執行時間變更。

### <a name="data-loading"></a>資料載入

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. 多重 \_ 分區 \_ commit \_ protocol (enum) 

設定在雜湊分散式資料表上執行複製時要使用的認可通訊協定。 在每個個別的分區位置上，複本會在交易區塊中執行，以確保在複製期間發生錯誤時不會內嵌任何資料。 不過，有一種特定的失敗情況，那就是在所有位置都能成功進行複製，但在所有交易認可之前， (硬體) 失敗。 此參數可用來防止在該情況下的資料遺失，方法是在下列認可通訊協定之間選擇：

-   **2pc：** (預設) 在分區位置上執行複製的交易會先使用於 postgresql 的 \' [兩階段認可](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) ，然後再進行認可。 失敗的認可哥以手動復原或使用認可準備的認可或復原來中止。
    使用2pc 時，在所有背景工作上的 [最大 \_ 備妥 \_ 交易](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) 應該會增加，通常與最大連接的值相同 \_ 。
-   **1pc：** 在分區放置上執行複製的交易會以單一迴圈的方式認可。 如果在所有放置的複製成功之後，認可失敗，資料可能會遺失 (很罕見的) 。

#### <a name="citusshard_replication_factor-integer"></a>citus. 分區 \_ 複寫 \_ 因素 (整數) 

設定分區的複寫因數，也就是將放置分區的節點數目，並預設為1。 此參數可在執行時間設定，並在協調器上生效。 此參數的理想值取決於叢集的大小和節點失敗率。  例如，如果您執行大型叢集並更頻繁地觀察節點失敗，您可能會想要增加此複寫因數。

#### <a name="citusshard_count-integer"></a>citus. 分區 \_ count (integer) 

設定雜湊資料分割資料表的分區計數，並將預設值設定為32。  建立雜湊資料分割資料表時， [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF 會使用這個值。 此參數可在執行時間設定，並在協調器上生效。

#### <a name="citusshard_max_size-integer"></a>citus. 分區 \_ \_ 大小上限 (整數) 

設定分區在分割之前將成長的大小上限，並預設為 1 GB。 當來源檔案的 \' 大小 (用於某個分區的暫存) 超過此設定值時，資料庫會確保會建立新的分區。 此參數可在執行時間設定，並在協調器上生效。

### <a name="planner-configuration"></a>Planner 設定

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus： limit \_ 子句 \_ row \_ fetch \_ count (integer) 

設定要針對 limit 子句優化的每個工作提取的資料列數目。
在某些情況下，select 查詢 with limit 子句可能需要提取每個工作的所有資料列，才能產生結果。 在這些情況下，以及近似值會產生有意義的結果，此設定值會設定從每個分區提取的資料列數目。 預設會停用限制近似值，且此參數會設定為-1。 此值可在執行時間設定，並在協調器上生效。

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus \_ \_ \_ (浮點數的相異誤差率) 

超大規模 (Citus) 可以使用於 postgresql-hll 擴充功能計算 (相異) 的計數。 此設定專案會在計算 (相異) 的計數時，設定所需的錯誤率。 0.0 （預設值）會停用 count (相異) 的近似值;和1.0 不提供結果精確度的保證。 建議您將此參數設定為0.005，以獲得最佳結果。 此值可在執行時間設定，並在協調器上生效。

#### <a name="citustask_assignment_policy-enum"></a>citus \_ \_ (列舉) 的工作指派原則

> [!NOTE]
> 只有當 [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) 大於1，或針對 [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables)進行查詢時，才適用此 GUC。

設定將工作指派給背景工作角色時要使用的原則。 協調器會根據分區位置將工作指派給背景工作角色。 此設定值會指定進行這些指派時要使用的原則。
目前有三種可能的工作指派原則可供使用。

-   **貪婪：** 「預設」原則是預設值，目的是將工作平均分散到不同的背景工作。
-   **迴圈** 配置資源：迴圈配置資源原則會以迴圈配置資源的方式，將工作指派給背景工作角色，以在不同的複本之間交替。 當資料表的分區計數較低（相較于背景工作角色數目）時，此原則可提供更佳的叢集使用率。
-   **第一個複本：** 第一個複本原則會根據分區的放置順序來指派工作 (複本) 為。 換句話說，分區的片段查詢會指派給具有該分區第一個複本的背景工作。
    這種方法可讓您有強大的保證，哪些分區將用於哪些節點 (也就是更強的記憶體存放區保證) 。

此參數可在執行時間設定，並在協調器上生效。

### <a name="intermediate-data-transfer"></a>中繼資料傳輸

#### <a name="citusbinary_worker_copy_format-boolean"></a>citus \_ \_ \_ (布林值) 的二進位工作者複製格式

使用二進位複製格式在背景工作之間傳輸中繼資料。
在大型資料表聯結期間，超大規模 (Citus) 可能必須在不同的背景工作之間動態重新分割和亂數據。 依預設，此資料會以文字格式傳輸。 啟用此參數會指示資料庫使用於 postgresql 的二進位序列化格式來傳送此資料。 此參數在背景工作上是有效的，而且必須在於 postgresql 中變更。 在編輯設定檔之後，使用者可以傳送 SIGHUP 信號或重新開機伺服器，讓此變更生效。

#### <a name="citusbinary_master_copy_format-boolean"></a>citus 二進位 \_ 主要 \_ 複製 \_ 格式 (布林值) 

使用二進位複製格式，在協調器與背景工作之間傳輸資料。 執行分散式查詢時，背景工作會將其中繼結果傳送給協調器，以進行最終匯總。 依預設，此資料會以文字格式傳輸。 啟用此參數會指示資料庫使用於 postgresql 的二進位序列化格式來傳送此資料。
此參數可在執行時間設定，並在協調器上生效。

#### <a name="citusmax_intermediate_result_size-integer"></a>citus。 (整數) 的最大 \_ 中繼 \_ 結果 \_ 大小

Cte 的中繼結果大小上限（以 KB 為單位），無法向下推送到背景工作節點以執行，以及用於複雜的子查詢。 預設值為 1 GB，而值為-1 表示沒有限制。
超過限制的查詢將會取消，並產生錯誤訊息。

### <a name="ddl"></a>DDL

#### <a name="citusenable_ddl_propagation-boolean"></a>citus： \_ \_ (布林值啟用 ddl 傳播) 

指定是否要自動將 DDL 變更從協調器傳播至所有背景工作角色。 預設值為 true。 因為某些架構變更需要資料表的存取獨佔鎖定，而且因為自動傳播會依序套用至所有的背景工作，所以它可能會使超大規模 (Citus) 叢集暫時較不具回應性。 您可以選擇停用此設定，並手動傳播變更。

### <a name="executor-configuration"></a>執行程式設定

#### <a name="general"></a>一般

##### <a name="citusall_modifications_commutative"></a>citus。所有的修改都能 \_ \_ 交換

超大規模 (Citus) 會強制執行交替規則，並為修改作業取得適當的鎖定，以確保行為的正確性。 例如，它會假設 INSERT 語句與另一個 INSERT 語句，而不是 UPDATE 或 DELETE 子句。 同樣地，它會假設 UPDATE 或 DELETE 子句不會與另一個 UPDATE 或 DELETE 子句一起進行。 這種預防措施表示更新和刪除需要超大規模 (Citus) 才能取得更強的鎖定。

如果您有可與插入或其他更新交換的 UPDATE 語句，則您可以藉由將此參數設定為 true 來放寬這些交替假設。 當此參數設定為 true 時，所有命令都會被視為可交換並宣告共用鎖定，以改善整體輸送量。 此參數可在執行時間設定，並在協調器上生效。

##### <a name="citusremote_task_check_interval-integer"></a>citus。遠端 \_ 工作 \_ 檢查 \_ 間隔 (整數) 

設定超大規模 (Citus) 檢查工作追蹤程式執行程式所管理之作業狀態的頻率。 預設為10毫秒。 協調器會將工作指派給背景工作角色，然後定期檢查每個工作的 \' 進度。 這個設定值會設定兩次後續檢查之間的時間間隔。 這個參數在協調器上是有效的，而且可以在執行時間設定。

##### <a name="citustask_executor_type-enum"></a>citus \_ (列舉) 的工作執行程式 \_ 類型

超大規模 (Citus) 有三個執行分散式 SELECT 查詢的執行程式類型。  您可以藉由設定此設定參數來選取所需的執行程式。 此參數接受的值包括：

-   **適應性：** 預設值。 對於涉及跨多個分區的匯總和共置聯結的查詢，最適合快速回應。
-   工作**追蹤器：** 工作追蹤器執行程式很適合長時間執行的複雜查詢，需要跨背景工作節點跳過資料，以及有效率的資源管理。
-   **即時：** (淘汰的) 與自動調整執行程式的用途類似，但較不具彈性，而且可能會導致背景工作節點有更多的連接壓力。

此參數可在執行時間設定，並在協調器上生效。

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus。多 \_ 任務 \_ 查詢 \_ 記錄 \_ 層級 (列舉) {#multi_task_logging}

為任何會產生多項工作的查詢設定記錄層級 (也就是叫用一個以上的分區) 。 在多租使用者應用程式遷移期間，記錄功能很有用，因為您可以選擇對這類查詢進行錯誤或警告，以找出這些查詢並為其新增租使用者 \_ 識別碼篩選。 此參數可在執行時間設定，並在協調器上生效。 這個參數的預設值是 \' off \' 。

此列舉支援的值為：

-   **off：** 將任何產生多項工作的查詢關閉記錄 (也就是跨多個分區) 
-   **debug：** DEBUG 嚴重性層級的記錄語句。
-   **記錄：** 記錄嚴重性層級的記錄語句。 記錄行將包含已執行的 SQL 查詢。
-   **請注意：** 記錄語句的嚴重性層級。
-   **警告：** 警告嚴重性層級的記錄語句。
-   **錯誤：** 錯誤嚴重性層級的記錄語句。

`error`在開發測試期間使用，以及在 `log` 實際生產環境部署期間較低的記錄層級，可能會很有用。
選擇 `log` 會導致多工查詢出現在資料庫記錄中，查詢本身會顯示在語句之後 \" 。\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus：啟用重新分割聯結 \_ \_ (布林值) 

一般情況下，嘗試使用自動調整執行程式來執行重新分割聯結將會失敗，並顯示錯誤訊息。  但是，設定 `citus.enable_repartition_joins` 為 true 可讓超大規模 (Citus) 暫時切換到工作追蹤器執行程式來執行聯結。  預設值為 false。

#### <a name="task-tracker-executor-configuration"></a>工作追蹤器執行程式設定

##### <a name="citustask_tracker_delay-integer"></a>citus。工作 \_ 追蹤器 \_ 延遲 (整數) 

此參數會將工作管理的時間四捨五入和預設值設定為200毫秒。 工作追蹤程式進程會定期喚醒、對指派給它的所有工作進行逐步解說，以及排程和執行這些工作。  然後，工作追蹤程式會進入睡眠狀態一段時間，然後再重新流覽這些工作。
這個設定值會決定該睡眠期間的長度。 此參數在背景工作上是有效的，而且必須在於 postgresql 中變更。 在編輯設定檔之後，使用者可以傳送 SIGHUP 信號或重新開機伺服器，讓變更生效。

您可以減少這個參數，藉由減少管理四捨五入之間的時間間隔，來修剪由於工作追蹤器執行程式所造成的延遲。
當分區查詢很短，因此會定期更新其狀態時，減少延遲會很有用。

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus 指派工作 \_ 批次大小的最大值 \_ \_ \_ (整數) 

協調器上的工作追蹤器執行程式會以同步方式將工作分批指派給背景工作角色。 此參數會設定要在單一批次中指派的最大工作數目。 選擇較大的批次大小可加快工作指派的速度。 但是，如果背景工作數目很大，則所有工作者可能需要較長的時間來取得工作。  此參數可在執行時間設定，並在協調器上生效。

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus：每個節點的最大執行工作 \_ \_ \_ \_ (整數) 

工作追蹤程式進程會排程並執行適當的指派給它的工作。 這個設定值會設定在任何指定時間于一個節點上並存執行的工作數目上限，並預設為8。

這項限制可確保您不 \' 會同時有許多工作，而有助於避免磁片 i/o 爭用。 如果您的查詢是從記憶體或 Ssd 提供，您可以增加每個節點的最大執行工作， \_ \_ \_ \_ 而不需要擔心。

##### <a name="cituspartition_buffer_size-integer"></a>citus：資料分割 \_ 緩衝區 \_ 大小 (整數) 

設定要用於資料分割作業且預設為 8 MB 的緩衝區大小。
超大規模 (Citus) 可讓您在聯結兩個大型資料表時，將資料表資料重新分割成多個檔案。 當此分割區緩衝區填滿之後，會將重新分割的資料排清到磁片上的檔案中。  您可以在執行時間設定這個設定專案，並在背景工作角色上生效。

#### <a name="explain-output"></a>說明輸出

##### <a name="citusexplain_all_tasks-boolean"></a>citus 說明 \_ \_ (布林值的所有工作) 

根據預設，超大規模 (Citus) 會在執行分散式查詢的 [說明](http://www.postgresql.org/docs/current/static/sql-explain.html) 時，顯示單一的任意工作的輸出。 在大部分的情況下，說明的輸出會類似于各項工作。 有時候，某些工作將會以不同的方式進行規劃，或有更高的執行時間。 在這些情況下，啟用此參數可能會很有用，在這之後，說明輸出將會包含所有的工作。 說明所有工作可能會導致說明需要較長的時間。

## <a name="postgresql-parameters"></a>于 postgresql 參數

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) -設定日期和時間值的顯示格式
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) -設定間隔值的顯示格式
* [時區](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) ：設定用來顯示和解讀時間戳記的時區
* [application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) -設定要在統計資料和記錄中報告的應用程式名稱
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) -啟用陣列中的 Null 元素輸入
* [自動資料清理](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) -啟動自動資料清理子流程
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) -分析為 reltuples 分數之前的元組插入、更新或刪除數目
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -分析之前的元組插入、更新或刪除的最小數目
* 自動資料清理執行之間的睡眠時間[autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME)
* 以毫秒為單位的[autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY)自動資料清理成本延遲（毫秒）
* napping 前的可用[autovacuum_vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT)清理成本量，適用于自動資料清理
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -在清理之前的元組數目更新或刪除，reltuples 為分數
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -在清理之前更新或刪除的最小元組數目
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) -設定每個自動資料清理背景工作進程要使用的最大記憶體
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) -先前執行寫入的頁面數已排清到磁片
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) -設定 \' 字串常值中是否允許 ""
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) -在四捨五入之間的背景寫入器睡眠時間
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) -先前執行寫入的頁面數已排清到磁片
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -背景寫入器每一次排清的 lru 頁面數目上限
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) -每個回合的平均緩衝區使用量的倍數
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -設定 bytea 的輸出格式
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) -在 CREATE function 期間檢查函數主體
* 在檢查點期間用來排清中途緩衝區的[checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET)時間，做為檢查點間隔的一部分
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) -設定自動 WAL 檢查點之間的最長時間
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) -如果檢查點區段更頻繁地填滿，則會啟用警告
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -設定用戶端的字元集編碼
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) -設定傳送給用戶端的訊息層級
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) -設定交易認可與排清 WAL 至磁片之間的延遲（以微秒為單位）
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) -在執行之前，設定最小並行開啟的交易 commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) -讓 planner 使用條件約束來優化查詢
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) -設定規劃在索引掃描期間處理每個索引項目目的成本預估
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) -設定規劃每個運算子或函式呼叫的成本預估
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) -將處理每個元組的成本預估 (資料列) 
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) -將即將抓取之資料指標資料列的分數設定為規劃工具的估計值
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) -設定在檢查鎖死之前等候鎖定的時間量（以毫秒為單位）
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -縮排剖析和計畫樹狀結構顯示
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -記錄每個查詢的剖析樹狀結構
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -記錄每個查詢的執行計畫
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) -記錄每個查詢的重寫剖析樹
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -設定預設統計資料目標
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -設定要在其中建立資料表和索引的預設資料表空間
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) 設定預設文字搜尋設定
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -設定新交易的預設延遲狀態
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) -設定每個新交易的交易隔離等級
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -設定新交易的預設唯讀狀態
* default_with_oids-預設會使用 Oid 建立新的資料表
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) -設定規劃磁碟快取大小的假設
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) -啟用 planner 的點陣圖掃描計畫使用
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) -啟用 planner 的收集合並計畫使用
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) -啟用 planner 的雜湊匯總計畫使用
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) -啟用 planner 的雜湊聯結計畫使用
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) -啟用規劃工具使用僅限索引掃描計畫
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) -啟用 planner 的索引掃描計畫使用
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) -啟用 planner 使用具體化
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) -啟用規劃合併聯結計畫的使用
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) -啟用 planner 的嵌套迴圈聯結計畫使用
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) -讓 planner 使用連續掃描計畫
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) -啟用 planner 的明確排序步驟使用
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) -讓 PLANNER 使用 TID 掃描計畫
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) -在一般字串常值中警告反斜線轉義
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -終止任何錯誤的會話
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) -設定浮點值所顯示的位數
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -強制使用平行查詢設備
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) -設定不折迭子查詢以外的清單大小
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) -啟用遺傳查詢優化
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo：投入時間用來設定其他 geqo 參數的預設值
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo：演算法的反覆運算次數
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo：人口中的個人數目
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo：隨機路徑選取的種子
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo：人口內的選擇壓力
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) -從使用 geqo 的專案設定閾值
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) -設定 gin 精確搜尋所允許的結果上限
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) -設定 gin 索引的暫止清單大小上限
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) -設定任何閒置交易允許的最大持續時間
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) -設定不簡維聯結結構以外的清單大小
* [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) -設定格式化貨幣金額的地區設定
* [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) -設定格式化數位的地區設定
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) -針對大型物件上的許可權檢查啟用回溯相容性模式
* [lock_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) -設定任何等候鎖定的最大允許持續時間（以毫秒為單位） () 。 0關閉此功能
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) -設定將記錄自動資料清理動作的最短執行時間
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) -記錄每個檢查點
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -記錄每個成功的連接
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -設定伺服器記錄檔輸出的目的地
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) -包含持續時間的會話記錄結尾
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) -記錄每個已完成 SQL 語句的持續時間
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -設定已記錄訊息的詳細資訊
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) -記錄長時間鎖定等候
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) -設定要記錄語句的最短執行時間（以毫秒為單位） () 。 -1 停用記錄語句持續時間
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -導致記錄在此層級或以上的所有語句產生錯誤
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) -設定記錄的訊息層級
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -記錄每個複寫命令
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) -設定記錄的語句類型
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) -針對每個查詢，將累計效能統計資料寫入伺服器記錄檔
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) -記錄使用超過此 kb 數目的暫存檔案的使用
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) -設定維護作業所要使用的最大記憶體
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) -設定平行背景工作數目的最大數目，一次只能使用中
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) -為每個執行程式節點設定平行進程的最大數目
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) -設定每一頁面的述詞鎖定的元組數目上限
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) -設定每個關聯的述詞鎖定頁面和元組數目上限
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) -設定當熱待命伺服器正在處理封存的 WAL 資料時，取消查詢之前的延遲上限
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) -設定當熱待命伺服器正在處理已串流處理的 WAL 資料時，取消查詢之前的延遲上限
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -每個訂用帳戶的資料表同步處理背景工作數目上限
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) -設定觸發檢查點的 wal 大小
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) -設定平行掃描的最小索引資料量
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -設定縮減 wal 的大小下限
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) -發出自于 postgresql 9.4 以來已變更意義之結構的警告
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) -設定啟動工作進程以進行平行查詢的規劃成本預估
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) -設定規劃人員將每個元組 (資料列) 從背景工作傳遞至主要後端的成本預估
* [pg_stat_statements。儲存](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -跨伺服器關機儲存 pg_stat_statements 的統計資料
* [pg_stat_statements. track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -選取要追蹤的語句 pg_stat_statements
* [pg_stat_statements。 track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) -選取是否要追蹤公用程式命令 pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) -產生 SQL 片段時，將所有識別碼加上引號
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) -設定 planner 的 nonsequentially 提取磁片的成本預估頁面
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) -啟用資料列安全性
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) -針對不符合架構的名稱設定架構搜尋順序
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) -設定依序提取磁片頁面的規劃工具估計成本
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) -設定觸發程式和重寫規則的會話行為
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -導致 ' ... '以字面形式處理反斜線的字串
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) -設定任何語句所允許的最大持續時間 (毫秒) 。 0關閉此功能
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) -啟用同步的連續掃描
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) -設定目前交易的同步處理層級
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -tcp keepalive 重新傳輸的最大數目
* 發出 TCP keepalive 之間的[tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE)時間
* TCP keepalive 重新傳輸之間的[tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL)時間
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) -設定每個資料庫會話所使用的暫存緩衝區數目上限
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) -設定用於臨時表和排序檔案的資料表空間 (s) 
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) -收集執行命令的相關資訊
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) -收集資料庫活動的統計資料
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) -收集資料庫活動的函數層級統計資料
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) -收集資料庫 i/o 活動的時間統計資料
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -將 "EXPR = null" 視為 "EXPR 為 null"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -真空成本延遲（毫秒）
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) -napping 前可用的總成本
* [vacuum_cost_page_dirty](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) -藉由真空來變動總數頁面的成本
* 在緩衝區快取中找到之頁面的[vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) -真空成本
* 在緩衝區快取中找不到頁面的[vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS)清理成本
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -清理和熱清除應延後的交易數（如果有的話）
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -清理應該凍結資料表資料列的最小存留期
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -清理應該掃描整個資料表以凍結元組的年齡
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -清理應該凍結資料表資料列中 MultiXactId 的最小存留期
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) multixact 的存留期，則應該掃描整個資料表以凍結元組
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) -將 wal 接收者狀態報表之間的最大間隔設定為主要
* 在 WAL 寫入器中執行的 WAL 排清之間的[wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY)時間
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -由 wal 寫入器寫出以觸發排清的 wal 數量
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) -在寫入暫存磁片檔案之前，設定內部排序作業和雜湊表所使用的記憶體數量
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) -設定二進位值如何以 XML 編碼
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) -設定隱含剖析和序列化作業中的 XML 資料是否要視為檔或內容片段

## <a name="next-steps"></a>接下來的步驟

* 除了伺服器參數以外，另一種形式的設定是超大規模 (Citus) server 群組中的資源設定 [選項](concepts-hyperscale-configuration-options.md) 。
* 基礎于 postgresql 資料基底也有設定 [參數](http://www.postgresql.org/docs/current/static/runtime-config.html)。
