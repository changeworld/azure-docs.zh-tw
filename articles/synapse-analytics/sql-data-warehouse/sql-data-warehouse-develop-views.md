---
title: 使用 T-sql views
description: 在 Synapse SQL 集區中使用 T-sql views 和開發解決方案的秘訣。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212287"
---
# <a name="views-in-synapse-sql-pool"></a>Synapse SQL 集區中的 Views

檢視能以許多不同的方式使用，提升您的方案品質。

SQL 集區支援標準檢視和具體化檢視。 兩者都是使用 SELECT 運算式建立的虛擬資料表，並以邏輯資料表形式呈現至查詢。

檢視會封裝一般資料計算的複雜性，並將抽象層新增至計算變更，因此不需要重新撰寫查詢。

## <a name="standard-view"></a>標準視圖

標準檢視會在每次使用檢視時計算其資料。 磁碟上沒有儲存任何資料。 人們通常會使用標準檢視作為協助整理資料庫中邏輯物件和查詢的工具。

若要使用標準檢視，查詢必須與其建立直接參考。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 文件。

SQL 集區中的 Views 只會儲存為中繼資料。 因此，無法使用下列選項：

* 沒有結構描述繫結選項
* 無法透過 view 更新基表
* 無法在臨時表上建立 Views
* 不支援 EXPAND / NOEXPAND 提示
* SQL 集區中沒有已編制索引的 views

您可以使用標準視圖來強制執行資料表之間的效能優化聯結。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。

檢視的另一項好處是強制執行特定查詢或聯結提示。 以這種方式使用檢視，可確保一律以最佳方式執行聯結，使用者不需要記住其聯結的正確建構。

## <a name="materialized-view"></a>具體化檢視

具體化檢視會在 SQL 集區中預先計算、儲存和維護其資料，就像資料表一樣。 每次使用具體化檢視時，都不需要重新計算。

當資料載入基表時，SQL 集區會同步重新整理具體化的視圖。  查詢最佳化工具會自動使用已部署的具體化視圖來改善查詢效能，即使查詢中未參考這些 views 也一樣。  

查詢最能受益于具體化 views 的查詢（通常是具有聯結和匯總的查詢）會在產生小型結果集的大型資料表上進行。  

如需具體化檢視語法和其他需求的詳細資料，請參閱 [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。  

如需查詢微調指導方針，請參閱[使用具體化視圖進行效能微調](performance-tuning-materialized-views.md)。

## <a name="example"></a>範例

常見的應用程式模式是在載入資料時，使用 CREATE TABLE AS SELECT （CTAS）來重新建立資料表，後面接著物件重新命名模式。  

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

不過，這種方法可能會導致資料表出現並從使用者的觀點消失，以及發出「資料表不存在」錯誤訊息。

當基礎物件重新命名時，Views 可以用來為使用者提供一致的展示層。 藉由透過 views 提供資料的存取權，使用者就不需要看到基礎資料表。

這一層提供一致的使用者體驗，同時確保資料倉儲設計人員能持續改進資料模型。 能夠改進基礎資料表，意味著設計人員可在資料載入過程中使用 CTAS 充分發揮效能。

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱[SQL 集區開發總覽](sql-data-warehouse-overview-develop.md)。
