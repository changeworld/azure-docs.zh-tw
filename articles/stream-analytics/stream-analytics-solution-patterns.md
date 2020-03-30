---
title: Azure 串流分析解決方案模式
description: 瞭解 Azure 流分析的常見解決方案模式，例如儀表板、事件消息傳送、資料存儲、參考資料豐富和監視。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535777"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure 串流分析解決方案模式

與 Azure 中的許多其他服務一樣，流分析最好與其他服務一起使用，以創建更大的端到端解決方案。 本文討論簡單的 Azure 流分析解決方案和各種體系結構模式。 您可以基於這些模式來開發更複雜的解決方案。 本文中描述的模式可用於各種方案。 [Azure 解決方案體系結構](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)上提供了特定于方案的模式示例。

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>創建流分析作業，為即時儀表板體驗提供支援

借助 Azure 流分析，您可以快速啟動即時儀表板和警報。 簡單的解決方案從事件中心或 IoT 中心引入事件，並將[Power BI 儀表板與流式資料集一起饋送](/power-bi/service-real-time-streaming)。 有關詳細資訊，請參閱詳細的教程[使用流分析分析撥打電話資料，並在 Power BI 儀表板中視覺化結果](stream-analytics-manage-job.md)。

![ASA 電源 BI 儀表板](media/stream-analytics-solution-patterns/pbidashboard.png)

此解決方案只需幾分鐘即可從 Azure 門戶生成。 不涉及廣泛的編碼，SQL 語言用於表示業務邏輯。

此解決方案模式提供從事件源到瀏覽器中 Power BI 儀表板的最低延遲。 Azure 流分析是唯一具有此內置功能的 Azure 服務。

## <a name="use-sql-for-dashboard"></a>將 SQL 用於儀表板

Power BI 儀表板提供低延遲，但不能用於生成完整的 Power BI 報告。 常見的報告模式是首先將資料輸出到 SQL 資料庫。 然後使用 Power BI 的 SQL 連接器查詢 SQL 以訪問最新資料。

![ASA SQL 儀表板](media/stream-analytics-solution-patterns/sqldashboard.png)

使用 SQL 資料庫可為您提供更大的靈活性，但代價是延遲稍高。 此解決方案最適合延遲要求大於一秒的作業。 使用此方法，您可以最大化 Power BI 功能，以進一步切片和分塊報表的資料，以及更多的視覺化選項。 您還可以靈活地使用其他儀表板解決方案，如 Tableau。

SQL 不是高輸送量資料存儲。 Azure 流分析對 SQL 資料庫的最大輸送量當前約為 24 MB/s。 如果解決方案中的事件源以更高的速率生成資料，則需要在流分析中使用處理邏輯來降低 SQL 的輸出速率。 可以使用篩選、視窗聚合、模式與時態聯接匹配和分析函數等技術。 使用[Azure 流分析輸出到 Azure SQL 資料庫](stream-analytics-sql-output-perf.md)中描述的技術，可以進一步優化 SQL 的輸出速率。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>通過事件消息傳遞將即時見解融入到應用程式中

流分析的第二個常用用途是生成即時警報。 在此解決方案模式中，流分析中的業務邏輯可用於檢測[時間和空間模式](stream-analytics-geospatial-functions.md)或[異常](stream-analytics-machine-learning-anomaly-detection.md)，然後生成警報信號。 但是，與流分析使用 Power BI 作為首選終結點的儀表板解決方案不同，可以使用許多中間資料接收器。 這些接收器包括事件中心、服務匯流排和 Azure 函數。 作為應用程式產生器，您需要決定哪個資料接收器最適合您的方案。

必須實現下游事件消費者邏輯，才能在現有業務工作流中生成警報。 由於可以在 Azure 函數中實現自訂邏輯，因此 Azure 函數是執行此集成的最快方式。 有關使用 Azure 函數作為流分析作業的輸出的教程，可以在[Azure 流分析作業中運行 Azure 函數](stream-analytics-with-azure-functions.md)。 Azure 函數還支援各種類型的通知，包括文本和電子郵件。 邏輯應用也可用於此類集成，與流分析和邏輯應用之間的事件中心。

![ASA 事件消息應用](media/stream-analytics-solution-patterns/eventmessagingapp.png)

另一方面，事件中心提供最靈活的集成點。 許多其他服務（如 Azure 資料資源管理器和時間序列見解）可以使用事件中心的事件。 服務可以直接連接到 Azure 流分析的事件中心接收器以完成解決方案。 事件中心也是 Azure 上可用於此類集成方案的最高輸送量消息傳遞代理。

## <a name="dynamic-applications-and-websites"></a>動態應用程式和網站

您可以使用 Azure 流分析和 Azure 信號R 服務創建自訂即時視覺化效果，如儀表板或地圖視覺化效果。 使用 SignalR，可以更新 Web 用戶端並即時顯示動態內容。

![ASA 動態應用](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>通過資料存儲將即時見解融入到應用程式中

如今，大多數 Web 服務和 Web 應用程式都使用請求/回應模式為展示層提供服務。 請求/回應模式易於構建，並且可以使用無狀態前端和可擴展存儲（如 Cosmos DB）輕鬆以低回應時間進行縮放。

高資料量通常會在基於 CRUD 的系統中造成性能瓶頸。 [事件源解決方案模式](/azure/architecture/patterns/event-sourcing)用於解決性能瓶頸。 從傳統資料存儲中提取時態模式和見解也很困難且效率低下。 現代大容量資料驅動應用程式通常採用基於資料流程的體系結構。 Azure 流分析作為動態資料的計算引擎是該體系結構中的關鍵。

![ASA 事件源應用](media/stream-analytics-solution-patterns/eventsourcingapp.png)

在此解決方案模式中，事件由 Azure 流分析處理並聚合到資料存儲中。 應用程式層使用傳統的請求/回應模式與資料存儲進行交互。 由於 Stream Analytics 能夠即時處理大量事件，因此應用程式具有高度可擴充性，無需大量擴展資料存儲層。 資料存儲層本質上是系統中的一個具體視圖。 [Azure 流分析輸出到 Azure Cosmos DB](stream-analytics-documentdb-output.md)描述了如何使用 Cosmos DB 作為流分析輸出。

在實際應用程式中，處理邏輯很複雜，需要獨立升級邏輯的某些部分，可以將多個流分析作業與事件中心一起組合，作為仲介事件代理。

![ASA 複雜事件源應用](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

此模式提高了系統的彈性和可管理性。 但是，即使流分析保證一次處理，重複事件在仲介事件中心中出現的可能性很小。 下游流分析作業在回望視窗中使用邏輯鍵對事件進行 dedupe 事件非常重要。 有關事件傳遞的詳細資訊，請參閱[事件傳遞保證](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)參考。

## <a name="use-reference-data-for-application-customization"></a>使用參考資料進行應用程式自訂

Azure 流分析參考資料功能專為最終使用者自訂而設計，如警報閾值、處理規則和[地理圍欄](geospatial-scenarios.md)。 應用程式層可以接受參數更改並將其存儲在 SQL 資料庫中。 流分析作業定期查詢資料庫中的更改，並通過參考資料聯接使自訂參數可訪問。 有關如何將參考資料用於應用程式自訂的詳細資訊，請參閱[SQL 參考資料和](sql-reference-data.md)[參考資料聯接](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

此模式還可用於實現規則引擎，其中規則的閾值是從參考資料定義的。 有關規則的詳細資訊，請參閱[Azure 流分析中基於閾值的過程可配置規則](stream-analytics-threshold-based-rules.md)。

![ASA 參考資料應用](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>將機器學習添加到即時洞察中

Azure 流分析的內置[異常檢測模型](stream-analytics-machine-learning-anomaly-detection.md)是向即時應用程式介紹機器學習的便捷方法。 有關更廣泛的機器學習需求，請參閱[Azure 流分析與 Azure 機器學習的評分服務集成](stream-analytics-machine-learning-integration-tutorial.md)。

對於希望將線上培訓和評分合併到同一流分析管道中的高級使用者，請參閱以下示例，瞭解如何使用[線性回歸](stream-analytics-high-frequency-trading.md)執行此操作。

![ASA 機器學習應用](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>近即時資料倉儲

另一種常見的模式是即時資料倉儲，也稱為流資料倉儲。 除了從應用程式到達事件中心和 IoT 中心的事件外，在[IoT Edge 上運行的 Azure 流分析](stream-analytics-edge.md)還可用於滿足資料清理、資料縮減以及資料存儲和轉發需求。 在 IoT Edge 上運行的流分析可以正常處理系統中的頻寬限制和連接問題。 SQL 輸出配接器可用於輸出到 SQL 資料倉儲;但是，最大輸送量限制為 10 MB/s。

![ASA 資料倉儲](media/stream-analytics-solution-patterns/datawarehousing.png)

通過一些延遲權衡來提高輸送量的一種方法是將資料存檔到 Azure Blob 存儲中，然後[使用 Polybase 將它們導入到 SQL 資料倉儲](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)中。 您必須通過[按時間戳記存檔資料](stream-analytics-custom-path-patterns-blob-storage-output.md)並定期導入，手動將從流分析到 blob 存儲的輸出和從 Blob 存儲的輸入拼接在一起。

在此使用模式中，Azure 流分析用作近即時 ETL 引擎。 新到達的事件會不斷轉換和存儲，以便下游分析服務消耗。

![ASA 高輸送量資料倉儲](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>存檔即時資料以進行分析

大多數資料科學和分析活動仍離線進行。 資料可以通過 Azure 資料湖存儲 Gen2 輸出和 Parquet 輸出格式由 Azure 流分析存檔。 此功能消除了將資料直接饋入 Azure 資料湖分析、Azure 資料塊和 Azure HDInsight 的摩擦。 Azure 流分析在此解決方案中用作幾乎即時 ETL 引擎。 您可以使用各種計算引擎在資料湖中流覽存檔資料。

![ASA 離線分析](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用參考資料進行濃縮

資料擴充通常是 ETL 引擎的要求。 Azure 流分析使用 SQL 資料庫和 Azure Blob 存儲中的[參考資料](stream-analytics-use-reference-data.md)支援資料擴充。 資料擴充可用於在 Azure 資料湖和 SQL 資料倉儲中登陸資料。

![ASA 離線分析，資料豐富](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>從存檔資料操作見解

如果將離線分析模式與接近即時的應用程式模式相結合，則可以創建回饋迴圈。 回饋迴圈允許應用程式自動調整以更改資料中的模式。 此回饋迴圈可以像更改警報的閾值一樣簡單，也可以像重新訓練機器學習模型一樣複雜。 相同的解決方案體系結構可以應用於在雲中運行的 ASA 作業和 IoT Edge 上。

![ASA 洞察操作](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何監視 ASA 作業

Azure 流分析作業可以全天候運行，以便即時連續處理傳入事件。 其發佈時間保證對整個應用的健康至關重要。 雖然流分析是業內唯一提供[99.9% 可用性保證的](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)流分析服務，但您仍可能遭受某種程度的停機時間。 多年來，流分析引入了指標、日誌和作業狀態，以反映作業的運行狀況。 所有這些都通過 Azure 監視器服務顯示，並可以進一步匯出到 OMS。 有關詳細資訊，請參閱[瞭解流分析作業監視以及如何監視查詢](stream-analytics-monitoring.md)。

![ASA 監控](media/stream-analytics-solution-patterns/monitoring.png)

有兩個關鍵事項需要監視：

- [作業失敗狀態](job-states.md)

    首先，您需要確保作業正在運行。 如果沒有處於運行狀態的作業，則不會生成新的指標或日誌。 由於各種原因，作業可能會更改為失敗狀態，包括 SU 利用率較高（即資源耗盡）。

- [浮水印延遲指標](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此指標反映處理管道在掛時鐘時間（秒）中落後多遠。 一些延遲歸因於固有的處理邏輯。 因此，監測上升趨勢比監視絕對值重要得多。 穩定狀態延遲應由應用程式設計解決，而不是通過監視或警報來解決。

失敗後，活動日誌和[診斷日誌](stream-analytics-job-diagnostic-logs.md)是開始查找錯誤的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>構建彈性和要徑任務應用程式

無論 Azure 流分析的 SLA 保證以及運行端到端應用程式的謹慎程度如何，都會發生中斷。 如果應用程式至關重要，則需要為中斷做好準備，以便正常恢復。

對於警報應用程式，最重要的是檢測下一個警報。 您可以選擇從恢復時的目前時間重新開機作業，忽略過去的警報。 作業開始時間語義按第一次輸出時間，而不是第一個輸入時間。 輸入向後回轉，以確保指定時間內的第一個輸出完整且正確。 因此，您不會意外獲得部分聚合和觸發警報。

您也可以選擇從過去的一段時間開始輸出。 事件中心和 IoT 中心的保留原則都包含合理數量的資料，以便從過去進行處理。 權衡是，您可以以多快的速度趕上目前時間並開始及時生成新警報。 資料會隨著時間的推移迅速失去其價值，因此快速趕上目前時間非常重要。 有兩種方法可以快速趕上：

- 在追趕時提供更多資源 （SU）。
- 從目前時間重新開機。

從當前重新開機時間非常簡單，在加工過程中留出間隙。 以這種方式重新開機可能適合警報方案，但對於儀表板方案可能存在問題，並且不是存檔和資料倉儲方案的啟動器。

調配更多資源可以加快流程，但處理速率激增的影響很複雜。

- 測試作業是否可擴展到更多 S。 並非所有查詢都是可擴展的。 您需要確保查詢是[並行化的](stream-analytics-parallelization.md)。

- 確保上游事件中心或 IoT 中心中有足夠的分區，可以添加更多輸送量單位 （TUs） 以擴展輸入輸送量。 請記住，每個事件中心 TU 以 2 MB/s 的輸出速率進行最大值。

- 請確保在輸出接收器中預配了足夠的資源（即 SQL 資料庫、Cosmos DB），因此它們不會限制輸出激增，這有時會導致系統鎖定。

最重要的是預測處理速率變化，在投入生產之前測試這些方案，並準備在故障恢復期間正確擴展處理。

在傳入事件都延遲的極端情況下，如果已對作業應用延遲到達視窗，[則所有延遲事件都可能被刪除](stream-analytics-time-handling.md)。 事件的下降在開始時可能看起來是一種神秘的行為;但是，考慮到流分析是一個即時處理引擎，它預計傳入事件將接近掛時時間。 它必須刪除違反這些限制的事件。

### <a name="lambda-architectures-or-backfill-process"></a>蘭姆達架構或回填過程

幸運的是，以前的資料存檔模式可用於正常處理這些後置事件。 其思想是，存檔作業在到達時處理傳入事件，並將事件存檔到 Azure Blob 或 Azure 資料湖存儲中及其事件時間的正確時間存儲桶中。 無論事件到達多晚，它都不會被丟棄。 它總是降落在正確的時間桶。 在恢復期間，可以重新處理存檔的事件，並將結果回填到首選存儲中。 這與 lambda 模式的實現方式類似。

![ASA 回填](media/stream-analytics-solution-patterns/backfill.png)

回填過程必須使用離線批次處理系統來完成，該系統很可能具有與 Azure 流分析不同的程式設計模型。 這意味著您必須重新實現整個處理邏輯。

對於回填，至少臨時向輸出接收器預配更多資源以處理比穩定狀態處理需求更高的輸送量仍然很重要。

|案例  |僅從現在開始重新開機  |從上次停止時間重新開機 |從現在開始重新開機 = 使用存檔事件回填|
|---------|---------|---------|---------|
|**儀表板**   |創建間隙    |適合短中斷    |用於長時間中斷 |
|**提醒**   |可以接受 |適合短中斷    |不需要 |
|**事件源應用** |可以接受 |適合短中斷    |用於長時間中斷 |
|**資料倉儲**   |資料丟失  |可以接受 |不需要 |
|**離線分析**  |資料丟失  |可以接受 |不需要|

## <a name="putting-it-all-together"></a>總整理

不難想像，上述所有解決方案模式都可以在複雜的端到端系統中組合在一起。 組合的系統可以包括儀表板、警報、事件源應用程式、資料倉儲和離線分析功能。

關鍵是以可組合模式設計系統，以便可以獨立構建、測試、升級和恢復每個子系統。

## <a name="next-steps"></a>後續步驟

現在，您已經看到使用 Azure 流分析的各種解決方案模式。 接下來，您可以深入了解並建立您的第一個串流分析作業：

* [使用 Azure 門戶創建流分析作業](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 創建流分析作業](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)。
