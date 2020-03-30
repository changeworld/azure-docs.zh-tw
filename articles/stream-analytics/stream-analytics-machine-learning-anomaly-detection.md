---
title: Azure 串流分析中的異常偵測
description: 本文說明如何一起使用 Azure 串流分析和 Azure Machine Learning 來偵測異常行為。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 51b9c827d453eef2e2e75e1aa5222204eaa38d0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77525527"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 串流分析中的異常偵測

Azure 串流分析 (在雲端和 Azure IoT Edge 中都可取得) 提供內建的機器學習型異常偵測功能，可用來監視兩個最常出現的異常狀況：暫存和持續性。 利用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函式，您可以直接在串流分析作業中執行異常偵測。

機器學習模型假設有一個均勻取樣的時間序列。 如果此時間序列不均勻，您可以在呼叫異常偵測之前，透過輪轉視窗插入彙總步驟。

機器學習操作目前不支援季節性趨勢或多變數相關性。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>在 Azure 流分析中使用機器學習進行異常檢測

以下視頻演示如何使用 Azure 流分析中的機器學習函數即時檢測異常。 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>模型行為

一般而言，在滑動視窗中使用更多資料可改善模型的精確度。 所指定滑動視窗中的資料會被視為屬於該時間範圍的正常值範圍。 此模型只會考慮整個滑動視窗的事件歷程記錄，以檢查目前的事件是否異常。 隨著滑動視窗移動，系統會從模型的訓練中收回舊值。

函式的運作方式為根據其目前為止所看到的內容建立某種標準。 與所建立的標準進行比較 (在信賴等級內)，藉此識別極端值。 視窗大小應該以針對正常行為定型模型所需的事件數目下限為基礎，如此在發生異常狀況時，才能夠加以辨識。

模型的回應時間隨歷史記錄大小而增加，因為它需要與大量過去的事件進行比較。 建議只包含所需的事件數目，以獲得較佳效能。

時間序列中的間距可能是未能在特定時間點及時收到事件的模型結果。 此情況由流分析使用歸因邏輯處理。 相同滑動視窗的歷程記錄大小，以及持續時間用來計算事件預計抵達的的平均速率。

[此處](https://aka.ms/asaanomalygenerator)提供的異常產生器可用於向 Iot 中心提供具有不同異常模式的資料。 可以使用這些異常檢測功能設置 ASA 作業，以便從此 Iot 中心讀取並檢測異常。

## <a name="spike-and-dip"></a>峰值和谷值

時間序列事件資料流中的暫存異常狀況也稱為峰值和谷值。 您可以使用 Machine Learning 型運算子[AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 來監視峰值和谷值。

![峰值和谷值異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在相同的滑動視窗中，如果第二個峰值小於第一個峰值，則較小峰值的計算分數可能不足以與指定的信賴等級內第一個峰值的分數比較。 您可以嘗試降低模型的置信度以檢測此類異常。 不過，如果您開始取得太多警示，則可使用較高的信賴區間。

下列範例查詢假設在歷程記錄有 120 個事件的 2 分鐘滑動視窗中，每秒一個事件的均勻輸入速率。 最終的 SELECT 陳述式會擷取並輸出 95% 信賴等級的分數和異常狀態。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_SpikeAndDip(CAST(temperature AS float), 95, 120, 'spikesanddips')
            OVER(LIMIT DURATION(second, 120)) AS SpikeAndDipScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'Score') AS float) AS
    SpikeAndDipScore,
    CAST(GetRecordPropertyValue(SpikeAndDipScores, 'IsAnomaly') AS bigint) AS
    IsSpikeAndDipAnomaly
INTO output
FROM AnomalyDetectionStep
```

## <a name="change-point"></a>變更點

時間序列事件資料流中的持續性異常狀況是事件資料流中值分佈的變更，如等級變更和趨勢。 在串流分析中，使用 Machine Learning 型 [AnomalyDetection_ChangePoint](https://docs.microsoft.com/stream-analytics-query/anomalydetection-changepoint-azure-stream-analytics) 運算子偵測這類異常。

持續性變更會比峰值和谷值持續更久，並可能表示發生重大事件。 肉眼通常無法看見持續性變更，但可以使用 **AnomalyDetection_ChangePoint** 運算子進行偵測。

下圖是等級變更的範例：

![等級變更異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-level-change.png)

下圖是趨勢變更的範例：

![趨勢變更異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-trend-change.png)

下列範例查詢假設在歷程記錄大小為 1200 個事件的 20 分鐘滑動視窗中，每秒一個事件的均勻輸入速率。 最終的 SELECT 陳述式會擷取並輸出 80% 信賴等級的分數和異常狀態。

```SQL
WITH AnomalyDetectionStep AS
(
    SELECT
        EVENTENQUEUEDUTCTIME AS time,
        CAST(temperature AS float) AS temp,
        AnomalyDetection_ChangePoint(CAST(temperature AS float), 80, 1200) 
        OVER(LIMIT DURATION(minute, 20)) AS ChangePointScores
    FROM input
)
SELECT
    time,
    temp,
    CAST(GetRecordPropertyValue(ChangePointScores, 'Score') AS float) AS
    ChangePointScore,
    CAST(GetRecordPropertyValue(ChangePointScores, 'IsAnomaly') AS bigint) AS
    IsChangePointAnomaly
INTO output
FROM AnomalyDetectionStep

```

## <a name="performance-characteristics"></a>性能特徵

這些模型的性能取決於歷史記錄大小、視窗持續時間、事件裝載以及是否使用功能級別分區。 本節討論這些配置，並提供有關如何保持每秒 1K、5K 和 10K 事件的攝取速率的示例。

* **歷史大小**- 這些模型與**歷史大小**線性執行。 歷史記錄大小越長，模型對新事件進行評分的時間越長。 這是因為模型將新事件與歷史記錄緩衝區中的每個過去事件進行比較。
* **視窗持續時間**-**視窗持續時間**應反映接收歷史記錄大小指定的相同事件所需的時間。 如果沒有視窗中的許多事件，Azure 流分析將造成缺少的值。 因此，CPU 限定是歷史記錄大小的函數。
* **事件裝載**-**事件裝載**越大，模型執行的工作就越多，這會影響 CPU 限定。 假定業務邏輯使用更多輸入分區有意義，則可以通過使其令人尷尬的並行性來擴展。
* **函數級分區** - **功能級別分區**是在異常檢測函式呼叫```PARTITION BY```中使用來完成的。 這種類型的分區會增加開銷，因為需要同時為多個模型維護狀態。 功能級分區用於設備級分區等方案。

### <a name="relationship"></a>關聯性
歷史記錄大小、視窗持續時間和事件總負載以下列方式相關：

視窗持續時間（以毫秒計） = 1000 = 歷史記錄大小 / （每秒的總輸入事件 / 輸入分區計數）

按設備 Id 對函數進行分區時，將"分區 BY 設備 Id"添加到異常檢測函式呼叫中。

### <a name="observations"></a>觀察
下表包括非分區情況下單個節點 （6 SU） 的輸送量觀察值：

| 歷史記錄大小（事件） | 視窗持續時間（毫秒） | 每秒輸入事件總數 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1,650 |
| 6,000 | 10,910 | 1,100 |

下表包括分區情況下單個節點 （6 SU） 的輸送量觀察值：

| 歷史記錄大小（事件） | 視窗持續時間（毫秒） | 每秒輸入事件總數 | 裝置計數 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1,091 | 1,100 | 10 |
| 600 | 10,910 | 1,100 | 10 |
| 6,000 | 218,182 | <550 | 10 |
| 60 | 21,819 | 550 | 100 |
| 600 | 218,182 | 550 | 100 |
| 6,000 | 2,181,819 | <550 | 100 |

運行上述非分區配置的示例代碼位於 Azure 示例的["流式處理"](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh)中。 該代碼創建一個沒有函數級分區的流分析作業，該作業使用事件中心作為輸入和輸出。 輸入負載使用測試用戶端生成。 每個輸入事件都是一個 1KB 的 json 文檔。 事件類比發送 JSON 資料的 IoT 設備（最多 1K 設備）。 歷史記錄大小、視窗持續時間和總事件裝載在 2 個輸入分區上變化。

> [!Note]
> 要獲得更準確的估計，請自訂示例以適合您的方案。

### <a name="identifying-bottlenecks"></a>識別瓶頸
使用 Azure 流分析作業中的"指標"窗格來識別管道中的瓶頸。 查看**輸入/輸出事件**中的輸送量和["浮水印延遲"](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)或 **"積壓事件**"，以查看作業是否跟上輸入速率。 對於事件中心指標，請查找**限制請求**並相應地調整閾值單位。 對於 Cosmos DB 指標，請查看"輸送量"下**每個分區鍵範圍的最大值消耗的 RU/s，** 以確保分區金鑰範圍被統一使用。 對於 Azure SQL DB，請監視**日誌 IO**和**CPU**。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

