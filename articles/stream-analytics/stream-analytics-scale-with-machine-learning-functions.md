---
title: 在 Azure 串流分析中調整 Machine Learning Studio (傳統) 函數
description: 本文說明如何藉由設定資料分割和串流單位，來調整使用 Machine Learning Studio (傳統) 函式的串流分析作業。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: feeb709f67a0e75f5980ec0520b95feb7edd5960
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93124402"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>使用 Azure Machine Learning Studio (傳統版) 函式調整串流分析作業

> [!TIP]
> 強烈建議使用 [Azure Machine Learning UDF](machine-learning-udf.md)，而不是Azure Machine Learning Studio (傳統版) UDF，以改善效能和可靠性。

本文討論如何有效率地調整使用 Azure Machine Learning Studio (傳統) 功能的 Azure 串流分析作業。 如需有關如何調整串流分析作業的一般資訊，請參閱 [調整作業](stream-analytics-scale-jobs.md)文章。

## <a name="what-is-a-studio-classic-function-in-stream-analytics"></a>什麼是串流分析中的 Studio (傳統) 函數？

串流分析中的 Machine Learning Studio (傳統) 函式可以像串流分析查詢語言中的一般函式呼叫一樣使用。 不過，在幕後，這些函式呼叫實際上是 Studio (傳統) Web 服務要求。

您可以在相同的 web 服務 API 呼叫中「批次處理」多個資料列，以改善 Studio (傳統) web 服務要求的輸送量。 此群組稱為「迷你批次」。 如需詳細資訊，請參閱 [Azure Machine Learning Studio (傳統版) Web 服務](../machine-learning/classic/consume-web-services.md)。 串流分析中的 Studio (傳統) 支援目前為預覽狀態。

## <a name="configure-a-stream-analytics-job-with-studio-classic-functions"></a>使用 Studio (傳統) 函數來設定串流分析作業

有兩個參數可設定您的串流分析作業所使用的 Studio (傳統) 函式：

* Studio (傳統) 函數呼叫的批次大小。
* 為串流分析作業佈建的串流單位 (SU) 數目。

若要判斷 SU 的適當值，請決定您要最佳化串流分析作業的延遲或是最佳化每個 SU 的輸送量。 可以隨時將 SU 新增至作業，以提高妥善資料分割的串流分析查詢輸送量。 額外的 SU 會增加執行作業的成本。

判斷串流分析作業的「容許」延遲。 增加批次大小會增加您的 Studio (傳統) 要求的延遲，以及串流分析作業的延遲。

增加批次大小可讓串流分析工作處理 **更多** 具有相同 Studio (傳統) web 服務要求 **數目** 的事件。 Studio (傳統) web 服務延遲的增加通常是批次大小增加的線性。 

在任何特定情況下，請務必考慮 Studio (傳統) web 服務最符合成本效益的批次大小。 Web 服務要求的預設批次大小為1000。 您可以使用[串流分析 REST API](/previous-versions/azure/mt653706(v=azure.100) "串流分析 REST API") 或[適用於串流分析的 PowerShell 用戶端](stream-analytics-monitor-and-manage-jobs-use-powershell.md) 變更此預設大小。

決定批次大小後，您可以根據函式每秒需要處理的事件數目，設定串流單位 (SU) 的數目。 如需串流單位的詳細資訊，請參閱[串流分析調整作業](stream-analytics-scale-jobs.md)。

每6個 su 都會取得與 Studio (傳統) web 服務的20個同時連線。 不過，1 SU 的作業和 3 SU 的作業能有 20 個同時連線。  

如果您的應用程式每秒產生 200,000 個事件，批次大小為 1000，則產生的 Web 服務延遲為 200 毫秒。 這種速率表示每次連線都可以對 Studio (傳統) web 服務提出五次要求。 透過 20 個連線，串流分析作業可以在 200 毫秒內處理 20,000 個事件，在 1 秒內可處理 100,000 個事件。

若要每秒處理 200,000 個事件，串流分析作業需要 40 個同時連線，結果出現 12 個 SU。 下圖說明串流分析作業對 Studio (傳統) web 服務端點的要求–每6個 su 有20個同時連線到 Studio (傳統) web 服務的最大值。

![使用 Studio (傳統) 函式調整串流分析兩個作業範例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "使用 Studio (傳統) 函式調整串流分析兩個作業範例")

一般情況下，* *_B_* _ 針對批次大小， _*_L_*_ 在批次大小 B 的 web 服務延遲（以毫秒為單位），具有 _*_N_*_ 個 su 的串流分析作業的輸送量為：

![使用 Studio (傳統) 函數公式調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用 Studio (傳統) 函數公式調整串流分析")

您也可以在 Studio (傳統) web 服務上設定「並行呼叫數上限」。 建議將此參數設定為最大值 (目前為200)。

如需此設定的詳細資訊，請參閱 [Machine Learning Studio (傳統) Web 服務的縮放文章](../machine-learning/classic/create-endpoint.md)。

## <a name="example--sentiment-analysis"></a>範例 – 情感分析
下列範例包含情感 analysis Studio (傳統) 函數的串流分析作業，如 [串流分析 Machine Learning Studio (傳統) 整合教學](stream-analytics-machine-learning-integration-tutorial.md)課程中所述。

此查詢是一個簡單的完整資料分割查詢，後面接著 _ *情感* * 函數，如下列範例所示：

```SQL
    WITH subquery AS (
        SELECT text, sentiment(text) as result from input
    )

    Select text, result.[Score]
    Into output
    From subquery
```

讓我們來檢查建立串流分析作業所需的設定，此作業以每秒 10,000 則推文的速率執行推文的情感分析。

使用 1 個 SU，此串流分析作業是否能夠處理此流量？ 使用預設批次大小 1000 時，作業可以跟得上輸入。 情感 analysis Studio (傳統) web 服務的預設延遲 (預設批次大小為 1000) 不會建立超過一秒的延遲。

串流分析作業的 **整體** 或端對端延遲通常是幾秒鐘的時間。 深入瞭解此串流分析作業， *尤其是* Studio (傳統) 函式呼叫。 若批次大小為 1000，則 10,000 個事件的輸送量大約需要對 Web 服務提出 10 個要求。 即使是一個 SU，也有足夠的同時連線可容納此輸入流量。

如果輸入事件速率增加 100 倍，則串流分析作業每秒需要處理 1,000,000 則推文。 有兩個選項可完成這種增加規模：

1. 增加批次大小。
2. 分割輸入串流以平行處理事件。

使用第一個選項時，會增加作業 **延遲** 。

使用第二個選項時，您必須布建更多 su，才能讓相同的並行 Studio (傳統) web 服務要求。 這個較大量的 SU 會增加作業 **成本** 。

讓我們看看以下各種批次大小與延遲測量值的變化：

| Latency | 批次大小 |
| --- | --- |
| 200 毫秒 | 一批次 1000 個事件或以下 |
| 250 毫秒 | 一批次 5000 個事件 |
| 300 毫秒 | 一批次 10,000 個事件 |
| 500 毫秒 | 一批次 25,000 個事件 |

1. 使用第一個選項 ( **不要** 佈建更多 SU)。 批次大小可以增加到 **25,000** 。 以這種方式增加批次大小，可讓工作處理具有20個同時連接到 Studio (傳統) web 服務 (的1000000事件，) 的延遲為500毫秒。 因此，串流分析作業的額外延遲是因為對 Studio 的情感函式要求 (傳統) web 服務要求會從 **200 毫秒** 增加到 **500 毫秒** 。 不過，批次大小 **無法** 無限增加，因為 Studio (傳統) web 服務需要要求的承載大小為 4 MB 或更小，而 web 服務要求會在100秒的作業後超時。
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

現在，您應該已經充分瞭解 Studio 在串流分析中 (傳統) 函式的運作方式。 您可能也了解串流分析作業會從資料來源「提取」資料，而且每次「提取」會傳回一批事件以供串流分析作業處理。 此提取模型如何影響 Studio (傳統) web 服務要求？

一般來說，我們為 Studio (傳統) 函式設定的批次大小，將不會被每個串流分析作業「提取」所傳回的事件數目整除。 發生這種情況時，會以「部分」的批次呼叫 Studio (傳統) web 服務。 使用部分批次可避免在將各次提取的事件聯合時產生額外的作業延遲負荷。

## <a name="new-function-related-monitoring-metrics"></a>新的函式相關監視計量
在串流分析作業的 [監視] 區域中，已加入三個額外的函式相關計量。 分別是 [函式要求]、[函式事件]、[失敗的函式要求]，如下圖所示。

![使用 Studio (傳統) 函數計量調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用 Studio (傳統) 函數計量調整串流分析")

其定義如下：

**函式要求** ：函式要求的數目。

**函式事件** ：函式要求中的事件數目。

**失敗的函式要求** ：失敗的函式要求數目。

## <a name="key-takeaways"></a>重要心得

若要使用 Studio (傳統) 函數來調整串流分析作業，請考慮下列因素：

1. 輸入事件速率。
2. 執行中串流分析作業容許的延遲 (，因此 Studio (傳統) web 服務要求) 的批次大小。
3. 布建的串流分析 su 和 Studio (傳統) web 服務要求的數目， (其他與函數相關的成本) 。

以完全分割的串流分析查詢為例。 如果需要更複雜的查詢，[Microsoft Q&A 的 Azure 串流分析問題頁面](/answers/topics/azure-stream-analytics.html)是可向串流分析小組取得其他協助的絕佳資源。

## <a name="next-steps"></a>後續步驟
若要深入了解串流分析，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](/rest/api/streamanalytics/)