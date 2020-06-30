---
title: 教學課程：使用 R 部署群集模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程系列的第三部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署群集模型。
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 07/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2a90003c9de2cf0e942b518c6d4e9c3569039caf
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85251415"
---
# <a name="tutorial-deploy-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署群集模型
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在這個三部分教學課程系列的第三部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 Azure SQL Database 內的資料庫中部署以 R 開發的群集模型。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

您將使用內嵌的 R 指令碼建立預存程序，以執行群集。 您的模型執行於 Azure SQL Database 中，因此能夠根據儲存在資料庫中的資料輕易定型。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 建立一個會產生模型的預存程序
> * 在 SQL Database 中執行群集
> * 使用群集資訊

在[第一部分](clustering-model-prepare-data-tutorial.md)，您已了解如何準備資料庫中的資料，以執行群集。

在[第二部分](clustering-model-build-tutorial.md)中，您已了解如何使用 R 建立和定型 K-Means 群集模型。

## <a name="prerequisites"></a>必要條件

* 本教學課程系列的第三部分假設您已完成[**第一部分**](clustering-model-prepare-data-tutorial.md)和[**第二部分**](clustering-model-build-tutorial.md)。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>建立一個會產生模型的預存程序

執行下列 T-SQL 指令碼來建立預存程序。 此程序會重新建立您在本教學課程系列的第一部分和第二部分中所開發的步驟：

* 根據客戶的購買和退貨記錄來分類客戶
* 使用 K-Means 演算法產生客戶的四個叢集

此程序會將產生的客戶叢集對應儲存在資料庫資料表 **customer_return_clusters** 中。

```sql
USE [tpcxbb_1gb]
DROP PROC IF EXISTS generate_customer_return_clusters;
GO
CREATE procedure [dbo].[generate_customer_return_clusters]
AS
/*
  This procedure uses R to classify customers into different groups
  based on their purchase & return history.
*/
BEGIN
    DECLARE @duration FLOAT
    , @instance_name NVARCHAR(100) = @@SERVERNAME
    , @database_name NVARCHAR(128) = db_name()
-- Input query to generate the purchase history & return metrics
    , @input_query NVARCHAR(MAX) = N'
SELECT ss_customer_sk AS customer,
    round(CASE
            WHEN (
                    (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio,
    round(CASE
            WHEN (
                    (orders_items = 0)
                    OR (returns_items IS NULL)
                    OR (orders_items IS NULL)
                    OR ((returns_items / orders_items) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio,
    round(CASE
            WHEN (
                    (orders_money = 0)
                    OR (returns_money IS NULL)
                    OR (orders_money IS NULL)
                    OR ((returns_money / orders_money) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio,
    round(CASE
            WHEN (returns_count IS NULL)
                THEN 0.0
            ELSE returns_count
            END, 0) AS frequency
FROM (
    SELECT ss_customer_sk,
        -- return order ratio
        COUNT(DISTINCT (ss_ticket_number)) AS orders_count,
        -- return ss_item_sk ratio
        COUNT(ss_item_sk) AS orders_items,
        -- return monetary amount ratio
        SUM(ss_net_paid) AS orders_money
    FROM store_sales s
    GROUP BY ss_customer_sk
    ) orders
LEFT OUTER JOIN (
    SELECT sr_customer_sk,
        -- return order ratio
        count(DISTINCT (sr_ticket_number)) AS returns_count,
        -- return ss_item_sk ratio
        COUNT(sr_item_sk) AS returns_items,
        -- return monetary amount ratio
        SUM(sr_return_amt) AS returns_money
    FROM store_returns
    GROUP BY sr_customer_sk
    ) returned ON ss_customer_sk = sr_customer_sk
 '
EXECUTE sp_execute_external_script
      @language = N'R'
    , @script = N'
# Define the connection string
connStr <- paste("Driver=SQL Server; Server=", instance_name,
               "; Database=", database_name,
               "; Trusted_Connection=true; ",
                  sep="" );

# Input customer data that needs to be classified.
# This is the result we get from the query.
customer_returns <- RxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);
# Output table to hold the customer cluster mappings
return_cluster = RxSqlServerData(table = "customer_return_clusters",
                                 connectionString = connStr);

# Set seed for random number generator for predictability
set.seed(10);

# Generate clusters using rxKmeans and output clusters to a table
# called "customer_return_clusters"
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters = 4,
                   outFile = return_cluster,
                   outColName = "cluster",
                   writeModelVars = TRUE ,
                   extraVarsToWrite = c("customer"),
                   overwrite = TRUE);
'
    , @input_data_1 = N''
    , @params = N'@instance_name nvarchar(100), @database_name nvarchar(128), @input_query nvarchar(max), @duration float OUTPUT'
    , @instance_name = @instance_name
    , @database_name = @database_name
    , @input_query = @input_query
    , @duration = @duration OUTPUT;
END;

GO
```

## <a name="perform-clustering-in-sql-database"></a>在 SQL Database 中執行群集

現在您已建立預存程序，接下來請執行下列程式碼以執行群集。

```sql
--Empty table of the results before running the stored procedure
TRUNCATE TABLE customer_return_clusters;

--Execute the clustering
--This will load the table customer_return_clusters with cluster mappings
EXECUTE [dbo].[generate_customer_return_clusters];
```

請確認作業順利執行，而且我們已取得客戶及其叢集對應的清單。

```sql
--Select data from table customer_return_clusters
--to verify that the clustering data was loaded
SELECT TOP (5) *
FROM customer_return_clusters;
```

```result
cluster  customer  orderRatio  itemsRatio  monetaryRatio  frequency
1        29727     0           0           0              0
4        26429     0           0           0.041979       1
2        60053     0           0           0.065762       3
2        97643     0           0           0.037034       3
2        32549     0           0           0.031281       4
```

## <a name="use-the-clustering-information"></a>使用群集資訊

由於您已將群集程序儲存在資料庫中，因此它可以有效率地針對儲存在相同資料庫中的客戶資料執行群集。 每當客戶資料更新時，您都可以執行此程序，並使用更新的群集資訊。

假設您想要將促銷電子郵件傳送給叢集 3 中的客戶，也就是退貨行為較為頻繁的群組 (您可以在[第二部分](clustering-model-build-tutorial.md#analyze-the-results)的說明中查看這四個叢集的行為)。 下列程式碼會選取叢集 3 中所含客戶的電子郵件地址。

```sql
USE [tpcxbb_1gb]

SELECT customer.[c_email_address],
    customer.c_customer_sk
FROM dbo.customer
JOIN [dbo].[customer_return_clusters] AS r ON r.customer = customer.c_customer_sk
WHERE r.cluster = 3
```

您可以變更 **r.cluster** 的值，以傳回其他叢集中所含客戶的電子郵件地址。

## <a name="clean-up-resources"></a>清除資源

完成此教學課程後，您可以從伺服器刪除 tpcxbb_1gb 資料庫。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源] 或 [SQL 資料庫]。
1. 在 [依名稱篩選...] 欄位中輸入 **tpcxbb_1gb**，然後選取您的訂用帳戶。
1. 選取您的 **tpcxbb_1gb** 資料庫。
1. 在 [概觀] 頁面上，按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第三部分中，您已完成下列步驟：

* 建立一個會產生模型的預存程序
* 在 SQL Database 中執行群集
* 使用群集資訊

若要深入了解如何在 Azure SQL Database 機器學習服務 (預覽) 中使用 R，請參閱：

* [教學課程：準備使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中定型預測模型所需的資料](predictive-model-prepare-data-tutorial.md)
* [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](machine-learning-services-functions.md)
* [在 Azure SQL Database 機器學習服務 (預覽) 中使用 R 和 SQL 資料](machine-learning-services-data-issues.md)
* [將 R 套件新增至 Azure SQL Database 機器學習服務 (預覽)](machine-learning-services-add-r-packages.md)
