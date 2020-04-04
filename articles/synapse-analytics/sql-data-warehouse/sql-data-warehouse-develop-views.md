---
title: 使用 T-SQL 檢視
description: 在 Synapse SQL 池中使用 T-SQL 檢視和開發解決方案的提示。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633401"
---
# <a name="views-in-synapse-sql-pool"></a>Synapse SQL 池中的檢視

檢視能以許多不同的方式使用，提升您的方案品質。

SQL 池同時支持標準和具體化視圖。 兩者都是使用 SELECT 運算式創建的虛擬表,並作為邏輯表呈現給查詢。

視圖封裝了常見數據計算的複雜性,並將抽象層添加到計算更改中,因此無需重寫查詢。

## <a name="standard-view"></a>標準檢視

每次使用檢視時,標準視圖都會計算其數據。 磁碟上沒有存儲任何數據。 人們通常使用標準檢視作為工具,幫助組織資料庫中的邏輯對象和查詢。

要使用標準檢視,查詢需要直接引用它。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

SQL 池中的檢視僅存儲為元數據。 因此,以下選項不可用:

* 沒有結構描述繫結選項
* 無法透過檢視更新基表
* 無法透過暫存表建立檢視
* 不支援 EXPAND / NOEXPAND 提示
* SQL 池中沒有索引檢視

可以使用標準視圖在表之間強制實施性能優化聯接。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。

檢視的另一項好處是強制執行特定查詢或聯結提示。 以這種方式使用檢視，可確保一律以最佳方式執行聯結，使用者不需要記住其聯結的正確建構。

## <a name="materialized-view"></a>具體化檢視

具體化檢視在 SQL 池中預先計算、存儲和維護其數據,就像表一樣。 每次使用具體化視圖時,無需重新計算。

當數據載入到基表中時,SQL 池會同步刷新具體化視圖。  查詢優化器自動使用已部署的實物視圖來提高查詢性能,即使查詢中未引用這些視圖也是如此。  

從具體化檢視中受益最大的查詢是在生成小結果集的大型表上的複雜查詢(通常是具有聯接和聚合的查詢)。  

有關具體化檢視語法和其他要求的詳細資訊,請參閱[建立重要性檢視作為 SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  

有關查詢調優指導,選中[具有具體視圖的性能調優](performance-tuning-materialized-views.md)。

## <a name="example"></a>範例

常見的應用程式模式是使用 CREATE TABLE 作為 SELECT (CTAS) 重新建立表,然後在載入資料時重新命名物件模式。  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

但是,此方法可能會導致表出現並從使用者視圖中消失,同時發出"表不存在"錯誤消息。

視圖可用於在重命名基礎物件時為使用者提供一致的表示層。 通過檢視提供對數據的訪問,使用者不需要查看基礎表。

這一層提供一致的使用者體驗，同時確保資料倉儲設計人員能持續改進資料模型。 能夠改進基礎資料表，意味著設計人員可在資料載入過程中使用 CTAS 充分發揮效能。

## <a name="next-steps"></a>後續步驟

有關更多開發提示,請參閱[SQL 池開發概述](sql-data-warehouse-overview-develop.md)。
