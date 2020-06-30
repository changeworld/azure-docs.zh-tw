---
title: 教學課程：準備在 R 中定型預測模型所需的資料
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程系列的第一部分中，您將準備 Azure SQL Database 內資料庫中的資料，以供您透過 Azure SQL Database 機器學習服務 (預覽) 在 R 中定型預測模型。
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
ms.date: 07/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 698cc089f770d60b6399864c9832fbc8d104c16f
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253795"
---
# <a name="tutorial-prepare-data-to-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：準備使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中定型預測模型所需的資料

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

在這個三部分教學課程系列的第一部分中，您會使用 R 來匯入和準備來自 Azure SQL Database 內資料庫中的資料。在本系列稍後的內容中，您則會使用此資料透過 Azure SQL Database 機器學習服務 (預覽) 在 R 中定型和部署預測性的機器學習模型。

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

在本教學課程系列中，我們假設您是滑雪設備的出租業者，且您想要預測未來某個日期的出租數目。 此資訊可協助您準備好庫存、員工和設備。

在本系列的第一和第二部分中，您會在 RStudio 中開發一些 R 指令碼，以便準備資料並定型機器學習模型。 然後，在第三部分中，則會使用預存程序在資料庫內執行這些 R 指令碼。

在本文中，您將學會如何：

> [!div class="checklist"]
>
> * 使用 R 將資料庫範例匯入到 Azure SQL Database 中的資料庫
> * 將資料庫中的資料載入到 R 資料框架中
> * 藉由依某些資料行分類來以 R 準備資料

在[第二部分](predictive-model-build-compare-tutorial.md)中，您將了解如何在 R 中建立及定型多個機器學習模型，然後選擇最精確的模型。

在[第三部分](predictive-model-deploy-tutorial.md)中，您會了解如何將模型儲存在資料庫中，然後從您在第一和第二部分中開發的 R 指令碼建立預存程序。 預存程序會在資料庫中執行，以根據新資料做出預測。

## <a name="prerequisites"></a>必要條件

* Azure 訂用帳戶 - 如果您沒有 Azure 訂用帳戶，請在開始前[建立帳戶](https://azure.microsoft.com/free/)。

* 已啟用[具備機器學習服務的 Azure SQL Database (搭配 R)](machine-learning-services-overview.md)。

* RevoScaleR 套件 - 請參閱 [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler?view=sql-server-2017#versions-and-platforms) 以了解在本機安裝此套件的選項。

* R IDE - 本教學課程使用 [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/)。

* SQL 查詢工具 - 本教學課程假設您使用 [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) 或 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

## <a name="sign-in-to-the-azure-portal"></a>登入 Azure 入口網站

登入 [Azure 入口網站](https://portal.azure.com/)。

## <a name="import-the-sample-database"></a>匯入範例資料庫

本教學課程使用的範例資料集已儲存至 **.bacpac** 資料庫備份檔案，供您下載及使用。

1. 下載檔案 [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac)。

1. 使用下列詳細資料，遵循[將 BACPAC 檔案匯入至 Azure SQL Database 或 Azure SQL 受控執行個體中的資料庫](../../azure-sql/database/database-import.md)：

   * 從您下載的 **TutorialDB.bacpac** 檔案匯入
   * 在公開預覽期間，請為新資料庫選擇 **Gen5/虛擬核心**組態
   * 將新資料庫命名為 "TutorialDB"

## <a name="load-the-data-into-a-data-frame"></a>將資料載入資料框架

若要在 R 中使用資料，請將資料庫中的資料載入資料框架 (`rentaldata`) 中。

在 RStudio 中建立新的 RScript 檔案，並執行下列指令碼。 將 **Server**、**UID** 和 **PWD** 取代為您自己的連線資訊。

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Logical SQL server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

您應該會看見如下所示的結果。

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## <a name="prepare-the-data"></a>準備資料

在此範例資料庫中，大部分的準備工作都已完成，但您在此將再執行一項準備。
使用下列 R 指令碼，藉由將資料類型變更為「因素」，將三個資料行識別為「類別」。

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

您應該會看見如下所示的結果。

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

資料現已備妥，可供訓練之用。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行此教學課程，請從您的伺服器刪除 TutorialDB 資料庫。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源] 或 [SQL 資料庫]。
1. 在 [依名稱篩選...] 欄位中輸入 **TutorialDB**，然後選取您的訂用帳戶。
1. 選取您的 TutorialDB 資料庫。
1. 在 [概觀] 頁面上，按一下 [刪除]。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第一部分中，您已完成下列步驟：

* 使用 R 將資料庫範例匯入到 Azure SQL Database 中的資料庫
* 將資料庫中的資料載入到 R 資料框架中
* 藉由依某些資料行分類來以 R 準備資料

若要建立會使用 TutorialDB 資料庫中所含資料的機器學習模型，請遵循本教學課程系列的第二部分：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立預測模型](predictive-model-build-compare-tutorial.md)
