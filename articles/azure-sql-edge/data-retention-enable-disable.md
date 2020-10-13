---
title: 啟用及停用資料保留原則-Azure SQL Edge
description: 瞭解如何在 Azure SQL Edge 中啟用和停用資料保留原則
keywords: SQL Edge，資料保留
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: ee2d65d66caef5cd9405d6e3d0e094de2e30ae87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90902498"
---
# <a name="enable-and-disable-data-retention-policies"></a>啟用及停用資料保留原則

本主題描述如何針對資料庫和資料表啟用和停用資料保留原則。 

## <a name="enable-data-retention-for-a-database"></a>啟用資料庫的資料保留

下列範例顯示如何使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)來啟用資料保留。

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>檢查資料庫是否已啟用資料保留

您可以使用下列命令來檢查是否已針對資料庫啟用資料保留
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>啟用資料表的資料保留

您必須針對要自動清除資料的每個資料表啟用資料保留。 在資料庫和資料表上啟用資料保留時，背景系統工作會定期掃描資料表，以找出並刪除任何過時的 (過時) 資料列。 您可以在資料表建立期間，使用 [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql) 或 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)來啟用資料保留。

下列範例顯示如何使用 [Create table](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql)來啟用資料表的資料保留。 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

`WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`Create table 命令的部分會設定資料表的資料保留。 此命令會使用下列必要參數 

- DATA_DELETION-指出資料保留是否為開啟或關閉。
- FILTER_COLUMN-資料表中資料行的名稱，將用來確定資料列是否已過時。 篩選資料行只能是具有下列資料類型的資料行 
    - Date
    - SmallDateTime
    - Datetime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD-整數值，後面接著單元描述元。 允許的單位為 [日]、[天]、[周]、[周]、[月]、[年]。

下列範例顯示如何使用 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)來啟用資料表的資料保留。  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>檢查是否已啟用資料表的資料保留

您可以使用下列命令來檢查已啟用資料保留的資料表

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

值 data_retention_period =-1，且 data_retention_period_unit 為無限，表示資料表上未設定資料保留期。

下列查詢可用來識別用來做為資料保留 filter_column 的資料行。 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>相互關聯 DB 和資料表資料保留設定

資料庫和資料表上的資料保留設定會搭配使用，以判斷是否要在資料表上執行過時資料列的自動清除。 

|資料庫選項 | 資料表選項 | 行為 |
|----------------|--------------|----------|
| OFF | OFF | 資料保留原則已停用，且已停用自動和手動清除過時記錄。|
| OFF | 開啟  | 資料表已啟用資料保留原則。 已停用已淘汰記錄的自動清除功能，不過手動清除方法可用來清除過時的記錄。 |
| 開啟 | OFF | 資料保留原則已在資料庫層級啟用。 不過，因為在資料表層級停用此選項，所以不會對過時資料列進行以保留為基礎的清除。|
| 開啟 | 開啟 | 同時針對資料庫和資料表啟用資料保留原則。 已啟用自動清除過時記錄。 |

## <a name="disable-data-retention-on-a-table"></a>停用資料表的資料保留 

您可以使用 [Alter table](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql)來停用資料表的資料保留。 下列命令可用來停用資料表的資料保留。

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>停用資料庫的資料保留

您可以使用 [Alter Database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options)來停用資料表的資料保留。 下列命令可用來停用資料庫的資料保留。

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>後續步驟
- [資料保留和自動資料清除](data-retention-overview.md)
- [使用保留原則管理歷程記錄資料](data-retention-cleanup.md)
