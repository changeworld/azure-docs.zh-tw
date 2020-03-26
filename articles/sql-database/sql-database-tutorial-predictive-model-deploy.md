---
title: 教學課程：在 R 中部署預測模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程的第三部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署預測模型。
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
ms.openlocfilehash: 9fa816b2a8e736f03c99b66b898f48bd2a483b31
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/24/2020
ms.locfileid: "68596765"
---
# <a name="tutorial-deploy-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署預測模型

在這個三部分教學課程的第三部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 SQL 資料庫中部署以 R 開發的預測模型。

您將使用內嵌的 R 指令碼建立預存程序，以使用模型進行預測。 您的模型執行於 Azure SQL 資料庫中，因此能夠根據儲存在資料庫中的資料輕易定型。

在本文中，藉由使用您在第一和第二部分中所開發的 R 指令碼，您會了解如何：

> [!div class="checklist"]
> * 建立會產生機器學習模型的預存程序
> * 將模型儲存在資料庫資料表中
> * 建立一個使用此模型進行預測的預存程序
> * 以新資料執行模型

在[第一部分](sql-database-tutorial-predictive-model-prepare-data.md)中，您已了解如何匯入資料庫範例，然後準備要用來在 R 中定型預測模型的資料。

在[第二部分](sql-database-tutorial-predictive-model-build-compare.md)中，您已了解如何在 R 中建立及定型多個機器學習模型，然後選擇最精確的模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

* 本教學課程系列的第三部分假設您已完成[**第一部分**](sql-database-tutorial-predictive-model-prepare-data.md)和[**第二部分**](sql-database-tutorial-predictive-model-build-compare.md)。

## <a name="create-a-stored-procedure-that-generates-the-model"></a>建立一個會產生模型的預存程序

在本教學課程系列的第二部分中，您已判定決策樹 (dtree) 是最精確的模型。 現在，請使用所開發的 R 指令碼建立預存程序 (`generate_rental_rx_model`)，以使用 RevoScaleR 套件中的 rxDTree 來定型及產生 dtree 模型。

在 Azure Data Studio 或 SSMS 中執行下列命令。

```sql
-- Stored procedure that trains and generates an R model using the rental_data and a decision tree algorithm
DROP PROCEDURE IF EXISTS generate_rental_rx_model;
GO
CREATE PROCEDURE generate_rental_rx_model (@trained_model VARBINARY(max) OUTPUT)
AS
BEGIN
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

rental_train_data$Holiday <- factor(rental_train_data$Holiday);
rental_train_data$Snow    <- factor(rental_train_data$Snow);
rental_train_data$WeekDay <- factor(rental_train_data$WeekDay);

#Create a dtree model and train it using the training data set
model_dtree <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = rental_train_data);
#Serialize the model before saving it to the database table
trained_model <- as.raw(serialize(model_dtree, connection=NULL));
'
        , @input_data_1 = N'
            SELECT RentalCount
                 , Year
                 , Month
                 , Day
                 , WeekDay
                 , Snow
                 , Holiday
            FROM dbo.rental_data
            WHERE Year < 2015
            '
        , @input_data_1_name = N'rental_train_data'
        , @params = N'@trained_model varbinary(max) OUTPUT'
        , @trained_model = @trained_model OUTPUT;
END;
GO
```

## <a name="store-the-model-in-a-database-table"></a>將模型儲存在資料庫資料表中

在 TutorialDB 資料庫中建立資料表，然後將模型儲存至資料表。

1. 建立用來儲存模型的資料表 (`rental_rx_models`)。

    ```sql
    USE TutorialDB;
    DROP TABLE IF EXISTS rental_rx_models;
    GO
    CREATE TABLE rental_rx_models (
          model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(MAX) NOT NULL
        );
    GO
    ```

1. 使用模型名稱 "rxDTree"，將模型以二進位物件的形式儲存至資料表。

    ```sql
    -- Save model to table
    TRUNCATE TABLE rental_rx_models;
    
    DECLARE @model VARBINARY(MAX);
    
    EXECUTE generate_rental_rx_model @model OUTPUT;
    
    INSERT INTO rental_rx_models (
          model_name
        , model
        )
    VALUES (
         'rxDTree'
        , @model
        );
    
    SELECT *
    FROM rental_rx_models;
    ```

## <a name="create-a-stored-procedure-that-makes-predictions"></a>建立一個進行預測的預存程序

建立會使用定型的模型和一組新資料進行預測的預存程序 (`predict_rentalcount_new`)。

```sql
-- Stored procedure that takes model name and new data as input parameters and predicts the rental count for the new data
DROP PROCEDURE IF EXISTS predict_rentalcount_new;
GO
CREATE PROCEDURE predict_rentalcount_new (
      @model_name VARCHAR(100)
    , @input_query NVARCHAR(MAX)
    )
AS
BEGIN
    DECLARE @rx_model VARBINARY(MAX) = (
            SELECT model
            FROM rental_rx_models
            WHERE model_name = @model_name
            );

    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
require("RevoScaleR");

#Convert types to factors
rentals$Holiday <- factor(rentals$Holiday);
rentals$Snow    <- factor(rentals$Snow);
rentals$WeekDay <- factor(rentals$WeekDay);

#Before using the model to predict, we need to unserialize it
rental_model <- unserialize(rx_model);

#Call prediction function
rental_predictions <- rxPredict(rental_model, rentals);
'
        , @input_data_1 = @input_query
        , @input_data_1_name = N'rentals'
        , @output_data_1_name = N'rental_predictions'
        , @params = N'@rx_model varbinary(max)'
        , @rx_model = @rx_model
    WITH RESULT SETS(("RentalCount_Predicted" FLOAT));
END;
GO
```

## <a name="execute-the-model-with-new-data"></a>以新資料執行模型

現在，您可以使用預存程序 `predict_rentalcount_new` 從新資料預測出租計數。

```sql
-- Use the predict_rentalcount_new stored procedure with the model name and a set of features to predict the rental count
EXECUTE dbo.predict_rentalcount_new @model_name = 'rxDTree'
    , @input_query = '
        SELECT CONVERT(INT,  3) AS Month
             , CONVERT(INT, 24) AS Day
             , CONVERT(INT,  4) AS WeekDay
             , CONVERT(INT,  1) AS Snow
             , CONVERT(INT,  1) AS Holiday
        ';
GO
```

您應該會看到如下的結果。

```results
RentalCount_Predicted
332.571428571429
```

您已在 Azure SQL 資料庫中成功建立、定型和部署模型。 接著您在預存程序中使用該模型根據新資料來預測值。

## <a name="clean-up-resources"></a>清除資源

在 TutorialDB 資料庫使用完畢後，請從您的 Azure SQL Database 伺服器中加以刪除。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源]  或 [SQL 資料庫]  。
1. 在 [依名稱篩選...]  欄位中輸入 **TutorialDB**，然後選取您的訂用帳戶。
1. 選取您的 TutorialDB 資料庫。
1. 在 [概觀]  頁面上，按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第三部分中，您已完成下列步驟：

* 建立會產生機器學習模型的預存程序
* 將模型儲存在資料庫資料表中
* 建立一個使用此模型進行預測的預存程序
* 以新資料執行模型

若要深入了解如何在 Azure SQL Database 機器學習服務 (預覽) 中使用 R，請參閱：

* [使用機器學習服務在 Azure SQL Database 中撰寫進階的 R 函式 (預覽)](sql-database-machine-learning-services-functions.md)
* [在 Azure SQL Database 機器學習服務 (預覽) 中使用 R 和 SQL 資料](sql-database-machine-learning-services-data-issues.md)
* [將 R 套件新增至 Azure SQL Database 機器學習服務 (預覽)](sql-database-machine-learning-services-add-r-packages.md)
