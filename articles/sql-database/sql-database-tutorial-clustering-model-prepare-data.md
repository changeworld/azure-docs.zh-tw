---
title: 教學課程：準備在 R 中執行群集所需的資料
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程系列的第一部分中，您將準備 Azure SQL 資料庫中的資料，以透過 Azure SQL Database 機器學習服務 (預覽) 在 R 中執行群集。
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
ms.openlocfilehash: 800dbfc05c47a949bf024e9a5c671979b49ad201
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68639982"
---
# <a name="tutorial-prepare-data-to-perform-clustering-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：準備使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中執行群集所需的資料

在這個三部分教學課程系列的第一部分中，您會使用 R 來匯入和準備來自 Azure SQL 資料庫中的資料。在本系列稍後的內容中，您則會使用此資料透過 Azure SQL Database 機器學習服務 (預覽) 在 R 中定型和部署群集模型。

*叢集*可以解釋成將資料組織成群組，而群組的成員在某些方面是相似的。
您將使用 **K-Means** 演算法在產品購買和退貨資料集中，執行客戶叢集。 透過將客戶叢集，您可以鎖定特定群組，以更有效率地專注於行銷工作。
K-Means 叢集是*非監督式學習*演算法，會根據相似性找出資料中的模式。

在本系列的第一和第二部分中，您會在 RStudio 中開發一些 R 指令碼，以便準備資料並定型機器學習模型。 然後，在第三部分中，則會使用預存程序在 SQL 資料庫內執行這些 R 指令碼。

在本文中，您將學會如何：

> [!div class="checklist"]
> * 將範例資料庫匯入 Azure SQL 資料庫中
> * 使用 R 依不同的維度區分客戶
> * 將 Azure SQL 資料庫中的資料載入到 R 資料框架中

在[第二部分](sql-database-tutorial-clustering-model-build.md)中，您將了解如何使用 R 建立和定型 K-Means 群集模型。

在[第三部分](sql-database-tutorial-clustering-model-deploy.md)中，您將了解如何使用 R 在 Azure SQL 資料庫中建立可根據新資料執行群集的預存程序。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

* Azure 訂用帳戶 - 如果您沒有 Azure 訂用帳戶，請在開始前[建立帳戶](https://azure.microsoft.com/free/)。

* 已啟用機器學習服務的 Azure SQL Database - 在公開預覽期間，Microsoft 會將您加入，並為您現有的或新的資料庫啟用機器學習服務。 請遵循[註冊預覽版](sql-database-machine-learning-services-overview.md#signup)中的步驟。

* RevoScaleR 套件 - 請參閱 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) 以了解在本機安裝此套件的選項。

* R IDE - 本教學課程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* SQL 查詢工具 - 本教學課程假設您使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="import-the-sample-database"></a>匯入範例資料庫

本教學課程使用的範例資料集已儲存至 **.bacpac** 資料庫備份檔案，供您下載及使用。 此資料集衍生自 [tpcx-bb](http://www.tpc.org/tpcx-bb/default.asp) 資料集 (由 [Transaction Processing Performance Council (TPC)](http://www.tpc.org/default.asp) 提供)。

1. 下載 [tpcxbb_1gb.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/tpcxbb_1gb.bacpac) 檔案。

1. 請依照[匯入 BACPAC 檔案以建立 Azure SQL 資料庫](https://docs.microsoft.com/azure/sql-database/sql-database-import)中的指示，並使用下列詳細資料：

   * 從您下載的 **tpcxbb_1gb.bacpac** 檔案匯入
   * 在公開預覽期間，請為新資料庫選擇 **Gen5/虛擬核心**組態
   * 將新資料庫命名為 "tpcxbb_1gb"

## <a name="separate-customers"></a>劃分客戶

在 RStudio 中建立新的 RScript 檔案，並執行下列指令碼。
在 SQL 查詢中，您將依據下列維度來區分客戶：

* **orderRatio** = 退貨訂單率 (部分退貨或全部退貨的訂單總數與訂單總數比較)
* **itemsRatio** = 退貨率 (退貨總數與購買項目數目比較)
* **monetaryRatio** = 退貨金額率 (退貨的貨幣金額總計與購買金額比較)
* **frequency** = 退貨頻率

在 **paste** 函式中，將 **Server**、**UID** 和 **PWD** 取代為您自己的連線資訊。

```r
# Define the connection string to connect to the tpcxbb_1gb database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=tpcxbb_1gb",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");


#Define the query to select data from SQL Server
input_query <- "
SELECT ss_customer_sk AS customer
    ,round(CASE 
            WHEN (
                       (orders_count = 0)
                    OR (returns_count IS NULL)
                    OR (orders_count IS NULL)
                    OR ((returns_count / orders_count) IS NULL)
                    )
                THEN 0.0
            ELSE (cast(returns_count AS NCHAR(10)) / orders_count)
            END, 7) AS orderRatio
    ,round(CASE 
            WHEN (
                     (orders_items = 0)
                  OR (returns_items IS NULL)
                  OR (orders_items IS NULL)
                  OR ((returns_items / orders_items) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_items AS NCHAR(10)) / orders_items)
            END, 7) AS itemsRatio
    ,round(CASE 
            WHEN (
                     (orders_money = 0)
                  OR (returns_money IS NULL)
                  OR (orders_money IS NULL)
                  OR ((returns_money / orders_money) IS NULL)
                 )
            THEN 0.0
            ELSE (cast(returns_money AS NCHAR(10)) / orders_money)
            END, 7) AS monetaryRatio
    ,round(CASE 
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
"
```

## <a name="load-the-data-into-a-data-frame"></a>將資料載入資料框架

現在請使用下列指令碼，利用 **rxSqlServerData** 函式將查詢的結果傳回至 R 資料框架。
在此程序中，您將定義所選資料行的類型 (使用 colClasses) 以確保類型會正確傳輸至 R。

```r
# Query SQL Server using input_query and get the results back
# to data frame customer_returns
# Define the types for selected columns (using colClasses),
# to make sure that the types are correctly transferred to R
customer_returns <- rxSqlServerData(
                     sqlQuery=input_query,
                     colClasses=c(customer ="numeric",
                                  orderRatio="numeric",
                                  itemsRatio="numeric",
                                  monetaryRatio="numeric",
                                  frequency="numeric" ),
                     connectionString=connStr);

# Transform the data from an input dataset to an output dataset
customer_data <- rxDataStep(customer_returns);

# Take a look at the data just loaded from SQL Server
head(customer_data, n = 5);
```

您應該會看見如下所示的結果。

```results
  customer orderRatio itemsRatio monetaryRatio frequency
1    29727          0          0      0.000000         0
2    26429          0          0      0.041979         1
3    60053          0          0      0.065762         3
4    97643          0          0      0.037034         3
5    32549          0          0      0.031281         4
```

## <a name="clean-up-resources"></a>清除資源

***如果您不打算繼續進行本教學課程***，請從 Azure SQL Database 伺服器中刪除 tpcxbb_1gb 資料庫。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源]  或 [SQL 資料庫]  。
1. 在 [依名稱篩選...]  欄位中輸入 **tpcxbb_1gb**，然後選取您的訂用帳戶。
1. 選取您的 **tpcxbb_1gb** 資料庫。
1. 在 [概觀]  頁面上，按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第一部分中，您已完成下列步驟：

* 將範例資料庫匯入 Azure SQL 資料庫中
* 使用 R 依不同的維度區分客戶
* 將 Azure SQL 資料庫中的資料載入到 R 資料框架中

若要建立會使用這項客戶資料的機器學習模型，請遵循本教學課程系列的第二部分：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立預測模型](sql-database-tutorial-clustering-model-build.md)
