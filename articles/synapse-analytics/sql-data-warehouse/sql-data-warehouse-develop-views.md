---
title: 使用 T-SQL 視圖
description: 在 Azure SQL 資料倉儲中使用 T-SQL 檢視開發解決方案的秘訣。
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
ms.openlocfilehash: 9eccc225c5473291b7535339ff64a036730894ae
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351525"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Azure SQL 資料倉儲中的檢視
檢視能以許多不同的方式使用，提升您的方案品質。 

Azure SQL 資料倉儲支援標準和具體化視圖。 兩者都是使用 SELECT 運算式創建的虛擬表，並作為邏輯表呈現給查詢。 視圖封裝了常見資料計算的複雜性，並將抽象層添加到計算更改中，因此無需重寫查詢。

## <a name="standard-view"></a>標準視圖
每次使用視圖時，標準視圖都會計算其資料。 磁片上沒有存儲任何資料。 人們通常使用標準視圖作為工具，説明組織資料庫中的邏輯物件和查詢。 要使用標準視圖，查詢需要直接引用它。 如需詳細資訊，請參閱 [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) 文件。

SQL 資料倉儲中的檢視只會儲存為中繼資料。 因此無法使用下列選項︰
* 沒有結構描述繫結選項
* 無法透過檢視更新基底資料表
* 無法在暫存資料表上建立檢視
* 不支援 EXPAND / NOEXPAND 提示
* SQL 資料倉儲中沒有索引檢視表

可以使用標準視圖在表之間強制實施性能優化聯接。 例如，檢視可以納入備援散發金鑰作為聯結準則的一部分，以將資料移動最小化。 檢視的另一項好處是強制執行特定查詢或聯結提示。 以這種方式使用檢視，可確保一律以最佳方式執行聯結，使用者不需要記住其聯結的正確建構。

## <a name="materialized-view"></a>具體化檢視
具體化視圖在 Azure SQL 資料倉儲中預先計算、存儲和維護其資料，就像表一樣。 每次使用具體化視圖時，無需重新計算。 當資料載入到基表中時，Azure SQL 資料倉儲會同步刷新具體化視圖。  查詢最佳化工具自動使用已部署的具體化視圖來提高查詢性能，即使查詢中未引用這些視圖也是如此。  從具體化視圖中受益最大的查詢是在生成小結果集的大型表上的複雜查詢（通常是具有聯接和聚合的查詢）。  

有關具體化視圖語法和其他要求的詳細資訊，請參閱[創建重要性視圖作為 SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。  

有關查詢調優指導，選中[具有具體視圖的性能調優](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)。 

## <a name="example"></a>範例
常見的應用程式模式就是在載入資料時，使用後面接著物件重新命名模式的 CREATE TABLE AS SELECT (CTAS) 來重建資料表。  下列範例會將新的日期記錄加入至日期維度。 請注意新的資料表 DimDate_New 最初是如何建立，然後重新命名，以取代原始版本的資料表。

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
不過，此方法可能導致資料表在使用者的檢視中出現和消失，還有「資料表不存在」錯誤訊息。 儘管基礎物件已重新命名，檢視可為使用者提供一致的展示層。 透過檢視存取資料，意味著使用者不需要看見基礎資料表。 這一層提供一致的使用者體驗，同時確保資料倉儲設計人員能持續改進資料模型。 能夠改進基礎資料表，意味著設計人員可在資料載入過程中使用 CTAS 充分發揮效能。   

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀](sql-data-warehouse-overview-develop.md)。


