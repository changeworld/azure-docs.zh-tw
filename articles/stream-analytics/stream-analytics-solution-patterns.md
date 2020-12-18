---
title: Azure 串流分析解決方案模式
description: 瞭解 Azure 串流分析的常見解決方案模式，例如儀表板、事件訊息、資料存放區、參考資料擴充和監視。
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 02276ebf8e8a2289db2a1f6eac506443cea61c31
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97682714"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure 串流分析解決方案模式

如同 Azure 中的許多其他服務，串流分析最適合與其他服務搭配使用，以建立更大的端對端解決方案。 本文討論簡單的 Azure 串流分析解決方案和各種架構模式。 您可以建立這些模式，以開發更複雜的解決方案。 本文所述的模式可用於各種不同的案例。 案例特定模式的範例可在 [Azure 解決方案架構](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)上取得。

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>建立串流分析作業以提供即時儀表板體驗

使用 Azure 串流分析時，您可以快速地建立即時儀表板和警示。 簡單的解決方案會從事件中樞或 IoT 中樞內嵌事件，然後 [使用串流資料集將 Power BI 儀表板送入](/power-bi/service-real-time-streaming)。 如需詳細資訊，請參閱 [使用串流分析分析詐騙電話資料的詳細教學課程，並在 Power BI 儀表板中將結果視覺化](stream-analytics-real-time-fraud-detection.md)。

![ASA Power BI 儀表板](media/stream-analytics-solution-patterns/power-bi-dashboard.png)

此解決方案可以在 Azure 入口網站的幾分鐘內建立。 不涉及大量程式碼撰寫，並使用 SQL 語言來表達商務邏輯。

此解決方案模式可在瀏覽器中提供從事件來源到 Power BI 儀表板的最低延遲。 Azure 串流分析是唯一具有此內建功能的 Azure 服務。

## <a name="use-sql-for-dashboard"></a>將 SQL 用於儀表板

Power BI 儀表板提供低延遲，但無法用來產生完備的 Power BI 報表。 常見的報告模式是先將您的資料輸出至 SQL Database。 然後使用 Power BI 的 SQL connector 查詢 SQL 以取得最新資料。

![ASA SQL 儀表板](media/stream-analytics-solution-patterns/sql-dashboard.png)

使用 SQL Database 可提供更大的彈性，但代價是稍微高一點的延遲。 此解決方案最適合延遲需求超過一秒的作業。 使用這個方法，您可以將 Power BI 功能最大化，以進一步切割和骰子報表的資料，以及更多的視覺效果選項。 您也可以彈性地使用其他儀表板解決方案，例如 Tableau。

SQL 不是高輸送量資料存放區。 從 Azure 串流分析 SQL Database 的最大輸送量目前大約是 24 MB/s。 如果解決方案中的事件來源以較高的速率產生資料，您必須在串流分析中使用處理邏輯，以降低 SQL 的輸出速率。 您可以使用篩選、視窗型匯總、與時態性聯結的模式比對等技術，以及分析函數。 您可以使用 [Azure 串流分析輸出](stream-analytics-sql-output-perf.md)中所述的技術，進一步優化 SQL 的輸出速率，以 Azure SQL Database。

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>使用事件訊息，將即時深入解析併入您的應用程式

串流分析的第二個最常用用途是產生即時警示。 在此解決方案模式中，可以使用串流分析中的商務邏輯來偵測 [時態和空間模式](stream-analytics-geospatial-functions.md) 或 [異常](stream-analytics-machine-learning-anomaly-detection.md)狀況，然後產生警示信號。 不過，不同于「串流分析」使用 Power BI 作為慣用端點的儀表板解決方案，可以使用一些中繼資料接收。 這些接收器包括事件中樞、服務匯流排及 Azure Functions。 作為應用程式建立器，您需要決定哪一個資料接收最適合您的案例。

下游事件取用者邏輯必須實作為在現有商務工作流程中產生警示。 由於您可以在 Azure Functions 中執行自訂邏輯，因此 Azure Functions 是執行這項整合最快的方式。 您可以 [從 Azure 串流分析作業的「執行 Azure Functions](stream-analytics-with-azure-functions.md)」中找到使用 azure 函式作為串流分析作業輸出的教學課程。 Azure Functions 也支援各種類型的通知，包括文字和電子郵件。 邏輯應用程式也可用於這類整合，以及串流分析和邏輯應用程式之間的事件中樞。

![ASA 事件訊息應用程式](media/stream-analytics-solution-patterns/event-messaging-app.png)

另一方面，事件中樞提供最具彈性的整合點。 許多其他服務，例如 Azure 資料總管和時間序列深入解析都可以取用事件中樞的事件。 服務可以直接連接至 Azure 串流分析的事件中樞接收，以完成解決方案。 事件中樞也是 Azure 上可用於這類整合案例的最高輸送量訊息代理程式。

## <a name="dynamic-applications-and-websites"></a>動態應用程式和網站

您可以使用 Azure 串流分析和 Azure SignalR Service 來建立自訂即時視覺效果，例如儀表板或地圖視覺效果。 使用 SignalR 時，可以即時更新和顯示動態內容的 web 用戶端。

![ASA 動態應用程式](media/stream-analytics-solution-patterns/dynamic-app.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>透過資料存放區，將即時深入解析併入您的應用程式

現今大部分的 web 服務和 web 應用程式都會使用要求/回應模式來提供展示層。 要求/回應模式很容易建立，而且可以使用無狀態前端和可擴充的存放區（例如 Cosmos DB），輕鬆地以低回應時間進行調整。

高資料量通常會在 CRUD 型系統中產生效能瓶頸。 [事件來源解決方案模式](/azure/architecture/patterns/event-sourcing)可用來解決效能瓶頸。 從傳統資料存放區解壓縮時，暫時模式和見解也很困難且沒有效率。 新式大量資料驅動的應用程式通常採用以資料流程為基礎的架構。 Azure 串流分析作為移動中資料的計算引擎是該架構中的 linchpin。

![ASA 事件來源應用程式](media/stream-analytics-solution-patterns/event-sourcing-app.png)

在此解決方案模式中，Azure 串流分析會處理事件並將其匯總至資料存放區。 應用層會使用傳統的要求/回應模式與資料存放區互動。 由於串流分析能夠即時處理大量事件，因此應用程式可高度擴充，而不需要大量設定資料存放區層。 資料存放區層基本上是系統中的具體化視圖。 [Azure 串流分析輸出至 Azure Cosmos DB](stream-analytics-documentdb-output.md) 描述如何使用 Cosmos DB 作為串流分析輸出。

在實際的應用程式中，如果處理邏輯很複雜，而且需要獨立地升級邏輯的某些部分，則可以將多個串流分析工作與事件中樞組合在一起，以作為中繼事件代理程式。

![ASA 複雜事件來源應用程式](media/stream-analytics-solution-patterns/event-sourcing-app-complex.png)

此模式可改善系統的復原能力和管理性。 不過，儘管串流分析只保證一次處理，但可能有重複的事件可能會落在中繼事件中樞的機會。 下游串流分析作業必須在回顧視窗中使用邏輯索引鍵來重複使用事件。 如需事件傳遞的詳細資訊，請參閱 [事件傳遞保證](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) 參考。

## <a name="use-reference-data-for-application-customization"></a>使用參考資料來自訂應用程式

Azure 串流分析的參考資料功能是專為終端使用者自訂而設計，例如警示閾值、處理規則和 [地理柵欄](geospatial-scenarios.md)。 應用層可以接受參數變更，並將它們儲存在 SQL Database 中。 串流分析作業會定期查詢資料庫的變更，並透過參考資料聯結讓自訂參數可供存取。 如需有關如何使用參考資料來自訂應用程式的詳細資訊，請參閱 [SQL 參考資料](sql-reference-data.md) 和 [參考資料聯結](/stream-analytics-query/reference-data-join-azure-stream-analytics)。

這個模式也可以用來執行規則引擎，其中規則的閾值是從參考資料定義。 如需規則的詳細資訊，請參閱 [在 Azure 串流分析中處理可設定的閾值型規則](stream-analytics-threshold-based-rules.md)。

![ASA 參考資料應用程式](media/stream-analytics-solution-patterns/reference-data-app.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>將 Machine Learning 新增至您的即時見解

「Azure 串流分析」內建的 [異常偵測模型](stream-analytics-machine-learning-anomaly-detection.md) ，可讓您輕鬆地對即時應用程式引進 Machine Learning。 如需更廣泛的 Machine Learning 需求，請參閱 [Azure 串流分析與 Azure Machine Learning 的評分服務整合](stream-analytics-machine-learning-integration-tutorial.md)。

若為想要將線上訓練和評分納入相同串流分析管線的 advanced users，請參閱此如何搭配 [線性回歸](stream-analytics-high-frequency-trading.md)的範例。

![ASA Machine Learning 應用程式](media/stream-analytics-solution-patterns/machine-learning-app.png)

## <a name="real-time-data-warehousing"></a>即時資料倉儲

另一種常見的模式是即時資料倉儲，也稱為串流處理資料倉儲。 除了從您的應用程式抵達事件中樞和 IoT 中樞的事件之外，在 [IoT Edge 上執行的 Azure 串流分析](stream-analytics-edge.md) 可以用來滿足資料清理、資料縮減，以及資料儲存和轉寄需求。 在 IoT Edge 上執行的串流分析可正常處理系統中的頻寬限制和連線能力問題。 串流分析在寫入 Azure Synapse Analytics 時，可支援最多 200MB/秒的輸送量速率。

![ASA 資料倉儲](media/stream-analytics-solution-patterns/data-warehousing.png)


## <a name="archiving-real-time-data-for-analytics"></a>封存即時資料以進行分析

大部分的資料科學和分析活動仍會離線。 透過 Azure Data Lake 存放區 Gen2 輸出和 Parquet 輸出格式，Azure 串流分析可以封存資料。 這項功能可免除將資料直接送入 Azure Data Lake Analytics、Azure Databricks 和 Azure HDInsight 的分歧。 Azure 串流分析可作為此解決方案中的近乎即時 ETL 引擎。 您可以使用各種計算引擎，探索 Data Lake 中的封存資料。

> [!div class="mx-imgBorder"]
> ![ASA 離線分析](media/stream-analytics-solution-patterns/offline-analytics.png)

## <a name="use-reference-data-for-enrichment"></a>使用擴充的參考資料

資料擴充通常是 ETL 引擎的需求。 Azure 串流分析支援使用來自 SQL Database 和 Azure Blob 儲存體的 [參考資料](stream-analytics-use-reference-data.md) 來進行資料擴充。 Azure Data Lake 和 Azure Synapse Analytics 的資料登陸都可以完成資料擴充。


![使用資料擴充的 ASA 離線分析](media/stream-analytics-solution-patterns/offline-analytics-enriched.png)

## <a name="operationalize-insights-from-archived-data"></a>從封存的資料讓見解

如果您將離線分析模式與近乎即時的應用程式模式結合，您就可以建立意見反應迴圈。 意見反應迴圈可讓應用程式自動調整以變更資料中的模式。 此意見反應迴圈可以像變更警示的臨界值一樣簡單，也可以像重新定型 Machine Learning 模型一樣複雜。 相同的解決方案架構可同時套用至在雲端中執行的 ASA 作業和 IoT Edge 上。

![ASA insights 運算化](media/stream-analytics-solution-patterns/insights-operationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>如何監視 ASA 作業

Azure 串流分析作業可以執行24/7 來持續即時處理內送事件。 其執行時間保證對整個應用程式的健全狀況很重要。 雖然串流分析是產業中唯一提供  [99.9% 可用性保證](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)的串流分析服務，但您仍可能會產生某種程度的停機時間。 多年來，串流分析引進了度量、記錄和作業狀態，以反映工作的健康情況。 所有這些都是透過 Azure 監視器服務呈現，且可進一步匯出至 OMS。 如需詳細資訊，請參閱 [瞭解串流分析作業監視和如何監視查詢](stream-analytics-monitoring.md)。

![ASA 監視](media/stream-analytics-solution-patterns/monitoring.png)

有兩個重要事項需要監視：

- [作業失敗狀態](job-states.md)

    首先也是最重要的，您必須確定工作正在執行。 如果沒有工作處於執行中狀態，則不會產生新的計量或記錄。 作業可能因各種原因而變更為失敗狀態，包括具有高 SU 使用率等級 (也就是) 資源不足。

- [浮水印延遲計量](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    此計量會反映您處理管線的背後 (秒) 的時鐘時間。 某些延遲是以固有的處理邏輯為屬性。 因此，監視增加的趨勢比監視絕對值更為重要。 穩定的狀態延遲應該由您的應用程式設計來處理，而不是藉由監視或警示來解決。

失敗時，活動記錄和 [診斷記錄](stream-analytics-job-diagnostic-logs.md) 是開始尋找錯誤的最佳位置。

## <a name="build-resilient-and-mission-critical-applications"></a>打造具有復原能力與任務關鍵性的應用程式

無論 Azure 串流分析的 SLA 保證為何，以及如何謹慎執行端對端應用程式，都會發生中斷。 如果您的應用程式是任務關鍵性的，您必須為中斷做好準備，才能正常復原。

針對警示應用程式，最重要的一點就是偵測下一個警示。 您可以選擇在復原時從目前的時間重新開機作業，並忽略過去的警示。 作業開始時間的語法是第一個輸出時間，而不是第一個輸入時間。 輸入會回溯一段適當的時間，以保證指定時間的第一個輸出是完整且正確的。 如此一來，您就不會收到部分匯總，也不會觸發警示。

您也可以選擇在過去一段時間內開始輸出。 事件中樞和 IoT 中樞的保留原則都會保存合理數量的資料，以允許過去的處理。 缺點是您最多可以趕上目前的時間，並開始產生及時的新警示。 資料會在一段時間內快速遺失其值，因此請務必快速趕上目前的時間。 有兩種方式可以快速趕上：

- 在趕上 (SU) 布建更多資源。
- 從目前的時間重新開機。

從目前的時間重新開機是很簡單的作法，因為它會在處理期間保持間距。 以這種方式重新開機對於警示案例可能會有問題，但對於儀表板案例可能會有問題，而且是封存和資料倉儲案例的非入門版。

布建更多資源可以加速處理常式，但處理速率激增的效果很複雜。

- 測試您的作業是否可調整為更大量的 su。 並非所有查詢都是可調整的。 您必須確定 [您的查詢已平行](stream-analytics-parallelization.md)化。

- 請確定上游事件中樞或 IoT 中樞內有足夠的磁碟分割，您可以新增更多輸送量單位 (Tu) 調整輸入輸送量。 請記住，每個事件中樞 TU 到超出輸出速率為 2 MB/s。

- 確定您已在輸出接收中布建足夠的資源 (例如 SQL Database Cosmos DB) ，因此它們不會節流輸出中的激增，有時會導致系統鎖定。

最重要的一點是要預測處理速率變更、在進入生產環境之前先測試這些案例，並準備好在失敗復原期間正確調整處理。

在大部分的內送事件都延遲的情況下，如果您已將延遲時間範圍套用至您的作業， [可能會捨棄所有延遲的事件](stream-analytics-time-handling.md) 。 卸載事件可能會在一開始就看似神秘的行為;不過，考慮串流分析是即時處理引擎，它預期傳入的事件會接近時鐘時間。 它必須捨棄違反這些條件約束的事件。

### <a name="lambda-architectures-or-backfill-process"></a>Lambda 架構或回填進程

幸好先前的資料封存模式可用來正常處理這些晚期事件。 其概念是，封存作業會以抵達時間處理傳入事件，並將事件封存到 Azure Blob 或 Azure Data Lake 存放區中的適當時間值區（其事件時間）。 事件到達的延遲並不重要，因為它不會被捨棄。 它一律會落在正確的時間值區。 在復原期間，您可以重新處理封存的事件，並將結果回填至所選的存放區。 這類似于如何實作為 lambda 模式。

![ASA 回填](media/stream-analytics-solution-patterns/back-fill.png)

回填程式必須使用離線批次處理系統來完成，而這種系統最可能有與 Azure 串流分析不同的程式設計模型。 這表示您必須重新執行整個處理邏輯。

針對回填，至少要暫時將更多資源布建到輸出接收器，以處理比穩定狀態處理需求更高的輸送量。

|案例  |立即從現在重新開機  |從上次停止的時間重新開機 |從現在開始重新開機 + 封存事件的回填|
|---------|---------|---------|---------|
|**儀表板**   |建立間距    |短暫中斷時正常運作    |長時間中斷使用 |
|**警示**   |可以接受 |短暫中斷時正常運作    |不需要 |
|**事件來源應用程式** |可以接受 |短暫中斷時正常運作    |長時間中斷使用 |
|**資料倉儲**   |資料遺失  |可以接受 |不需要 |
|**離線分析**  |資料遺失  |可以接受 |不需要|

## <a name="putting-it-all-together"></a>總整理

不難想像上述所有解決方案模式都可以結合在複雜的端對端系統中。 結合的系統可以包括儀表板、警示、事件來源應用程式、資料倉儲和離線分析功能。

關鍵是要以可組合的模式來設計您的系統，因此每個子系統都可以獨立建立、測試、升級及復原。

## <a name="next-steps"></a>後續步驟

您現在已瞭解使用 Azure 串流分析的各種解決方案模式。 接下來，您可以深入了解並建立您的第一個串流分析作業：

* [使用 Azure 入口網站建立串流分析作業](stream-analytics-quick-create-portal.md)。
* [使用 Azure PowerShell 建立串流分析作業](stream-analytics-quick-create-powershell.md)。
* [使用 Visual Studio 建立串流分析作業](stream-analytics-quick-create-vs.md)。
