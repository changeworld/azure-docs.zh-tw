---
title: 使用具體化檢視進行效能微調
description: 在使用具體視圖以提高查詢性能時,應了解建議和注意事項。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 30ca03633b9b0788235439204a3c1926fe6b6a6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429976"
---
# <a name="performance-tuning-with-materialized-views"></a>使用具體化檢視進行效能微調

在 Synapse SQL 池中,具體化檢視為複雜的分析查詢提供了一種低維護方法,以在沒有任何查詢更改的情況下獲得快速性能。 本文討論了使用具體化視圖的一般指導。

## <a name="materialized-views-vs-standard-views"></a>具體檢視與標準檢視

SQL 池同時支持標準和具體化視圖。  兩者都是使用 SELECT 運算式創建的虛擬表,並作為邏輯表呈現給查詢。  視圖揭示了常見數據計算的複雜性,並將抽象層添加到計算更改中,因此無需重寫查詢。  

每次使用檢視時,標準視圖都會計算其數據。  磁碟上沒有存儲任何數據。 人們通常使用標準檢視作為工具,幫助組織資料庫中的邏輯對象和查詢。  要使用標準檢視,查詢需要直接引用它。

具體化檢視在 SQL 池中預先計算、存儲和維護其數據,就像表一樣。  每次使用具體化視圖時,都不需要重新計算。  這就是為什麼在具體化檢視中使用所有數據或子集的查詢可以獲得更快的性能。  更妙的是,查詢可以使用具體化視圖而不直接引用它,因此無需更改應用程式代碼。  

大多數標準視圖要求仍適用於具體化視圖。 有關具體化檢視語法和其他要求的詳細資訊,請參閱[建立重要性檢視作為 SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

| 比較                     | 檢視                                         | 具體化檢視
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|檢視定義                 | 存儲在 Azure 數據倉庫中。              | 存儲在 Azure 數據倉庫中。
|檢視內容                    | 每次使用檢視時生成。   | 在創建檢視期間在 Azure 數據倉庫中預處理並存儲在其中。 在將數據添加到基礎表中時更新。
|資料重新整理                    | 始終更新                               | 始終更新
|從複雜查詢檢索檢視資料的速度     | 緩慢                                         | 快速  
|額外儲存空間                   | 否                                           | 是
|語法                          | CREATE VIEW                                  | 建立影像檢視作為選擇

## <a name="benefits-of-using-materialized-views"></a>使用詳細化檢視的好處

正確設計的具體化檢視提供了以下好處:

- 使用 JOIN 和聚合函數縮短複雜查詢的執行時間。 查詢越複雜,執行時間節省的可能性就越高。 當查詢的計算成本高且生成的數據集較小時,將獲得最大的好處。  

- SQL 池中的優化程式可以自動使用已部署的實物視圖來改進查詢執行計劃。  此過程對提供更快查詢性能的使用者是透明的,並且不需要查詢直接引用具體化視圖。

- 需要對視圖進行低維護。  具體化檢視將資料儲存在兩個位置,一個在檢視創建時的初始數據的群集列存儲索引和增量數據更改的增量存儲。  基表的所有資料更改都以同步方式自動添加到增量存儲中。  後台進程(元組移動器)會定期將數據從增量存儲移動到視圖的列存儲索引。  此設計允許查詢具體化檢視返回與直接查詢基表相同的數據。
- 具體化視圖中的數據可以與基表不同分佈。  
- 具體化檢視中的數據獲得與常規表中的數據相同的高可用性和彈性優勢。  

與其他資料倉儲提供者相比,SQL 池中實現的具體化檢視還提供以下附加優勢:

- 自動和同步數據刷新,在基表中進行數據更改。 使用者不必採取任何動作。
- 廣泛的聚合功能支援。 請參閱[將具體化檢視創建為選擇(轉算 SQL)。](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- 對特定於查詢的具體具體具體化視圖建議的支援。  請參考[EXPLAIN(轉用 SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)。

## <a name="common-scenarios"></a>常見的案例  

具體化檢視通常用於以下方案:

**需要提高針對大數據的大小的複雜分析查詢的性能**

複雜的分析查詢通常使用更多的聚合函數和表聯接,從而導致更多的計算密集型操作,如隨機和聯接在查詢執行中。  這就是為什麼這些查詢需要更長的時間才能完成,特別是在大型表中。  

用戶可以為從查詢的常見計算返回的數據創建具體化檢視,因此當查詢需要此數據時不需要重新計算,從而允許更低的計算成本和更快的查詢回應。

**無需或最少更改查詢,需要更快的性能**

數據倉庫中的架構和查詢更改通常保持在最低限度,以支援常規 ETL 操作和報告。  如果視圖產生的成本可以通過查詢性能的提升來抵消,則使用者可以使用具體化視圖進行查詢性能調優。

與其他調優選項(如縮放和統計資訊管理)相比,創建和維護具體化視圖的影響要小得多,其潛在的性能收益也更高。

- 創建或維護具體化檢視不會影響針對基表運行的查詢。
- 查詢優化器可以自動使用已部署的具體化視圖,而無需在查詢中直接查看引用。 此功能減少了性能調優中查詢更改的需求。

**需要不同的資料分發策略,以加快查詢性能**

Azure 資料倉庫是一個分散式和大規模並行處理 (MPP) 系統。   數據倉庫表中的數據使用三種[分發策略](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)之一(哈希、round_robin或複製)分佈在 60 個節點上。  

數據分佈在表創建時指定,直到刪除表之前保持不變。 作為磁碟上的虛擬表的實物化視圖支援哈希和round_robin數據分發。  用戶可以選擇與基表不同的數據分佈,但最適合經常使用視圖的查詢的性能。  

## <a name="design-guidance"></a>設計指引

以下是有關使用具體化檢視提高查詢性能的一般指南:

**為您的工作負載設計**

在開始創建具體化視圖之前,在查詢模式、重要性、頻率和結果數據的大小方面深入瞭解工作負載非常重要。  

用戶可以為查詢優化器建議的具體視圖運行 EXPLAIN WITH_RECOMMENDATIONS<SQL_statement>。  由於這些建議特定於查詢,因此,對於同一工作負荷中的其他查詢,對單個查詢具有優勢的物化視圖可能不是最佳選擇。  

在考慮到您的工作負載需求時評估這些建議。  理想的具體化視圖是那些有利於工作負載性能的視圖。  

**注意快速查詢與成本之間的權衡**

對於每個具體化視圖,都有數據存儲成本和維護視圖的成本。  隨著基表中的數據變化,具體化視圖的大小增加,其物理結構也會發生變化。  

為了避免查詢性能下降,每個具體化檢視由數據倉庫引擎單獨維護,包括將行從增量存儲移動到列存儲索引段並合併數據更改。  

當具體檢視和基表更改的數量增加時,維護工作量會攀升。   使用者應檢查所有具體化視圖產生的成本是否可以被查詢性能提升抵消。  

您可以針對資料庫中的詳細化檢視清單執行此查詢:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

減少影像檢視數的選項:

- 確定工作負荷中複雜查詢經常使用的常見數據集。  創建具體化檢視以儲存這些資料集,以便優化器在創建執行計畫時將它們用作構建基塊。  

- 刪除使用率低或不再需要的占物視圖。  禁用的具體化視圖未維護,但仍會產生存儲成本。  

- 合併在同一或類似基表上創建的具體化視圖,即使它們的數據不重疊。  合併具體化視圖可能會導致比單獨視圖的總和更大的視圖,但視圖維護成本應降低。  例如：

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

**並非所有效能調優都需要查詢變更**

數據倉庫優化器可以自動使用已部署的實物視圖來提高查詢性能。  此支援透明地應用於不引用檢視的查詢,以及使用具體化視圖創建中不支援的聚合的查詢。  無需查詢更改。 可以檢查查詢的估計執行計劃,以確認是否使用了具體化視圖。  

**監控影像檢視**

具體化檢視儲存在數據倉庫中,就像具有群集列存儲索引 (CCI) 的表一樣。  從具體化檢視讀取數據包括掃描索引和應用增量存儲中的更改。  當增量存儲中的行數過高時,從具體化視圖解析查詢可能比直接查詢基表需要更長的時間。  為了避免查詢性能下降,最好運行[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)監視視圖的overhead_ratio(total_rows / base_view_row)。  如果overhead_ratio過高,請考慮重建具體化視圖,以便增量存儲中的所有行都移動到列存儲索引。  

**具體化檢視和結果集快取**

這兩個功能大約在同一時間在 SQL 池中引入,以便查詢性能調優。 結果集緩存用於實現對靜態數據的重複查詢的高併發和快速回應時間。  

要使用緩存的結果,快取請求查詢的形式必須與生成緩存的查詢匹配。  此外,緩存的結果必須應用於整個查詢。  具體化檢視允許在基表中更改數據。  具體化檢視中的數據可以應用於查詢的一段。  這種支援允許共用某些計算的不同查詢使用相同的具體化視圖,從而更快地實現性能。

## <a name="example"></a>範例

此示例使用類似 TPCDS 的查詢,該查詢查找通過目錄花費比在商店中花費更多資金的客戶。 它還標識首選客戶及其原籍國。   該查詢涉及從涉及 SUM() 和 GROUP BY 的三個子 SELECT 語句的 UNION 中選擇前 100 條記錄。

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

檢查查詢的估計執行計劃。  有 18 個洗牌和 17 個聯接操作,這需要更多時間執行。 現在,讓我們為三個子 SELECT 語句中的每個語句創建一個具體化視圖。

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

再次檢查原始查詢的執行計劃。  現在聯接的數量從 17 更改為 5,並且不再進行隨機播放。  按一下計劃中的「篩選器操作」圖示。 其輸出清單顯示從具體化檢視而不是基表讀取數據。  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

對於具體化檢視,相同的查詢運行速度會更快,而不會更改任何代碼。  

## <a name="next-steps"></a>後續步驟

有關更多開發提示,請參閱[Synapse SQL 開發概述](develop-overview.md)。
 