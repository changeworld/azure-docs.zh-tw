---
title: 使用 SQL 集區的 t-sql 視圖
description: 在 Azure Synapse Analytics 中使用 T-sql 視圖和開發具有專用 SQL 集區和無伺服器 SQL 集區之解決方案的秘訣。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: 6fb75257a86e9e46d6c180a2a38193adecb2b36a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/01/2020
ms.locfileid: "96451670"
---
# <a name="t-sql-views-with-dedicated-sql-pool-and-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中具有專用 SQL 集區和無伺服器 SQL 集區的 t-sql 視圖

在本文中，您將找到使用 T-sql 視圖的秘訣，以及使用 Azure Synapse Analytics 中的專用 SQL 集區和無伺服器 SQL 集區來開發解決方案的秘訣。

## <a name="why-use-views"></a>為何要使用 views

檢視能以許多不同的方式使用，提升您的方案品質。  本文強調一些範例，說明如何使用視圖擴充您的解決方案，並包含需要考慮的限制。

### <a name="sql-pool---create-view"></a>SQL 集區-建立視圖

> [!NOTE]
> 本文中不會討論 CREATE VIEW 的語法。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

## <a name="architectural-abstraction"></a>架構抽象概念

常見的應用程式模式是使用 CREATE TABLE 重新建立資料表， [以選取](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) ，在載入資料時，會接著物件重新命名模式。

下列範例會將新的日期記錄加入至日期維度。 請注意新的資料表 DimDate_New 最初是如何建立，然後重新命名，以取代原始版本的資料表。

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;
```

請記住，此方法可能會導致資料表出現並從使用者的觀點中消失，而且會出現「資料表不存在」錯誤訊息的提示。 您可以使用 Views，在基礎物件重新命名時，為使用者提供一致的展示層。

藉由透過視圖提供資料的存取權，使用者就不需要基礎資料表的可見度。 除了一致的使用者體驗之外，這一層還可確保分析設計師可以發展資料模型。 發展基礎資料表的能力表示，設計人員可以使用 CTAS 在資料載入過程中發揮最大效能。

## <a name="performance-optimization"></a>效能最佳化

您也可以使用 Views 來強制資料表之間的效能優化聯結。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。

強制執行特定查詢或聯結提示是使用 T-sql 視圖的另一項優點。 因此，「視圖」功能可確保一律以最佳方式執行聯接。 您可以避免使用者需要記住其聯結的正確結構。

## <a name="limitations"></a>限制

Synapse SQL 中的 Views 只會儲存為中繼資料。 因此，無法使用下列選項：

* 沒有架構系結選項
* 無法透過視圖更新基表
* 無法在臨時表上建立 Views
* 不支援 EXPAND/NOEXPAND 提示
* Synapse SQL 中沒有任何索引視圖

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱 [Synapse SQL 開發概觀](develop-overview.md)。



