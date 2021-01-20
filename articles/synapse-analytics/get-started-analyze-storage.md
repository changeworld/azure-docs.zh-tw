---
title: 教學課程：開始分析儲存體帳戶中的資料
description: 在此教學課程中，您將了解如何分析位於儲存體帳戶中的資料。
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: ad16b63360364acd88ab12fb4715d1fd3115c0fb
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209367"
---
# <a name="analyze-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

在此教學課程中，您將了解如何分析位於儲存體帳戶中的資料。

## <a name="overview"></a>概觀

目前為止，我們已介紹資料位於工作區中資料庫的案例。 現在，我們將示範如何使用儲存體帳戶中的檔案。 在此案例中，我們將使用工作區的主要儲存體帳戶，以及我們在建立工作區時所指定的容器。

* 儲存體帳戶的名稱：**contosolake**
* 儲存體帳戶中容器的名稱：**users**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>在您的儲存體帳戶中建立 CSV 和 Parquet 檔案

在新的程式碼資料格中，于筆記本中執行下列程式碼。 其會在儲存體帳戶中建立 CSV 檔案和 Parquet 檔案。

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats_csvformat")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats_parquetformat")
```

### <a name="analyze-data-in-a-storage-account"></a>分析儲存體帳戶中的資料

您可以分析工作區預設 ADLS Gen2 帳戶中的資料，也可以透過 [管理] > [連結的服務] > [新增]，將 ADLS Gen2 或 Blob 儲存體帳戶連結至工作區 (下列步驟會參考主要 ADLS Gen2 帳戶)。

1. 在 Synapse Studio 中，移至 [資料] 中樞，然後選取 [連結的項目]。
1. 移至 [儲存體帳戶] > [myworkspace (主要 - contosolake)]。
1. 選取 [使用者 (主要)]。 您應該會看到 [NYCTaxi] 資料夾。 在其中，您應該會看到兩個名為 **PassengerCountStats_csvformat** 和 **PassengerCountStats_parquetformat** 的資料夾。
1. 開啟 **PassengerCountStats_parquetformat** 資料夾。 您將在其中看到名稱像是 `part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet` 的 parquet 檔案。
1. 以滑鼠右鍵按一下 **parquet**，然後選取 [ **新增筆記本**]，再選取 [ **載入至資料框架**]。 使用如下的儲存格建立新的筆記本：

    ```py
    %%pyspark
    df = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    display(df.limit(10))
    ```

1. 附加至名為 **Spark1** 的 Spark 集區。 執行資料格。
1. 按一下 [返回 **使用者** ] 資料夾。 再以滑鼠右鍵按一下 **parquet** 檔案，然後選取 [**新增 SQL 腳本**] 選取 [  >  **前 100** 個數據列]。 其會建立如下所示的 SQL 指令碼：

    ```sql
    SELECT 
        TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [result]
    ```

    在腳本視窗中，確定 [ **連接到]** 欄位設定為 **內建** 的無伺服器 SQL 集區。

1. 執行指令碼。



## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [使用管線協調活動](get-started-pipelines.md)
