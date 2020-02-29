---
title: 適用於工作負載管理的資源類別
description: 在 Azure Synapse 分析中使用資源類別來管理並行和計算資源以進行查詢的指導方針。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c94b2a755d85bdf425980574b63d8fd74a232b19
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195986"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>使用 Azure Synapse 分析中的資源類別進行工作負載管理

在 Azure Synapse 中使用資源類別來管理 SQL 分析查詢之記憶體和並行的指引。  

## <a name="what-are-resource-classes"></a>什麼是資源類別

查詢的效能處理能力取決於使用者的資源類別。  在 SQL 分析中，資源類別是預先決定的資源限制，可控制查詢執行的計算資源和平行存取。 資源類別可設定同時執行的查詢數目限制，以及指派給每個查詢的計算資源，以協助您設定查詢的資源。  記憶體和平行存取之間有取捨。

- 較小型的資源類別會減少每個查詢的記憶體上限，但會增加並行存取數。
- 較大的資源類別會增加每個查詢的記憶體上限，但會減少並行。

有兩種類型的資源類別：

- 靜態資源類別：適合在大小固定的資料集上增加並行存取作業數目。
- 動態資源類別，非常適合大小不斷增長的資料集，而且需要在服務等級相應增加時提高效能。

資源類別會使用並行位置來測量資源耗用量。  [並行位置](#concurrency-slots)稍後會在本文中加以說明。

- 若要檢視資源類別的資源使用率，請參閱[記憶體和並行存取限制](memory-concurrency-limits.md)。
- 若要調整資源類別，您可以在不同的使用者下執行查詢，或[變更目前使用者的資源類別](#change-a-users-resource-class)成員資格。

### <a name="static-resource-classes"></a>靜態資源類別

靜態資源類別會配置相同數量的記憶體，而不論目前的效能等級為何，這會以[資料倉儲單位](what-is-a-data-warehouse-unit-dwu-cdwu.md)來測量。 由於不論效能等級為何，查詢取得的記憶體配置都相同，[相應放大資料倉儲](quickstart-scale-compute-portal.md)可讓在資源類別內執行更多的查詢。  如果資料磁碟區為已知且不會改變，靜態資源類別就非常適合。

這些預先定義的資料庫角色會用來實作靜態資源類別：

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>動態資源類別

動態資源類別會根據目前的服務等級來配置數量不一的記憶體。 靜態資源類別有利於較高的並行存取和靜態資料磁碟區，動態資源類別則更適合正在成長或數量不一的資料。  當您相應增加為較大的服務等級時，查詢會自動獲得更多記憶體。  

這些預先定義的資料庫角色會用來實作動態資源類別：

- smallrc
- mediumrc
- largerc
- xlargerc

每個資源類別的記憶體配置如下所示。 

| 服務等級  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25%               | 25%                    | 25%                    | 70%                    |
| DW200c         | 12.5%             | 12.5%                  | 22%                    | 70%                    |
| DW300c         | 8%                | 10%                    | 22%                    | 70%                    |
| DW400c         | 6.25%             | 10%                    | 22%                    | 70%                    |
| DW500c         | 20%               | 10%                    | 22%                    | 70%                    |
| DW1000c 至<br> DW30000c | 3%       | 10%                    | 22%                    | 70%                    |



### <a name="default-resource-class"></a>預設的資源類別

根據預設，每位使用者都是動態資源類別 **smallrc** 的成員。

服務管理員的資源類別固定為 smallrc 且無法變更。  服務管理員是在佈建程序期間建立的使用者。  此內容中的服務系統管理員，是使用新的伺服器建立新的 SQL 分析實例時，為「伺服器管理員登入」指定的登入。

> [!NOTE]
> 定義為 Active Directory 管理員的使用者或群組，也會是服務管理員。
>
>

## <a name="resource-class-operations"></a>資源類別作業

資源類別是專為改善資料管理和操作活動效能而設計的。 在大型資源類別下執行，複雜查詢也能受益。 例如，當資源類別大到足以在記憶體內執行查詢時，就能改善大型聯結和排序的查詢效能。

### <a name="operations-governed-by-resource-classes"></a>作業都受到資源類別控管

這些作業都受到資源類別控管：

- INSERT-SELECT、UPDATE、DELETE
- SELECT (當查詢使用者資料表時)
- ALTER INDEX - REBUILD 或 REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- 載入資料
- 資料移動服務 (DMS) 進行的資料移動作業

> [!NOTE]  
> 動態管理檢視 (DMV) 或其他系統檢視上的 SELECT 陳述式不受任何並行限制所控管。 不論在系統上執行的查詢數目多寡，您都可監視該系統。
>
>

### <a name="operations-not-governed-by-resource-classes"></a>作業都不受資源類別控管

儘管使用者是較大資源類別的成員，有些查詢還是一律會在 smallrc 資源類別中執行。 這些排除的查詢不會計入並行限制。 例如，如果並行限制為 16，許多使用者可以從系統檢視中選取，而不會影響可用的並行位置。

下列陳述式會從資源類別中排除，且一律在 smallrc 中執行：

- CREATE 或 DROP TABLE
- ALTER TABLE ...SWITCH、SPLIT 或 MERGE PARTITION
- ALTER INDEX DISABLE
- DROP INDEX
- CREATE、UPDATE 或 DROP STATISTICS
- TRUNCATE TABLE
- ALTER AUTHORIZATION
- CREATE LOGIN
- CREATE、ALTER 或 DROP USER
- CREATE、ALTER 或 DROP PROCEDURE
- CREATE 或 DROP VIEW
- INSERT VALUES
- 從系統檢視和 DMV 來 SELECT
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>並行位置

並行位置是追蹤查詢執行可用之資源的便利方式。 它們就像是您因為音樂會的座位有限，而購買來保留座位的門票。 每個資料倉儲的並行位置總數取決於服務等級。 在查詢開始執行之前，它必須能夠保留足夠的並行位置。 當查詢完成時，它會釋放其並行位置。  

- 比起以 2 個並行位置執行的查詢，以 10 個並行位置執行的查詢可以存取 5 倍以上的計算資源。
- 如果每個查詢需要 10 個並行位置且有 40 個並行位置，則只能同時執行 4 個查詢。

只有資源控管的查詢可取用並行位置。 系統查詢和一些簡單的查詢不會耗用任何位置。 使用的並行位置確切數目取決於查詢的資源類別。

## <a name="view-the-resource-classes"></a>檢視資源類別

資源類別會實作為預先定義的資料庫角色。 有兩種類型的資源類別：動態和靜態。 檢視資源類別清單，請使用下列查詢：

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>變更使用者的資源類別

資源類別會藉由將使用者指派給資料庫角色來實作。 當使用者執行查詢時，查詢會利用使用者的資源類別來執行。 例如，如果使用者是 staticrc10 資料庫角色的成員，其查詢就會以少量的記憶體執行。 如果資料庫使用者是 xlargerc 或 staticrc80 資料庫角色的成員，其查詢就會以海量儲存體執行。

若要增加使用者的資源類別，請使用[sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql)將使用者新增至大型資源類別的資料庫角色。  下列程式碼會將使用者新增至 largerc 資料庫角色。  每個要求都會取得系統記憶體的22%。

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

若要減少資源類別，使用 [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) \(英文\)。  如果 ' loaduser ' 不是成員或任何其他資源類別，它們會進入預設的 smallrc 資源類別，並提供3% 的記憶體授與。  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>資源類別優先順序

使用者可以是多個資源類別的成員。 當使用者屬於多個資源類別時：

- 動態資源類別的優先順序高於靜態資源類別。 例如，如果使用者是 mediumrc (動態) 和 staticrc80 (靜態) 的成員，查詢會使用 mediumrc 來執行。
- 較大資源類別的優先順序高於較小資源類別。 例如，如果使用者是 mediumrc 和 largerc 的成員，查詢會使用 largerc 來執行。 同樣地，如果使用者是 staticrc20 和 statirc80 的成員，查詢會使用 staticrc80 資源配置來執行。

## <a name="recommendations"></a>建議

>[!NOTE]
>請考慮利用工作負載管理功能（[工作負載隔離](sql-data-warehouse-workload-isolation.md)、[分類](sql-data-warehouse-workload-classification.md)和[重要性](sql-data-warehouse-workload-importance.md)），以更充分掌控您的工作負載和可預測的效能。  
>
>

我們建議您建立專用於執行特定類型查詢或載入作業的使用者。 為該使用者提供永久的資源類別，而不是經常變更資源類別。 靜態資源類別會對工作負載提供更高的整體控制權，因此建議在考慮動態資源類別之前，先使用靜態資源類別。

### <a name="resource-classes-for-load-users"></a>載入使用者適用的資源類別

`CREATE TABLE` 預設會使用叢集的資料行存放區索引。 將資料壓縮到資料行存放區索引是記憶體密集型作業，而記憶體壓力會降低索引品質。 記憶體壓力可能會導致在載入資料時需要較高的資源類別。 若要確保載入具有足夠的記憶體，您可以建立要指定來執行載入的使用者，並將該使用者指派給較高的資源類別。

若要有效率地處理載入，所需的記憶體取決於所載入之資料表的性質和資料大小。 如需有關記憶體需求的詳細資訊，請參閱[將資料列群組品質最大化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

一旦您決定記憶體需求之後，請選擇是否要將載入使用者指派給靜態或動態資源類別。

- 當資料表記憶體需求落在特定範圍內時，請使用靜態資源類別。 載入會使用適當的記憶體來執行。 當您調整資料倉儲時，載入不需要更多記憶體。 記憶體配置藉由使用靜態資源類別來維持一致。 這種一致性可節省記憶體，並允許同時執行多個查詢。 我們建議新的解決方案先使用靜態資源類別，因為這些提供更好的控制。
- 當資料表記憶體需求相當廣泛時，請使用動態資源類別。 載入所需的記憶體可能比目前的 DWU 或 cDWU 層級提供的更多。 調整資料倉儲會增加更多記憶體給載入作業，讓負載的執行速度更快。

### <a name="resource-classes-for-queries"></a>查詢適用的資源類別

有些查詢需要大量計算，有些則沒有。  

- 當查詢很複雜，但不需要高平行存取時，請選擇動態資源類別。  例如，產生每日或每週報表，偶爾需要使用資源。 如果報表正在處理大量資料，則調整資料倉儲會為使用者現有的資源類別提供更多記憶體。
- 若對資源的預期一整天都不一樣，請選擇靜態資源類別。 例如，當許多人查詢資料倉儲時，靜態資源類別會運作得很好。 調整資料倉儲時，配置給使用者的記憶體數量不會變更。 因此，可以在系統上平行執行更多查詢。

適當的記憶體授權取決於許多因素，例如查詢的資料量、資料表架構的性質，以及各種聯結、選取和群組述詞。 一般來說，配置更多的記憶體讓查詢能夠更快完成，但會減少整體並行能力。 如果您不在意並行能力，則超額配置記憶體不會損害輸送量。

若要調整效能，請使用不同的資源類別。 下一節提供可協助您找出最佳資源類別的預存程序。

## <a name="example-code-for-finding-the-best-resource-class"></a>尋找最佳資源類別的範例程式碼

您可以使用下列指定的預存程式，找出指定 SLO 的每個資源類別的並行和記憶體授與，以及在指定資源類別的非分割 CCI 資料表上，記憶體密集型 CCI 作業的最佳資源類別：

以下是此預存程序的用途：

1. 在指定的 SLO，查看對每個資源類別授與的並行和記憶體。 使用者必須同時為結構描述和 tablename 提供 NULL，如此範例所示。  
2. 在指定資源類別的非分割 CCI 資料表上，查看記憶體密集型 CCI 作業（載入、複製資料表、重建索引等）的最佳資源類別。 此預存程序會使用資料表結構描述來找出所需授與的記憶體。

### <a name="dependencies--restrictions"></a>相依性 & 限制

- 這個預存程式並非設計來計算分割的 cci 資料表的記憶體需求。
- 這個預存程式不會將記憶體需求納入選取部分的 CTAS/插入-SELECT，並假設它是 SELECT。
- 此預存程序會使用暫存資料表，可在此預存程序建立所在的工作階段中使用。
- 這個預存程式取決於目前的供應專案（例如，硬體設定、DMS config），如果有任何變更，則此預存程式將無法正常運作。  
- 這個預存程式取決於現有的平行存取限制供應專案，如果這些變更，則此預存程式將無法正確運作。  
- 這個預存程式取決於現有的資源類別供應專案，如果這些變更，則此預存程式將無法正確運作。  

>[!NOTE]  
>如果您在使用所提供的參數來執行預存程序後沒有獲得輸出，則可能有兩種情況。
>
>1. DW 參數包含無效的 SLO 值
>2. 或者，對於資料表上的 CCI 作業，沒有任何相符的資源類別。
>
>例如，在 DW100c 時，可用的最高記憶體授與是 1 GB，而如果資料表架構的寬度足以跨越 1 GB 的需求。

### <a name="usage-example"></a>使用範例

語法：  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: 提供 Null 參數以從 DW DB 解壓縮目前的 DWU，或以 ' DW100c ' 的形式提供任何支援的 DWU
2. @SCHEMA_NAME: 提供資料表的結構描述名稱
3. @TABLE_NAME: 提供相關的資料表名稱

執行此預存程序的範例：

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

下列陳述式會建立要在前述範例中使用的 Table1。
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>預存程序定義

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500 
  ELSE Mem*100 
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>後續步驟

如需管理資料庫使用者和安全性的詳細資訊，請參閱[在 SQL 分析中保護資料庫](./sql-data-warehouse-overview-manage-security.md)。 如需較大資源類別如何改善叢集資料行存放區索引品質的詳細資訊，請參閱[資料行存放區壓縮的記憶體最佳化](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md)。

