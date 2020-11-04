---
title: 使用 IDENTITY 建立代理金鑰
description: 使用 IDENTITY 屬性在專用 SQL 集區的資料表上建立代理索引鍵的建議和範例。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 07/20/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96e81b3d7781f1c6f7bf5743a083e9640dd6c831
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323584"
---
# <a name="using-identity-to-create-surrogate-keys-using-dedicated-sql-pool-in-azuresynapse-analytics"></a>使用身分識別在 AzureSynapse 分析中使用專用的 SQL 集區建立代理金鑰

在本文中，您將找到使用 IDENTITY 屬性在專用 SQL 集區的資料表上建立代理索引鍵的建議和範例。

## <a name="what-is-a-surrogate-key"></a>什麼是代理金鑰

資料表上的 Surrogate 索引鍵是每個資料列都有唯一識別碼的資料行。 索引鍵並非從資料表資料產生。 資料製造模型者在設計資料倉儲模型時，都喜歡在其資料表上建立 Surrogate 索引鍵。 您可以使用 IDENTITY 屬性，在不影響載入效能的情況下，以簡單又有效的方式達成此目標。
> [!NOTE]
> 在 Azure Synapse Analytics 中，識別值會在每個散發中自行增加，且不會與其他散發套件中的識別值重迭。  如果使用者以 "SET IDENTITY_INSERT ON" 或 reseeds IDENTITY 明確插入重複的值，則 Synapse 中的識別值不保證是唯一的。 如需詳細資訊，請參閱 [CREATE TABLE (transact-sql) IDENTITY (屬性) ](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=azure-sqldw-latest)。 


## <a name="creating-a-table-with-an-identity-column"></a>建立具有 IDENTITY 資料行的資料表

IDENTITY 屬性的設計目的是在專用 SQL 集區的所有散發中向外延展，而不會影響載入效能。 因此，IDENTITY 的實作便是為了達成這些目標。

您在初次建立資料表時，可以使用類似下列陳述式的語法，將資料表定義為具有 IDENTITY 屬性：

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1) NOT NULL
,    C2 INT NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;
```

然後，您可以使用 `INSERT..SELECT` 來填入資料表。

本節的其餘部分會重點說明此實作的細節，以協助您更全面地了解它們。  

### <a name="allocation-of-values"></a>值的配置

識別屬性不保證因為資料倉儲的分散式架構而配置代理值的順序。 IDENTITY 屬性的設計目的是在專用 SQL 集區的所有散發中向外延展，而不會影響載入效能。 

下列範例將做出說明：

```sql
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)    NOT NULL
,    C2 VARCHAR(30)                NULL
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

INSERT INTO dbo.T1
VALUES (NULL);

INSERT INTO dbo.T1
VALUES (NULL);

SELECT *
FROM dbo.T1;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

在上述範例中，有兩個資料列落在發佈 1 中。 第一個資料列在資料行 `C1` 中具有 1 的 Surrogate 值，而第二個資料列則具有 61 的 Surrogate 值。 這兩個值都是由 IDENTITY 屬性所產生。 不過，值的配置並不是連續的。 這是設計的行為。

### <a name="skewed-data"></a>偏斜資料

資料類型的值範圍會平均分散於發佈上。 如果分散式資料表受到偏斜資料的影響，則可供資料類型使用的值範圍可能會提前耗盡。 例如，如果所有資料最後都位於單一發佈中，則該資料表實際上將只能存取該資料類型六十分之一的值。 因此，IDENTITY 屬性僅限用於 `INT` 和 `BIGINT` 資料類型。

### <a name="selectinto"></a>SELECT..INTO

將現有的 IDENTITY 資料行選取至新的資料表時，新的資料行會繼承 IDENTITY 屬性，除非下列其中一項條件成立：

- SELECT 陳述式包含聯結。
- 利用 UNION 來聯結多個 SELECT 陳述式。
- IDENTITY 資料行在 SELECT 清單中列出多次。
- IDENTITY 資料行是運算式的一部分。

如果其中任何一個狀況成立，都會將資料行建立成 NOT NULL，而不是繼承 IDENTITY 屬性。

### <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

CREATE TABLE AS SELECT (CTAS) 會遵循針對 SELECT..INTO 記錄的相同 SQL Server 行為。 不過，您無法在陳述式 `CREATE TABLE` 部分的資料行定義中指定 IDENTITY 屬性。 您也無法在 CTAS 的 `SELECT` 部分使用 IDENTITY 函式。 若要填入資料表，您必須使用 `CREATE TABLE` 來定義資料表，接著使用 `INSERT..SELECT` 來填入它。

## <a name="explicitly-inserting-values-into-an-identity-column"></a>明確地將值插入 IDENTITY 資料行

專用的 SQL 集區支援 `SET IDENTITY_INSERT <your table> ON|OFF` 語法。 您可以使用此語法來明確地將值插入 IDENTITY 資料行。

許多資料製造模型者喜歡在其維度的特定資料列中使用預先定義的負數值。 其中一個範例為 -1 或「未知的成員」資料列。

下一個指令碼會示範如何使用 SET IDENTITY_INSERT 明確地加入此資料列：

```sql
SET IDENTITY_INSERT dbo.T1 ON;

INSERT INTO dbo.T1
(   C1
,   C2
)
VALUES (-1,'UNKNOWN')
;

SET IDENTITY_INSERT dbo.T1 OFF;

SELECT     *
FROM    dbo.T1
;
```

## <a name="loading-data"></a>正在載入資料

IDENTITY 屬性的存在會對您的資料載入程式碼造成一些影響。 本節會重點說明使用 IDENTITY 將資料載入資料表的一些基本模式。

若要使用 IDENTITY 將資料載入資料表並產生 Surrogate 索引鍵，請建立資料表，然後使用 INSERT..SELECT 或 INSERT..VALUES 來執行載入。

下列範例會重點說明基本模式：

```sql
--CREATE TABLE with IDENTITY
CREATE TABLE dbo.T1
(    C1 INT IDENTITY(1,1)
,    C2 VARCHAR(30)
)
WITH
(   DISTRIBUTION = HASH(C2)
,   CLUSTERED COLUMNSTORE INDEX
)
;

--Use INSERT..SELECT to populate the table from an external table
INSERT INTO dbo.T1
(C2)
SELECT     C2
FROM    ext.T1
;

SELECT *
FROM   dbo.T1
;

DBCC PDW_SHOWSPACEUSED('dbo.T1');
```

> [!NOTE]
> 目前在使用 IDENTITY 資料行將資料載入資料表時，並無法使用 `CREATE TABLE AS SELECT`。
>

如需載入資料的詳細資訊，請參閱 [為專用的 SQL 集區設計解壓縮、載入和轉換 (ELT) ](design-elt-data-loading.md) ，以及  [載入最佳做法](guidance-for-loading-data.md)。

## <a name="system-views"></a>系統檢視表

您可以使用 [sys.identity_columns](/sql/relational-databases/system-catalog-views/sys-identity-columns-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 目錄檢視，以識別具有 IDENTITY 屬性的資料行。

為了協助您深入了解資料庫結構描述，此範例示範如何整合 sys.identity_column 與其他系統目錄檢視：

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       CASE WHEN ic.column_id IS NOT NULL
             THEN 1
        ELSE 0
        END AS is_identity
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
LEFT JOIN   sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="limitations"></a>限制

無法使用 IDENTITY 屬性：

- 資料行資料類型不是 INT 或 BIGINT 時
- 資料行也是散發索引鍵時
- 資料表為外部資料表時

專用的 SQL 集區不支援下列相關功能：

- [身分識別 ( # B1 ](/sql/t-sql/functions/identity-function-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [@@IDENTITY](/sql/t-sql/functions/identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [SCOPE_IDENTITY](/sql/t-sql/functions/scope-identity-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_CURRENT](/sql/t-sql/functions/ident-current-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_INCR](/sql/t-sql/functions/ident-incr-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [IDENT_SEED](/sql/t-sql/functions/ident-seed-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="common-tasks"></a>常見工作

本節提供一些範例程式碼，可在您使用 IDENTITY 資料行時用來執行一般工作。

在下列所有工作中，資料行 C1 都會是 IDENTITY。

### <a name="find-the-highest-allocated-value-for-a-table"></a>找出資料表的最大配置值

使用 `MAX()` 函式來判斷針對分散式資料表所配置的最大值：

```sql
SELECT MAX(C1)
FROM dbo.T1
```

### <a name="find-the-seed-and-increment-for-the-identity-property"></a>找出 IDENTITY 屬性的種子和增量

您可以透過下列查詢，來使用目錄檢視以探索資料表的識別值增量和種子設定值：

```sql
SELECT  sm.name
,       tb.name
,       co.name
,       ic.seed_value
,       ic.increment_value
FROM        sys.schemas AS sm
JOIN        sys.tables  AS tb           ON  sm.schema_id = tb.schema_id
JOIN        sys.columns AS co           ON  tb.object_id = co.object_id
JOIN        sys.identity_columns AS ic  ON  co.object_id = ic.object_id
                                        AND co.column_id = ic.column_id
WHERE   sm.name = 'dbo'
AND     tb.name = 'T1'
;
```

## <a name="next-steps"></a>後續步驟

- [資料表概觀](sql-data-warehouse-tables-overview.md)
- [CREATE TABLE (Transact-SQL) IDENTITY (屬性)](/sql/t-sql/statements/create-table-transact-sql-identity-property?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC CHECKINDENT](/sql/t-sql/database-console-commands/dbcc-checkident-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
