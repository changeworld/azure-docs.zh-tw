---
title: 開發 Synapse SQL 功能的資源
description: Synapse SQL 的開發概念、設計決策、建議和程式碼撰寫技巧。
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: f4c61b43855e4a845956a937d9d9cfe738d6f47a
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90032940"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>在 Azure Synapse Analytics 中 Synapse SQL 功能的設計決策和程式碼撰寫技術
在本文中，您可以找到 Synapse SQL 的 SQL 集區和 SQL 隨選 (預覽) 功能的資源清單。 建議的文章分為兩個部分：主要設計決策和開發和程式碼撰寫技巧。

這些文章的目標是協助您針對 Synapse 分析內的 Synapse SQL 元件開發最佳的技術方法。

## <a name="key-design-decisions"></a>主要的設計決策
下列文章強調 Synapse SQL 開發的概念和設計決策：

| 發行項 | SQL 集區 | SQL 隨選 |
| ------- | -------- | ------------- |
| [連線](connect-overview.md)                    | 是 | 是 |
| [資源類別和並行處理](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | 是    | 否 |
| [交易](develop-transactions.md)              | 是 | 否 |
| [使用者定義的結構描述](develop-user-defined-schemas.md) | 是 | 是 |
| [資料表散發](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | 是 | 否 |
| [資料表索引](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | 是 | 否 |
| [資料表的資料分割](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | 是 | 否 |
| [統計資料](develop-tables-statistics.md)            | 是 | 是 |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | 是 | 否 |
| [外部資料表](develop-tables-external-tables.md) | 是 | 是 |
| [CETAS](develop-tables-cetas.md)                     | 是 | 是 |


## <a name="recommendations"></a>建議

您可以在下面找到重要的文章，以強調開發的特定程式碼撰寫技術、秘訣和建議：

| 發行項 | SQL 集區 | SQL 隨選 |
| ------- | -------- | ------------- |
| [預存程序](develop-stored-procedures.md)  | 是                | 否                      |
| [標籤](develop-label.md)                           | 是                | 否                      |
| [檢視](develop-views.md)                             | 是                | 是                     |
| [暫存資料表](develop-tables-temporary.md)       | 是                | 是                     |
| [動態 SQL](develop-dynamic-sql.md)                 | 是                | 是                     |
| [迴圈](develop-loops.md)                         | 是                | 是                     |
| [依據選項分組](develop-group-by-options.md)       | 是                | 否                      |
| [變數指派](develop-variable-assignment.md) | 是                | 是                     |

## <a name="next-steps"></a>後續步驟
如需詳細的參考資訊，請參閱 [sql 集區 t-sql 語句](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)。

