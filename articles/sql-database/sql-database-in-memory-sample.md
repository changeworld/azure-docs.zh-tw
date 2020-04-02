---
title: 記憶體內部範例
description: 使用 OLTP 和資料行存放區範例試用 Azure SQL 資料庫記憶體內部技術。
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528206"
---
# <a name="in-memory-sample"></a>記憶體內部範例

Azure SQL Database 中的記憶體內部技術可讓您改善應用程式的效能，還能降低您的資料庫成本。 您可以藉由使用 Azure SQL Database 中的記憶體內部技術，來達成各種工作負載的效能改進。

在本文中，您會看到兩個範例，分別示範如何在 Azure SQL Database 中使用記憶體內部 OLTP 以及資料行存放區索引。

如需詳細資訊，請參閱
- [記憶體內部 OLTP 概觀和使用案例](https://msdn.microsoft.com/library/mt774593.aspx) (包括客戶案例研究參考和入門資訊)
- [記憶體內部 OLTP 的文件](https://msdn.microsoft.com/library/dn133186.aspx)
- [資料行存放區索引指南](https://msdn.microsoft.com/library/gg492088.aspx)
- 混合式交易/分析處理 (HTAP)，也稱為[即時作業分析](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. 安裝記憶體中 OLTP 範例

在 [Azure 入口網站](https://portal.azure.com/)中按幾下滑鼠，即可建立 AdventureWorksLT 範例資料庫。 然後，本節中的步驟會說明如何使用記憶體內部 OLTP 物件擴充 AdventureWorksLT 資料庫，並示範效能優點。

如需更簡單、但更美觀的記憶體內部 OLTP 效能示範，請參閱︰

- 版本︰[in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- 原始程式碼︰[in-memory-oltp-demo-source-code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>安裝步驟

1. 在 [Azure 入口網站](https://portal.azure.com/)中，於伺服器上建立進階或業務關鍵資料庫。 將 [來源] **** 設定為 AdventureWorksLT 範例資料庫。 如需詳細指示，請參閱[建立您的第一個 Azure SQL 資料庫](sql-database-single-database-get-started.md)。

2. 使用 SQL Server Management Studio [(SSMS.exe)](https://msdn.microsoft.com/library/mt238290.aspx)連接到資料庫。

3. 將 [In-Memory OLTP Transact-SQL 指令碼](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) 複製到剪貼簿。 T-SQL 指令碼會在步驟 1 建立的 AdventureWorksLT 範例資料庫中建立所需的 In-Memory 物件。

4. 將 T-SQL 指令碼貼到 SSMS 中，然後執行該指令碼。 `MEMORY_OPTIMIZED = ON` 子句 CREATE TABLE 陳述式很重要。 例如：


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>錯誤 40536


如果您在執行 T-SQL 指令碼時收到錯誤 40536，請執行下列 T-SQL 指令碼來確認資料庫是否支援 In-Memory：


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


結果為 **0** 表示不支援 In-Memory，而 **1** 表示提供支援。 若要診斷問題，請確定資料庫位於「進階」服務層級。


#### <a name="about-the-created-memory-optimized-items"></a>關於已建立的記憶體最佳化項目

**資料表**：此範例包含下列記憶體最佳化資料表：

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


您可以透過 SSMS 中的 [物件總管]****，檢查記憶體最佳化資料表。 右鍵按**下 表** > **Filter** > **篩選器篩選器設定** > **為記憶體最佳化 。** 值等於 1。


或者您可以查詢目錄檢視，例如：


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**原生編譯的預存程序**：您可以透過目錄檢視查詢來檢查 SalesLT.usp_InsertSalesOrder_inmem：


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>執行範例 OLTP 工作負載

下列兩個預存程序 ** 的唯一差別在於第一個程序會使用記憶體最佳化資料表版本，而第二個程序會使用一般磁碟資料表：

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


在本節中，您會了解如何使用便利的 **ostress.exe** 公用程式，在壓力層級執行兩個預存程序。 您可以比較完成兩個壓力回合所需的時間。


當您執行 ostress.exe 時，建議您將指定的參數值傳遞至下列兩者：

- 使用 -n100 來執行大量的並行連線。
- 使用 -r500 讓每個連線執行幾百次迴圈。


不過，您可能想從較小的值 (-n10 和 -r50) 開始，以確保一切都運作正常。


### <a name="script-for-ostressexe"></a>Ostress.exe 的指令碼


本節顯示 ostress.exe 命令列中內嵌的 T-SQL 指令碼。 此指令碼會使用您稍早安裝的 T-SQL 指令碼所建立的項目。


下列指令碼會在下列記憶體最佳化資料表 ** 中插入有 5 個細項的範例銷售訂單：

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


若要針對 ostress.exe 製作上述 T-SQL 指令碼的 *_ondisk* 版本，請以 *_ondisk* 取代兩個出現的 *_inmem* 子字串。 這類取代會影響資料表和預存程序的名稱。


### <a name="install-rml-utilities-and-ostress"></a>安裝 RML 公用程式和 `ostress`


您最好規劃在 Azure 虛擬機器 (VM) 上執行 ostress.exe。 您會在 AdventureWorksLT 資料庫所在的相同 Azure 地理區域中建立 [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/)。 但是您可以改在您的膝上型電腦上執行 ostress.exe。


在 VM 上或你選擇的任何主機上，安裝 Replay Markup Language (RML) 公用程式。 這些公用程式包括 ostress.exe。

如需詳細資訊，請參閱
- [記憶體內部 OLTP 的範例資料庫](https://msdn.microsoft.com/library/mt465764.aspx)中的 ostress.exe 討論。
- [記憶體的名稱 OLTP 的範例資料庫](https://msdn.microsoft.com/library/mt465764.aspx)。
- [安裝 ostress.exe 的部落格](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)。



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>先執行 _inmem** 壓力工作負載


您可以使用 RML 命令提示字元 ** 視窗來執行 ostress.exe 命令列。 命令列參數會將 `ostress` 導向至：

- 同時執行 100 個連線 (-n100)。
- 每個連線會執行 T-SQL 指令碼 50 次 (-r50)。


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


若要執行上述的 ostress.exe 命令列：


1. 在 SSMS 中執行下列命令來重設資料庫資料內容，以刪除先前執行插入的所有資料：

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. 將上述 ostress.exe 命令列的文字複製到剪貼簿。

3. 以正確的實數值取代參數 -S -U -P -d 的 `<placeholders>` 。

4. 在 [RML 命令] 視窗中執行已編輯的命令列。


#### <a name="result-is-a-duration"></a>結果是持續時間


當 `ostress.exe` 完成時，它會在 RML 命令視窗中寫入執行持續時間做為輸出的最後一行。 例如，較短的測試回合持續大約 1.5 分鐘：

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>重置,編輯 *_ondisk*,然後重新執行


在獲得 _inmem** 執行的結果之後，請針對 _ondisk** 執行回合執行下列步驟：


1. 在 SSMS 中執行下列命令來重設資料庫，以刪除先前執行插入的所有資料：
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. 編輯 ostress.exe 命令列，以 *_ondisk* 取代所有的 *_inmem*。

3. 第二次重新執行 ostress.exe，並擷取持續時間結果。

4. 再次重設資料庫 (以負責刪除可能的大量測試資料)。


#### <a name="expected-comparison-results"></a>預期的比較結果

就這個過度簡單的工作負載而言，我們的「記憶體內部」測試顯示當 `ostress` 是在與資料庫相同 Azure 區域中的 Azure VM 上執行時，可獲得「九倍」**** 的效能改善。

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. 安裝記憶體內分析範例


在本節中，您將比較使用資料行存放區索引與使用傳統 B 型樹狀結構索引時的 IO 和統計資料結果。


針對 OLTP 工作負載的即時分析，通常最好使用非叢集式資料行存放區索引。 有關詳細資訊,請參閱[描述的儲存索引](https://msdn.microsoft.com/library/gg492088.aspx)。



### <a name="prepare-the-columnstore-analytics-test"></a>準備資料行存放區分析測試


1. 使用 Azure 入口網站，從範例建立全新的 AdventureWorksLT 資料庫。
   - 使用相同的名稱。
   - 選擇任何進階服務層級。

2. 將 [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) 複製到剪貼簿。
   - T-SQL 指令碼會在步驟 1 建立的 AdventureWorksLT 範例資料庫中建立所需的 In-Memory 物件。
   - 此指令碼會建立維度資料表和兩個事實資料表。 每個事實資料表會填入 350 萬個資料列。
   - 此指令碼可能需要 15 分鐘才能完成。

3. 將 T-SQL 指令碼貼到 SSMS 中，然後執行該指令碼。 **CREATE INDEX** 陳述式中的 **COLUMNSTORE** 關鍵字很重要，如下所示：<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. 將 AdventureWorksLT 設為相容性層級 130：<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    層級 130 並未與 In-Memory 功能直接相關。 但層級 130 通常會提供比層級 120 更快的查詢效能。


#### <a name="key-tables-and-columnstore-indexes"></a>重要資料表和資料行存放區索引


- dbo.FactResellerSalesXL_CCI 是具有叢集式「資料行存放區」索引的資料表，此資料表已在「資料」** 層級進一步壓縮。

- dbo.FactResellerSalesXL_PageCompressed 是具有對等一般叢集式索引的資料表，此資料表只在「頁面」** 層級壓縮。


#### <a name="key-queries-to-compare-the-columnstore-index"></a>用來比較資料行存放區索引的重要查詢


有[您可以執行的數種 T-SQL 查詢類型](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql)可用來查看效能改進。 在步驟 2 的 T-SQL 指令碼中，請注意這一組查詢。 其中的不同之處只有一行：


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


叢集資料行存放區索引位於 FactResellerSalesXL\_CCI 資料表上。

下列 T-SQL 指令碼摘錄列出每個資料表查詢的 IO 和 TIME 統計資料。


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

在定價層為 P2 的資料庫中，相較於傳統索引，使用叢集資料行存放區索引進行此查詢預期可提升大約九倍的效能。 使用 P15 時，您可以預期使用資料行存放區索引大約可提升 57 倍的效能。



## <a name="next-steps"></a>後續步驟

- [快速入門 1:記憶體中 OLTP 技術,實現更快的 T-SQL 性能](https://msdn.microsoft.com/library/mt694156.aspx)

- [在現有 Azure SQL 應用程式使用記憶體中 OLTP](sql-database-in-memory-oltp-migration.md)

- 針對記憶體內部 OLAP [監視記憶體內部 OLTP 儲存體](sql-database-in-memory-oltp-monitoring.md)


## <a name="additional-resources"></a>其他資源

#### <a name="deeper-information"></a>更深入的資訊

- [瞭解 Quorum 如何透過 SQL 資料庫中的記憶體內 OLTP 使金鑰資料庫的工作負載翻倍,同時將 DTU 降低 70%](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [Azure SQL Database 中的記憶體內部 OLTP 部落格文章](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [了解記憶體內部 OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [了解資料行存放區索引](https://msdn.microsoft.com/library/gg492088.aspx)

- [了解即時作業分析](https://msdn.microsoft.com/library/dn817827.aspx)

- 請參閱[一般工作負載模式和移轉考量](https://msdn.microsoft.com/library/dn673538.aspx) (其中描述記憶體內部 OLTP 經常提供顯著效能改善的工作負載模式)

#### <a name="application-design"></a>應用程式設計

- [In-Memory OLTP (記憶體中最佳化)](https://msdn.microsoft.com/library/dn133186.aspx)

- [在現有 Azure SQL 應用程式使用記憶體中 OLTP](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>工具

- [Azure 入口網站](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL 伺服器資料工具 (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
