---
title: CREATE TABLE AS SELECT (CTAS)
description: CREATE TABLE 的說明和範例，請選取 Synapse SQL 中的 SELECT (CTAS) 語句，以開發解決方案。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 95158193a978702392106c8aa3347de211fd1a3e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98115441"
---
# <a name="create-table-as-select-ctas"></a>CREATE TABLE AS SELECT (CTAS)

本文說明在 Synapse SQL 中 CTAS) T-sql 語句，以開發解決方案的 (CREATE TABLE。 此文章也提供程式碼範例。

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

[CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) 語句是其中一個最重要的 t-sql 功能。 CTAS 是一種平行作業，會根據 SELECT 語句的輸出來建立新的資料表。 CTAS 是使用單一命令來建立資料並將其插入資料表的最簡單且最快速的方式。

## <a name="selectinto-vs-ctas"></a>選擇。。。INTO 和 CTAS

CTAS 是可自訂的 [SELECT .。。INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 語句。

以下範例是簡單的 SELECT .。。到：

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

選擇。。。INTO 不允許您在作業過程中變更散發方法或索引類型。 您可以 `[dbo].[FactInternetSales_new]` 使用 ROUND_ROBIN 的預設散發類型和叢集資料行存放區索引的預設資料表結構來建立。

另一方面，您可以使用 CTAS 來指定資料表資料的散發，以及資料表結構類型。 若要將前一個範例轉換成 CTAS：

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
> 如果您只是想要變更 CTAS 作業中的索引，且來源資料表是雜湊散發，請維護相同的散發資料行和資料類型。 這可避免在作業期間進行跨散發資料移動，這會更有效率。

## <a name="use-ctas-to-copy-a-table"></a>使用 CTAS 複製資料表

最常見的 CTAS 用法之一是建立資料表的複本，以便變更 DDL。 假設您最初將資料表建立為 `ROUND_ROBIN` ，而且現在想要將它變更為資料行上所散發的資料表。 CTAS 是您變更散發資料行的方式。 您也可以使用 CTAS 來變更資料分割、索引或資料行類型。

假設您使用的預設散發類型來建立此資料表 `ROUND_ROBIN` ，而不在中指定散發資料行 `CREATE TABLE` 。

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

現在您想要使用來建立這個資料表的新複本，讓您可以利用叢集資料行存放區 `Clustered Columnstore Index` 資料表的效能。 您也想要在上散發此資料表 `ProductKey` ，因為您預期會在此資料行上進行聯結，而且想要避免在聯結時移動資料 `ProductKey` 。 最後，您也想要在上加入資料分割 `OrderDateKey` ，讓您可以藉由卸載舊的資料分割來快速刪除舊的資料。 以下是 CTAS 語句，它會將您的舊資料表複製到新的資料表中。

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

最後，您可以重新命名資料表，以在新的資料表中交換，然後卸載舊的資料表。

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>使用 CTAS 來解決不支援的功能

您也可以使用 CTAS 來解決下列幾個不支援的功能。 這種方法通常很有用，因為您的程式碼不僅符合規範，而且通常在 Synapse SQL 上的執行速度會更快。 這項效能是完全平行化設計的結果。 案例包括：

* ANSI JOINS on UPDATEs
* ANSI JOINs on DELETEs
* MERGE 陳述式

> [!TIP]
> 請嘗試考慮「先 CTAS」。 使用 CTAS 解決問題通常是很好的方法，即使您要撰寫更多資料也是如此。

## <a name="ansi-join-replacement-for-update-statements"></a>更新陳述式的 ANSI 聯結取代

您可能會發現您有複雜的更新。 此更新會使用 ANSI 聯結語法來執行更新或刪除，將兩個以上的資料表聯結在一起。

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

原始查詢可能看起來像這個範例：

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

Synapse SQL 在語句的子句中不支援 ANSI 聯結 `FROM` `UPDATE` ，因此您無法使用先前的範例而不加以修改。

您可以使用 CTAS 和隱含聯結的組合來取代上述範例：

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

## <a name="ansi-join-replacement-for-merge"></a>合併的 ANSI 聯結取代 

在 Azure Synapse Analytics 中，TARGET 必須是雜湊分散式資料表，否則不符合 TARGET 的 [MERGE](/sql/t-sql/statements/merge-transact-sql?view=sql-server-ver15) (preview) 。  使用者可以使用包含 [UPDATE](/sql/t-sql/queries/update-transact-sql?view=sql-server-ver15) 或 [DELETE](/sql/t-sql/statements/delete-transact-sql?view=sql-server-ver15) 的 ANSI 聯結作為因應措施，根據與另一個資料表聯結的結果來修改目標資料表資料。  範例如下。

```sql
CREATE TABLE dbo.Table1   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
CREATE TABLE dbo.Table2   
    (ColA INT NOT NULL, ColB DECIMAL(10,3) NOT NULL);  
GO  
INSERT INTO dbo.Table1 VALUES(1, 10.0);  
INSERT INTO dbo.Table2 VALUES(1, 0.0);  
GO  
UPDATE dbo.Table2   
SET dbo.Table2.ColB = dbo.Table2.ColB + dbo.Table1.ColB  
FROM dbo.Table2   
    INNER JOIN dbo.Table1   
    ON (dbo.Table2.ColA = dbo.Table1.ColA);  
GO  
SELECT ColA, ColB   
FROM dbo.Table2;

```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>明確陳述資料類型和輸出可為 null

當您遷移程式碼時，可能會發現您在這種類型的編碼模式下執行：

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

您可能會想要將此程式碼遷移至 CTAS，而且是正確的。 不過，這裡有隱藏的問題。

下列程式碼不會產生相同的結果：

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

請注意，資料行 "result" 會帶有運算式的資料類型和可 Null 性。 如果您不小心，將資料類型向前傳送可能會導致值的微妙差異。

嘗試此範例：

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

為結果而儲存的值不相同。 當結果資料行中的保存值用於其他運算式時，錯誤會變得更重要。

![CTAS 結果的螢幕擷取畫面](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

這對資料移轉相當重要。 雖然第二個查詢是比較精確的，但還是有問題。 相較于來源系統，資料會不同，這會導致遷移中的完整性問題。 這是罕見案例之一，也就是「錯誤」的答案實際上是正確的答案！

我們在這兩個結果之間看到差異的原因，是因為隱含類型轉換。 在第一個範例中，資料表定義了資料行定義。 插入資料列時，會發生隱含類型轉換。 在第二個範例中，不會有隱含類型轉換，因為運算式會定義資料行的資料類型。

另外也請注意，第二個範例中的資料行已定義為可為 Null 的資料行，而在第一個範例中則否。 當資料表是在第一個範例中建立時，會明確定義資料行 null 屬性。 在第二個範例中，它會保留在運算式中，預設會產生 Null 定義。

若要解決這些問題，您必須在 CTAS 語句的 SELECT 部分中明確設定類型轉換和可 null 性。 您無法在 ' CREATE TABLE ' 中設定這些屬性。
下列範例示範如何修正程式碼：

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
* 使用 ISNull （而非聯合）強制 Null 屬性。 請參閱下列附注。
* ISNull 是最外層的函式。
* ISNull 的第二個部分是常數0。

> [!NOTE]
> 若要正確設定可 null 性，請務必使用 ISNull 而非聯合。 聯合不是具決定性的函數，因此運算式的結果一律會是可為 Null。 但 ISNULL 不同。 它是具決定性的。 因此，當 ISNull 函數的第二個部分是常數或常值時，產生的值將不會是 Null。

確保計算的完整性對於資料表分割切換而言也很重要。 假設您已將此資料表定義為事實資料表：

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

不過，[金額] 欄位是計算運算式。 它不是來源資料的一部分。

若要建立分割的資料集，您可能會想要使用下列程式碼：

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

查詢將會順利執行。 當您嘗試進行分割區切換時，就會發生此問題。 資料表定義不相符。 若要使資料表定義相符，請修改 CTAS 來加入函式， `ISNULL` 以保留資料行的 null 屬性。

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

您可以在 CTAS 上看到類型一致性和維護可 null 性屬性，是工程最佳做法。 它有助於維護計算中的完整性，也可以確保資料分割切換是可行的。

CTAS 是 Synapse SQL 中最重要的其中一個語句。 請確定您已徹底了解。 請參閱 [CTAS 檔](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="next-steps"></a>後續步驟

如需更多開發祕訣，請參閱 [開發概觀](sql-data-warehouse-overview-develop.md)。