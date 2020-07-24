---
title: Azure 串流分析解決方案模式
description: 瞭解 Azure 串流分析的常見解決方案模式，例如儀表板管理、事件訊息、資料存放區、參考資料擴充和監視。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cb9c851ca33aa6eeb6d0fe0576f98ecb0693be02
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/23/2020
ms.locfileid: "86999265"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure 串流分析解決方案模式

就像 Azure 中的許多其他服務一樣，串流分析最好與其他服務搭配使用，以建立較大的端對端解決方案。 本文討論簡單的 Azure 串流分析解決方案和各種架構模式。 您可以建立這些模式來開發更複雜的解決方案。 本文中所述的模式可用於各種不同的案例。 案例特定模式的範例可在[Azure 解決方案架構](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)上取得。

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>建立串流分析作業以提供即時儀表板管理體驗

使用 Azure 串流分析，您可以快速地建立即時儀表板和警示。 簡單的解決方案會從事件中樞或 IoT 中樞中內嵌事件，並[使用串流資料集將 Power BI 儀表板饋送](/power-bi/service-real-time-streaming)。 如需詳細資訊，請參閱[使用串流分析分析通話資料和在 Power BI 儀表板中將結果視覺化](stream-analytics-manage-job.md)的詳細教學課程。

![ASA Power BI 儀表板](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

這項解決方案只能在 Azure 入口網站的幾分鐘內建立。 不涉及任何延伸的編碼，而且會使用 SQL 語言來表達商務邏輯。

此解決方案模式可提供從事件來源到瀏覽器中 Power BI 儀表板的最低延遲。 Azure 串流分析是唯一具備此內建功能的 Azure 服務。

## <a name="use-sql-for-dashboard"></a>使用適用于儀表板的 SQL

[Power BI] 儀表板會提供低延遲，但不能用來產生完整的成熟 Power BI 報表。 常見的報告模式是先將您的資料輸出到 SQL Database。 然後使用 Power BI 的 SQL connector 查詢 SQL，以取得最新的資料。

![ASA SQL 儀表板](media/stream-analytics-solution-patterns/sql-dashboard.png)

使用 SQL Database 可為您提供更大的彈性，但代價是延遲稍微高一點。 對於延遲需求大於一秒的作業，此解決方案是最佳做法。 使用此方法，您可以最大化 Power BI 功能，以進一步切割和細分報表的資料，以及更多的視覺效果選項。 您也會獲得使用其他儀表板解決方案的彈性，例如 Tableau。

SQL 不是高輸送量資料存放區。 從 Azure 串流分析 SQL Database 的最大輸送量目前約為 24 MB/s。 如果您解決方案中的事件來源以較高的速率產生資料，您必須使用串流分析中的處理邏輯，以降低 SQL 的輸出速率。 如篩選、視窗型匯總、使用時態性聯結的模式比對，以及分析函式，都可以使用這類的技術。 使用 Azure 串流分析輸出中所述的技術，可以進一步優化 SQL 的輸出速率[，以 Azure SQL Database](stream-analytics-sql-output-perf.md)。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>使用事件訊息，將即時深入解析納入您的應用程式

串流分析的第二個最常見用法是產生即時警示。 在此解決方案模式中，串流分析中的商務邏輯可以用來偵測[時態性和空間模式](stream-analytics-geospatial-functions.md)或[異常](stream-analytics-machine-learning-anomaly-detection.md)，然後產生警示信號。 不過，不同于串流分析使用 Power BI 做為慣用端點的儀表板解決方案，可以使用數個中繼資料接收。 這些接收包括事件中樞、服務匯流排和 Azure Functions。 身為應用程式產生器，您必須決定哪一個資料接收最適合您的案例。

下游事件取用者邏輯必須執行，才能在您現有的商務工作流程中產生警示。 因為您可以在 Azure Functions 中執行自訂邏輯，所以 Azure Functions 是執行這項整合的最快方式。 如需使用 Azure Function 作為串流分析作業輸出的教學課程，請參閱[從 Azure 串流分析作業執行 Azure Functions](stream-analytics-with-azure-functions.md)。 Azure Functions 也支援各種類型的通知，包括文字和電子郵件。 邏輯應用程式也可以用於這類整合，串流分析和邏輯應用程式之間事件中樞。

![ASA 事件訊息應用程式](media/stream-analytics-solution-patterns/event-messaging-app.png)

另一方面，事件中樞則提供最具彈性的整合點。 許多其他服務（例如 Azure 資料總管和時間序列深入解析）都可以取用來自事件中樞的事件。 服務可以從 Azure 串流分析直接連線到事件中樞接收，以完成解決方案。 事件中樞也是 Azure 上提供的最高輸送量訊息代理程式，適用于這類整合案例。

## <a name="dynamic-applications-and-websites"></a>動態應用程式和網站

您可以使用 Azure 串流分析和 Azure SignalR Service，建立自訂的即時視覺效果，例如儀表板或地圖視覺效果。 您可以使用 SignalR 來更新 web 用戶端，並即時顯示動態內容。

![ASA 動態應用程式](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>透過資料存放區，將即時深入解析納入您的應用程式

現今大部分的 web 服務和 web 應用程式會使用要求/回應模式來提供展示層。 要求/回應模式很容易建立，而且可以使用無狀態前端和可擴充的存放區（例如 Cosmos DB），輕鬆地以低回應時間進行調整。

高資料量通常會在 CRUD 型系統中建立效能瓶頸。 [事件來源解決方案模式](/azure/architecture/patterns/event-sourcing)是用來解決效能瓶頸。 從傳統資料存放區解壓縮時，時態性模式和深入解析也很容易且效率不佳。 現代化的大量資料驅動應用程式通常會採用以資料流程為基礎的架構。 Azure 串流分析做為移動中資料的計算引擎是該架構中的 linchpin。

![ASA 事件來源應用程式](media/stream-analytics-solution-patterns/event-sourcing-app.png)

在此解決方案模式中，事件會由 Azure 串流分析處理並匯總至資料存放區。 應用層會使用傳統的要求/回應模式與資料存放區互動。 由於串流分析的即時處理大量事件的能力，因此應用程式具有高度擴充性，而不需要大量設定資料存放區層。 資料存放區層基本上是系統中的具體化視圖。 [Azure 串流分析輸出至 Azure Cosmos DB](stream-analytics-documentdb-output.md)描述 Cosmos DB 如何當做串流分析輸出使用。

在實際的應用程式中，如果處理邏輯很複雜，而且需要獨立升級邏輯的特定部分，則可以將多個串流分析作業連同事件中樞組成，做為中繼事件代理人。

![ASA 複雜事件來源應用程式](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

此模式可以改善系統的復原能力和管理性。 不過，即使串流分析保證只處理一次，事件也有可能會落在媒介事件中樞中。 下游串流分析作業必須使用回顧視窗中的邏輯索引鍵來重複重複事件。 如需事件傳遞的詳細資訊，請參閱[事件傳遞保證](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics)參考。

## <a name="use-reference-data-for-application-customization"></a>使用參考資料進行應用程式自訂

Azure 串流分析參考資料功能是特別針對使用者自訂（例如警示閾值、處理規則和[地理柵欄](geospatial-scenarios.md)）所設計。 應用層可以接受參數變更，並將它們儲存在 SQL Database 中。 串流分析作業會定期查詢資料庫的變更，並讓自訂參數可透過參考資料聯結來存取。 如需如何使用參考資料進行應用程式自訂的詳細資訊，請參閱[SQL 參考資料](sql-reference-data.md)和[參考資料聯結](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

此模式也可以用來執行規則引擎，其中規則的臨界值是從參考資料定義而來。 如需規則的詳細資訊，請參閱[在 Azure 串流分析中處理可設定的閾值型規則](stream-analytics-threshold-based-rules.md)。

![ASA 參考資料應用程式](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>將 Machine Learning 新增至您的即時深入解析

Azure 串流分析的內建[異常偵測模型](stream-analytics-machine-learning-anomaly-detection.md)，是為您的即時應用程式引進 Machine Learning 的便利方式。 如需更廣泛的 Machine Learning 需求，請參閱[Azure 串流分析與 Azure Machine Learning 的評分服務整合](stream-analytics-machine-learning-integration-tutorial.md)。

對於想要將線上訓練和評分納入相同串流分析管線的 advanced 使用者，請參閱此範例，瞭解如何使用[線性回歸](stream-analytics-high-frequency-trading.md)。

![ASA Machine Learning 應用程式](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="near-real-time-data-warehousing"></a>近乎即時的資料倉儲

另一個常見的模式是即時資料倉儲，也稱為「串流資料倉儲」。 除了抵達應用程式事件中樞和 IoT 中樞的事件之外，在[IoT Edge 上執行的 Azure 串流分析](stream-analytics-edge.md)可以用來滿足資料清理、資料縮減，以及資料存放區和轉送需求。 IoT Edge 上執行的串流分析可以適當地處理系統中的頻寬限制和連線問題。 SQL 輸出介面卡可以用來輸出至 SQL 資料倉儲;不過，最大輸送量限制為 10 MB/s。

![ASA 資料倉儲](media/stream-analytics-solution-patterns/data-warehousing.png)

有一些延遲取捨來改善輸送量的其中一個方法，就是將事件封存到 Azure Blob 儲存體，然後[使用 Polybase 將它們匯入 SQL 資料倉儲](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md)。 您必須手動將串流分析的輸出結合到 blob 儲存體，並透過[時間戳記](stream-analytics-custom-path-patterns-blob-storage-output.md)和定期匯入來封存資料，從 blob 儲存體輸入到 SQL 資料倉儲。

在此使用模式中，Azure 串流分析是用來做為近乎即時的 ETL 引擎。 針對下游分析服務的耗用量，會持續轉換並儲存新抵達的事件。

![ASA 高輸送量資料倉儲](media/stream-analytics-solution-patterns/data-warehousing-high-throughput.png)

## <a name="archiving-real-time-data-for-analytics"></a>封存即時資料以進行分析

大部分的資料科學和分析活動仍然會離線。 Azure 串流分析可透過 Azure Data Lake 存放區 Gen2 輸出和 Parquet 輸出格式來封存資料。 這項功能可移除將資料直接送入 Azure Data Lake Analytics、Azure Databricks 和 Azure HDInsight 的摩擦。 在此解決方案中，Azure 串流分析是用來做為近乎即時的 ETL 引擎。 您可以使用各種計算引擎，在 Data Lake 中探索封存的資料。

> [!div class="mx-imgBorder"]
> ![ASA 離線分析](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用擴充的參考資料

資料擴充通常是 ETL 引擎的需求。 Azure 串流分析支援使用來自 SQL Database 和 Azure Blob 儲存體的[參考資料](stream-analytics-use-reference-data.md)來進行資料擴充。 Azure Data Lake 和 SQL 資料倉儲中的資料登陸都可以進行資料擴充。


![使用資料擴充的 ASA 離線分析](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>從封存的資料讓見解

如果您將離線分析模式與近乎即時的應用程式模式結合，您就可以建立意見反應迴圈。 「意見反應」迴圈可讓應用程式針對資料中的變更模式自動調整。 此意見反應迴圈可以像變更警示的臨界值一樣簡單，或與重新定型 Machine Learning 模型一樣複雜。 相同的解決方案架構可以同時套用至在雲端和 IoT Edge 上執行的 ASA 作業。

![ASA 深入解析運算化](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何監視 ASA 作業

Azure 串流分析作業可以執行24/7，即時處理傳入事件。 其執行時間保證對整體應用程式的健全狀況非常重要。 雖然串流分析是業界唯一提供[99.9% 可用性保證](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)的串流分析服務，但您仍可能會產生某種程度的停機時間。 多年來，串流分析引進了計量、記錄和作業狀態，以反映作業的健全狀況。 這些全都是透過 Azure 監視器服務呈現，而且可以進一步匯出至 OMS。 如需詳細資訊，請參閱[瞭解串流分析作業監視和如何監視查詢](stream-analytics-monitoring.md)。

![ASA 監視](media/stream-analytics-solution-patterns/monitoring.png)

有兩個要監視的重要事項：

- [作業失敗狀態](job-states.md)

    首先和最重要的是，您必須確定作業正在執行。 如果沒有處於執行中狀態的作業，就不會產生任何新的計量或記錄。 作業可能會因為各種原因而變更為失敗狀態，包括具有高 SU 使用率層級（亦即，資源不足）。

- [浮水印延遲計量](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此計量會反映處理管線落後時鐘時間（秒）的進度。 部分延遲是以固有的處理邏輯為屬性。 因此，監視增加的趨勢比監視絕對值更重要。 穩定狀態延遲應由您的應用程式設計來處理，而不是透過監視或警示來解決。

失敗時，[活動記錄] 和 [[診斷記錄](stream-analytics-job-diagnostic-logs.md)] 是開始尋找錯誤的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>建立具有彈性和任務關鍵性的應用程式

不論 Azure 串流分析的 SLA 保證，以及如何謹慎執行您的端對端應用程式，都會發生中斷。 如果您的應用程式是要徑任務，您必須做好中斷的準備，才能正常復原。

針對警示應用程式，最重要的就是偵測下一個警示。 在復原時，您可以選擇從目前的時間重新開機作業，忽略過去的警示。 作業開始時間的語義是第一個輸出時間，而不是第一個輸入時間。 輸入會向前回溯一段適當的時間，以確保指定時間的第一個輸出是完整且正確的。 因此，您不會取得部分匯總並會意外觸發警示。

您也可以選擇從過去一段時間開始輸出。 事件中樞和 IoT 中樞的保留原則都會保留合理的資料量，以允許過去的處理。 其取捨是您最多可以趕上目前的時間，並開始產生及時的新警示。 資料會在一段時間內快速失去其價值，因此請務必快速趕上目前的時間。 有兩種方式可以快速趕上：

- 在趕上時布建更多資源（SU）。
- 從目前的時間重新開機。

從目前的時間重新開機是很簡單的作法，因為這是在處理期間保持間隙的取捨。 以這種方式重新開機對於警示案例可能會是正常的，但對於儀表板案例會有問題，而且是封存和資料倉儲案例的非入門。

布建更多資源可以加速處理常式，但處理速率激增的效果很複雜。

- 測試您的作業是否可調整為更多的 su。 並非所有查詢都可調整。 您必須確定[您的查詢已平行](stream-analytics-parallelization.md)處理。

- 請確定上游事件中樞或 IoT 中樞中有足夠的磁碟分割，您可以新增更多的輸送量單位（Tu）來調整輸入輸送量。 請記住，每個事件中樞的 TU 都會以 2 MB/s 的輸出速率到超出。

- 請確定您已在輸出接收器（也就是 SQL Database、Cosmos DB）中布建足夠的資源，因此它們不會在輸出中調節激增，這有時候會導致系統鎖定。

最重要的是，在進入生產環境之前先測試這些案例，並準備好在失敗復原期間正確調整處理。

在內送事件全部延遲的極端案例中，如果您已將延遲抵達的視窗套用至作業，則[可能會捨棄所有延遲的事件](stream-analytics-time-handling.md)。 卸載事件可能會在一開始就是一種神秘的行為;不過，考慮串流分析是即時處理引擎，它預期傳入事件會接近時鐘時間。 它必須捨棄違反這些條件約束的事件。

### <a name="lambda-architectures-or-backfill-process"></a>Lambda 架構或回填進程

幸好，先前的資料封存模式可用來適當地處理這些晚期事件。 其概念是，封存作業會處理抵達時間內送的事件，並將事件封存到 Azure Blob 中的正確時間值區，或使用其事件時間來封存 Azure Data Lake。 事件抵達的頻率並不重要，而且永遠不會被捨棄。 它一律會落在正確的時間值區中。 在復原期間，可以重新處理已封存的事件，並將結果回填到所選的存放區。 這類似于實作為 lambda 模式的方式。

![ASA 回填](media/stream-analytics-solution-patterns/back-fill.png)

回填程式必須使用離線批次處理系統來完成，這很可能會有不同于 Azure 串流分析的程式設計模型。 這表示您必須重新執行整個處理邏輯。

針對回填，至少必須暫時布建更多資源給輸出接收，以處理比穩定狀態處理需求更高的輸送量。

|案例  |立即從現在重新開機  |從上次停止的時間重新開機 |透過封存事件從 now + 回填重新開機|
|---------|---------|---------|---------|
|**儀表板管理**   |建立間距    |短暫中斷就沒問題    |長時間中斷時使用 |
|**警示**   |可以接受 |短暫中斷就沒問題    |不需要 |
|**事件來源應用程式** |可以接受 |短暫中斷就沒問題    |長時間中斷時使用 |
|**資料倉儲**   |資料遺失  |可以接受 |不需要 |
|**離線分析**  |資料遺失  |可以接受 |不需要|

## <a name="putting-it-all-together"></a>總整理

並不難想像上述所有解決方案模式都可以結合在複雜的端對端系統中。 結合的系統可以包含儀表板、警示、事件來源應用程式、資料倉儲，以及離線分析功能。

關鍵在於將您的系統設計成可組合的模式，以便每個子系統可以獨立建立、測試、升級和復原。

## <a name="next-steps"></a>接下來的步驟

您現在已看到使用 Azure 串流分析的各種解決方案模式。 接下來，您可以深入了解並建立您的第一個串流分析作業：

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)。
