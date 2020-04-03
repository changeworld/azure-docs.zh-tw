---
title: 依選項群組
description: 在 Synapse SQL 池中按選項實現組的技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 28ac075d043f7605b6dfdac6879063fbe9308123
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619050"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>依 Synapse SQL 池中的選項群組

在本文中,您將在 SQL 池中找到按選項實現組的提示。

## <a name="what-does-group-by-do"></a>GROUP BY 有什麼用途？

[GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL 子句可將資料彙總至摘要的一組資料列。 GROUP BY 具有 SQL 池不支援的一些選項。 這些選項具有解決方法,如下所示:

* GROUP BY 搭配 ROLLUP
* GROUPING SETS
* GROUP BY 搭配 CUBE

## <a name="rollup-and-grouping-sets-options"></a>Rollup 和 grouping sets 選項

此處最簡單的選項是使用 UNION ALL 執行匯總,而不是依賴顯式語法。 結果完全相同。

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

## <a name="cube-options"></a>Cube 選項
可以使用"全聯盟"方法創建具有 CUBE 的組。 問題是程式碼可能很快就會很麻煩且不易處理。 要緩解此問題,可以使用此更高級的方法。

使用前面的示例,第一步是定義「多維數據集」,該「多維數據集」定義我們想要創建的所有聚合級別。 

請注意兩個派生表的 CROSS JOIN,因為這為我們生成所有級別。 代碼的其餘部份用於格式化:

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

下圖顯示了 CTAS 的結果:

![依 Cube 分組](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

第二個步驟是指定目標資料表來儲存過渡結果：

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

第三個步驟是對執行彙總的資料行 cube 執行迴圈。 查詢將針對臨時表中的每一行運行一次#Cube。 結果儲存在#Results暫時:

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

最後,您可以通過從#Results暫時讀取來傳回結果:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

通過將代碼分解為多個部分並生成迴圈構造,代碼變得更加可管理和可維護。

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。

