---
title: 使用 group by 選項
description: 在 Synapse SQL 集區中執行群組依據選項的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 260bd7cc6402a7cb4ed17d004d0f61b7462c51d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85213443"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Synapse SQL 集區中的分組依據選項

在本文中，您可以找到在 SQL 集區中執行群組依據選項的秘訣。

## <a name="what-does-group-by-do"></a>GROUP BY 有什麼用途？

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 子句可將資料彙總至摘要的一組資料列。 GROUP BY 有一些 SQL 集區不支援的選項。 這些選項有因應措施，如下所示：

* GROUP BY 搭配 ROLLUP
* GROUPING SETS
* GROUP BY 搭配 CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 選項

最簡單的選項是使用 UNION ALL 來執行匯總，而不是依賴明確的語法。 結果會完全相同。

下列範例使用 GROUP BY 陳述式搭配 ROLLUP 選項：

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

藉由使用 ROLLUP，上述範例會要求下列彙總：

* 國家及區域
* 國家/地區
* 總計

若要取代 ROLLUP，並傳回相同的結果，您可以使用 UNION ALL，並明確指定所需的彙總：

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

若要取代 GROUPING SETS，則適用範例原則。 您只需要針對想要查看的彙總層級建立 UNION ALL 區段。

## <a name="cube-options"></a>Cube 選項

您可以使用「聯集全部」方法，以 CUBE 建立群組。 問題是程式碼可能很快就會很麻煩且不易處理。 若要減輕這個問題，您可以使用這個更先進的方法。

使用上述範例，第一個步驟是定義 ' cube '，以定義我們想要建立的所有匯總層級。

請記下兩個衍生資料表的交叉聯結，因為這樣會為我們產生所有層級。 其餘的程式碼會提供格式：

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

下圖顯示 CTAS 的結果：

![依 Cube 分組](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

第二個步驟是指定用來儲存過渡結果的目標資料表：

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

第三個步驟是對執行彙總的資料行 cube 執行迴圈。 查詢將針對 #Cube 臨時表中的每個資料列執行一次。 結果會儲存在 #Results 臨時表中：

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

最後，您可以從 #Results 臨時表中讀取來傳回結果：

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

藉由將程式碼分解成各區段，並產生迴圈結構，程式碼就變得更容易管理及維護。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
