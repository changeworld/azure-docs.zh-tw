---
title: 用於在 Azure 突觸分析中開發突觸 SQL 池的資源
description: SQL 資料倉儲的開發概念、設計決策、建議和編碼技術。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 12f91633834ee98582eaad886b48eb8619378265
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411658"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Azure 突觸分析中突觸 SQL 池的設計決策和編碼技術 
 在本文中,您將找到其他資源,以説明您更好地瞭解 Azure Synapse 中 SQL 池的關鍵設計決策、建議和編碼技術。

## <a name="key-design-decisions"></a>主要的設計決策
以下文章重點介紹了使用 Azure Synapse 中的 SQL 池功能開發分散式資料倉庫的概念和設計決策:

* [連線](../sql/connect-overview.md)
* [併發](resource-classes-for-workload-management.md)
* [交易](sql-data-warehouse-develop-transactions.md)
* [使用者定義的架構](sql-data-warehouse-develop-user-defined-schemas.md)
* [資料表散發](sql-data-warehouse-tables-distribute.md)
* [資料表索引](sql-data-warehouse-tables-index.md)
* [資料表分割](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術
以下文章介紹開發 SQL 池的特定編碼技術、提示和建議:

* [預存程序](sql-data-warehouse-develop-stored-procedures.md)
* [標籤](sql-data-warehouse-develop-label.md)
* [檢視](sql-data-warehouse-develop-views.md)
* [暫存表](sql-data-warehouse-tables-temporary.md)
* [動態 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [迴圈](sql-data-warehouse-develop-loops.md)
* [依選項群組](sql-data-warehouse-develop-group-by-options.md)
* [變數賦值](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>後續步驟
有關詳細資訊參考,請參閱[T-SQL 語句](sql-data-warehouse-reference-tsql-statements.md)。
