---
title: 使用具體化檢視進行效能微調
description: 在使用具體視圖以提高查詢性能時，應瞭解建議和注意事項。
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: e359537bbd0686d27825eee79e1cd35516ba6099
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350915"
---
# <a name="performance-tuning-with-materialized-views"></a>使用具體化檢視進行效能微調 
Azure SQL 資料倉儲中的具體化視圖為複雜的分析查詢提供了一種低維護方法，以在沒有任何查詢更改的情況下獲得快速性能。 本文討論了使用具體化視圖的一般指導。


## <a name="materialized-views-vs-standard-views"></a>具體視圖與標準視圖
Azure SQL 資料倉儲支援標準和具體化視圖。  兩者都是使用 SELECT 運算式創建的虛擬表，並作為邏輯表呈現給查詢。  視圖封裝了常見資料計算的複雜性，並將抽象層添加到計算更改中，因此無需重寫查詢。  

每次使用視圖時，標準視圖都會計算其資料。  磁片上沒有存儲任何資料。 人們通常使用標準視圖作為工具，説明組織資料庫中的邏輯物件和查詢。  要使用標準視圖，查詢需要直接引用它。 

具體化視圖在 Azure SQL 資料倉儲中預先計算、存儲和維護其資料，就像表一樣。  每次使用具體化視圖時，無需重新計算。  這就是為什麼在具體化視圖中使用所有資料或子集的查詢可以獲得更快的性能。  更妙的是，查詢可以使用具體化視圖而不直接引用它，因此無需更改應用程式代碼。  

標準視圖上的大多數要求仍適用于具體化視圖。 有關具體化視圖語法和其他要求的詳細資訊，請參閱[創建重要性視圖作為 SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)。



| 比較                     | 檢視                                         | 具體化檢視             
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------| 
|檢視定義                 | 存儲在 Azure 資料倉儲中。              | 存儲在 Azure 資料倉儲中。    
|檢視內容                    | 每次使用視圖時生成。   | 在創建視圖期間在 Azure 資料倉儲中預處理並存儲在其中。 在將資料添加到基礎資料表中時更新。                                             
|資料重新整理                    | 始終更新                               | 始終更新                          
|從複雜查詢檢索視圖資料的速度     | 緩慢                                         | 快速  
|額外存儲空間                   | 否                                           | 是                             
|語法                          | CREATE VIEW                                  | 創建具體化視圖作為選擇           
     
## <a name="benefits-of-using-materialized-views"></a>使用具體化視圖的好處

設計合理的具體視圖可以提供以下好處：

- 縮短具有 JOIN 和彙總函式的複雜查詢的執行時間。 查詢越複雜，執行時間節省的可能性就越高。 當查詢的計算成本高且生成的資料集較小時，將獲得最大的好處。  

- Azure SQL 資料倉儲中的優化程式可以自動使用已部署的實物視圖來改進查詢執行計畫。  此過程對提供更快查詢性能的使用者是透明的，並且不需要查詢直接引用具體化視圖。 

- 要求對視圖進行低維護。  基表的所有增量資料更改都以同步方式自動添加到具體化視圖中。  此設計允許查詢具體化視圖返回與直接查詢基表相同的資料。 
- 具體化視圖中的資料可以與基表不同分佈。  
- 具體化視圖中的資料獲得與常規表中的資料相同的高可用性和彈性優勢。  
 
與其他資料倉儲提供程式相比，在 Azure SQL 資料倉儲中實現的具體化視圖還提供以下其他好處： 

- 自動和同步資料刷新，在基表中進行資料更改。 使用者不必採取任何動作。 
- 廣泛的聚合功能支援。 請參閱[將具體化視圖創建為選擇（轉算 SQL）。](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest)
- 對特定于查詢的具體具體具體化視圖建議的支援。  請參閱[EXPLAIN（轉用 SQL）](https://docs.microsoft.com/sql/t-sql/queries/explain-transact-sql?view=azure-sqldw-latest)。

## <a name="common-scenarios"></a>常見的案例  

具體化視圖通常用於以下方案： 

**需要提高針對大資料的大小的複雜分析查詢的性能**

複雜的分析查詢通常使用更多的彙總函式和表聯接，從而導致更多的計算密集型操作，如隨機和聯接在查詢執行中。  這就是為什麼這些查詢需要更長的時間才能完成，特別是在大表中。  使用者可以為從查詢的常見計算返回的資料創建具體化視圖，因此當查詢需要此資料時無需重新計算，從而允許更低的計算成本和更快的查詢回應。 

**無需或最少更改查詢，需要更快的性能**

資料倉儲中的架構和查詢更改通常保持在最低限度，以支援常規 ETL 操作和報告。  如果視圖產生的成本可以通過查詢性能的提升來抵消，則可以使用具體化視圖進行查詢性能調優。 與其他調整選項（如縮放和統計資訊管理）相比，創建和維護具體化視圖的影響要小得多，其潛在的性能收益也更高。

- 創建或維護具體化視圖不會影響針對基表運行的查詢。
- 查詢最佳化工具可以自動使用已部署的具體化視圖，而無需在查詢中直接查看引用。 此功能減少了性能調優中查詢更改的需求。 

**需要不同的資料分發策略，以加快查詢性能**

Azure 資料倉儲是一個分散式大規模並行處理 （MPP） 系統。   資料倉儲表中的資料使用三種[分發策略](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute)之一（雜湊、round_robin或複製）分佈在 60 個節點上。  資料分佈在表創建時間指定，並保持不變，直到刪除表。 作為磁片上的虛擬表的實物化視圖支援雜湊和round_robin資料分發。  使用者可以選擇與基表不同的資料分佈，但最適合使用視圖的查詢的性能。  

## <a name="design-guidance"></a>設計指引 

以下是有關使用具體化視圖提高查詢性能的一般指南：

**為您的工作負載設計**

在開始創建具體化視圖之前，在查詢模式、重要性、頻率和結果資料的大小方面深入瞭解工作負載非常重要。  

使用者可以為查詢最佳化工具建議的具體視圖運行 EXPLAIN WITH_RECOMMENDATIONS<SQL_statement>。  由於這些建議特定于查詢，因此，對於同一工作負荷中的其他查詢，對單個查詢具有優勢的物化視圖可能不是最佳選擇。  在考慮到您的工作負載需求時評估這些建議。  理想的具體化視圖是那些有利於工作負載性能的視圖。  

**注意快速查詢與成本之間的權衡** 

對於每個具體化視圖，都有資料存儲成本和維護視圖的成本。  隨著基表中的資料變化，具體化視圖的大小增加，其物理結構也會發生變化。  為了避免查詢性能下降，每個具體化視圖由資料倉儲引擎單獨維護。  當具體化視圖和基表更改的數量增加時，維護工作負載會更高。   使用者應檢查所有具體化視圖產生的成本是否可以被查詢性能提升抵消。  

您可以針對資料庫中的具體化視圖清單運行此查詢： 

```sql
SELECT V.name as materialized_view, V.object_id 
FROM sys.views V 
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
``` 

減少具體化視圖數的選項： 

- 確定工作負荷中複雜查詢經常使用的常見資料集。  創建具體化視圖以存儲這些資料集，以便優化器在創建執行計畫時將它們用作構建基塊。  

- 刪除使用率低或不再需要的占物視圖。  禁用的具體化視圖未維護，但仍會產生存儲成本。  

- 合併在同一或類似基表上創建的具體化視圖，即使它們的資料不重疊。  組合具體化視圖可能會導致大小大於單獨視圖的總和，但視圖維護成本應降低。  例如：

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

**並非所有性能調優都需要查詢更改**

資料倉儲優化器可以自動使用已部署的實物視圖來提高查詢性能。  此支援透明地應用於不引用在具體視圖創建中不支援的聚合的視圖和查詢。  無需查詢更改。 可以檢查查詢的估計執行計畫，以確認是否使用了具體化視圖。  

**監控具體視圖** 

具體化視圖存儲在資料倉儲中，就像具有群集列存儲索引 （CCI） 的表一樣。  從具體化視圖讀取資料包括掃描 CCI 索引段並應用基表中的任何增量更改。 當增量更改數過高時，從具體化視圖解析查詢可能比直接查詢基表需要更長的時間。  為了避免查詢性能下降，最好運行[DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?view=azure-sqldw-latest)來監視視圖overhead_ratio（total_rows / 最大（1，base_view_row）。  如果使用者overhead_ratio過高，使用者應重新生成具體化視圖。 

**具體化視圖和結果集緩存**

這兩個功能大約在同一時間在 Azure SQL 資料倉儲中引入，以便查詢性能調優。  結果集緩存用於從針對靜態資料的重複查詢中獲得高併發和快速回應。  要使用緩存的結果，緩存請求查詢的形式必須與生成緩存的查詢匹配。  此外，緩存的結果必須應用於整個查詢。  具體化視圖允許在基表中更改資料。  具體化視圖中的資料可以應用於查詢的一段。  這種支援允許共用某些計算的不同查詢使用相同的具體化視圖，從而更快地實現性能。

## <a name="example"></a>範例

此示例使用類似 TPCDS 的查詢，該查詢查找通過目錄花費比在商店中花費更多資金的客戶，識別首選客戶及其原產國。   該查詢涉及從涉及 SUM（） 和 GROUP BY 的三個子 SELECT 語句的 UNION 中選擇前 100 條記錄。 

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

檢查查詢的估計執行計畫。  有 18 個洗牌和 17 個聯接操作，這需要更多時間執行。 現在，讓我們為三個子 SELECT 語句中的每個語句創建一個具體化視圖。   

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
再次檢查原始查詢的執行計畫。  現在聯接的數量從 17 更改為 5，並且不再進行隨機播放。  按一下計畫中的"篩選器"操作圖示，其輸出清單顯示資料是從具體視圖而不是基表讀取的。  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

對於具體化視圖，相同的查詢運行速度會更快，而不會更改任何代碼。  

## <a name="next-steps"></a>後續步驟
如需更多開發秘訣，請參閱 [SQL 資料倉儲開發概觀](sql-data-warehouse-overview-develop.md)。
