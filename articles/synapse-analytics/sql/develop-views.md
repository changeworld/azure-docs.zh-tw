---
title: 使用突觸 SQL 的 T-SQL 檢視
description: 使用 T-SQL 檢視和使用 Synapse SQL 開發解決方案的提示。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428650"
---
# <a name="t-sql-views-using-synapse-sql"></a>使用突觸 SQL 的 T-SQL 檢視
在本文中,您將找到使用 T-SQL 檢視和使用 Synapse SQL 開發解決方案的提示。 

## <a name="why-use-views"></a>為何要使用檢視？

檢視能以許多不同的方式使用，提升您的方案品質。  本文重點介紹了一些如何使用視圖豐富解決方案的示例,並包括需要考慮的限制。

### <a name="sql-pool---create-view"></a>SQL 池 - 建立檢視

> [!NOTE]
> **SQL 池**:本文中未討論 CREATE VIEW 的語法。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

### <a name="sql-on-demand-preview---create-view"></a>SQL 依需(預覽) - 建立檢視

> [!NOTE]
> **SQL 按需**:本文中未討論 CREATE VIEW 的語法。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

## <a name="architectural-abstraction"></a>架構抽象概念

常見的應用程式模式是使用[CREATE TABLE 作為 SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS) 重新建立表,在載入資料時,後面是物件重命名模式。

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

請記住,此方法可能會導致表出現並從使用者視圖中消失,並提示"表不存在"錯誤消息。 視圖可用於在重命名基礎物件時為使用者提供一致的表示層。

通過檢視提供對數據的訪問,使用者不需要查看基礎表。 除了一致的用戶體驗外,此層還可確保分析設計人員能夠改進數據模型。 發展基礎表的能力意味著設計人員可以使用 CTAS 在數據載入過程中最大限度地提高性能。

## <a name="performance-optimization"></a>效能最佳化

視圖還可用於在表之間強制實施性能優化聯接。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。

強制特定查詢或聯接提示是使用 T-SQL 檢視的另一個好處。 因此,視圖功能可確保始終以最佳方式執行聯接。 您將避免使用者記住其聯接的正確構造。

## <a name="limitations"></a>限制

Synapse SQL 中的檢視僅存儲為中繼資料。 因此,以下選項不可用:

* 沒有架構繫結選項
* 無法透過檢視更新基表
* 無法透過暫存表建立檢視
* 不支援 EXPAND/ NOEXPAND 提示
* Synapse SQL 中沒有索引檢視

## <a name="next-steps"></a>後續步驟

有關更多開發提示,請參閱[Synapse SQL 開發概述](develop-overview.md)。



