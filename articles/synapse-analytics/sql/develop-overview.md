---
title: 開發突觸 SQL 功能的資源
description: Synapse SQL 的開發概念、設計決策、建議和編碼技術。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429014"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Azure 突觸分析中突觸 SQL 功能的設計決策和編碼技術
在本文中,您將找到 Synapse SQL 的 SQL 池和 SQL 按需(預覽)功能的資源清單。 推薦的文章分為兩個部分:關鍵設計決策以及開發和編碼技術。

這些文章的目標是説明您為 Synapse 分析中的 Synapse SQL 元件開發最佳技術方法。

## <a name="key-design-decisions"></a>主要的設計決策
以下文章重點介紹了 Synapse SQL 開發的概念和設計決策:

|                                                          |   SQL 池   | 依需 SQL |
| -----------------------------------------------------    | ---- | ---- |
| [連線](connect-overview.md)                    | 是 | 是 |
| [資源類和併發性](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 是    | 否 |
| [交易](develop-transactions.md)              | 是 | 否 |
| [使用者定義的結構描述](develop-user-defined-schemas.md) | 是 | 是 |
| [表分佈](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | 是 | 否 |
| [表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | 是 | 否 |
| [表分區](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | 是 | 否 |
| [統計資料](develop-tables-statistics.md)            | 是 | 是 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | 是 | 否 |
| [外部表](develop-tables-external-tables.md) | 是 | 是 |
| [CETAS](develop-tables-cetas.md)                     | 是 | 是 |


## <a name="recommendations"></a>建議

下面您將找到強調特定編碼技術、提示和開發建議的基本文章:

|                                            | SQL 池 | 依需 SQL |
| ------------------------------------------ | ------------------ | ----------------------- |
| [預存程序](develop-stored-procedures.md)  | 是                | 否                      |
| [標籤](develop-label.md)                           | 是                | 否                      |
| [檢視](develop-views.md)                             | 是                | 是                     |
| [暫存表](develop-tables-temporary.md)       | 是                | 是                     |
| [動態 SQL](develop-dynamic-sql.md)                 | 是                | 是                     |
| [循環](develop-loops.md)                         | 是                | 是                     |
| [依據選項分組](develop-group-by-options.md)       | 是                | 否                      |
| [變數指派](develop-variable-assignment.md) | 是                | 是                     |

## <a name="next-steps"></a>後續步驟
有關詳細資訊參考,請參閱[SQL 池 T-SQL 語句](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

