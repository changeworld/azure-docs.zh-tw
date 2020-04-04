---
title: 使用預存程序
description: 通過在 Synapse SQL 池中實現儲存過程來開發解決方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3283fbeec2226a825625b4e3ede6942a609ae723
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633447"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>在 Synapse SQL 池中使用儲存過程

本文提供了透過實現儲存過程來開發SQL池解決方案的提示。

## <a name="what-to-expect"></a>未來展望

SQL 池支援 SQL Server 中使用的許多 T-SQL 功能。 更重要的是有相應放大的特定功能，您可用來將解決方案效能最大化。

此外,為了説明您保持 SQL 池的規模和性能,還有其他具有行為差異的功能和功能。

## <a name="introducing-stored-procedures"></a>預存程序簡介

儲存過程是封裝 SQL 代碼的好方法,該代碼存儲在 SQL 池數據附近。 存儲過程還説明開發人員通過將代碼封裝到可管理單元中來模組化其解決方案,從而促進更高的代碼可重用性。 每個預存程序也可接受參數，使其更具彈性。

SQL 池提供了簡化和簡化的儲存過程實現。 與 SQL Server 相比,最大的區別是存儲過程不是預編譯代碼。

通常,對於數據倉庫,與針對大量數據卷運行查詢所需的時間相比,編譯時間較小。 更重要的是確保存儲過程代碼正確優化為大型查詢。

> [!TIP]
> 目標是要節省時數、分鐘數和秒數，而不是毫秒數。 因此,將儲存過程視為 SQL 邏輯的容器會很有説明。

當 SQL 池執行儲存過程時,SQL 語句將在運行時進行分析、轉換和優化。 在此過程中，每個陳述式都會轉換為分散式查詢。 針對資料執行的 SQL 程式碼與提交的查詢不同。

## <a name="nesting-stored-procedures"></a>巢狀預存程序

當預存程序呼叫其他預存程序或執行動態 SQL 時，內部預存程序或程式碼叫用據稱就是巢狀。

SQL 池最多支援八個嵌套級別。 相反,SQL Server 中的嵌套級別為 32。

最上層預存程序呼叫等同於巢狀層級 1。

```sql
EXEC prc_nesting
```

如果預存程序也會進行另一個 EXEC 呼叫，這會將巢狀層級提高到二。

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

如果第二個程序接著會執行一些動態 SQL，巢狀層級會提高到三。

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

SQL 池目前不支援[@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 因此,您需要跟蹤嵌套級別。 您不太可能超過八個嵌套級別限制。 但是,如果您這樣做,則需要重新編寫代碼以適合此限制中的嵌套級別。

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL 池不允許使用 INSERT 語句使用儲存過程的結果集。 但是,可以使用替代方法。 如需範例，請參閱[暫存資料表](sql-data-warehouse-tables-temporary.md)上的文章。

## <a name="limitations"></a>限制

Transact-SQL 儲存過程的某些方面未在 SQL 池中實現,如下所示:

* 暫存預存程序
* 編號預存程序
* 擴充預存程序
* CLR 預存程序
* 加密選項
* 複寫選項
* 資料表值參數
* 唯讀參數
* 預設參數
* 執行內容
* return 陳述式

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[開發概觀](sql-data-warehouse-overview-develop.md)。
