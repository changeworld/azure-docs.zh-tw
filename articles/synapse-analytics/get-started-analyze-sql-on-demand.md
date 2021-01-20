---
title: 教學課程：開始使用無伺服器 SQL 集區分析資料
description: 在本教學課程中，您將了解如何使用無伺服器 SQL 集區，使用位於 Spark 資料庫中的資料來分析資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: c9f8760bd1a7b5d3700f3fdf03331fe7013e116f
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209401"
---
# <a name="analyze-data-with-serverless-sql-pool-in-azure-synapse-analytics"></a>使用 Azure Synapse Analytics 中的無伺服器 SQL 集區來分析資料

在本教學課程中，您將了解如何使用無伺服器 SQL 集區，使用位於 Spark 資料庫中的資料來分析資料。 

## <a name="analyze-nyc-taxi-data-in-blob-storage-using-serverless-sql-pool"></a>使用無伺服器 SQL 集區分析 Blob 儲存體中的 NYC 計程車資料

1. 在 [已 **連結**] 下的 **資料** 中樞內，以滑鼠右鍵按一下 **Azure Blob 儲存體 > 範例資料集 > Nyc_tlc_yellow**、**新的 SQL 腳本**，然後選取 [**選取前 100** 個數據列]。
1. 這會使用下列程式碼建立新的 SQL 指令碼：

    ```
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK     'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT = 'parquet'
        ) AS [result];
    ```
1. 按一下 [執行] 

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-serverless-sql-pool"></a>使用無伺服器 SQL 集區分析 Spark 資料庫中的 NYC 計程車資料

Spark 資料庫中的資料表會自動顯示，且可由無伺服器 SQL 集區進行查詢。

1. 在 Synapse Studio 中，移至 [開發] 中樞，然後建立新的 SQL 指令碼。
1. 將 **Connect** 設定為 **內建** 的無伺服器 SQL 集區。
1. 將下列文字貼到指令碼中，然後執行指令碼。

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > 當您第一次執行使用無伺服器 SQL 集區的查詢時，無伺服器 SQL 集區需要大約 10 秒的時間，才能收集執行查詢所需的 SQL 資源。 後續查詢的速度會更快。
  


## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [分析儲存體中的資料](get-started-analyze-storage.md)
