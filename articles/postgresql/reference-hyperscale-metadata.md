---
title: 系統資料表-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 分散式查詢執行的中繼資料
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/17/2020
ms.locfileid: "97652841"
---
# <a name="system-tables-and-views"></a>系統資料表和視圖

超大規模 (Citus) 會建立和維護包含伺服器群組中分散式資料相關資訊的特殊資料表。 協調器節點會在規劃如何跨背景工作節點執行查詢時，參考這些資料表。

## <a name="coordinator-metadata"></a>協調器中繼資料

超大規模 (Citus) 會根據散發資料行，將每個分散式資料表分割成多個邏輯分區。 協調器接著會維護中繼資料表格，以追蹤這些分區健康情況和位置的統計資料和相關資訊。

在本節中，我們會說明每個中繼資料表及其架構。
您可以在登入協調器節點之後，使用 SQL 來查看和查詢這些資料表。

> [!NOTE]
>
> 超大規模 (Citus) 執行舊版 Citus 引擎的伺服器群組可能無法提供以下所列的所有資料表。

### <a name="partition-table"></a>分割區資料表

Pg \_ dist \_ 分割區資料表會儲存有關資料庫中哪些資料表所散發的中繼資料。 針對每個分散式資料表，它也會儲存散發方法的相關資訊，以及散發資料行的詳細資訊。

| 名稱         | 類型     | 描述                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | 此資料列對應的分散式資料表。 此值會參考 pg_class 系統目錄資料表中的 relfilenode 資料行。                                                                                                                   |
| partmethod   | char     | 用於分割/散發的方法。 此資料行對應至不同散發方法的值為 append： ' a '、hash： ' h '、參考資料表： ' n '                                                                          |
| partkey      | 文字     | 散發資料行的詳細資訊，包括資料行編號、類型和其他相關資訊。                                                                                                                                      |
| colocationid | integer  | 此資料表所屬的共置群組。 相同群組中的資料表允許共置聯結和分散式匯總與其他優化之間。 這個值會參考 pg_dist_colocation 資料表中的 colocationid 資料行。                      |
| repmodel     | char     | 用於資料複寫的方法。 此資料行的值對應至不同的複寫方法： Citus 語句型複寫： ' c '、于 postgresql 串流複寫： ' '、兩階段認可 (用於參考資料表) ： t ' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>分區資料表

Pg \_ dist \_ 分區資料表會儲存有關資料表個別分區的中繼資料。 Pg_dist_shard 包含分區所屬的分散式資料表的相關資訊，以及分區之散發資料行的相關統計資料。
針對附加分散式資料表，這些統計資料會對應至散發資料行的最小/最大值。 針對雜湊分散式資料表，它們是指派給該分區的雜湊標記範圍。 這些統計資料是在 SELECT 查詢期間用於剪除不相關的分區。

| 名稱          | 類型     | 描述                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | 此資料列對應的分散式資料表。 此值會參考 pg_class 系統目錄資料表中的 relfilenode 資料行。                                                          |
| shardid       | BIGINT   | 指派給此分區的全域唯一識別碼。                                                                                                                                           |
| shardstorage  | char     | 用於此分區的儲存體類型。 下表將討論不同的儲存體類型。                                                                                               |
| shardminvalue | 文字     | 針對附加分散式資料表，此分區中散發資料行的最小值 (包含) 。 針對雜湊分散式資料表，指派給該分區的最小雜湊權杖值 (內含) 。 |
| shardmaxvalue | 文字     | 針對附加分散式資料表，此分區中散發資料行的最大值 (內含) 。 針對雜湊分散式資料表，指派給該分區的最大雜湊 token 值 (內含) 。 |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>分區儲存體類型

Pg dist 分區中的 shardstorage 資料行 \_ \_ 指出用於分區的儲存體類型。 以下是不同分區儲存體類型及其標記法的簡短總覽。

| 儲存類型 | Shardstorage 值 | 描述                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| TABLE        | 不要                | 指出分區儲存屬於一般分散式資料表的資料。         |
| 柱 狀     | 交流                | 指出分區儲存單欄式資料。 分散式 cstore_fdw 資料表所使用的 ()  |
| FOREIGN      | 約翰                | 表示分區會儲存外部資料。 分散式 file_fdw 資料表所使用的 ()     |

### <a name="shard-placement-table"></a>分區放置資料表

Pg \_ dist \_ 放置資料表會追蹤背景工作節點上分區複本的位置。 指派給特定節點之分區的每個複本都稱為分區放置。 此資料表會儲存每個分區放置之健全狀況和位置的相關資訊。

| 名稱        | 類型   | 描述                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | BIGINT | 與此位置相關聯的分區識別碼。 此值會參考 pg_dist_shard 目錄資料表中的 shardid 資料行。             |
| shardstate  | int    | 描述此放置的狀態。 不同的分區狀態會在下一節中討論。                                         |
| shardlength | BIGINT | 針對附加分散式資料表，在背景工作節點上的分區位置大小（以位元組為單位）。 若為雜湊分散式資料表，則為零。            |
| placementid | BIGINT | 每個個別放置的唯一自動產生的識別碼。                                                                           |
| groupid     | int    | 表示使用串流複製模型時，一組主伺服器和零或多個次要伺服器的群組。 |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>分區放置狀態

超大規模 (Citus) 管理每個放置的分區健康情況。 如果放置讓系統處於不一致的狀態，Citus 會自動將它標示為無法使用。 放置狀態會記錄在 shardstate 資料行內的 pg_dist_shard_placement 資料表中。 以下簡要介紹不同的分區放置狀態：

| 狀態名稱 | Shardstate 值 | 描述                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 完成  | 1                | 系統會在中建立新的分區狀態。 處於此狀態的分區位置會視為最新狀態，並用於查詢計劃和執行中。                                                                                                                                                                                                                                                                                 |
| INACTIVE   | 3                | 處於此狀態的分區位置會視為非使用中，因為與相同分區的其他複本不同步。 當這個位置的附加、修改 (插入、更新、刪除) 或 DDL 作業失敗時，就會發生此狀態。 在規劃和執行期間，查詢規劃工具會忽略此狀態的放置。 使用者可以將這些分區中的資料，與已完成的複本同步處理為背景活動。 |
| TO_DELETE  | 4                | 如果 Citus 嘗試卸載分區位置以回應 master_apply_delete_command 呼叫，而失敗，則會將放置移到此狀態。 然後使用者可以將這些分區刪除為後續的背景活動。                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>背景工作節點資料表

Pg \_ dist \_ node 資料表包含叢集中背景工作節點的相關資訊。

| 名稱             | 類型    | 描述                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeid           | int     | 針對個別節點自動產生的識別碼。                                                                                                                                          |
| groupid          | int     | 使用「串流複寫」模型時，用來代表一部主伺服器和零或多個次要伺服器群組的識別碼。 依預設，它會與同值相同。         |
| nodename         | 文字    | 于 postgresql 背景工作節點的主機名稱或 IP 位址。                                                                                                                                     |
| nodeport         | int     | 于 postgresql 背景工作節點正在接聽的埠號碼。                                                                                                                              |
| noderack         | 文字    |  (背景工作節點的選擇性) 機架放置資訊。                                                                                                                                 |
| hasmetadata      | boolean | 保留供內部使用。                                                                                                                                                                 |
| isactive         | boolean | 節點是否正在接受分區放置。                                                                                                                                     |
| noderole         | 文字    | 節點是否為主要或次要                                                                                                                                                 |
| nodecluster      | 文字    | 包含此節點的叢集名稱                                                                                                                                               |
| shouldhaveshards | boolean | 如果為 false，則分區會在重新平衡時移出節點 (清空) ，也不會從新的分散式資料表分區到節點上，除非它們與分區共置。 |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>分散式物件資料表

Citus.pg \_ dist \_ 物件表包含物件的清單，例如在協調器節點上建立並傳播至背景工作節點的類型和函式。 當系統管理員將新的背景工作節點新增至叢集時，超大規模 (Citus) 會自動在新節點上建立分散式物件的複本 (以正確的順序來滿足) 的物件相依性。

| 名稱                        | 類型    | 描述                                          |
|-----------------------------|---------|------------------------------------------------------|
| classid                     | oid     | 分散式物件的類別                      |
| objid                       | oid     | 分散式物件的物件識別碼                  |
| objsubid                    | integer | 分散式物件的物件子識別碼，例如 attnum |
| 類型                        | 文字    | 用於 pg 升級期間的穩定位址部分   |
| object_names                | 文字 []  | 用於 pg 升級期間的穩定位址部分   |
| object_args                 | 文字 []  | 用於 pg 升級期間的穩定位址部分   |
| distribution_argument_index | integer | 僅適用于分散式函數/程式      |
| colocationid                | integer | 僅適用于分散式函數/程式      |

\"穩定位址可 \" 唯一識別特定伺服器以外的物件。 超大規模 (Citus) 在於 postgresql 升級期間，使用以 [pg \_ 識別 \_ 物件 \_ 為 \_ address ( # B3 ](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) 函數所建立的穩定位址來追蹤物件。

以下 \' 是如何 `create_distributed_function()` 將專案加入至資料表的範例 `citus.pg_dist_object` ：

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>共置群組資料表

Pg \_ dist \_ 共置資料表包含哪些資料表 \' 分區應放置在一起或共置的相關[](concepts-hyperscale-colocation.md)資訊。
當兩個數據表位於相同的共置群組時，超大規模 (Citus) 可確保具有相同資料分割值的分區將放置在相同的背景工作節點上。
共置可啟用聯結優化、特定分散式匯總和外鍵支援。 當分區計數、複寫因數和資料分割資料行類型全都符合兩個數據表時，便會推斷分區共置;不過，如有需要，您可以在建立分散式資料表時指定自訂共置群組。

| 名稱                   | 類型 | 描述                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | 此資料列對應的共置群組唯一識別碼。          |
| shardcount             | int  | 此共置群組中所有資料表的分區計數                          |
| replicationfactor      | int  | 此共置群組中所有資料表的複寫因數。                  |
| distributioncolumntype | oid  | 這個共置群組中所有資料表的散發資料行類型。 |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Rebalancer 策略資料表

下表定義 [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) 可以用來判斷分區移動位置的策略。

| 名稱                           | 類型    | 描述                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Rebalance_table_shards 是否應該依預設選擇此策略。 使用 citus_set_default_rebalance_strategy 來更新此資料行             |
| shard_cost_function            | regproc | 成本函式的識別碼，其必須以 Bigint 的形式 shardid，並傳回其成本的概念，例如實類型                                |
| node_capacity_function         | regproc | 容量函式的識別碼，必須將節點識別碼視為 int，然後將其節點容量的概念傳回為 real 類型                          |
| shard_allowed_on_node_function | regproc | 給定 shardid Bigint 和 nodeidarg int 之函式的識別碼，會傳回布林值，指出 Citus 是否可將分區儲存在節點上 |
| default_threshold              | float4  | Deeming 節點太滿或太空的閾值，以決定何時 rebalance_table_shards 應嘗試移動分區                    |
| minimum_threshold              | float4  | 防止 rebalance_table_shards ( # A1 的臨界值引數設定過低的保護措施                                                  |

超大規模 (Citus) 安裝在資料表中隨附下列策略：

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

預設策略會 `by_shard_count` 將每個分區指派為相同的成本。 其效果是在節點之間將分區計數設為均衡。 另一個預先定義的策略 `by_disk_size` 會將成本指派給每個符合其磁片大小（以位元組為單位）的分區，再加上與其共存的分區。 磁片大小的計算方式是使用 `pg_total_relation_size` ，因此它包含索引。 此策略會嘗試在每個節點上達到相同的磁碟空間。 請注意閾值 0.1--它可防止不必要的分區移動因磁碟空間中的顯著差異而造成。

#### <a name="creating-custom-rebalancer-strategies"></a>建立自訂 rebalancer 策略

以下是可在新的分區 rebalancer 策略中使用的函式範例，並使用[citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy)函數在[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table)中註冊。

-   依主機名稱模式設定節點容量例外狀況：

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   依照 [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)所測量的查詢數目，重新平衡移至分區的查詢數目：

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   將節點上的特定分區 (10000) 隔離 (位址 \' 10.0.0.1 \') ：

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>查詢統計資料資料表

超大規模 (Citus) 提供有關 `citus_stat_statements` 如何執行查詢的統計資料。 它 \' 類似于 (，並且可以與于 postgresql 中的 [ [pg \_ stat \_ 語句]](https://www.postgresql.org/docs/current/static/pgstatstatements.html)) 聯結，以追蹤查詢速度的相關統計資料。

此視圖可以追蹤查詢，以在多租使用者應用程式中起始租使用者，這有助於決定何時要進行租使用者隔離。

| 名稱          | 類型   | 描述                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| queryid       | BIGINT | 識別碼 (適用于 pg_stat_statements 聯結)                                    |
| userid        | oid    | 執行查詢的使用者                                                           |
| dbid          | oid    | 協調器的資料庫實例                                                 |
| 查詢         | 文字   | 匿名查詢字串                                                          |
| 遺囑 執行 人      | 文字   | 使用的 Citus 執行程式：自我調整、即時、工作追蹤器、路由器或插入-選取 |
| partition_key | 文字   | 路由器執行的查詢中的散發資料行值，否則為 Null               |
| calls         | BIGINT | 查詢的執行次數                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

結果：

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

警告：

-   統計資料不會複寫，且在資料庫當機 \' 或容錯移轉時，即無法承受
-   追蹤有限的查詢數目（由 `pg_stat_statements.max` GUC (預設值5000設定）) 
-   若要截斷資料表，請使用 `citus_stat_statements_reset()` 函數

### <a name="distributed-query-activity"></a>分散式查詢活動

超大規模 (Citus) 提供特殊的視圖，以監看整個叢集中的查詢和鎖定，包括在內部用來建立分散式查詢結果的分區特定查詢。

-   **citus \_ dist \_ stat \_ 活動**：顯示在所有節點上執行的分散式查詢。 的超集合 `pg_stat_activity` ，在後者的任何地方都可以使用。
-   **citus \_ worker \_ stat \_ 活動**：顯示背景工作的查詢，包括針對個別分區的片段查詢。
-   **citus \_ 鎖定 \_ 等候**：整個叢集中的封鎖查詢。

前兩個視圖包括 [pg \_ stat \_ 活動](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) 的所有資料行，加上起始查詢的工作者主機/埠，以及叢集協調器節點的主機/埠。

例如，請考慮計算分散式資料表中的資料列：

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

我們可以看到查詢出現在 `citus_dist_stat_activity` ：

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

此查詢需要來自所有分區的資訊。 部分資訊是在分區中 `users_table_102038` ，這種情況會儲存在中 `localhost:9700` 。 您可以藉由查看視圖來查看存取分區的查詢 `citus_worker_stat_activity` ：

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query`欄位會顯示要計算的分區所複製的資料。

> [!NOTE]
> 如果路由器查詢 (例如多租使用者應用程式中的單一租使用者，請選取
> * 在不含交易區塊的情況下執行 tenant_id = X ') 的資料表中，主要 \_ 查詢 \_ 主機 \_ 名和主要 \_ 查詢 \_ 主機 \_ 埠資料行在 citus \_ worker \_ stat 活動中會是 Null \_ 。

若要查看 `citus_lock_waits` 運作方式，我們可以手動產生鎖定狀況。 首先，我們要 \' 從協調器設定測試資料表：

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

然後，在協調器上使用兩個會話，我們可以執行這一系列的語句：

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

此 `citus_lock_waits` 視圖會顯示情況。

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

在此範例中，查詢是源自協調器，但 view 也可以列出源自工作者的查詢之間的鎖定 (以超大規模 (Citus) MX 實例) 來執行。

## <a name="next-steps"></a>後續步驟

* 瞭解某些 [超大規模 (Citus) 函數](reference-hyperscale-functions.md) 如何改變系統資料表
* 複習[節點和資料表](concepts-hyperscale-nodes.md)的概念
