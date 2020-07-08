---
title: 使用預存程序
description: 在 Synapse SQL 集區中執行預存程式以開發解決方案的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 87c7eaa57f9da87bd83f89953afc09632d42b1f8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213392"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>在 Synapse SQL 集區中使用預存程式

本文提供藉由執行預存程式來開發 SQL 集區解決方案的秘訣。

## <a name="what-to-expect"></a>未來展望

SQL 集區支援 SQL Server 中使用的許多 T-sql 功能。 更重要的是有相應放大的特定功能，您可用來將解決方案效能最大化。

此外，為了協助您維護 SQL 集區的規模和效能，還有其他具有行為差異的特性和功能。

## <a name="introducing-stored-procedures"></a>預存程序簡介

預存程式是封裝 SQL 程式碼的絕佳方法，它會儲存在您的 SQL 集區資料附近。 預存程式也協助開發人員藉由將程式碼封裝成可管理的單位來模組化其解決方案，進而促進更好的程式碼重複使用 每個預存程序也可接受參數，使其更具彈性。

SQL 集區提供簡化且精簡的預存程式執行。 相較于 SQL Server，最大的差異是預存程式不是預先編譯器代碼。

一般來說，對於資料倉儲而言，編譯時間與針對大型資料磁片區執行查詢所花費的時間很小。 請務必確保預存程式程式碼已針對大型查詢進行正確的優化。

> [!TIP]
> 目標是要節省時數、分鐘數和秒數，而不是毫秒數。 因此，將預存程式視為 SQL 邏輯的容器是很有説明的。

當 SQL 集區執行您的預存程式時，SQL 語句會在執行時間進行剖析、轉譯和優化。 在此過程中，每個陳述式都會轉換為分散式查詢。 針對資料執行的 SQL 程式碼與提交的查詢不同。

## <a name="nesting-stored-procedures"></a>巢狀預存程序

當預存程序呼叫其他預存程序或執行動態 SQL 時，內部預存程序或程式碼叫用據稱就是巢狀。

SQL 集區最多支援八個嵌套層級。 相反地，SQL Server 中的嵌套層級為32。

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

SQL 集區目前不[支援 @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。 因此，您必須追蹤「嵌套」層級。 您不太可能會超過八個嵌套層級的限制。 但是，如果您這樣做，您必須重新修改程式碼，以符合此限制內的嵌套層級。

## <a name="insertexecute"></a>INSERT..EXECUTE

SQL 集區不允許您使用含有 INSERT 語句之預存程式的結果集。 不過，您可以使用另一種方法。 如需範例，請參閱[暫存資料表](sql-data-warehouse-tables-temporary.md)上的文章。

## <a name="limitations"></a>限制

Transact-sql 預存程式的某些層面並未在 SQL 集區中執行，如下所示：

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
