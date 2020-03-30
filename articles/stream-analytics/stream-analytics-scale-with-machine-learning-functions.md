---
title: 調整 Azure 串流分析中的 Machine Learning 函式
description: 本文說明如何藉由設定資料分割和串流單位來調整使用 Machine Learning 函式的串流分析作業。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 5b08625d055063b3804a35a3344ff01c7edb79de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066994"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>使用 Azure 機器學習工作室（經典）功能擴展流分析作業

> [!TIP]
> 強烈建議使用[Azure 機器學習 UDF](machine-learning-udf.md)而不是 Azure 機器學習工作室（經典）UDF，以提高性能和可靠性。

本文討論如何有效地擴展使用 Azure 機器學習功能的 Azure 流分析作業。 如需有關如何調整串流分析作業的一般資訊，請參閱 [調整作業](stream-analytics-scale-jobs.md)文章。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>什麼是串流分析中的 Azure Machine Learning 函式？

串流分析中的 Machine Learning 函式可如同一般函式呼叫使用於串流分析查詢語言中。 但是，在幕後，這些函式呼叫實際上是 Azure 機器學習 Web 服務請求。

可以通過在同一 Web 服務 API 呼叫中將多行"批次處理"在一起來提高機器學習 Web 服務請求的輸送量。 此分組稱為小批次處理。 有關詳細資訊，請參閱[Azure 機器學習工作室（經典）Web 服務](../machine-learning/studio/consume-web-services.md)。 預覽版中對流分析中的 Azure 機器學習工作室（經典）的支援。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用 Machine Learning 函式設定串流分析作業

配置流分析作業使用的機器學習函數有兩個參數：

* 機器學習函式呼叫的批次處理大小。
* 為流分析作業預配的流單元 （S） 數。

要確定 SU 的適當值，請確定是要優化流分析作業的延遲還是每個 SU 的輸送量。 SUs 可能始終添加到作業中，以提高分區良好的流分析查詢的輸送量。 額外的 SUS 確實會增加運行作業的成本。

確定流分析作業的延遲*容差*。 增加批次處理大小將增加 Azure 機器學習請求的延遲和流分析作業的延遲。

增加批次處理大小允許流分析工作處理與**相同數量的**機器學習 Web 服務請求**的更多事件**。 機器學習 Web 服務延遲的增加通常與批次處理大小的增加是分線性的。 

在任何給定情況下，考慮機器學習 Web 服務最具成本效益的批次處理大小非常重要。 Web 服務請求的預設批次處理大小為 1000。 您可以使用[流分析 REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "串流分析 REST API")或[用於流分析的 PowerShell 用戶端](stream-analytics-monitor-and-manage-jobs-use-powershell.md)更改此預設大小。

確定批次處理大小後，可以根據函數每秒需要處理的事件數設置流式處理單元數 （S。 如需串流單位的詳細資訊，請參閱[串流分析調整作業](stream-analytics-scale-jobs.md)。

每 6 個 S AUS 獲得 20 個與機器學習 Web 服務的併發連接。 但是，1 個 SU 作業和 3 個 SU 作業獲得 20 個併發連接。  

如果應用程式每秒生成 200，000 個事件，批次處理大小為 1000，則生成的 Web 服務延遲為 200 毫秒。 此速率意味著每個連接每秒可以向機器學習 Web 服務發出五個請求。 通過 20 個連接，流分析作業可在 200 毫秒內處理 20，000 個事件，並在一秒鐘內處理 100，000 個事件。

要每秒處理 200，000 個事件，流分析作業需要 40 個併發連接，這些連接來自 12 個 S。 下圖說明串流分析作業向 Machine Learning Web 服務端點提出的要求︰每 6 個 SU 最多有 20 個對 Machine Learning Web 服務的同時連線。

![使用機器學習函數擴展流分析兩個作業示例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "使用機器學習函數擴展流分析兩個作業示例")

一般而言，***B*** 代表批次大小，***L*** 代表批次大小 B 的 Web 服務延遲 (以毫秒為單位)，而具有 ***N*** 個 SU 的串流分析作業的輸送量為：

![使用 Machine Learning 函式公式調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用 Machine Learning 函式公式調整串流分析")

您還可以在機器學習 Web 服務上配置"最大併發調用"。 建議將此參數設置為最大值（當前為 200）。

如需此設定的詳細資訊，請參閱 [Machine Learning Web 服務的調整文章](../machine-learning/studio/scaling-webservice.md)。

## <a name="example--sentiment-analysis"></a>範例 – 情感分析
以下範例包含具有情感分析 Machine Learning 函式的串流分析作業，如 [串流分析 Machine Learning 整合教學課程](stream-analytics-machine-learning-integration-tutorial.md)所述。

此查詢是一個簡單的完全分割查詢，其後接著 **sentiment** 函式，如下列範例所示：

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

讓我們來檢查創建流分析作業所需的配置，該作業以每秒 10，000 條推文的速度對推文進行情緒分析。

使用 1 SU，此流分析作業可以處理流量嗎？ 作業可以使用預設批次處理大小 1000 跟上輸入。 情緒分析機器學習 Web 服務的預設延遲（預設批次處理大小為 1000）不會產生不超過一秒的延遲。

流分析作業**的總體**或端到端延遲通常為幾秒鐘。 更深入了解此串流分析作業，尤其是 ** Machine Learning 函式呼叫。 批次處理大小為 1000 時，輸送量為 10，000 個事件需要大約 10 個 Web 服務請求。 即使是一個 SU，也有足夠的同時連線可容納此輸入流量。

如果輸入事件速率增加 100 倍，則串流分析作業每秒需要處理 1,000,000 則推文。 有兩個選項可完成這種增加規模：

1. 增加批次處理大小。
2. 分區輸入流以並行處理事件。

使用第一個選項時，會增加作業**延遲**。

使用第二個選項，您必須預配更多 S，以便有更多的併發機器學習 Web 服務請求。 這種更多的S，增加了工作**成本**。

讓我們查看縮放，每個批次大小使用以下延遲度量：

| Latency | 批次大小 |
| --- | --- |
| 200 毫秒 | 1000 個事件批次或以下 |
| 250 毫秒 | 5，000 個事件批次 |
| 300 毫秒 | 10，000 個事件批次 |
| 500 毫秒 | 25，000 個事件批次 |

1. 使用第一個選項 (**不要**佈建更多 SU)。 批次大小可以增加到 **25,000**。 以這種方式增加批次處理大小將允許工作處理 1，000，000 個事件，同時連接到機器學習 Web 服務（每次調用的延遲為 500 毫秒）。 所以因為對 Machine Learning Web 服務要求的情感函式要求而產生的額外串流分析作業延遲會從 **200 毫秒**增加至 **500 毫秒**。 但是，批次處理大小**無法**無限增加，因為機器學習 Web 服務要求請求的有效負載大小為 4 MB 或更小，Web 服務在運行 100 秒後請求超時。
1. 使用第二個選項，批次大小會保留為 1000、Web 服務延遲為 200 毫秒，則每 20 個對 Web 服務的同時連線就能夠處理 1000 * 20 * 5 個事件 = 每秒 100,000。 因此若要每秒處理 1,000,000 個事件，作業會需要 60 SU。 相較於第一個選項，串流分析作業會產生更多 Web 服務批次要求，進而使成本提高。

下表是不同 SU 和批次大小的串流分析作業輸送量 (以每秒的事件數目表示)。

| 批次大小 (ML 延遲) | 500 (200 毫秒) | 1,000 (200 毫秒) | 5,000 (250 毫秒) | 10,000 (300 毫秒) | 25,000 (500 毫秒) |
| --- | --- | --- | --- | --- | --- |
| **1 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **3 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **6 SU** |2,500 |5,000 |20,000 |30,000 |50,000 |
| **12 SU** |5,000 |10,000 |40,000 |60,000 |100,000 |
| **18 SU** |7,500 |15,000 |60,000 |90,000 |150,000 |
| **24 SU** |10,000 |20,000 |80,000 |120,000 |200,000 |
| **…** |… |… |… |… |… |
| **60 SU** |25,000 |50,000 |200,000 |300,000 |500,000 |

現在，您應該已充分了解串流分析中 Machine Learning 函式的運作方式。 您可能也了解串流分析作業會從資料來源「提取」資料，而且每次「提取」會傳回一批事件以供串流分析作業處理。 此提取模型如何影響 Machine Learning Web 服務要求？

通常，我們為機器學習函數設置的批次處理大小不會被每個流分析作業"拉取"返回的事件數完全分割。 發生此狀況時，會以「部分」的批次呼叫 Machine Learning Web 服務。 使用部分批次處理可避免在合併事件時產生額外的作業延遲開銷，從拉取到拉取。

## <a name="new-function-related-monitoring-metrics"></a>新的函式相關監視計量
在串流分析作業的 [監視] 區域中，已加入三個額外的函式相關計量。 它們是**功能請求**、**功能事件**和**失敗功能請求**，如下圖所示。

![使用 Machine Learning 函式計量調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用 Machine Learning 函式計量調整串流分析")

其定義如下：

**函式要求**︰函式要求的數目。

**函式事件**︰函式要求中的事件數目。

**失敗的函式要求**︰失敗的函式要求數目。

## <a name="key-takeaways"></a>重要心得

要使用機器學習功能擴展流分析作業，請考慮以下因素：

1. 輸入事件速率。
2. 正在運行的流分析作業的可容忍延遲（因此是機器學習 Web 服務請求的批次處理大小）。
3. 預配的流分析 S 和機器學習 Web 服務請求的數量（與功能相關的額外費用）。

以完全分割的串流分析查詢為例。 如果需要更複雜的查詢，[Azure 串流分析論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)是可向串流分析小組取得其他協助的絕佳資源。

## <a name="next-steps"></a>後續步驟
若要深入了解串流分析，請參閱：

* [使用 Azure 流分析開始](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure 流分析查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
