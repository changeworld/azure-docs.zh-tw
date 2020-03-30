---
title: 用於在 Azure 同步分析中開發資料倉儲的資源
description: SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350286"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Azure 突觸分析中資料倉儲的設計決策和編碼技術 
 在本文中，您將找到其他資源，以説明您更好地瞭解 Azure Synapse 中資料倉儲的關鍵設計決策、建議和編碼技術。

## <a name="key-design-decisions"></a>主要的設計決策
以下文章重點介紹了使用 Azure Synaps 中的 SQL 分析功能開發分散式資料倉儲的概念和設計決策：

* [connections](sql-data-warehouse-connect-overview.md)
* [併發](resource-classes-for-workload-management.md)
* [交易](sql-data-warehouse-develop-transactions.md)
* [使用者定義的結構描述](sql-data-warehouse-develop-user-defined-schemas.md)
* [資料表散發](sql-data-warehouse-tables-distribute.md)
* [資料表索引](sql-data-warehouse-tables-index.md)
* [資料表分割](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計資料](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術
以下文章介紹使用 SQL Analytics 開發資料倉儲的特定編碼技術、提示和建議：

* [預存程序](sql-data-warehouse-develop-stored-procedures.md)
* [標籤](sql-data-warehouse-develop-label.md)
* [檢視](sql-data-warehouse-develop-views.md)
* [臨時表](sql-data-warehouse-tables-temporary.md)
* [動態 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [迴圈](sql-data-warehouse-develop-loops.md)
* [依據選項分組](sql-data-warehouse-develop-group-by-options.md)
* [變數指派](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>後續步驟
有關詳細資訊參考，請參閱[T-SQL 語句](sql-data-warehouse-reference-tsql-statements.md)。
