---
title: 使用預存程序
description: 在 Azure Synapse Analytics 中為專用的 SQL 集區執行預存程式，以開發解決方案的秘訣。
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673581"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中使用專用 SQL 集區的預存程式

本文提供透過執行預存程式來開發專用 SQL 集區解決方案的秘訣。

## <a name="what-to-expect"></a>預期的情況

專用的 SQL 集區支援 SQL Server 中使用的許多 T-sql 功能。 更重要的是有相應放大的特定功能，您可用來將解決方案效能最大化。

此外，為了協助您維護專用 SQL 集區的規模和效能，有一些具有行為差異的其他特性和功能。

## <a name="introducing-stored-procedures"></a>預存程序簡介

預存程式很適合用來封裝您的 SQL 程式碼，其儲存位置接近您專用的 SQL 集區資料。 預存程式也可協助開發人員將程式碼封裝成可管理的單位來模組化其解決方案，從而加速更多程式碼重複使用性。 每個預存程序也可接受參數，使其更具彈性。

專用的 SQL 集區提供簡化且簡化的預存程式執行。 相較于 SQL Server，最大的差異在於預存程式不是預先編譯的程式碼。

一般情況下，在資料倉儲中，相較于針對大型資料磁片區執行查詢所花費的時間，編譯時間比較小。 更重要的是，確保預存程式程式碼已針對大型查詢正確地優化。

> [!TIP]
> 目標是要節省時數、分鐘數和秒數，而不是毫秒數。 因此，將預存程式視為 SQL 邏輯的容器會很有説明。

當專用的 SQL 集區執行預存程式時，SQL 語句會在執行時間進行剖析、轉譯和優化。 在此過程中，每個陳述式都會轉換為分散式查詢。 針對資料執行的 SQL 程式碼與提交的查詢不同。

## <a name="nesting-stored-procedures"></a>巢狀預存程序

當預存程序呼叫其他預存程序或執行動態 SQL 時，內部預存程序或程式碼叫用據稱就是巢狀。

專用的 SQL 集區最多支援八個嵌套層級。 相反地，SQL Server 中的嵌套層級為32。

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

專用的 SQL 集區目前不支援[@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)。 因此，您需要追蹤嵌套層級。 不太可能會超過八個嵌套層級的限制。 但是，如果您這麼做，就必須修改程式碼，以符合此限制內的嵌套層級。

## <a name="insertexecute"></a>INSERT..EXECUTE

專用的 SQL 集區不允許您使用 INSERT 語句來取用預存程式的結果集。 不過，您可以使用替代方法。 如需範例，請參閱[暫存資料表](sql-data-warehouse-tables-temporary.md)上的文章。

## <a name="limitations"></a>限制

Transact-sql 預存程式的某些層面並非在專用的 SQL 集區中執行，如下所示：

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
