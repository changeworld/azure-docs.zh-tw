---
title: 使用預存程式
description: 在 Synapse SQL 中執行預存程式以開發解決方案的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288914"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>在 Synapse SQL 中使用預存程式

在 Synapse SQL 集區中執行預存程式的秘訣 (資料倉儲) 開發解決方案。

## <a name="what-to-expect"></a>預期的情況

Synapse SQL 支援許多 SQL Server 中使用的 T-sql 功能。 更重要的是有相應放大的特定功能，您可用來將解決方案效能最大化。

為了維護 SQL 集區的規模和效能，也有一些特性和功能有行為差異，其他則不受支援。

## <a name="stored-procedures-in-synapse-sql"></a>Synapse SQL 中的預存程式

預存程式很適合用來封裝您的 SQL 程式碼，並將其儲存在資料倉儲中的資料附近。 預存程式可協助開發人員將程式碼封裝成可管理的單位來模組化其解決方案，從而加速程式碼的重複使用性。 每個預存程序也可接受參數，使其更具彈性。 在下列範例中，您可以看到卸載外部物件的程式（如果它們存在於資料庫中）：

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

您可以使用語句來執行這些程式， `EXEC` 您可以在其中指定程式名稱和參數：

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL 提供簡化且簡化的預存程式執行。 相較於 SQL Server，最大差異是預存程序不是預先編譯的程式碼。 在資料倉儲中，相對於針對大型資料磁碟區執行查詢所花費的時間，編譯時間很少。 更重要的是，務必要針對大型查詢最佳化預存程序程式碼。 目標是要節省時數、分鐘數和秒數，而不是毫秒數。 因此，將預存程序視為 SQL 邏輯的容器更有幫助。

當 Synapse SQL 執行您的預存程式時，SQL 語句會在執行時間進行剖析、轉譯和優化。 在此過程中，每個陳述式都會轉換為分散式查詢。 針對資料執行的 SQL 程式碼與提交的查詢不同。

## <a name="encapsulate-validation-rules"></a>封裝驗證規則

預存程式可讓您在儲存在 SQL database 中的單一模組中找出驗證邏輯。 在下列範例中，您可以瞭解如何驗證參數的值，以及如何變更其預設值。

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

呼叫程式時，sql 程式中的邏輯會驗證輸入參數。

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>巢狀預存程序

當預存程序呼叫其他預存程序或執行動態 SQL 時，內部預存程序或程式碼叫用據稱就是巢狀。
下列程式碼顯示的是嵌套程式的範例：

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

此程式會接受代表某個名稱的參數，然後呼叫其他程式以使用這個名稱來卸載物件。
Synapse SQL 集區最多支援八個嵌套層級。 這項功能與 SQL Server 稍有不同。 SQL Server 中的巢狀層級為 32。

最上層預存程序呼叫等同於巢狀層級 1。

```sql
EXEC clean_up 'mytest'
```

如果預存程序也會進行另一個 EXEC 呼叫，這會將巢狀層級提高到二。

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

如果第二個程序接著會執行一些動態 SQL，巢狀層級會提高到三。

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL 目前不支援[@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。 您需要追蹤巢狀層級。 您不太可能會超過八個巢狀層級限制，但如果會，則需要修改您的程式碼，以讓巢狀層級符合這項限制。

## <a name="insertexecute"></a>INSERT..EXECUTE

Synapse SQL 不允許您使用 INSERT 語句來取用預存程式的結果集。 您可以使用替代方法。 如需範例，請參閱[暫存資料表](develop-tables-temporary.md)上的文章。

## <a name="limitations"></a>限制

在 Synapse SQL 中不會實作為 Transact-sql 預存程式的某些層面，例如：

* 暫存預存程序
* 編號預存程序
* 擴充預存程序
* CLR 預存程序
* 加密選項
* 複寫選項
* 資料表值參數
* 唯讀參數
* 布建集區中 (的預設參數) 
* 執行內容
* return 陳述式

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](develop-overview.md)。
