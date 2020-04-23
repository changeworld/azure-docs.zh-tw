---
title: 教學課程：使用 R 建置叢集模型
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: 在這個三部分教學課程系列的第二部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建置用來執行群集的 K-Means 模型。
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
ms.openlocfilehash: ebea6117420ee6de67025dfd4cfba71e905cb9ec
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453092"
---
# <a name="tutorial-build-a-clustering-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中建置群集模型

在這三部分教學課程系列的第二部分中，您將使用 R 建立 K-Means 模型來執行群集。 在本系列的下一個部分中，您將使用 Azure SQL Database 機器學習服務 (預覽) 在 SQL 資料庫中部署此模型。

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

在本文中，您將學會如何：

> [!div class="checklist"]
> * 為 K-Means 演算法定義叢集數目
> * 執行叢集
> * 分析結果

在[第一部分](sql-database-tutorial-clustering-model-prepare-data.md)中，您已了解如何在 Azure SQL 資料庫中準備執行群集所需的資料。

在[第三部分](sql-database-tutorial-clustering-model-deploy.md)中，您將了解如何使用 R 在 Azure SQL 資料庫中建立可根據新資料執行群集的預存程序。

## <a name="prerequisites"></a>Prerequisites

* 本教學課程的第二部分假設您已完成[**第一部分**](sql-database-tutorial-clustering-model-prepare-data.md)及其必要條件。

## <a name="define-the-number-of-clusters"></a>定義叢集數目

若要為您的客戶資料進行叢集化，您將使用 **K-Means** 叢集演算法，這是對資料進行分組的最簡單、最廣為人知的方法之一。
您可以在 [K-means 叢集演算法的完整指南](https://www.kdnuggets.com/2019/05/guide-k-means-clustering-algorithm.html)中深入了解 K-Means。

此演算法接受兩個輸入：資料本身，以及預先定義的數字「*k*」代表要產生的叢集數目。
輸出為 *k* 個叢集，包含在叢集之間分割的輸入資料。

若要對要使用的演算法判斷其叢集數目，請使用「群組平方和」內的繪圖，並以擷取的叢集數目為依據。 要使用的適當叢集數目是在繪圖的折彎處或「肘線」處。

```r
# Determine number of clusters by using a plot of the within groups sum of squares,
# by number of clusters extracted. 
wss <- (nrow(customer_data) - 1) * sum(apply(customer_data, 2, var))
for (i in 2:20)
    wss[i] <- sum(kmeans(customer_data, centers = i)$withinss)
plot(1:20, wss, type = "b", xlab = "Number of Clusters", ylab = "Within groups sum of squares")
```

![肘線圖](./media/sql-database-tutorial-clustering-model-build/elbow-graph.png)

根據圖表，*k = 4* 看起來是理想的嘗試值。 *k* 值會將客戶分組成四個叢集。

## <a name="perform-clustering"></a>執行叢集

在下列 R 指令碼中，您將使用 **rxKmeans** 函式，這是 RevoScaleR 套件中的 K-Means 函式。

```r
# Output table to hold the customer group mappings.
# This is a table where the cluster mappings will be saved in the database.
return_cluster = RxSqlServerData(table = "return_cluster", connectionString = connStr);
# Set the seed for the random number generator for predictability
set.seed(10);
# Generate clusters using rxKmeans and output key / cluster to a table in SQL database
# called return_cluster
clust <- rxKmeans( ~ orderRatio + itemsRatio + monetaryRatio + frequency,
                   customer_returns,
                   numClusters=4,
                   outFile=return_cluster,
                   outColName="cluster",
                   extraVarsToWrite=c("customer"),
                   overwrite=TRUE);

# Read the customer returns cluster table from the database
customer_cluster <- rxDataStep(return_cluster);
```

## <a name="analyze-the-results"></a>分析結果

現在您已使用 K-Means 完成群集，下一個步驟是分析結果，並確認是否可以找到任何可採取動作的資訊。

**clust** 物件包含 K-Means 群集的結果。

```r
#Look at the clustering details to analyze results
clust
```

```results
Call:
rxKmeans(formula = ~orderRatio + itemsRatio + monetaryRatio + 
    frequency, data = customer_returns, outFile = return_cluster, 
    outColName = "cluster", extraVarsToWrite = c("customer"), 
    overwrite = TRUE, numClusters = 4)
Data: customer_returns
Number of valid observations: 37336
Number of missing observations: 0 
Clustering algorithm:  

K-means clustering with 4 clusters of sizes 31675, 671, 2851, 2139
Cluster means:
   orderRatio   itemsRatio monetaryRatio frequency
1 0.000000000 0.0000000000    0.00000000  0.000000
2 0.007451565 0.0000000000    0.04449653  4.271237
3 1.008067345 0.2707821817    0.49515232  1.031568
4 0.000000000 0.0004675082    0.10858272  1.186068
Within cluster sum of squares by cluster:
         1          2          3          4
    0.0000  1329.0160 18561.3157   363.2188
```

使用[第一部分](sql-database-tutorial-clustering-model-prepare-data.md#separate-customers)中定義的變數以提供四種叢集平均值：

* *orderRatio* = 退貨訂單率 (部分退貨或全部退貨的訂單總數與訂單總數比較)
* *itemsRatio* = 退貨率 (退貨總數與購買項目數目比較)
* *monetaryRatio* = 退貨金額率 (退貨的貨幣金額總計與購買金額比較)
* *frequency* = 退貨頻率

使用 K-Means 的資料採礦經常需要進一步分析結果，並採取更多步驟，以深入了解每個叢集，但可以提供一些良好的潛在客戶。
您可以透過以下幾種方式來解譯這些結果：

* 叢集 1 (最大的叢集) 似乎是不活躍的客戶群組 (所有值皆為零)。
* 叢集 3 似乎是在退貨行為方面比較明顯的群組。

## <a name="clean-up-resources"></a>清除資源

***如果您不打算繼續進行本教學課程***，請從 Azure SQL Database 伺服器中刪除 tpcxbb_1gb 資料庫。

在 Azure 入口網站中執行下列步驟：

1. 在 Azure 入口網站的左側功能表中，選取 [所有資源]  或 [SQL 資料庫]  。
1. 在 [依名稱篩選...]  欄位中輸入 **tpcxbb_1gb**，然後選取您的訂用帳戶。
1. 選取您的 **tpcxbb_1gb** 資料庫。
1. 在 [概觀]  頁面上，按一下 [刪除]  。

## <a name="next-steps"></a>後續步驟

在本教學課程系列的第二部分中，您已完成下列步驟：

* 為 K-Means 演算法定義叢集數目
* 執行叢集
* 分析結果

若要部署您所建立的機器學習模型，請遵循此教學課程系列的第三部分：

> [!div class="nextstepaction"]
> [教學課程：使用 Azure SQL Database 機器學習服務 (預覽) 在 R 中部署群集模型](sql-database-tutorial-clustering-model-deploy.md)