---
title: 教學課程：開始使用無伺服器 SQL 分析資料
description: 在本教學課程中，您將了解如何使用位於 Spark 資料庫中的資料，透過隨選 SQL 來分析資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 9c42d1d988bc280d5e62c24f109225d91cb446ce
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893487"
---
# <a name="analyze-data-with-sql-on-demand"></a>使用隨選 SQL 來分析資料

在本教學課程中，您將了解如何使用無伺服器 SQL 搭配隨選 SQL 集區，使用位於 Spark 資料庫中的資料來分析資料。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-sql-on-demand-pool"></a>使用 SQL 隨選集區分析 Blob 儲存體中的紐約市計程車資料

1. 在 [連結] 下的 [資料] 中樞中，以滑鼠右鍵按一下 [Azure Blob 儲存體 > 範例資料集 > nyc_tlc_yellow] 並選取 [選取前 100 個資料列]
1. 這會使用下列程式碼建立新的 SQL 指令碼：

    ```
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. 按一下 [執行] 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>使用 SQL 隨選分析 Spark 資料庫中的 NYC 計程車資料

Spark 資料庫中的資料表會自動顯示，且可由 SQL 隨選進行查詢。

1. 在 Synapse Studio 中，移至 [開發] 中樞，然後建立新的 SQL 指令碼。
1. 將 [連線到] 設定為 [SQL 隨選]。
1. 將下列文字貼到指令碼中，然後執行指令碼。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 當您第一次執行使用 SQL 隨選的查詢時，SQL 隨選需要大約 10 秒的時間，才能收集執行查詢所需的 SQL 資源。 後續查詢的速度會更快。
  


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [分析儲存體中的資料](get-started-analyze-storage.md)
