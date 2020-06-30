---
title: 資料分析師教學課程：在 Azure Synapse Studio (預覽) 中使用 SQL 隨選 (預覽) 來分析 Azure 開放資料集
description: 在本教學課程中，您將了解如何使用 SQL 隨選 (預覽) 輕鬆地執行合併了不同 Azure 開放資料集的探勘資料分析，並在 Azure Synapse Studio 中將結果視覺化。
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: sql
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: afe231186adec95b09a75d7fe59552ca8a07049b
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213137"
---
# <a name="use-sql-on-demand-preview-to-analyze-azure-open-datasets-and-visualize-the-results-in-azure-synapse-studio-preview"></a>使用 SQL 隨選 (預覽) 來分析 Azure 開放資料集，並在 Azure Synapse Studio (預覽) 中將結果視覺化

在本教學課程中，您將了解如何使用 SQL 隨選合併不同的 Azure 開放資料集，然後在 Azure Synapse Studio 中將結果視覺化，以執行探勘資料分析。

具體來說，您會分析[紐約市 (NYC) 計程車資料集](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)，其中包括：

- 上下車日期和時間。
- 上下車地點。 
- 行車距離：
- 車資明細。
- 費率類型。
- 付款類型。 
- 司機報告的乘客計數。

## <a name="automatic-schema-inference"></a>自動推斷結構描述

因為資料是以 Parquet 檔案格式儲存，所以可以使用自動架構推斷。 您可以輕鬆地查詢資料，而不需要列出檔案中所有資料行的資料類型。 您也可以使用虛擬資料行機制和 filepath 函式來篩選出特定的檔案子集。

讓我們先執行下列查詢以熟悉 NYC 計程車資料：

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
```

下列程式碼片段顯示 NYC 計程車資料的結果：

![NYC 計程車資料結果程式碼片段](./media/tutorial-data-analyst/1.png)

同樣地，您也可以使用下列查詢來查詢國定假日資料集：

```sql
SELECT TOP 100 * FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
        FORMAT='PARQUET'
    ) AS [holidays]
```

下列程式碼片段顯示國定假日資料集的結果：

![公用假日資料集結果程式碼片段](./media/tutorial-data-analyst/2.png)

最後，您也可以使用下列查詢來查詢天氣資料資料集：

```sql
SELECT
    TOP 100 *
FROM  
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
```

下列程式碼片段顯示天氣資料資料集的結果：

![氣象資料資料集結果程式碼片段](./media/tutorial-data-analyst/3.png)

您可以在 [NYC 計程車](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/)、[國定假日](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)和[天氣資料](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)資料集的說明中深入了解個別資料行的意義。

## <a name="time-series-seasonality-and-outlier-analysis"></a>時間序列、季節性和極端值分析

您可以使用下列查詢來輕鬆地摘要列出每年的計程車乘車數量：

```sql
SELECT
    YEAR(tpepPickupDateTime) AS current_year,
    COUNT(*) AS rides_per_year
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) >= '2009' AND nyc.filepath(1) <= '2019'
GROUP BY YEAR(tpepPickupDateTime)
ORDER BY 1 ASC
```

下列程式碼片段顯示每年計程車乘車數量的結果：

![每年計程車乘車結果程式碼片段數](./media/tutorial-data-analyst/4.png)

您可以在 Synapse Studio 中從 [資料表] 檢視切換至 [圖表] 檢視，以視覺方式呈現資料。 您可以選擇不同的圖表類型，例如**區域圖**、**橫條圖**、**直條圖**、**折線圖**、**圓形圖** 和**散佈圖**。 在本案例中，我們要將 [類別] 資料行設定為 [current_year] 的情況下繪製**直條圖**：

![顯示每年乘車次數的直條圖](./media/tutorial-data-analyst/5.png)

從這個視覺效果中，可以清楚地看出乘車次數多年來減少的趨勢。 這項減少的原因是，分享公司最近愈來愈受到歡迎。

> [!NOTE]
> 在撰寫本教學課程時，2019 年的資料不完整。 如此一來，這一年的乘車次數會大幅下降。

接下來，讓我們將分析重點放在單一年度，例如 2016 年。 下列查詢會傳回該年度內的每日乘車數量：

```sql
SELECT
    CAST([tpepPickupDateTime] AS DATE) AS [current_day],
    COUNT(*) as rides_per_day
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [nyc]
WHERE nyc.filepath(1) = '2016'
GROUP BY CAST([tpepPickupDateTime] AS DATE)
ORDER BY 1 ASC
```

下列程式碼片段顯示此查詢的結果：

![2016 年每日乘車次數結果程式碼片段](./media/tutorial-data-analyst/6.png)

同樣地，您可以在 **[類別]** 資料行設定為 **[current_day]** 且 **[圖例 (數列)]** 資料行設定為 **[rides_per_day]** 的情況下繪製**直條圖**，輕鬆地用視覺方式呈現資料。

![顯示 2016 年每日乘車次數的直條圖](./media/tutorial-data-analyst/7.png)

從繪圖圖表中，您可以看到有每週模式，而星期六為尖峰日。 由於夏季月份期間屬於假期，所以計程車乘車數量較少。 圖中也有一些地方出現計程車乘車數量大幅下滑的情形，但其發生時間和原因則沒有明顯模式。

接下來，讓我們將 NYC 計程車乘車資料集與國定假日資料集聯結起來，以了解這些下滑是否與國定假日有所關聯：

```sql
WITH taxi_rides AS
(
    SELECT
        CAST([tpepPickupDateTime] AS DATE) AS [current_day],
        COUNT(*) as rides_per_day
    FROM  
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/nyctlc/yellow/puYear=*/puMonth=*/*.parquet',
            FORMAT='PARQUET'
        ) AS [nyc]
    WHERE nyc.filepath(1) = '2016'
    GROUP BY CAST([tpepPickupDateTime] AS DATE)
),
public_holidays AS
(
    SELECT
        holidayname as holiday,
        date
    FROM
        OPENROWSET(
            BULK 'https://azureopendatastorage.blob.core.windows.net/holidaydatacontainer/Processed/*.parquet',
            FORMAT='PARQUET'
        ) AS [holidays]
    WHERE countryorregion = 'United States' AND YEAR(date) = 2016
)
SELECT
*
FROM taxi_rides t
LEFT OUTER JOIN public_holidays p on t.current_day = p.date
ORDER BY current_day ASC
```

![NYC 計程車乘車和公定假日資料集結果視覺效果](./media/tutorial-data-analyst/8.png)

這一次，我們想要醒目提示國定假日期間的計程車乘車數量。 基於這個目的，我們會選擇 [none] 來作為 [類別] 資料行，並選擇 [rides_per_day] 和 [holiday] 作為 [圖例 (數列)] 資料行。

![公定假日期間的計程車乘車次數圖表](./media/tutorial-data-analyst/9.png)

從繪圖中可以發現，國定假日期間有一些計程車乘車數量較低。 這仍無法有效解釋 1 月 23 日的大幅下滑。 讓我們查詢天氣資料資料集來查看 NYC 當天的天氣：

```sql
SELECT
    AVG(windspeed) AS avg_windspeed,
    MIN(windspeed) AS min_windspeed,
    MAX(windspeed) AS max_windspeed,
    AVG(temperature) AS avg_temperature,
    MIN(temperature) AS min_temperature,
    MAX(temperature) AS max_temperature,
    AVG(sealvlpressure) AS avg_sealvlpressure,
    MIN(sealvlpressure) AS min_sealvlpressure,
    MAX(sealvlpressure) AS max_sealvlpressure,
    AVG(precipdepth) AS avg_precipdepth,
    MIN(precipdepth) AS min_precipdepth,
    MAX(precipdepth) AS max_precipdepth,
    AVG(snowdepth) AS avg_snowdepth,
    MIN(snowdepth) AS min_snowdepth,
    MAX(snowdepth) AS max_snowdepth
FROM
    OPENROWSET(
        BULK 'https://azureopendatastorage.blob.core.windows.net/isdweatherdatacontainer/ISDWeather/year=*/month=*/*.parquet',
        FORMAT='PARQUET'
    ) AS [weather]
WHERE countryorregion = 'US' AND CAST([datetime] AS DATE) = '2016-01-23' AND stationname = 'JOHN F KENNEDY INTERNATIONAL AIRPORT'
```

![氣象資料資料集結果視覺效果](./media/tutorial-data-analyst/10.png)

查詢的結果指出，計程車的乘車次數發生下滑是因為：

- NYC 當天有暴風雪，降雪量很大 (將近 30 公分)。
- 天氣很冷 (溫度低於攝氏零度)。
- 當天風大 (風速大約 10 公尺/秒)。

本教學課程已示範資料分析師要如何快速執行探勘資料分析、使用 SQL 隨選輕鬆地合併不同資料集，以及使用 Azure Synapse Studio 將結果視覺化。

## <a name="next-steps"></a>後續步驟

若要了解如何將 SQL 隨選連線到 Power BI Desktop 並建立報告，請參閱[將 SQL 隨選連線到 Power BI Desktop 並建立報告](tutorial-connect-power-bi-desktop.md)。
 
