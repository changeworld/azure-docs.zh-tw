---
title: SQL 函數–超大規模資料庫 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 超大規模資料庫中的函式 (Citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: eaada1981929cec890ce3c8ca89fe47393730b05
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136804"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>超大規模資料庫中的函式 (Citus) SQL API

本節包含超大規模資料庫 (Citus) 所提供之使用者定義函數的參考資訊。 除了標準 SQL 命令以外，這些功能有助於提供額外的分散式功能來超大規模資料庫 (Citus) 。

> [!NOTE]
>
> 執行舊版 Citus 引擎的超大規模資料庫伺服器群組，可能不會提供下列所有功能。

## <a name="table-and-shard-ddl"></a>資料表和分區 DDL

### <a name="create_distributed_table"></a>建立 \_ 分散式 \_ 資料表

建立 \_ 分散式 \_ 資料表 ( # A1 函數是用來定義分散式資料表，如果它是雜湊分散式資料表，則會建立它的分區。 此函式會採用資料表名稱、散發資料行和選擇性散發方法，並插入適當的中繼資料，以將資料表標示為已散發。 如果未指定散發方法，函式會預設為「雜湊」分佈。 如果資料表是雜湊散發，此函數也會根據分區計數和分區複寫因數設定值來建立背景工作分區。 如果資料表包含任何資料列，則會自動散發至背景工作節點。

此函式會取代主要 \_ 建立 \_ 分散式 \_ 資料表 ( # A1 的使用，後面接著 master \_ create \_ worker \_ 分區 ( # A3。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 需要散發之資料表的名稱。

**散發資料 \_ 行：** 要散發資料表的資料行。

**散發 \_ 類型：** 根據要散發資料表的方式， (選擇性) 方法。 允許的值為 append 或 hash，預設值為 ' hash '。

**共置 \_ with：** (選擇性) 在另一個資料表的共置群組中包含目前的資料表。 根據預設，當資料表由相同類型的資料行散發時，會有相同的分區計數，而且具有相同的複寫因數。 的可能值 `colocate_with` 為 `default` 、 `none` 啟動新的共置群組，或要與該資料表共置之另一個資料表的名稱。   (參閱[資料表共置](concepts-hyperscale-colocation.md)。 ) 

請記住，的預設值 `colocate_with` 會隱含共置。 當資料表相關或將加入時，[共置](concepts-hyperscale-colocation.md)可能是很棒的事。  不過，當兩個數據表無關，但在其散發資料行中使用相同的 datatype 時，不小心共置它們會在[分區重新平衡](howto-hyperscale-scaling.md#rebalance-shards)期間降低效能。  資料表分區會在 cascade 中不必要地一起移動 \" 。\"

如果新的分散式資料表與其他資料表無關，最好指定 `colocate_with => 'none'` 。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

這個範例會通知資料庫，[存放庫識別碼] 資料行上的 [github \_ 事件] 資料表應該以雜湊散發 \_ 。

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>建立 \_ 參考 \_ 資料表

建立 \_ 參考 \_ 資料表 ( # A1 函數可用來定義小型參考或維度資料表。 此函式會採用資料表名稱，並建立只有一個分區的分散式資料表，並複寫到每個背景工作節點。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 需要散發的小型維度或參考資料表的名稱。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

這個範例會通知資料庫，全國資料表應該定義為參考資料表

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>升級 \_ 至 \_ 參考 \_ 資料表

升級 \_ 至 \_ 參考 \_ 資料表 ( # A1 函式會接受分區計數為一的現有分散式資料表，並將其升級為可辨識的參考資料表。 呼叫此函式之後，資料表就如同使用[create_reference_table](#create_reference_table)建立的。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** (具有分區 count = 1) 的分散式資料表名稱，將會以參考資料表的形式散發。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

這個範例會通知資料庫，全國資料表應該定義為參考資料表

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>標示已共置的 \_ 資料表 \_

 ( # A1 函式共置的標記 \_ 資料表 \_ 會採用分散式資料表 (來源) ，以及 (目標) 的其他專案清單，並將目標放在與來源相同的共置群組中。 如果來源尚未位於群組中，此函式會建立一個，並將來源和目標指派給它。

共置資料表應該透過 create_distributed_table 的參數在資料表散發時完成 `colocate_with` ，但[create_distributed_table](#create_distributed_table) `mark_tables_colocated` 稍後可以視需要加以處理。

#### <a name="arguments"></a>引數

**源 \_ 資料表 \_ 名稱：** 要指派目標的共置群組要符合的分散式資料表名稱。

**目標 \_ 資料表 \_ 名稱：** 分散式目標資料表的名稱陣列，必須為非空白。 這些分散式資料表必須符合中的來源資料表：

> -   distribution 方法
> -   散發資料行類型
> -   複寫類型
> -   分區計數

如果上述任何一項都不適用，超大規模資料庫 (Citus) 會引發錯誤。 例如，嘗試共置資料表 `apples` ，而且 `oranges` 其散發資料行類型不同會導致：

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

這個範例會 `products` 將和放 `line_items` 在與相同的共置群組中 `stores` 。 此範例假設這些資料表全都散發在具有相符類型的資料行上，很可能是 \" 存放區識別碼。\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>建立 \_ 分散式函式 \_

將函式從協調器節點傳播至背景工作，並將其標示為分散式執行。 在協調器上呼叫分散式函式時，超大規模資料庫 (Citus) 會使用 \" 散發引數的值 \" 來挑選要執行函式的背景工作節點。 在背景工作角色上執行函式會增加平行處理原則，並可讓程式碼更接近分區中的資料，以降低延遲。

在分散式函式執行期間，Postgres 搜尋路徑不會從協調器傳播到背景工作，因此分散式函式程式碼應完整限定資料庫物件的名稱。 也不會對使用者顯示函式發出的通知。

#### <a name="arguments"></a>引數

**函數 \_ 名稱：** 要散發之函式的名稱。 名稱必須在括弧中包含函式的參數類型，因為于 postgresql 中的多個函式可以有相同的名稱。 例如， `'foo(int)'` 與不同 `'foo(int, text)'` 。

**散發 \_ 參數 \_ 名稱：** (選擇性) 要用來散發的引數名稱。 為了方便起見 (或如果函式引數沒有) 的名稱，則允許位置預留位置，例如 `'$1'` 。 如果未指定此參數，則 `function_name` 只會在背景工作角色上建立名為的函式。 如果未來加入背景工作節點，則也會自動在此建立函數。

**共置 \_ with：** (選擇性) 當分散式函式讀取或寫入分散式資料表 (或更廣泛地說，共置群組) 時，請務必使用參數來命名該資料表 `colocate_with` 。 然後，每個函式呼叫都會在包含相關分區的背景工作節點上執行。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>中繼資料/設定資訊

### <a name="master_get_table_metadata"></a>主要 \_ 取得 \_ 資料表 \_ 中繼資料

主要 \_ 取得 \_ 資料表 \_ 中繼資料 ( # A1 函數，可用來傳回分散式資料表的散發相關中繼資料。 此中繼資料包括資料表的關聯識別碼、儲存體類型、散發方法、散發資料行、複寫計數、最大分區大小和分區放置原則。 在幕後，此函式會查詢超大規模資料庫 (Citus) 中繼資料資料表來取得所需的資訊，並將其串連為元組，然後再將它傳回給使用者。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 您要為其提取中繼資料之分散式資料表的名稱。

#### <a name="return-value"></a>傳回值

包含下列資訊的元組：

**logical \_ relid：** 分散式資料表的 Oid。 它會參考 pg \_ 類別系統目錄資料表中的 relfilenode 資料行。

**部分 \_ 儲存 \_ 類型：** 用於資料表的儲存體類型。 可能不是 ' (標準資料表) 、' f ' (外部資料表) 或 ' c ' (單欄式資料表) 。

**part \_ 方法：** 用於資料表的散發方法。 可能是 ' a ' (附加) 或 ' h ' (雜湊) 。

**部分索引 \_ 鍵：** 資料表的散發資料行。

**元件 \_ 複本 \_ 計數：** 目前的分區複寫計數。

**部分 \_ \_ 大小上限：** 目前的最大分區大小（以位元組為單位）。

**部分 \_ 放置 \_ 原則：** 分區放置原則，用於放置資料表的分區。 可能是 1 (本機節點優先) 或 2 (迴圈配置資源) 。

#### <a name="example"></a>範例

下列範例會提取並顯示 github 事件資料表的資料表中繼資料 \_ 。

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>取得 \_ \_ 散發資料 \_ \_ 行的分區識別碼 \_

超大規模資料庫 (Citus) 會根據資料列的散發資料行值和資料表的散發方法，將分散式資料表的每個資料列指派給分區。 在大部分的情況下，精確的對應是資料庫管理員可以忽略的低層級詳細資料。 不過，若要判斷資料列的分區，請手動進行資料庫維護工作，或只是為了滿足好奇心，這是很有用的作法。 函 `get_shard_id_for_distribution_column` 式會提供雜湊和範圍分散式資料表以及參考資料表的這種資訊。 不適用於附加散發。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 分散式資料表。

**散發 \_ 值：** 散發資料行的值。

#### <a name="return-value"></a>傳回值

分區 ID 超大規模資料庫 (Citus) 會與給定資料表的散發資料行值產生關聯。

#### <a name="example"></a>範例

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>資料行 \_ 到資料 \_ 行 \_ 名稱

將 `partkey` 的資料行轉譯 `pg_dist_partition` 為文字資料行名稱。 翻譯有助於判斷分散式資料表的散發資料行。

如需更詳細的討論，請參閱[選擇散發資料行](concepts-hyperscale-choose-distribution-column.md)。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 分散式資料表。

資料**行 \_ var \_ text：** `partkey` 資料表中的值 `pg_dist_partition` 。

#### <a name="return-value"></a>傳回值

`table_name`的散發資料行名稱。

#### <a name="example"></a>範例

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

輸出：

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus \_ 關聯性 \_ 大小

取得指定之分散式資料表的所有分區所使用的磁碟空間。
磁碟空間包含 \" 主要分叉的大小， \" 但會排除分區的可見度對應和可用空間對應。

#### <a name="arguments"></a>引數

**logicalrelid：** 分散式資料表的名稱。

#### <a name="return-value"></a>傳回值

以位元組為 Bigint 的大小。

#### <a name="example"></a>範例

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ 資料表 \_ 大小

取得指定之分散式資料表的所有分區所使用的磁碟空間，但不包括索引 (但包括快顯通知、可用空間對應，以及可見度對應) 。

#### <a name="arguments"></a>引數

**logicalrelid：** 分散式資料表的名稱。

#### <a name="return-value"></a>傳回值

以位元組為 Bigint 的大小。

#### <a name="example"></a>範例

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ \_ 關聯 \_ 大小總計

取得指定之分散式資料表的所有分區所使用的總磁碟空間，包括所有索引和快顯通知資料。

#### <a name="arguments"></a>引數

**logicalrelid：** 分散式資料表的名稱。

#### <a name="return-value"></a>傳回值

以位元組為 Bigint 的大小。

#### <a name="example"></a>範例

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>citus \_ stat \_ 語句 \_ 重設

從[citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table)中移除所有資料列。
此函式可獨立于之外運作 `pg_stat_statements_reset()` 。 若要重設所有的統計資料，請呼叫這兩個函數。

#### <a name="arguments"></a>引數

N/A

#### <a name="return-value"></a>傳回值

None

## <a name="server-group-management-and-repair"></a>伺服器群組管理和修復

### <a name="master_copy_shard_placement"></a>主要 \_ 複製 \_ 分區 \_ 位置

如果在修改命令或 DDL 作業期間無法更新分區位置，則會將它標示為非作用中。 \_ \_ \_ 然後可以呼叫主要複製分區放置函式，以使用狀況良好放置中的資料修復非使用中的分區位置。

若要修復分區，函式會先卸載狀況不良的分區位置，並使用協調器上的架構重新建立它。 建立分區放置之後，函式會從狀況良好的位置複製資料，並更新中繼資料，以將新的分區位置標示為狀況良好。 此函式可確保分區會受到保護，避免在修復期間進行任何並行的修改。

#### <a name="arguments"></a>引數

**分區 \_ id：** 要修復的分區識別碼。

**來源 \_ 節點 \_ 名稱：** (\" 來源節點) 存在狀況良好的分區位置之節點的 DNS 名稱 \" 。

**來源 \_ 節點 \_ 埠：** 資料庫伺服器正在接聽之來源背景工作角色節點上的埠。

**目標 \_ 節點 \_ 名稱：** 無效分區位置所在節點的 DNS 名稱 (\" 目標 \" 節點) 。

**目標 \_ 節點 \_ 埠：** 資料庫伺服器正在接聽之目標背景工作節點上的埠。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

下列範例會修復分區12345的非使用中分區位置，這存在於在埠5432上「不正確的主機」上執行的資料庫伺服器上 \_ 。 若要修復此問題，它會使用在埠上「良好主機」上執行之伺服器上的狀況良好分區位置所提供的資料 \_
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>主要 \_ 移動 \_ 分區 \_ 位置

此函式) 會將指定的分區 (和分區與其共存，從一個節點移至另一個節點。 通常會在分區重新平衡期間間接使用，而不是由資料庫管理員直接呼叫。

有兩種方式可以移動資料：封鎖或非封鎖。 封鎖方法表示在移動期間，所有對分區的修改都會暫停。
第二種方式是避免封鎖分區寫入，這會依賴 Postgres 10 邏輯複寫。

成功移動作業之後，來源節點中的分區就會被刪除。 如果移動在任何時間點失敗，此函式會擲回錯誤，並讓來源和目標節點保持不變。

#### <a name="arguments"></a>引數

**分區 \_ id：** 要移動之分區的識別碼。

**來源 \_ 節點 \_ 名稱：** (\" 來源節點) 存在狀況良好的分區位置之節點的 DNS 名稱 \" 。

**來源 \_ 節點 \_ 埠：** 資料庫伺服器正在接聽之來源背景工作角色節點上的埠。

**目標 \_ 節點 \_ 名稱：** 無效分區位置所在節點的 DNS 名稱 (\" 目標 \" 節點) 。

**目標 \_ 節點 \_ 埠：** 資料庫伺服器正在接聽之目標背景工作節點上的埠。

**分區 \_ 傳輸 \_ 模式：** (選擇性) 指定複寫的方法，是否要使用於 postgresql 邏輯複寫或跨背景工作複製命令。 可能的值包括：

> -   `auto`：如果可以進行邏輯複寫，則需要複本識別，否則請使用舊版行為 (例如分區 repair、于 postgresql 9.6) 。 這是預設值。
> -   `force_logical`：即使資料表沒有複本識別，還是使用邏輯複寫。 在複寫期間，資料表的任何並行更新/刪除語句都會失敗。
> -   `block_writes`：針對缺少主要金鑰或複本識別的資料表，使用複製 (封鎖寫入) 。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>重新平衡 \_ 資料表 \_ 分區

重新平衡 \_ 資料表 \_ 分區 ( # A1 函式會移動指定資料表的分區，使其平均分散于背景工作之間。 函式會先計算所需進行的移動清單，以確保伺服器群組在指定的臨界值內達到平衡。 然後，它會將分區位置逐一從來源節點移至目的地節點，並更新對應的分區中繼資料以反映移動。

在判斷分區是否平均分佈時，會為每個分區指派成本 \" 。 \"根據預設，每個分區都有相同的成本 (1) 的值，因此散發以讓背景工作角色的成本變高，與 equalizing 每個工作者的分區數目相同。 分區計數會呼叫常數成本 \" 策略 \_ \_ \" ，而且這是預設的重新平衡策略。

預設策略適用于下列情況：

*  分區的大小大致相同
*  分區大約會取得相同的流量量
*  背景工作節點都是相同的大小/類型
*  分區尚未釘選到特定背景工作

如果其中任何一個假設不存在，則預設的重新平衡可能會導致不正確的計畫。 在此情況下，您可以使用參數來自訂策略 `rebalance_strategy` 。

建議您在執行重新平衡資料表分區之前先呼叫[get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) \_ \_ ，以查看並驗證要執行的動作。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** (選擇性) 需要重新平衡其分區的資料表名稱。 如果是 Null，則重新平衡所有現有的共置群組。

**閾值：** (選擇性) 介於0.0 和1.0 之間的浮點數，表示節點使用率與平均使用率的最大差異。 例如，指定0.1 會導致分區 rebalancer 嘗試平衡所有節點，以保留相同數目的分區±10%。
具體而言，分區 rebalancer 會嘗試將所有背景工作節點的使用率，平均 (1-閾值) \* 平均 \_ 使用率。 \. (1
+ 臨界值) \* 平均 \_ 使用率範圍。

**最大 \_ 分區 \_ 移動：** (選擇性) 要移動的分區數目上限。

**排除的 \_ 分區 \_ 清單：** (在重新平衡作業期間不應移動之分區的選擇性) 識別碼。

**分區 \_ 傳輸 \_ 模式：** (選擇性) 指定複寫的方法，是否要使用於 postgresql 邏輯複寫或跨背景工作複製命令。 可能的值包括：

> -   `auto`：如果可以進行邏輯複寫，則需要複本識別，否則請使用舊版行為 (例如分區 repair、于 postgresql 9.6) 。 這是預設值。
> -   `force_logical`：即使資料表沒有複本識別，還是使用邏輯複寫。 在複寫期間，資料表的任何並行更新/刪除語句都會失敗。
> -   `block_writes`：針對缺少主要金鑰或複本識別的資料表，使用複製 (封鎖寫入) 。

** \_ 僅清空：** (選擇性) 若為 true，請將分區移出 `shouldhaveshards` 在[pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中設定為 false 的背景工作節點; 不移動任何其他分區。

重新**平衡 \_ 策略：** (選擇性) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)中的策略名稱。
如果省略這個引數，此函式會選擇預設策略，如下表所示。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

下列範例會嘗試重新平衡 \_ 預設閾值內的 github 事件資料表分區。

```postgresql
SELECT rebalance_table_shards('github_events');
```

此範例使用方式會嘗試重新平衡 github \_ 事件資料表，而不需要移動識別碼為1和2的分區。

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>取得重新 \_ 平衡 \_ 資料表 \_ 分區 \_ 計畫

輸出[rebalance_table_shards](#rebalance_table_shards)規劃的分區移動，而不執行它們。
雖然不太可能，但取得重新 \_ 平衡 \_ 資料表 \_ 分區 \_ 計畫所輸出的計畫，可能會 \_ \_ 與使用相同引數的重新平衡資料表分區呼叫相比，有些許不同。 它們不會同時執行，因此關於伺服器群組的事實（ \- 例如，磁碟空間） \- 可能會因呼叫而有所不同。

#### <a name="arguments"></a>引數

重新平衡資料表分區的相同引數 \_ \_ ：關聯、臨界值、最大 \_ 分區 \_ 移動、排除的 \_ 分區 \_ 清單和清空 \_ 。 如需引數的意義，請參閱該函式的檔。

#### <a name="return-value"></a>傳回值

包含下列資料行的元組：

-   **資料表 \_ 名稱**：要移動其分區的資料表
-   **shardid**：有問題的分區
-   **分區 \_ 大小**：以位元組為單位的大小
-   未**通過：來源**節點的主機名稱
-   **sourceport**：來源節點的埠
-   **targetname**：目的地節點的主機名稱
-   **targetport**：目的地節點的埠

### <a name="get_rebalance_progress"></a>取得重新 \_ 平衡 \_ 進度

分區重新平衡開始之後，函式會 `get_rebalance_progress()` 列出每個相關分區的進度。 它會監視由規劃和執行的移動 `rebalance_table_shards()` 。

#### <a name="arguments"></a>引數

N/A

#### <a name="return-value"></a>傳回值

包含下列資料行的元組：

-   **sessionid**：重新平衡監視的 Postgres PID
-   **資料表 \_ 名稱**：要移動其分區的資料表
-   **shardid**：有問題的分區
-   **分區 \_ 大小**：以位元組為單位的大小
-   未**通過：來源**節點的主機名稱
-   **sourceport**：來源節點的埠
-   **targetname**：目的地節點的主機名稱
-   **targetport**：目的地節點的埠
-   **進度**： 0 = 等待移動;1 = 移動;2 = 完成

#### <a name="example"></a>範例

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ 新增重新 \_ 平衡 \_ 策略

將資料列附加至[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) 。

#### <a name="arguments"></a>引數

如需這些引數的詳細資訊，請參閱中的對應資料行值 `pg_dist_rebalance_strategy` 。

**名稱：** 新策略的識別碼

**分區 \_ cost \_ 函數：** 識別用來判斷 \" \" 每個分區成本的函數

**節點 \_ 容量 \_ 函數：** 識別用來測量節點容量的函式

**node 函式 \_ 上允許的分區 \_ \_ \_ ：** 識別判斷哪些分區可放置在哪些節點上的函式

**預設 \_ 臨界值：** 浮動點臨界值，可微調節點間累計分區成本的精確程度

**最小 \_ 閾值：** (選擇性) 保護措施資料行，保存重新平衡 \_ 資料表 \_ 分區 ( # A3 的閾值引數所允許的最小值。 其預設值為0

#### <a name="return-value"></a>傳回值

N/A

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ 設定 \_ 預設的重新 \_ 平衡 \_ 策略

更新[pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)資料表，並將其引數所命名的策略變更為重新平衡分區時所選擇的預設值。

#### <a name="arguments"></a>引數

**名稱：** pg \_ dist 重新 \_ 平衡 \_ 策略中的策略名稱

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ 遠端 \_ 連接 \_ 統計資料

Citus \_ 遠端連線 \_ 統計資料 ( # A1 函式會 \_ 顯示每個遠端節點的使用中連接數目。

#### <a name="arguments"></a>引數

N/A

#### <a name="example"></a>範例

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>主要 \_ 清空 \_ 節點

主要 \_ 清空 \_ 節點 ( # A1 函式會將分區從指定的節點移至 `shouldhaveshards` [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table)中設定為 true 的其他節點。 在移除伺服器群組中的節點，並關閉節點的實體伺服器之前，請先呼叫函數。

#### <a name="arguments"></a>引數

**nodename：** 要清空之節點的主機名稱名稱。

**nodeport：** 要清空之節點的埠號碼。

**分區 \_ 傳輸 \_ 模式：** (選擇性) 指定複寫的方法，是否要使用於 postgresql 邏輯複寫或跨背景工作複製命令。 可能的值包括：

> -   `auto`：如果可以進行邏輯複寫，則需要複本識別，否則請使用舊版行為 (例如分區 repair、于 postgresql 9.6) 。 這是預設值。
> -   `force_logical`：即使資料表沒有複本識別，還是使用邏輯複寫。 在複寫期間，資料表的任何並行更新/刪除語句都會失敗。
> -   `block_writes`：針對缺少主要金鑰或複本識別的資料表，使用複製 (封鎖寫入) 。

重新**平衡 \_ 策略：** (選擇性) [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table)中的策略名稱。
如果省略這個引數，此函式會選擇預設策略，如下表所示。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="example"></a>範例

以下是在標準于 postgresql 埠) 上移除單一節點 (例如 ' 10.0.0.1 ' 的一般步驟：

1.  清空節點。

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  等待命令完成

3.  移除節點

清空多個節點時，建議改用[rebalance_table_shards](#rebalance_table_shards) 。 這麼做可讓超大規模資料庫 (Citus) 進行前計畫，並將分區的最小次數移動。

1.  針對您想要移除的每個節點執行：

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  使用[rebalance_table_shards](#rebalance_table_shards)一次清空所有專案

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  等到清空重新平衡完成

4.  移除節點

### <a name="replicate_table_shards"></a>複寫 \_ 資料表 \_ 分區

複寫 \_ 資料表 \_ 分區 ( # A1 函式會複寫給定資料表的複寫後分區。 函式會先計算複寫的分區和位置的清單，以供從中提取複寫。 然後，函式會將這些分區複製並更新對應的分區中繼資料，以反映該複本。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 需要複寫其分區之資料表的名稱。

**分區 \_ 複寫 \_ 因素：** (選擇性) 要達到每個分區的所需複寫因素。

**最大 \_ 分區 \_ 複本：** (選擇性) 要複製的最大分區數目，以達到所需的複寫因數。

**排除的 \_ 分區 \_ 清單：** (在複寫作業期間不應複製之分區的選擇性) 識別碼。

#### <a name="return-value"></a>傳回值

N/A

#### <a name="examples"></a>範例

下列範例會嘗試將 github 事件資料表的分區複寫 \_ 至分區複寫 \_ \_ 因數。

```postgresql
SELECT replicate_table_shards('github_events');
```

這個範例會嘗試將 github \_ 事件資料表的分區帶到所需的複寫因素，最多10個分區複本。 Rebalancer 最多會複製10個分區，以嘗試達到所需的複寫因數。

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>\_將租使用者隔離 \_ 到 \_ 新的 \_ 分區

此函式會建立新的分區，以保存散發資料行中具有特定單一值的資料列。 這特別適用于多租使用者超大規模資料庫 (Citus) 使用案例，其中大型租使用者可以單獨放在自己的分區，最後是其本身的實體節點。

#### <a name="arguments"></a>引數

**資料表 \_ 名稱：** 要取得新分區的資料表名稱。

**租使用者 \_ 識別碼：** 將指派給新分區之散發資料行的值。

**cascade \_ 選項：** 當設定為時， \" (選擇性) CASCADE， \" 也會將分區與目前資料表的[共置群組](concepts-hyperscale-colocation.md)中的所有資料表隔離。

#### <a name="return-value"></a>傳回值

**分區 \_ id：** 此函數會傳回指派給新建立之分區的唯一識別碼。

#### <a name="examples"></a>範例

建立新的分區以保存租使用者135的 lineitems：

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>後續步驟

* 本文中的許多函數會修改系統[中繼資料資料表](reference-hyperscale-metadata.md)
* [伺服器參數](reference-hyperscale-parameters.md)自訂某些函式的行為
