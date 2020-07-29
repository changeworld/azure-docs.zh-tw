---
title: 調整 Azure 串流分析中的 Machine Learning 函式
description: 本文說明如何藉由設定資料分割和串流單位來調整使用 Machine Learning 函式的串流分析作業。
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2020
ms.openlocfilehash: 1493a15a97ca88d0ed914f78b1906088c03dff10
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037404"
---
# <a name="scale-your-stream-analytics-job-with-azure-machine-learning-studio-classic-functions"></a>使用 Azure Machine Learning Studio (傳統版) 函式調整串流分析作業

> [!TIP]
> 強烈建議使用 [Azure Machine Learning UDF](machine-learning-udf.md)，而不是Azure Machine Learning Studio (傳統版) UDF，以改善效能和可靠性。

本文討論如何有效率地調整使用 Azure Machine Learning 函式的 Azure 串流分析作業。 如需有關如何調整串流分析作業的一般資訊，請參閱 [調整作業](stream-analytics-scale-jobs.md)文章。

## <a name="what-is-an-azure-machine-learning-function-in-stream-analytics"></a>什麼是串流分析中的 Azure Machine Learning 函式？

串流分析中的 Machine Learning 函式可如同一般函式呼叫使用於串流分析查詢語言中。 不過，在幕後，這些函式呼叫實際上是 Azure Machine Learning Web 服務要求。

您可以藉由在相同的 Web 服務 API 呼叫中同時「批次處理」多個資料列，來改善 Machine Learning Web 服務要求的輸送量。 此群組稱為「迷你批次」。 如需詳細資訊，請參閱 [Azure Machine Learning Studio (傳統版) Web 服務](../machine-learning/studio/consume-web-services.md)。 串流分析中的 Azure Machine Learning Studio (傳統版) 支援處於預覽狀態。

## <a name="configure-a-stream-analytics-job-with-machine-learning-functions"></a>使用 Machine Learning 函式設定串流分析作業

有兩個參數可設定串流分析作業所使用的 Machine Learning 函式：

* Machine Learning 函式呼叫的批次大小。
* 為串流分析作業佈建的串流單位 (SU) 數目。

若要判斷 SU 的適當值，請決定您要最佳化串流分析作業的延遲或是最佳化每個 SU 的輸送量。 可以隨時將 SU 新增至作業，以提高妥善資料分割的串流分析查詢輸送量。 額外的 SU 會增加執行作業的成本。

判斷串流分析作業的「容許」延遲。 增加批次大小會增加 Azure Machine Learning 要求的延遲，以及串流分析作業的延遲。

增加批次大小可讓串流分析作業以**相同數量**的 Machine Learning Web 服務要求處理**更多事件**。 Machine Learning Web 服務延遲的增加，通常與批次大小的增加有著次線性關係。 

在任何特定情況下，請務必針對 Machine Learning Web 服務考慮最符合成本效益的批次大小。 Web 服務要求的預設批次大小為1000。 您可以使用[串流分析 REST API](https://docs.microsoft.com/previous-versions/azure/mt653706(v=azure.100) "串流分析 REST API") 或[適用於串流分析的 PowerShell 用戶端](stream-analytics-monitor-and-manage-jobs-use-powershell.md) 變更此預設大小。

決定批次大小後，您可以根據函式每秒需要處理的事件數目，設定串流單位 (SU) 的數目。 如需串流單位的詳細資訊，請參閱[串流分析調整作業](stream-analytics-scale-jobs.md)。

每 6 個 SU 可和 Machine Learning Web 服務有 20 個同時連線。 不過，1 SU 的作業和 3 SU 的作業能有 20 個同時連線。  

如果您的應用程式每秒產生 200,000 個事件，批次大小為 1000，則產生的 Web 服務延遲為 200 毫秒。 這個速率表示每個連線每 1 秒可以向 Machine Learning Web 服務提出五個要求。 透過 20 個連線，串流分析作業可以在 200 毫秒內處理 20,000 個事件，在 1 秒內可處理 100,000 個事件。

若要每秒處理 200,000 個事件，串流分析作業需要 40 個同時連線，結果出現 12 個 SU。 下圖說明串流分析作業向 Machine Learning Web 服務端點提出的要求︰每 6 個 SU 最多有 20 個對 Machine Learning Web 服務的同時連線。

![使用 Machine Learning 函式調整串流分析的兩個作業範例](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-00.png "使用 Machine Learning 函式調整串流分析的兩個作業範例")

一般而言，***B*** 代表批次大小，***L*** 代表批次大小 B 的 Web 服務延遲 (以毫秒為單位)，而具有 ***N*** 個 SU 的串流分析作業的輸送量為：

![使用 Machine Learning 函式公式調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-02.png "使用 Machine Learning 函式公式調整串流分析")

您也可以設定 Machine Learning Web 服務的「同時呼叫數上限」。 建議將此參數設定為最大值 (目前為200)。

如需此設定的詳細資訊，請參閱 [Machine Learning Web 服務的調整文章](../machine-learning/studio/create-endpoint.md)。

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

讓我們來檢查建立串流分析作業所需的設定，此作業以每秒 10,000 則推文的速率執行推文的情感分析。

使用 1 個 SU，此串流分析作業是否能夠處理此流量？ 使用預設批次大小 1000 時，作業可以跟得上輸入。 情感分析 Machine Learning Web 服務的預設延遲 (預設批次大小為 1000) 不會產生超過一秒的延遲。

串流分析作業的**整體**或端對端延遲通常是幾秒鐘的時間。 更深入了解此串流分析作業，尤其是  Machine Learning 函式呼叫。 若批次大小為 1000，則 10,000 個事件的輸送量大約需要對 Web 服務提出 10 個要求。 即使是一個 SU，也有足夠的同時連線可容納此輸入流量。

如果輸入事件速率增加 100 倍，則串流分析作業每秒需要處理 1,000,000 則推文。 有兩個選項可完成這種增加規模：

1. 增加批次大小。
2. 分割輸入串流以平行處理事件。

使用第一個選項時，會增加作業**延遲**。

使用第二個選項時，您必須佈建更多 SU，以擁有更多的同時 Machine Learning Web 服務要求。 這個較大量的 SU 會增加作業**成本**。

讓我們看看以下各種批次大小與延遲測量值的變化：

| Latency | 批次大小 |
| --- | --- |
| 200 毫秒 | 一批次 1000 個事件或以下 |
| 250 毫秒 | 一批次 5000 個事件 |
| 300 毫秒 | 一批次 10,000 個事件 |
| 500 毫秒 | 一批次 25,000 個事件 |

1. 使用第一個選項 (**不要**佈建更多 SU)。 批次大小可以增加到 **25,000**。 以這種方式增加批次大小將可讓作業利用對 Machine Learning Web 服務的 20 個同時連線來處理 1,000,000 個事件 (每次呼叫的延遲為 500 毫秒)。 所以因為對 Machine Learning Web 服務要求的情感函式要求而產生的額外串流分析作業延遲會從 **200 毫秒**增加至 **500 毫秒**。 不過，批次大小**無法**無限增加，因為 Machine Learning Web 服務所需的要求承載大小是 4 MB 或更小，且Web 服務會在作業 100 秒後要求逾時。
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

一般來說，我們為 Machine Learning 函式設定的批次大小不會正好可被每次串流分析作業「提取」所傳回的事件數目除盡。 發生此狀況時，會以「部分」的批次呼叫 Machine Learning Web 服務。 使用部分批次可避免在將各次提取的事件聯合時產生額外的作業延遲負荷。

## <a name="new-function-related-monitoring-metrics"></a>新的函式相關監視計量
在串流分析作業的 [監視] 區域中，已加入三個額外的函式相關計量。 分別是 [函式要求]、[函式事件]、[失敗的函式要求]，如下圖所示。

![使用 Machine Learning 函式計量調整串流分析](./media/stream-analytics-scale-with-ml-functions/stream-analytics-scale-with-ml-functions-01.png "使用 Machine Learning 函式計量調整串流分析")

其定義如下：

**函式要求**：函式要求的數目。

**函式事件**：函式要求中的事件數目。

**失敗的函式要求**：失敗的函式要求數目。

## <a name="key-takeaways"></a>重要心得

若要使用 Machine Learning 函式調整串流分析作業，請考慮下列因素：

1. 輸入事件速率。
2. 執行中串流分析作業容許的延遲 (和 Machine Learning Web 服務要求的批次大小)。
3. 佈建的串流分析 SU 和 Machine Learning Web 服務要求數目 (額外的函式相關成本)。

以完全分割的串流分析查詢為例。 如果需要更複雜的查詢，[Microsoft Q&A 的 Azure 串流分析問題頁面](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)是可向串流分析小組取得其他協助的絕佳資源。

## <a name="next-steps"></a>後續步驟
若要深入了解串流分析，請參閱：

* [開始使用 Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [調整 Azure Stream Analytics 工作](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics 查詢語言參考](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure 串流分析管理 REST API 參考](https://msdn.microsoft.com/library/azure/dn835031.aspx)
