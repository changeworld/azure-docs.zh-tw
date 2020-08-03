---
title: 教學課程：開始使用隨選 SQL 分析資料
description: 在本教學課程中，您將了解如何使用位於 Spark 資料庫中的資料，透過隨選 SQL 來分析資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 4011cd93879d9203d8231f24bbf531d14e6e815a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093291"
---
# <a name="analyze-data-with-sql-on-demand"></a>使用隨選 SQL 來分析資料

在本教學課程中，您將了解如何使用位於 Spark 資料庫中的資料，透過隨選 SQL 來分析資料。 

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
> [使用 Spark 進行分析](get-started-analyze-spark.md)
