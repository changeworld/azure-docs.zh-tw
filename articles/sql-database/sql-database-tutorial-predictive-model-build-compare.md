---
title: 教學課程：在 R 中定型和比較預測模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程系列的第二部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立兩個預測模型，然後選取最精確的模型。
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
ms.openlocfilehash: 0985b37280e3cd363ba1728a5ec33b0012611ab2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "81452922"
---
# <a name="tutorial-create-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建立預測模型

在這個三部分教學課程系列的第二部分中，您將在 R 中建立兩個預測模型，然後選取最精確的模型。 在本系列的下一個部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 SQL 資料庫中部署此模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

在本文中，您將學會如何：

> [!div class="checklist"]
> * 定型兩個機器學習模型
> * 從這兩種模型進行預測
> * 比較結果以選擇最精確的模型

在[第一部分](sql-database-tutorial-predictive-model-prepare-data.md)中，您已了解如何匯入資料庫範例，然後準備要用來在 R 中定型預測模型的資料。

在[第三部分](sql-database-tutorial-predictive-model-deploy.md)中，您會了解如何將模型儲存在資料庫中，然後從您在第一和第二部分中開發的 R 指令碼建立預存程序。 預存程序會在 SQL 資料庫中執行，以根據新資料做出預測。

## <a name="prerequisites"></a>Prerequisites

* 本教學課程的第二部分假設您已完成[**第一部分**](sql-database-tutorial-predictive-model-prepare-data.md)及其必要條件。

## <a name="train-two-models"></a>定型兩個模型

若要找出滑雪設備出租資料的最佳模型，請建立兩個不同的模型 (線性迴歸和決策樹)，並查看何者的預測較精確。 您將使用您在本系列的第一部分中建立的資料框架 `rentaldata`。

```r
#First, split the dataset into two different sets:
# one for training the model and the other for validating it
train_data = rentaldata[rentaldata$Year < 2015,];
test_data  = rentaldata[rentaldata$Year == 2015,];

#Use the RentalCount column to check the quality of the prediction against actual values
actual_counts <- test_data$RentalCount;

#Model 1: Use rxLinMod to create a linear regression model, trained with the training data set
model_linmod <- rxLinMod(RentalCount ~  Month + Day + WeekDay + Snow + Holiday, data = train_data);

#Model 2: Use rxDTree to create a decision tree model, trained with the training data set
model_dtree  <- rxDTree(RentalCount ~ Month + Day + WeekDay + Snow + Holiday, data = train_data);
```

## <a name="make-predictions-from-both-models"></a>從這兩種模型進行預測

使用預測函式和每個定型模型來預測出租計數。

```r
#Use both models to make predictions using the test data set.
predict_linmod <- rxPredict(model_linmod, test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

predict_dtree  <- rxPredict(model_dtree,  test_data, writeModelVars = TRUE, extraVarsToWrite = c("Year"));

#To verify it worked, look at the top rows of the two prediction data sets.
head(predict_linmod);
head(predict_dtree);
```

```results
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1         27.45858          42       2     11     4      0       0
2        387.29344         360       3     29     1      0       0
3         16.37349          20       4     22     4      0       0
4         31.07058          42       3      6     6      0       0
5        463.97263         405       2     28     7      1       0
6        102.21695          38       1     12     2      1       0
    RentalCount_Pred  RentalCount  Month  Day  WeekDay  Snow  Holiday
1          40.0000          42       2     11     4      0       0
2         332.5714         360       3     29     1      0       0
3          27.7500          20       4     22     4      0       0
4          34.2500          42       3      6     6      0       0
5         645.7059         405       2     28     7      1       0
6          40.0000          38       1     12     2      1       0
```

## <a name="compare-the-results"></a>比較結果

現在，您想要查看哪個模型做出最佳預測。 為此，使用基本繪圖函式來檢視您定型資料中的實際值與預測值之間的差異，將是快速輕鬆的方式。

```r
#Use the plotting functionality in R to visualize the results from the predictions
par(mfrow = c(2, 1));
plot(predict_linmod$RentalCount_Pred - predict_linmod$RentalCount, main = "Difference between actual and predicted. rxLinmod");
plot(predict_dtree$RentalCount_Pred  - predict_dtree$RentalCount,  main = "Difference between actual and predicted. rxDTree");
```

![比較兩個模型](./media/sql-database-tutorial-predictive-model-build-compare/compare-models.png)

看來決策樹模型是兩個模型中較精確的一個。

## <a name="clean-up-resources"></a>清除資源

如果您不打算繼續進行本教學課程，請從 Azure SQL Database 伺服器中刪除 TutorialDB 資料庫。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源]  或 [SQL 資料庫]  。
1. 在 [依名稱篩選...]  欄位中輸入 **TutorialDB**，然後選取您的訂用帳戶。
1. 選取您的 TutorialDB 資料庫。
1. 在 [概觀]  頁面上，按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第二部分中，您已完成下列步驟：

* 定型兩個機器學習模型
* 從這兩種模型進行預測
* 比較結果以選擇最精確的模型

若要部署您所建立的機器學習模型，請遵循此教學課程系列的第三部分：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署預測模型](sql-database-tutorial-predictive-model-deploy.md)
