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
ms.openlocfilehash: b1060bcc8603cb7f7395a50056424b3d6c0ebe5a
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015495"
---
# <a name="analyze-data-with-sql-pools"></a>使用 SQL 集區分析資料

Azure Synapse Analytics 可為您提供使用 SQL 集區分析資料的功能。 在此教學課程中，您將使用 NYC 計程車範例資料來探索 SQL 集區的分析功能。

## <a name="load-the-nyc-taxi-data-into-sqldb1"></a>將 NYC 計程車資料載入 SQLDB1

1. 在 Synapse Studio 中，瀏覽至 [開發] 中樞，然後建立新的 SQL 指令碼
1. 輸入下列程式碼：
    ```
    CREATE TABLE [dbo].[Trip]
    (
        [DateID] int NOT NULL,
        [MedallionID] int NOT NULL,
        [HackneyLicenseID] int NOT NULL,
        [PickupTimeID] int NOT NULL,
        [DropoffTimeID] int NOT NULL,
        [PickupGeographyID] int NULL,
        [DropoffGeographyID] int NULL,
        [PickupLatitude] float NULL,
        [PickupLongitude] float NULL,
        [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [DropoffLatitude] float NULL,
        [DropoffLongitude] float NULL,
        [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [PassengerCount] int NULL,
        [TripDurationSeconds] int NULL,
        [TripDistanceMiles] float NULL,
        [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
        [FareAmount] money NULL,
        [SurchargeAmount] money NULL,
        [TaxAmount] money NULL,
        [TipAmount] money NULL,
        [TollsAmount] money NULL,
        [TotalAmount] money NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    );

    COPY INTO [dbo].[Trip]
    FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/QID6392_20171107_05910_0.txt.gz'
    WITH
    (
        FILE_TYPE = 'CSV',
        FIELDTERMINATOR = '|',
        FIELDQUOTE = '',
        ROWTERMINATOR='0X0A',
        COMPRESSION = 'GZIP'
    )
    OPTION (LABEL = 'COPY : Load [dbo].[Trip] - Taxi dataset');
    ```
1. 此指令碼需要大約 1 分鐘的時間來執行。 其會將 200 萬列的 NYC 計程車資料載入名為 **dbo.Trip** 的表格

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

