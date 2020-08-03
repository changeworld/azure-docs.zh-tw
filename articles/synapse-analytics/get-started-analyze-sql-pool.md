---
title: 教學課程：開始使用 SQL 集區分析資料
description: 在此教學課程中，您將使用 NYC 計程車範例資料來探索 SQL 集區的分析功能。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: e2e1d0479b8edacaae8816d74db061eeedb805a7
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87325214"
---
# <a name="analyze-data-with-sql-pools"></a>使用 SQL 集區分析資料

Azure Synapse Analytics 可為您提供使用 SQL 集區分析資料的功能。 在此教學課程中，您將使用 NYC 計程車範例資料來探索 SQL 集區的分析功能。

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>將 NYC 計程車範例資料載入 SQLDB1 資料庫

1. 在 Synapse Studio 的最上方藍色功能表中，選取問號 ( **?** ) 圖示。
1. 選取 [開始使用] > [快速入門中樞]。
1. 在標示為 [查詢範例資料] 的卡片中，選取名為 **SQLDB1** 的 SQL 集區。
1. 選取 [查詢資料]。 「載入範例資料」通知會短暫地顯示。 靠近 Synapse Studio 頂端的淺藍色狀態列會指出正在將資料載入 SQLDB1。
1. 當狀態列變成綠色之後，請將其關閉。

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>探索 SQL 集區中的 NYC 計程車資料

1. 在 Synapse Studio 中，移至 [資料] 中樞。
1. 前往 [SQLDB1] > [資料表]。 您會看到數個已載入的資料表。
1. 以滑鼠右鍵按一下 [dbo.Trip] 資料表，然後選取 [新增 SQL 指令碼] > [選取前 100 個資料列]。
1. 請等到新 SQL 指令碼建立完成並開始執行。
1. 請注意，在 SQL 指令碼頂端的 [連線到] 會自動設定成名為 **SQLDB1** 的 SQL 集區。
1. 將 SQL 指令碼的文字取代為此程式碼並加以執行。

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    此查詢會顯示總行程距離和平均路程距離與乘客數的關聯。
1. 在 [SQL 指令碼結果] 視窗中，將 [視圖] 變更為 [圖表]，以折線圖形式查看結果的視覺效果。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用 Spark 進行分析](get-started-analyze-spark.md)

