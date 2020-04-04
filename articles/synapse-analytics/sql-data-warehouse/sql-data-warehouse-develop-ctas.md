---
title: CREATE TABLE AS SELECT (CTAS)
description: Synapse SQL 中用於開發解決方案的創建表作為 SELECT (CTAS) 語句的說明和示例。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633540"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

本文介紹了 Synapse SQL 中用於開發解決方案的創建表作為 SELECT (CTAS) T-SQL 語句。 此文章也提供程式碼範例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[創建表作為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) 語句是可用的最重要的 T-SQL 功能之一。 CTAS 是一個並行操作,它基於 SELECT 語句的輸出創建新錶。 CTAS 是創建數據並將其插入具有單個命令的表中的最簡單、最快的方法。

## <a name="selectinto-vs-ctas"></a>選擇。。。INTO 與 CTAS

CTAS 是 SELECT 的一個可自訂的版本[...INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)聲明。

下面是一個簡單的選擇的範例...到:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

選擇。。。INTO 不允許在操作中更改分佈方法或索引類型。 通過使用預設`[dbo].[FactInternetSales_new]`分佈類型ROUND_ROBIN和"CLUSTERED"列科索引的默認表結構進行創建。

另一方面,使用 CTAS,您可以指定表數據的分佈以及表結構類型。 要將前面的範例轉換為 CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> 如果僅在 CTAS 操作中嘗試更改索引,並且源表是哈希分佈的,請維護相同的分佈列和數據類型。 這樣可以避免操作過程中的交叉分佈數據移動,這更有效率。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 複製資料表

CTAS 最常見的用途之一是創建表的副本以更改 DDL。 假設您最初將表創建為`ROUND_ROBIN`,現在希望將其更改為分佈在列上的表。 CTAS 是更改分配列的方式。 您還可以使用 CTAS 更改分區、索引或列類型。

假設您使用 的預設分發類型 創建了`ROUND_ROBIN`此表 ,`CREATE TABLE`而不是指定 中的 分配列。

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

現在,您希望使用`Clustered Columnstore Index`創建此表的新副本,以便利用群集列儲存表的性能。 您還希望在上`ProductKey`分發此表,因為您預期此列上的聯接,並且希望避免在聯接期間在上`ProductKey`上 移動數據。 最後,您還希望在上`OrderDateKey`添加分區,以便可以通過刪除舊分區來快速刪除舊數據。 下面是 CTAS 語句,該語句將舊表複製到新表中。

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

最後,您可以重新命名表,在新表中交換,然後刪除舊表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 解決不支援的功能

您還可以使用 CTAS 解決下面列出的許多不支援的功能。 此方法通常可以證明是有説明的,因為不僅代碼符合要求,而且它通常會在 Synapse SQL 上運行得更快。 這種性能是其完全並行化設計的結果。 配置:

* ANSI JOINS on UPDATEs
* ANSI JOINs on DELETEs
* MERGE 陳述式

> [!TIP]
> 試著先想想"CTAS"。 使用 CTAS 解決問題通常是一種好方法,即使您因此寫入了更多數據也是如此。

## <a name="ansi-join-replacement-for-update-statements"></a>更新陳述式的 ANSI 聯結取代

您可能會發現您有一個複雜的更新。 使用 ANSI 聯接文法執行更新或刪除,更新將兩個以上表聯接在一起。

想像您必須更新這個資料表：

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

原始查詢可能如下所示,示例如下所示:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL`FROM``UPDATE`不支援 語句子句中的 ANSI 聯接,因此如果不修改它,就不能使用前面的示例。

可以使用 CTAS 與隱式聯接的組合來取代前面的範例:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>delete 陳述式的 ANSI 聯結取代

有時,刪除數據的最佳方法是使用 CTAS,尤其是`DELETE`對於 使用 ANSI 聯接語法的語句。 這是因為 Synapse SQL 不支援`FROM``DELETE`ANSI 在 語句子句中的聯接。 選擇要保留的資料,而不是刪除資料。

下面是轉換`DELETE`敘述的範例:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>取代 merge 陳述式

可以使用 CTAS 替換合併文句(至少部分)。 可以將和`INSERT`合併`UPDATE`到單個語句中。 任何已刪除的記錄都應從語句中`SELECT`限制,以忽略結果。

以下範例用於`UPSERT`:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>明確陳述資料類型和輸出可為 null

移動代碼時,您可能會發現您執行在這種類型的編碼模式中:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

您可能認為應該將此代碼遷移到 CTAS,而且正確。 但是,這裡有一個隱藏的問題。

以下代碼不會產生相同的結果:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

請注意，資料行 "result" 會帶有運算式的資料類型和可 Null 性。 如果不小心,將數據類型向前攜帶可能會導致值的細微差異。

請嘗試此範例:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

為結果而儲存的值不相同。 當結果列中的持久值用於其他表達式時,錯誤變得更加顯著。

![CTAS 結果的螢幕截圖](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

這對數據遷移非常重要。 儘管第二個查詢可以說是更準確的,但存在問題。 與源系統相比,數據會有所不同,從而導致遷移中的完整性問題。 這是罕見案例之一，也就是「錯誤」的答案實際上是正確的答案！

我們看到兩個結果之間的差異的原因是隱式類型轉換。 在第一個示例中,表定義列定義。 插入行時,將發生隱式類型轉換。 在第二個示例中,沒有隱式類型轉換,因為表達式定義列的數據類型。

另請注意,第二個範例中的列已定義為 NULable 列,而在第一個範例中,該列沒有。 在第一個示例中創建表時,顯式定義了列空。 在第二個示例中,它留給運算式,默認情況下會導致 NULL 定義。

要解決這些問題,必須在 CTAS 語句的 SELECT 部分中顯式設置類型轉換和空度。 不能在"創建表"中設置這些屬性。
下面的範例展示如何修復碼:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

請注意：

* 您可以使用 CAST 或 CONVERT。
* 使用 ISNULL,而不是「歐洲」來強制 NULL.。 請參閱以下註釋。
* ISNULL 是最外層函數。
* ISNULL 的第二部分是常量 0。

> [!NOTE]
> 要正確設置空值,使用 ISNULL 而不是 COALESCE 至關重要。 COALESCE不是一個確定性函數,因此表達式的結果永遠是可NULL的。 但 ISNULL 不同。 這是確定性的。 因此,當 ISNULL 函數的第二部分是常量或文本時,生成的值將不是 NULL。

確保計算的完整性對於表分區切換也很重要。 假設您將此表定義為事實表:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

但是,量欄位是計算表達式。 它不是源數據的一部分。

要建立分割區資料集,可能需要使用以下代碼:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

查詢運行良好。 當您嘗試執行分區切換時,問題就出現了。 表定義不匹配。 要使表定義匹配,請修改 CTAS 以添加`ISNULL`函數 以保留列的 null 可屬性。

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

您可以看到,類型一致性和維護 CTAS 上的空屬性是工程最佳實踐。 它有助於在計算中保持完整性,並確保分區切換成為可能。

CTAS 是 SynapsE SQL 中最重要的語句之一。 請確定您已徹底了解。 請參考[CTAS 文件](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="next-steps"></a>後續步驟

如需更多開發祕訣，請參閱 [開發概觀](sql-data-warehouse-overview-develop.md)。
