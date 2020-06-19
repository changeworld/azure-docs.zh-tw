---
title: 使用具體化檢視進行效能調整
description: 使用具體化檢視改善查詢效能時須知的建議和考量事項。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 7a54d1d644d1069957db7f94d6f5e261e1a8dfb2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747538"
---
# <a name="performance-tuning-with-materialized-views"></a>使用具體化檢視進行效能調整

Synapse SQL 集區中的具體化檢視提供低維護方法，可以在不變更任何查詢的情況下，讓複雜的分析查詢有快速的效能。 本文討論使用具體化檢視的一般指引。

## <a name="materialized-views-vs-standard-views"></a>具體化檢視與標準檢視的比較

SQL 集區支援標準和具體化檢視。  兩者都是使用 SELECT 運算式建立的虛擬資料表，並以邏輯資料表形式呈現至查詢。  檢視會封裝一般資料計算的複雜性，並將抽象層新增至計算變更，因此不需要重新撰寫查詢。  

標準檢視會在每次使用檢視時計算其資料。  磁碟上沒有儲存任何資料。 人們通常會使用標準檢視作為協助整理資料庫中邏輯物件和查詢的工具。  若要使用標準檢視，查詢必須與其建立直接參考。

具體化檢視會在 SQL 集區中預先計算、儲存和維護其資料，就像資料表一樣。  每次使用具體化檢視時，都不需要重新計算。  這就是為什麼在具體化檢視中使用全部資料或資料子集的查詢其效能會加速的原因。  更棒的是，查詢可以使用具體化檢視，而無需建立直接參考，因此不需要變更應用程式程式碼。  

標準檢視的大部分需求仍適用於具體化檢視。 如需具體化檢視語法和其他需求的詳細資料，請參閱 [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

| 比較                     | 檢視                                         | 具體化檢視
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|檢視定義                 | 儲存於 SQL 集區。              | 儲存於 SQL 集區。
|檢視內容                    | 每次使用檢視時產生。   | 在建立視圖期間預先處理並儲存在 SQL 集區中。 當資料新增至基礎資料表時更新。
|資料重新整理                    | 一律更新                               | 一律更新
|加快從複雜查詢擷取檢視資料的速度     | 慢速                                         | 快速  
|額外儲存空間                   | 否                                           | 是
|語法                          | CREATE VIEW                                  | CREATE MATERIALIZED VIEW AS SELECT

## <a name="benefits-of-using-materialized-views"></a>使用具體化檢視的優點

設計正確的具體化檢視有以下優點：

- 使用聯結和彙總函式縮短複雜查詢的執行時間。 查詢越複雜，越有可能節省執行時間。 對於計算成本高但所產生資料集很小的查詢，就能獲得最大效益。  
- SQL 集區中的最佳化工具會自動使用已部署的具體化檢視來改善查詢執行計畫。  對於使用者而言，此流程是透明的，因此能加速查詢效能，而且不需要查詢就能直接參考具體化檢視。
- 只需對檢視進行低度維護。  基底資料表中的所有增量資料變更都會同步自動新增至具體化檢視。  這種設計可讓查詢具體化檢視傳回與直接查詢基底資料相同的資料。
- 可以使用不同的方式從基底資料表散發具體化檢視中的資料。  
- 具體化檢視的資料會與一般資料表的資料具備相同的高可用性和復原優點。  

在 SQL 集區中實作的具體化檢視還有下列額外優點：

相較於其他資料倉儲提供者，在 Azure SQL 資料倉儲中實作的具體化檢視還有下列額外優點：

- 當基底資料表中有資料變更時，系統能自動並同步進行資料重新整理。 使用者不必採取任何動作。
- 支援廣泛彙總涵式。 請參閱 [CREATE MATERIALIZED VIEW AS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。
- 支援查詢特定的具體化檢視建議。  請參閱 [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="common-scenarios"></a>常見的案例  

具體化檢視通常用於下列情況中：

**需要對龐大資料量改善複雜分析查詢時的效能**

複雜分析查詢通常會使用較多的彙總函式和資料表聯結，導致在查詢執行中進行更大量的重型計算作業，例如重組和聯結。  這就是為什麼這些查詢需要較長的時間才能完成，尤其是大型資料表。  

使用者可以為從一般查詢計算傳回的資料建立具體化檢視，因此當查詢需要此資料時，不需進行任何的重新計算，這可降低計算成本，並加速查詢回應。

**需要在不變更或小幅變更查詢的情況下取得更快的效能**

SQL 集區中結構描述和查詢變更通常保持在最低的狀況下，以支援一般的 ETL 作業和報告。  如果查詢效能的好處能夠抵銷檢視所產生的成本，使用者就可以使用具體化檢視調整查詢效能。

相較於其他調整選項 (例如縮放和統計資料管理)，具體化檢視在建立和維護上所造成的生產變更影響較小，且其潛在效能增益也較高。

- 建立或維護具體化檢視並不會影響對基底資料表執行的查詢。
- 查詢最佳化工具會自動使用已部署的具體化檢視，而不需要在查詢中直接檢視參考。 這項功能可減少在效能調整時進行查詢變更的需求。

**需要不同的資料散發策略，以提供更快的查詢效能**

SQL 集區是分散式大量平行處理 (MPP) 系統。   SQL 集區資料表中的資料會使用三個[散發策略](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (雜湊、round_robin 或複寫) 中的其中一種，散發在 60 個節點之間。  

資料散發是在資料表建立時指定的，並保持不變，直到卸載資料表為止。 具體化檢視在磁碟上是虛擬資料表，支援雜湊和 round_robin 資料散發。  使用者可以選擇與基底資料表不同但已針對使用檢視最多的查詢效能最佳化的資料散發。  

## <a name="design-guidance"></a>設計指引

以下是使用具體化檢視來改善查詢效能的一般指引：

**設計您的工作負載**

在您開始建立具體化檢視之前，請務必先深入了解查詢模式、重要性、頻率和所產生資料大小等方面的工作負載。  

使用者可以針對查詢最佳化工具建議的具體化檢視，執行說明 EXPLAIN WITH_RECOMMENDATIONS <SQL_statement>。  由於這些建議是針對查詢，因此對單一查詢有益的具體化檢視，對於位於相同工作負載中的其他查詢可能不會是最理想的。  

在評估這些建議時，請考量您的工作負載需求。  理想的具體化檢視是有助於工作負載效能的檢視。  

**請留意在查詢速度加快與成本之間的取捨**

每個具體化檢視都會有資料儲存成本，以及檢視維護成本。  當基底資料表中的資料改變時，具體化檢視的大小會增加，其實體結構也會改變。  為了避免查詢效能降低，每個具體化檢視都由 SQL 集區引擎分別維護。  

當具體化檢視和基底資料表變更的數目增加時，維護工作負載會增加。   使用者應該檢查所有具體化檢視所產生的成本是否可以透過提升查詢效能來抵銷。  

您可以針對資料庫中的具體化檢視清單執行此查詢：

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

減少具體化檢視數的選項：

- 在工作負載中找出複雜查詢會頻繁使用的一般資料集。  建立具體化檢視以儲存這些資料集，讓最佳化工具在建立執行計畫時，可以將這些檢視當做建置組塊使用。  

- 捨棄使用率低或不再需要的具體化檢視。  已停用的具體化檢視不再進行維護，但仍會產生儲存成本。  

- 結合在相同或相似基底資料表上建立的具體化檢視，即使其資料並未重疊。  合併後的具體化檢視會大於個別檢視的總和，但檢視維護成本應該會減少。  例如：

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**並非所有效能調整都需要查詢變更**

SQL 集區最佳化工具會自動使用已部署的具體化檢視來改善查詢效能。  這項支援是以透明的方式套用至不會參考檢視的查詢及使用彙總值 (在具體化檢視建立時不支援) 的查詢。  不需進行任何查詢變更。 您可以檢查查詢的估計執行計畫，以確認是否有使用具體化檢視。  

**監視具體化檢視**

具體化檢視會儲存在 SQL 集區中，就如同具有叢集資料行存放區索引 (CCI) 的資料表一樣。  從具體化檢視讀取資料包括了掃描 CCI 索引區段，及套用基底資料表中的任何增量變更。 當增量變更的數目過高時，從具體化檢視解析查詢所需的時間會比直接查詢基底資料表更長。  

為了避免查詢效能降低，最好是執行 [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 來監視檢視的 overhead_ratio (total_rows / max(1, base_view_row))。  如果具體化檢視的 overhead_ratio 過高，使用者可以重建具體化檢視。

**具體化檢視和結果集快取**

這兩個功能導入至 SQL 集區的時間幾乎與查詢效能調整同時。  結果集快取用於從對靜態資料進行的重複查詢中取得高度並行與快速的回應。  

若要使用快取的結果，要求查詢的快取形式必須符合產生快取的查詢。  此外，快取的結果必須套用至整個查詢。  

具體化檢視允許在基底資料表中變更資料。  具體化檢視中的資料可以套用至查詢的某個片段。  此支援可讓共用部分計算的不同查詢使用相同的具體化檢視，而使效能加速。

## <a name="example"></a>範例

這個範例會使用類似 TPCDS 的查詢來找出透過目錄的消費金額比到店消費金額高的客戶，以找出首選客戶及其來源國家/地區。   查詢會牽涉從三個與 SUM() 和 GROUP BY 相關的 sub-SELECT 陳述式中選取前 100 筆記錄。

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

檢查該查詢的估計執行計畫。  共有 18 個重組作業和 17 個聯結作業，會需要更多的執行時間。 現在讓我們一一為這三個 sub-SELECT 陳述式建立一個具體化檢視。

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

再次檢查原始查詢的執行計畫。  現在，聯結的數目會從 17 變更為 5，且不會再重組。  按一下計畫中的 [篩選] 作業圖示，其 [輸出清單] 會顯示從具體化檢視中讀取的資料，而不是從基底資料表。  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

有了具體化檢視，同一個查詢的執行速度會更快，而且不需要變更任何程式碼。  

## <a name="next-steps"></a>後續步驟

如需更多開發秘訣，請參閱 [Synapse SQL 集區開發概觀](sql-data-warehouse-overview-develop.md)。
