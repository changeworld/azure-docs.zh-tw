---
title: 在 Azure Synapse 分析中開發資料倉儲的資源
description: SQL 資料倉儲的開發概念、設計決策、建議和程式碼撰寫技巧。
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153310"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Azure Synapse 分析中資料倉儲的設計決策和程式碼撰寫技術 
 在本文中，您會找到其他資源，以協助您進一步瞭解 Azure Synapse 中資料倉儲的重要設計決策、建議和程式碼撰寫技術。

## <a name="key-design-decisions"></a>主要的設計決策
下列文章強調使用 Azure Synapse 中的 SQL 分析功能來開發分散式資料倉儲的概念和設計決策：

* [連線](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [交易](sql-data-warehouse-develop-transactions.md)
* [使用者定義的結構描述](sql-data-warehouse-develop-user-defined-schemas.md)
* [資料表散發](sql-data-warehouse-tables-distribute.md)
* [資料表索引](sql-data-warehouse-tables-index.md)
* [資料表分割](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計資料](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術
下列文章提供使用 SQL 分析開發資料倉儲的特定程式碼撰寫技術、秘訣和建議的功能：

* [預存程序](sql-data-warehouse-develop-stored-procedures.md)
* [標籤](sql-data-warehouse-develop-label.md)
* [檢視](sql-data-warehouse-develop-views.md)
* [暫存資料表](sql-data-warehouse-tables-temporary.md)
* [動態 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [迴圈](sql-data-warehouse-develop-loops.md)
* [依據選項分組](sql-data-warehouse-develop-group-by-options.md)
* [變數指派](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>後續步驟
如需更多的參考資訊，請參閱[t-sql 語句](sql-data-warehouse-reference-tsql-statements.md)。
