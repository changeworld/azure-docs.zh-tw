---
title: 使用 Synapse SQL 的 t-sql views
description: 搭配 Synapse SQL 使用 T-sql views 和開發解決方案的秘訣。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 84cd5c2de0b1a6d0909a31071506d98627966775
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/31/2020
ms.locfileid: "87500737"
---
# <a name="t-sql-views-using-synapse-sql"></a>使用 Synapse SQL 的 t-sql views
在本文中，您會找到使用 T-sql views 的秘訣，並使用 Synapse SQL 來開發解決方案。 

## <a name="why-use-views"></a>為何要使用 views

檢視能以許多不同的方式使用，提升您的方案品質。  本文強調幾個範例，說明如何使用 views 豐富您的解決方案，並包含需要考慮的限制。

### <a name="sql-pool---create-view"></a>SQL 集區-建立視圖

> [!NOTE]
> **SQL 集**區： CREATE VIEW 的語法不會在本文中討論。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

### <a name="sql-on-demand-preview---create-view"></a>SQL 隨選（預覽）-建立視圖

> [!NOTE]
> **SQL 隨選**：建立視圖的語法不會在本文中討論。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

## <a name="architectural-abstraction"></a>架構抽象概念

常見的應用程式模式是使用[CREATE TABLE 做為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) （CTAS）來重新建立資料表，在載入資料時，後面接著物件重新命名模式。

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

請記住，這種方法可能會導致資料表出現並從使用者的觀點消失，並提示「資料表不存在」錯誤訊息。 當基礎物件重新命名時，Views 可以用來為使用者提供一致的展示層。

藉由透過 views 提供資料的存取權，使用者就不需要看到基礎資料表。 除了一致的使用者體驗之外，這一層可確保分析設計人員可以進化資料模型。 進化基礎資料表的能力表示，設計人員可以使用 CTAS，在資料載入過程中將效能最大化。

## <a name="performance-optimization"></a>效能最佳化

Views 也可以用來強制執行資料表之間的效能優化聯結。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。

強制執行特定查詢或聯結提示是使用 T-sql views 的另一個優點。 因此，views 功能可確保一律以最佳方式執行聯結。 您可以避免使用者記住其聯結的正確結構。

## <a name="limitations"></a>限制

Synapse SQL 中的 Views 只會儲存為中繼資料。 因此，無法使用下列選項：

* 沒有架構系結選項
* 無法透過 view 更新基表
* 無法在臨時表上建立 Views
* 不支援 EXPAND/NOEXPAND 提示
* Synapse SQL 中沒有已編制索引的 views

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱 [Synapse SQL 開發概觀](develop-overview.md)。



