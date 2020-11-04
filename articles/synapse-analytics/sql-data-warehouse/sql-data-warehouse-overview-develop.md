---
title: 在 Azure Synapse Analytics 中開發專用 SQL 集區的資源
description: Azure Synapse Analytics 中專用 SQL 集區的開發概念、設計決策、建議和程式碼撰寫技巧。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f158655cd7aeba647480325966c7f0cfcf65b13c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322148"
---
# <a name="design-decisions-and-coding-techniques-for-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中專用 SQL 集區的設計決策和程式碼撰寫技術 

 在本文中，您將找到其他資源，以協助您更瞭解 Azure Synapse 中專用 SQL 集區的重要設計決策、建議和程式碼撰寫技術。

## <a name="key-design-decisions"></a>主要的設計決策

下列文章強調說明如何使用 Azure Synapse 中的專用 SQL 集區功能來開發分散式資料倉儲的概念和設計決策：

* [連接](../sql/connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [交易](sql-data-warehouse-develop-transactions.md)
* [使用者定義架構](sql-data-warehouse-develop-user-defined-schemas.md)
* [資料表散發](sql-data-warehouse-tables-distribute.md)
* [資料表索引](sql-data-warehouse-tables-index.md)
* [資料表資料分割](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [統計](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>開發建議和程式碼撰寫技術

下列文章提供開發專用 SQL 集區的特定程式碼撰寫技術、秘訣和建議：

* [預存程式](sql-data-warehouse-develop-stored-procedures.md)
* [標籤](sql-data-warehouse-develop-label.md)
* [檢視](performance-tuning-materialized-views.md)
* [臨時表](sql-data-warehouse-tables-temporary.md)
* [動態 SQL](sql-data-warehouse-develop-dynamic-sql.md)
* [迴圈](sql-data-warehouse-develop-loops.md)
* [分組依據選項](sql-data-warehouse-develop-group-by-options.md)
* [變數指派](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>後續步驟

如需詳細的參考資訊，請參閱 [t-sql 語句](sql-data-warehouse-reference-tsql-statements.md)。
