---
title: 修改分散式資料表-超大規模 (Citus) -適用於 PostgreSQL 的 Azure 資料庫
description: 用來建立和修改分散式資料表的 SQL 命令-超大規模 (Citus) 使用 Azure 入口網站
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 8/10/2020
ms.openlocfilehash: cf9f9ca5b8690a38c6e5aa6f519378c0a2e3a4f2
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026432"
---
# <a name="distribute-and-modify-tables"></a>散發和修改資料表

## <a name="distributing-tables"></a>散發資料表

若要建立分散式資料表，您必須先定義資料表架構。 若要這樣做，您可以使用 [CREATE TABLE](http://www.postgresql.org/docs/current/static/sql-createtable.html) 語句定義資料表，就像使用一般于 postgresql 資料表一樣。

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    actor jsonb,
    org jsonb,
    created_at timestamp
);
```

接下來，您可以使用建立 \_ 分散式 \_ 資料表 ( # A1 函數來指定資料表散發資料行，並建立背景工作分區。

```sql
SELECT create_distributed_table('github_events', 'repo_id');
```

函式呼叫會通知超大規模 (Citus) \_ 應該藉由雜湊資料行值) ，將 github 事件資料表散發到存放庫 \_ 識別碼資料行 (。 此函數也會使用 citus. 分區 \_ count 和 citus. 分區 \_ replication \_ 因數設定值，在背景工作節點上建立分區。

它會建立 total of citus. 分區 \_ count of 分區，其中每個分區都擁有一部分的雜湊空間，並根據預設 citus. 分區複寫因數設定 \_ 值進行複寫 \_ 。 在背景工作角色上建立的分區複本與協調器上的資料表具有相同的資料表架構、索引和條件約束定義。 建立複本之後，此函式會將所有分散式中繼資料儲存在協調器上。

每個建立的分區都會被指派唯一的分區識別碼，而且其所有複本都有相同的分區識別碼。 分區會在背景工作節點上表示為名為 tablename shardid 的一般于 postgresql 資料表 \' \_ \' ，其中 tablename 是分散式資料表的名稱，而分區識別碼是指派的唯一識別碼。 您可以連接到背景工作 postgres 實例，以在個別分區上查看或執行命令。

您現在已準備好將資料插入分散式資料表中，並在其上執行查詢。 您也可以在 [資料表和分區 DDL](reference-hyperscale-functions.md#table-and-shard-ddl) 參考中深入瞭解本節中使用的 UDF。

### <a name="reference-tables"></a>參考資料表

上述方法會將資料表分散至多個水準分區。  另一個可能的原因是將資料表散發成單一分區，並將分區複寫到每個背景工作節點。 以這種方式散發的資料表稱為 *參考資料表。* 它們是用來儲存需要由叢集中的多個節點經常存取的資料。

參考資料表的常見候選項目包括：

-   需要聯結較大型分散式資料表的較小資料表。
-   多租使用者應用程式中缺少租使用者識別碼資料行或 \' 與租使用者相關聯的資料表。  (或在遷移期間，即使是與租使用者相關聯的某些資料表也是一樣 ) 
-   需要跨多個資料行的唯一條件約束，且夠小的資料表。

例如，假設多租使用者電子商務網站必須計算其任何商店中交易的銷售稅。 稅務資訊不是 \' 任何租使用者特定的。 將它放在共用資料表中，是合理的。 以美國為中心的參考資料表看起來可能像這樣：

```postgresql
-- a reference table

CREATE TABLE states (
  code char(2) PRIMARY KEY,
  full_name text NOT NULL,
  general_sales_tax numeric(4,3)
);

-- distribute it to all workers

SELECT create_reference_table('states');
```

現在的查詢（例如計算購物車的稅金）可以聯結 `states` 資料表，而不會有網路額外負荷，而且可以將外鍵加入至狀態碼以進行更好的驗證。

除了將資料表散發為單一複寫分區， `create_reference_table` UDF 會將它標示為超大規模 (Citus) 中繼資料資料表中的參考資料表。 超大規模 (Citus) 會自動執行兩階段認可 ([2pc](https://en.wikipedia.org/wiki/Two-phase_commit_protocol)) ，以針對以這種方式標記的資料表進行修改，以提供強大的一致性保證。

如果您有一個具有分區計數的分散式資料表，可以將它升級為可辨識的參考資料表，如下所示：

```postgresql
SELECT upgrade_to_reference_table('table_name');
```

如需使用參考資料表的另一個範例，請參閱 [多租使用者資料庫教學](tutorial-design-database-hyperscale-multi-tenant.md)課程。

### <a name="distributing-coordinator-data"></a>散發協調器資料

如果現有的于 postgresql 資料庫已轉換成超大規模 (Citus) 叢集的協調器節點，則其資料表中的資料可以有效率地散發，而且應用程式的最短中斷。

稍 `create_distributed_table` 早所述的函式適用于空白和非空白的資料表，而後者則會自動在整個叢集中散發資料表資料列。 您將知道它是否會根據訊息是否複製資料，請 \" 注意：從本機資料表 \. \" 複製資料。例如：

```postgresql
CREATE TABLE series AS SELECT i FROM generate_series(1,1000000) i;
SELECT create_distributed_table('series', 'i');
NOTICE:  Copying data from local table...
 create_distributed_table
 --------------------------

 (1 row)
```

資料表上的寫入會在資料移轉時遭到封鎖，而暫止的寫入則會在函式認可之後，當作分散式查詢來處理。  (如果函式失敗，則查詢會再次變成本機。 ) 讀取可以正常地繼續，而且在函式認可之後，就會變成分散式查詢。

當散發資料表 A 和 B，其中 A 的外鍵為 B 時，請先散發索引鍵目的地表 B。 以錯誤的循序執行，將會導致錯誤：

```
ERROR:  cannot create foreign key constraint
DETAIL:  Referenced table must be a distributed table or a reference table.
```

如果無法以正確的順序散發，請卸載外鍵、散發資料表，然後重新建立外鍵。

從外部資料庫（例如從 Amazon RDS 到超大規模 (Citus) Cloud）遷移資料時，請先建立超大規模 (Citus) 分散式資料表 `create_distributed_table` ，然後將資料複製到資料表中。
複製到分散式資料表可避免協調器節點上的空間不足。

## <a name="colocating-tables"></a>共置資料表

共置意指將相關資訊保留在同一部電腦上。 它會啟用有效率的查詢，同時利用整個資料集的水準擴充性。 如需詳細資訊，請參閱 [共置](concepts-hyperscale-colocation.md)。

資料表會在群組中共存。 若要手動控制資料表的共置群組指派，請使用的選擇性 `colocate_with` 參數 `create_distributed_table` 。 如果您不 \' 在意資料表的共置， \' 請省略此參數。 它會預設為值，此值會將 `'default'` 資料表與任何其他預設共置資料表（具有相同的散發資料行類型、分區計數和複寫因數）進行分組。 如果您想要中斷或更新此隱含共置，您可以使用 `update_distributed_table_colocation()` 。

```postgresql
-- these tables are implicitly co-located by using the same
-- distribution column type and shard count with the default
-- co-location group

SELECT create_distributed_table('A', 'some_int_col');
SELECT create_distributed_table('B', 'other_int_col');
```

當新的資料表與其他資料表不相關，也就是隱含共置群組時，請指定 `colocated_with => 'none'` 。

```postgresql
-- not co-located with other tables

SELECT create_distributed_table('A', 'foo', colocate_with => 'none');
```

將不相關的資料表分割成自己的共置群組，將可改善 [分區重新平衡](howto-hyperscale-scale-rebalance.md) 效能，因為相同群組中的分區必須一起移動。

當資料表確實相關時 (例如，當資料表聯結) 時，明確地共置這些資料表可能有意義。 適當共置的提升比任何重新平衡額外負荷更重要。

若要明確共置多個資料表，請散發一個資料表，然後將其他資料表放入其共置群組中。 例如：

```postgresql
-- distribute stores
SELECT create_distributed_table('stores', 'store_id');

-- add to the same group as stores
SELECT create_distributed_table('orders', 'store_id', colocate_with => 'stores');
SELECT create_distributed_table('products', 'store_id', colocate_with => 'stores');
```

共置群組的相關資訊會儲存在 [pg_dist_colocation](reference-hyperscale-metadata.md#colocation-group-table) 資料表中， [pg_dist_partition](reference-hyperscale-metadata.md#partition-table) 會顯示哪些資料表是指派給哪些群組。

## <a name="dropping-tables"></a>卸載資料表

您可以使用 [標準于 postgresql DROP TABLE] 命令來移除分散式資料表。 如同一般資料表，DROP TABLE 會移除任何存在於目標資料表的索引、規則、觸發程式和條件約束。 此外，它也會將分區放置在背景工作節點上，並清除其中繼資料。

```sql
DROP TABLE github_events;
```

## <a name="modifying-tables"></a>修改資料表

超大規模 (Citus) 會自動傳播許多種類的 DDL 語句。
修改協調器節點上的分散式資料表也會更新背景工作角色上的分區。 其他 DDL 語句需要手動傳播，而某些其他 DDL 語句則會被禁止，例如任何會修改散發資料行的專案。
嘗試執行不符合自動傳播資格的 DDL 將會引發錯誤，並讓協調器節點上的資料表保持不變。

以下是所傳播之 DDL 語句類別的參考。
您可以使用[configuration 參數](reference-hyperscale-parameters.md#citusenable_ddl_propagation-boolean)來啟用或停用自動傳播

### <a name="addingmodifying-columns"></a>新增/修改資料行

超大規模 (Citus) 會自動傳播大部分的 [ALTER TABLE](https://www.postgresql.org/docs/current/static/ddl-alter.html) 命令。 新增資料行或變更其預設值的運作方式，就像在單一電腦于 postgresql 資料庫中一樣：

```postgresql
-- Adding a column

ALTER TABLE products ADD COLUMN description text;

-- Changing default value

ALTER TABLE products ALTER COLUMN price SET DEFAULT 7.77;
```

現有資料行的重大變更（例如重新命名或變更其資料類型）也很有問題。 但是，無法改變散發資料 [行](concepts-hyperscale-nodes.md#distribution-column) 的資料類型。
此資料行會決定資料表資料如何透過超大規模 (Citus) 叢集來散發，而修改其資料類型則需要移動資料。

嘗試這麼做會導致錯誤：

```postgres
-- assumining store_id is the distribution column
-- for products, and that it has type integer

ALTER TABLE products
ALTER COLUMN store_id TYPE text;

/*
ERROR:  XX000: cannot execute ALTER TABLE command involving partition column
LOCATION:  ErrorIfUnsupportedAlterTableStmt, multi_utility.c:2150
*/
```

### <a name="addingremoving-constraints"></a>新增/移除條件約束

使用超大規模 (Citus) 可讓您繼續享受關係資料庫的安全性，包括資料庫條件約束 ([查看于 postgresql 檔](https://www.postgresql.org/docs/current/static/ddl-constraints.html)) 。
由於分散式系統的本質，超大規模 (Citus) 不會交互參考唯一性條件約束或背景工作節點之間的參考完整性。

若要在共置的分散式資料表之間設定外鍵，請一律將散發資料行包含在索引鍵中。 包含散發資料行可能會涉及組成按鍵的組合。

在這些情況下，可能會建立外鍵：

-   在兩個本機 (非分散式) 資料表之間
-   在兩個參考資料表之間
-   當索引鍵包含散發資料行時 [，兩個](concepts-hyperscale-colocation.md) 共置的分散式資料表之間，或
-   作為[參考參考資料表](concepts-hyperscale-nodes.md#type-2-reference-tables)的分散式資料表

不支援參考資料表至分散式資料表的外鍵。

> [!NOTE]
>
> 主鍵和唯一性條件約束必須包含散發資料行。 將它們新增至非散發資料行將會產生錯誤

此範例說明如何在分散式資料表上建立主鍵和外鍵：

```postgresql
--
-- Adding a primary key
-- --------------------

-- We'll distribute these tables on the account_id. The ads and clicks
-- tables must use compound keys that include account_id.

ALTER TABLE accounts ADD PRIMARY KEY (id);
ALTER TABLE ads ADD PRIMARY KEY (account_id, id);
ALTER TABLE clicks ADD PRIMARY KEY (account_id, id);

-- Next distribute the tables

SELECT create_distributed_table('accounts', 'id');
SELECT create_distributed_table('ads',      'account_id');
SELECT create_distributed_table('clicks',   'account_id');

--
-- Adding foreign keys
-- -------------------

-- Note that this can happen before or after distribution, as long as
-- there exists a uniqueness constraint on the target column(s) which
-- can only be enforced before distribution.

ALTER TABLE ads ADD CONSTRAINT ads_account_fk
  FOREIGN KEY (account_id) REFERENCES accounts (id);
ALTER TABLE clicks ADD CONSTRAINT clicks_ad_fk
  FOREIGN KEY (account_id, ad_id) REFERENCES ads (account_id, id);
```

同樣地，請將散發資料行包含在唯一性條件約束中：

```postgresql
-- Suppose we want every ad to use a unique image. Notice we can
-- enforce it only per account when we distribute by account id.

ALTER TABLE ads ADD CONSTRAINT ads_unique_image
  UNIQUE (account_id, image_url);
```

Not-null 條件約束可以套用至任何資料行 (分佈，或不) ，因為它們不需要在背景工作之間進行查閱。

```postgresql
ALTER TABLE ads ALTER COLUMN image_url SET NOT NULL;
```

### <a name="using-not-valid-constraints"></a>使用不正確條件約束

在某些情況下，強制執行新資料列的條件約束會很有用，但允許現有不符合規範的資料列保持不變。 超大規模 (Citus) 對檢查條件約束和外鍵支援這項功能，但使用於 postgresql \' s \" 不是有效的 \" 條件約束指定。

例如，請考慮將使用者設定檔儲存在 [參考資料表](concepts-hyperscale-nodes.md#type-2-reference-tables)中的應用程式。

```postgres
-- we're using the "text" column type here, but a real application
-- might use "citext" which is available in a postgres contrib module

CREATE TABLE users ( email text PRIMARY KEY );
SELECT create_reference_table('users');
```

在這段時間內，假設有幾個非位址進入表格。

```postgres
INSERT INTO users VALUES
   ('foo@example.com'), ('hacker12@aol.com'), ('lol');
```

我們想要驗證位址，但于 postgresql 通常不會讓我們加入現有資料列失敗的檢查條件約束。 但是， *它允許* 標示為不正確條件約束：

```postgres
ALTER TABLE users
ADD CONSTRAINT syntactic_email
CHECK (email ~
   '^[a-zA-Z0-9.!#$%&''*+/=?^_`{|}~-]+@[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?(?:\.[a-zA-Z0-9](?:[a-zA-Z0-9-]{0,61}[a-zA-Z0-9])?)*$'
) NOT VALID;
```

現在會保護新的資料列。

```postgres
INSERT INTO users VALUES ('fake');

/*
ERROR:  new row for relation "users_102010" violates
        check constraint "syntactic_email_102010"
DETAIL:  Failing row contains (fake).
*/
```

稍後在非尖峰時間，資料庫管理員可以嘗試修正錯誤的資料列，並重新驗證條件約束。

```postgres
-- later, attempt to validate all rows
ALTER TABLE users
VALIDATE CONSTRAINT syntactic_email;
```

于 postgresql 檔中有關于 [ALTER TABLE](https://www.postgresql.org/docs/current/sql-altertable.html) 區段中無效和 VALIDATE 條件約束的詳細資訊。

### <a name="addingremoving-indices"></a>新增/移除索引

超大規模 (Citus) 支援新增和移除 [索引](https://www.postgresql.org/docs/current/static/sql-createindex.html)：

```postgresql
-- Adding an index

CREATE INDEX clicked_at_idx ON clicks USING BRIN (clicked_at);

-- Removing an index

DROP INDEX clicked_at_idx;
```

加入索引會採用寫入鎖定，這在多租使用者的系統記錄中可能不需要 \" 。 \" 若要將應用程式停機時間降到最低，請改為 [同時](https://www.postgresql.org/docs/current/static/sql-createindex.html#SQL-CREATEINDEX-CONCURRENTLY) 建立索引。 這個方法需要的總工作量超過標準索引建立，且需要較長的時間才能完成。 不過，因為它允許在建立索引時繼續正常作業，所以這個方法適用于在生產環境中加入新的索引。

```postgresql
-- Adding an index without locking table writes

CREATE INDEX CONCURRENTLY clicked_at_idx ON clicks USING BRIN (clicked_at);
```
