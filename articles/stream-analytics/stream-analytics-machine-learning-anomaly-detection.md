---
title: Azure 串流分析中的異常偵測
description: 本文說明如何一起使用 Azure 串流分析和 Azure Machine Learning 來偵測異常行為。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/21/2019
ms.openlocfilehash: 69824df1b84f6cdfafa08a662816281442ad44fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86044374"
---
# <a name="anomaly-detection-in-azure-stream-analytics"></a>Azure 串流分析中的異常偵測

Azure 串流分析 (在雲端和 Azure IoT Edge 中都可取得) 提供內建的機器學習型異常偵測功能，可用來監視兩個最常出現的異常狀況：暫存和持續性。 利用 **AnomalyDetection_SpikeAndDip** 和 **AnomalyDetection_ChangePoint** 函式，您可以直接在串流分析作業中執行異常偵測。

機器學習模型假設有一個均勻取樣的時間序列。 如果此時間序列不均勻，您可以在呼叫異常偵測之前，透過輪轉視窗插入彙總步驟。

機器學習作業目前不支援季節性趨勢或多重變數行銷關聯性。

## <a name="anomaly-detection-using-machine-learning-in-azure-stream-analytics"></a>使用 Azure 串流分析中的機器學習服務進行異常偵測

下列影片示範如何使用 Azure 串流分析中的機器學習服務功能，即時偵測異常狀況。 

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Real-Time-ML-Based-Anomaly-Detection-In-Azure-Stream-Analytics/player]

## <a name="model-behavior"></a>模型行為

一般而言，在滑動視窗中使用更多資料可改善模型的精確度。 所指定滑動視窗中的資料會被視為屬於該時間範圍的正常值範圍。 此模型只會考慮整個滑動視窗的事件歷程記錄，以檢查目前的事件是否異常。 隨著滑動視窗移動，系統會從模型的訓練中收回舊值。

函式的運作方式為根據其目前為止所看到的內容建立某種標準。 與所建立的標準進行比較 (在信賴等級內)，藉此識別極端值。 視窗大小應該以針對正常行為定型模型所需的事件數目下限為基礎，如此在發生異常狀況時，才能夠加以辨識。

模型的回應時間會隨著歷程記錄大小而增加，因為它需要與較高的過去事件數目進行比較。 建議只包含所需的事件數目，以獲得較佳效能。

時間序列中的間距可能是未能在特定時間點及時收到事件的模型結果。 串流分析會使用插補邏輯來處理這種情況。 相同滑動視窗的歷程記錄大小，以及持續時間用來計算事件預計抵達的的平均速率。

您可以在 [這裡](https://aka.ms/asaanomalygenerator) 找到的異常產生器，用來將具有不同異常模式的資料摘要到 Iot 中樞。 您可以使用這些異常偵測函式來設定 ASA 作業，以讀取此 Iot 中樞並偵測異常狀況。

## <a name="spike-and-dip"></a>峰值和谷值

時間序列事件資料流中的暫存異常狀況也稱為峰值和谷值。 您可以使用 Machine Learning 型運算子[AnomalyDetection_SpikeAndDip](https://docs.microsoft.com/stream-analytics-query/anomalydetection-spikeanddip-azure-stream-analytics
) 來監視峰值和谷值。

![峰值和谷值異常的範例](./media/stream-analytics-machine-learning-anomaly-detection/anomaly-detection-spike-dip.png)

在相同的滑動視窗中，如果第二個峰值小於第一個峰值，則較小峰值的計算分數可能不足以與指定的信賴等級內第一個峰值的分數比較。 您可以嘗試降低模型的信賴等級來偵測這類異常狀況。 不過，如果您開始取得太多警示，則可使用較高的信賴區間。

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

## <a name="performance-characteristics"></a>效能特性

這些模型的效能取決於記錄大小、視窗持續時間、事件裝載，以及是否使用函數層級分割。 本節將討論這些設定，並提供如何維持每秒1千、中和1萬個事件的內嵌速率範例。

* 歷程**記錄大小**-這些模型會以線性方式以**記錄大小**執行。 歷程記錄大小愈久，模型花在為新事件評分的時間越長。 這是因為模型會將新事件與歷程記錄緩衝區中的每個過去事件進行比較。
* **視窗持續期間** - **視窗持續時間** 應該反映接收記錄大小所指定的事件數目所需的時間。 在視窗中，如果沒有這麼多事件，Azure 串流分析會插補遺漏值。 因此，CPU 耗用量是記錄大小的功能。
* **事件裝載** - **事件裝載**愈大，模型所執行的工作越多，就會影響 CPU 耗用量。 您可以藉由使其窘迫平行來相應放大作業，假設商務邏輯可使用更多輸入資料分割。
* **函數層級資料分割**  - **函數層級資料分割**是藉由在 ```PARTITION BY``` 異常偵測函式呼叫中使用來完成。 這種類型的資料分割會增加額外負荷，因為必須同時維護多個模型的狀態。 在裝置層級分割之類的案例中，會使用函式層級資料分割。

### <a name="relationship"></a>關聯性
記錄大小、視窗持續時間和事件裝載總計是以下列方式相關：

windowDuration (in ms) = 1000 * historySize/ (每秒輸入的事件總數/輸入分割區計數) 

依 deviceId 分割函數時，請將 "PARTITION BY deviceId" 新增至異常偵測函式呼叫。

### <a name="observations"></a>觀察
下表包含針對非資料分割案例 (6 SU) 的單一節點的輸送量觀察：

| 記錄大小 (事件)  | 視窗持續時間 (ms)  | 每秒輸入事件總數 |
| --------------------- | -------------------- | -------------------------- |
| 60 | 55 | 2,200 |
| 600 | 728 | 1650 |
| 6,000 | 10910 | 1,100 |

下表包含針對分割案例 (6 SU) 單一節點的輸送量觀察：

| 記錄大小 (事件)  | 視窗持續時間 (ms)  | 每秒輸入事件總數 | 裝置計數 |
| --------------------- | -------------------- | -------------------------- | ------------ |
| 60 | 1091 | 1,100 | 10 |
| 600 | 10910 | 1,100 | 10 |
| 6,000 | 218182 | <550 | 10 |
| 60 | 21819 | 550 | 100 |
| 600 | 218182 | 550 | 100 |
| 6,000 | 2181819 | <550 | 100 |

執行上述非資料分割設定的範例程式碼位於 Azure 範例的 [大規模串流](https://github.com/Azure-Samples/streaming-at-scale/blob/f3e66fa9d8c344df77a222812f89a99b7c27ef22/eventhubs-streamanalytics-eventhubs/anomalydetection/create-solution.sh) 存放庫中。 此程式碼會建立沒有函式層級分割的串流分析作業，這會使用事件中樞做為輸入和輸出。 輸入負載是使用測試用戶端產生的。 每個輸入事件都是 1KB json 檔。 事件會模擬傳送最多1千個裝置)  (之 JSON 資料的 IoT 裝置。 歷程記錄大小、視窗持續時間和事件裝載總計的變化超過2個輸入資料分割。

> [!Note]
> 如需更精確的評估，請自訂範例以符合您的案例。

### <a name="identifying-bottlenecks"></a>找出瓶頸
使用您 Azure 串流分析作業中的 [計量] 窗格來找出管線中的瓶頸。 檢閱 [輸入/輸出事件] 來了解輸送量，並檢閱[「浮水印延遲」](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/) \(英文\) 或 [待處理的事件] 來查看作業是否能與輸入速率保持一致。 針對事件中樞計量，請尋找 [節流的要求] 並據以調整閾值單位。 針對 Cosmos DB 計量，請檢閱 [輸送量] 底下的 [每個分割區索引鍵範圍每秒取用的 RU 上限]，以確保系統會一致地取用您的分割區索引鍵範圍。 針對 Azure SQL DB，請監視 [記錄 IO] 和 [CPU]。

## <a name="next-steps"></a>後續步驟

* [Azure Stream Analytics 介紹](stream-analytics-introduction.md)
* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)

