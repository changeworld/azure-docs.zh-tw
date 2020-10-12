---
title: 使用交易
description: 在 SQL 集區 (資料倉儲) 中實作交易以便開發解決方案的祕訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: de36d1eda21903480eee986df72c5274e1aa6dff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91288608"
---
# <a name="use-transactions-in-sql-pool"></a>使用 SQL 集區中的交易

在 SQL 集區 (資料倉儲) 中實作交易以便開發解決方案的祕訣。

## <a name="what-to-expect"></a>未來展望

如您所預期，SQL 集區支援交易作為資料倉儲工作負載的一部分。 不過，為了確保 SQL 集區的效能維持在一定的程度，某些功能會受到限制 (相較於 SQL Server)。 本文特別強調差異，並列出其他交易。

## <a name="transaction-isolation-levels"></a>交易隔離層級

SQL 集區實作 ACID 交易。 交易式支援的隔離等級預設為 READ UNCOMMITTED。  您可在連線至 master 資料庫時，開啟使用者資料庫的 [READ_COMMITTED_SNAPSHOT] 資料庫選項，將其變更為 [READ COMMITTED SNAPSHOT ISOLATION]。  

啟用之後，此資料庫中所有交易都會在 READ COMMITTED SNAPSHOT ISOLATION 的狀態下執行，且將不會接受在工作階段層級上設定為 READ UNCOMMITTED。 如需詳細資料，請參閱 [ALTER DATABASE SET 選項 (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest&preserve-view=true)。

## <a name="transaction-size"></a>交易大小
單一資料修改交易的大小是有限制的。 每個散發都會套用的限制。 如此一來，就可以藉由將限制乘以分佈計數來計算總配置。 

若要大致估計交易中的資料列總數，請將散發容量除以每個資料列的大小總計。 針對可變動的長度資料行，請考慮取得平均資料行長度，而不是使用大小上限。

在下表中，已進行下列假設：

* 已發生的資料平均散發
* 平均資料列長度是 250 個位元組

## <a name="gen2"></a>Gen2

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 每個散發的容量 (GB) | 散發的數目 | 交易大小上限 (GB) | 每個散發的資料列數 | 每個交易的資料列數上限 |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4,500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6,750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 每個散發的容量 (GB) | 散發的數目 | 交易大小上限 (GB) | 每個散發的資料列數 | 每個交易的資料列數上限 |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3.75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7.5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

系統會針對每個交易或作業套用交易大小限制。 它不會套用到所有並行交易。 因此允許每一個交易在記錄檔中寫入這個資料量。

若要優化寫入記錄的資料量，並將其最小化，請參閱「 [交易最佳做法](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) 」一文。

> [!WARNING]
> 交易大小上限僅可針對 HASH 或 ROUND_ROBIN 散發資料表 (資料會平均分佈) 來達成。 如果交易是以扭曲方式將資料寫入散發，則可能會在到達交易大小上限之前就先達到限制。
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>交易狀態

SQL 集區會使用 XACT_STATE() 函式 (採用值 -2) 來報告失敗的交易。 這個值表示交易已失敗並標示為僅可復原。

> [!NOTE]
> XACT_STATE 函式使用 -2 表示失敗的交易，以代表 SQL Server 中不同的行為。 SQL Server 使用值 -1 來代表無法認可的交易。 SQL Server 可以容忍交易內的某些錯誤，而不需將其標示為無法認可。 例如，`SELECT 1/0` 會導致錯誤，但不會強制交易進入無法認可的狀態。 SQL Server 也允許讀取無法認可的交易。 不過，SQL 集區不會讓您這麼做。 如果 SQL 集區交易內部發生錯誤，就會自動進入 -2 狀態，而且您將無法進行任何進一步的 Select 陳述式，直到陳述式為止。 因此，檢查您的應用程式程式碼是否使用 XACT_STATE() 就相當重要，因為您可能需要修改程式碼。

比方說，在 SQL Server 中，您可能會看到如下所示的交易：

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

前述程式碼會產生下列錯誤訊息：

Msg 111233, Level 16, State 1, Line 1 111233; 目前的交易已經中止，並已回復任何暫止的變更。 原因：處於僅限復原狀態中的交易，未在 DDL、DML 或 SELECT 語句之前明確復原。

您不會收到 ERROR_* 函式的輸出。

在 SQL 集區中，程式碼必須稍微改變：

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

目前已觀察到預期的行為。 已管理交易中的錯誤，且 ERROR_* 函式提供了預期的值。

唯一的變更就是交易的 ROLLBACK 必須在讀取 CATCH 區塊中的錯誤資訊之前發生。

## <a name="error_line-function"></a>Error_Line() 函式

另外值得注意的是，SQL 集區不會實作或支援 ERROR_LINE() 函式。 如果您的程式碼中有此函式，您必須將它移除，使其符合 SQL 集區的規範。 在程式碼中使用查詢標籤，而不需實作對等的功能。 如需詳細資訊，請參閱 [標籤](develop-label.md) 文章。

## <a name="use-of-throw-and-raiserror"></a>使用 THROW 和 RAISERROR

THROW 是在 SQL 集區中引發例外狀況的新式實作，但也支援 RAISERROR。 不過，有一些值得注意的差異。

* 使用者自訂錯誤訊息數目不能在 100000-150000 範圍中擲回
* RAISERROR 錯誤訊息固定為 50,000
* 不支援使用 sys.messages

## <a name="limitations"></a>限制

SQL 集區有一些與交易相關的其他限制。 如下所示：

* 沒有分散式交易
* 不允許巢狀交易
* 不允許儲存點
* 沒有具名的交易
* 沒有標示的交易
* 使用者定義的交易內部不支援 DDL，例如 CREATE TABLE

## <a name="next-steps"></a>後續步驟

若要深入了解最佳化交易，請參閱[交易的最佳做法](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。 另外也提供 [SQL 集區](best-practices-sql-pool.md)和 [SQL 隨選 (預覽)](best-practices-sql-on-demand.md) 的其他最佳做法指南。
