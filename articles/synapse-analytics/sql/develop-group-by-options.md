---
title: 在 Synapse SQL 中使用 GROUP BY 選項
description: Synapse SQL 可讓您藉由執行不同的群組依據選項來開發解決方案。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: ad2b13fbfbd9871a55efb1826fa1e978d4eeb453
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032294"
---
# <a name="group-by-options-in-synapse-sql"></a>Synapse SQL 中的 GROUP BY 選項
Synapse SQL 可讓您藉由執行不同的群組依據選項來開發解決方案。 

## <a name="what-does-group-by-do"></a>GROUP BY 執行的動作

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL 子句可將資料彙總至摘要的一組資料列。

SQL 隨選支援群組依據選項的整個範圍。 SQL 集區支援的分組依據選項數量有限。

## <a name="group-by-options-supported-in-sql-pool"></a>SQL 集區中支援的分組依據選項

GROUP BY 有一些 SQL 集區不支援的選項。 這些選項有因應措施，如下所示：

* GROUP BY 搭配 ROLLUP
* GROUPING SETS
* GROUP BY 搭配 CUBE

### <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 選項

最簡單的選項是使用 UNION ALL 來執行匯總，而不是依賴明確的語法。 應該會出現幾乎相同的結果

下列範例會使用 GROUP BY 語句搭配 ROLLUP 選項：

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
* Country
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

### <a name="cube-options"></a>Cube 選項

您可以使用「聯集全部」方法，以 CUBE 建立群組。 問題是程式碼可能很快就會很麻煩且不易處理。 若要減輕這個問題，您可以使用這個更先進的方法。

第一個步驟是定義 'cube'，其定義我們想要建立的所有彙總層級。 當產生所有層級時，請記下兩個衍生資料表的交叉聯結。 其餘的程式碼則是用於格式化。

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

下圖顯示 CREATE TABLE 的結果 [為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)：

![依 Cube 分組](./media/develop-group-by-options/develop-group-by-cube.png)

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

第三個步驟是對執行匯總的資料行 cube 執行迴圈。 查詢將針對 #Cube 臨時表中的每個資料列執行一次。 結果會儲存在 #Results 臨時表中：

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

如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。
