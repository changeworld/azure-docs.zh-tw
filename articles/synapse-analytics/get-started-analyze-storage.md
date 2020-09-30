---
title: 教學課程：開始分析儲存體帳戶中的資料
description: 在此教學課程中，您將了解如何分析位於儲存體帳戶中的資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: a0d5c758873413e549b31e3ec4cc41791fc8c371
ms.sourcegitcommit: 0194a29a960e3615f96a2d9d8a7e681cf3e8f9ab
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/10/2020
ms.locfileid: "89667418"
---
# <a name="analyze-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

在此教學課程中，您將了解如何分析位於儲存體帳戶中的資料。

## <a name="overview"></a>概觀

目前為止，我們已介紹資料位於工作區中資料庫的案例。 現在，我們將示範如何使用儲存體帳戶中的檔案。 在此案例中，我們將使用工作區的主要儲存體帳戶，以及我們在建立工作區時所指定的容器。

* 儲存體帳戶的名稱：**contosolake**
* 儲存體帳戶中容器的名稱：**users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在您的儲存體帳戶中建立 CSV 和 Parquet 檔案

在筆記本資料格中執行下列命令。 其會在儲存體帳戶中建立 CSV 檔案和 Parquet 檔案。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

1. 在 Synapse Studio 中，移至 [資料] 中樞，然後選取 [連結的項目]。
1. 移至 [儲存體帳戶] > [myworkspace (主要 - contosolake)]。
1. 選取 [使用者 (主要)]。 您應該會看到 [NYCTaxi] 資料夾。 在其中，您應該會看到名為 **PassengerCountStats.csv** 和 **PassengerCountStats.parquet** 的資料夾。
1. 開啟 [PassengerCountStats.parquet] 資料夾。 您將在其中看到名稱像是 `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` 的 parquet 檔案。
1. 在 **.parquet** 上按一下滑鼠右鍵，然後選取 [新增筆記本]。 其會建立具有資料格的筆記本，如下所示：

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. 執行資料格。
1. 以滑鼠右鍵按一下其中的 Parquet 檔案，然後選取 [新增指令碼] > [選取前 100 個資料列]。 其會建立如下所示的 SQL 指令碼：

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

    在指令碼視窗中，[連線至] 欄位會設定為 [隨選 SQL]。

1. 執行指令碼。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用管線協調活動](get-started-pipelines.md)
