---
title: 收集記錄資料
titleSuffix: Azure Cognitive Search
description: 藉由啟用診斷設定來收集和分析記錄資料，然後使用 Kusto 查詢語言來探索結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 52230d6b13c4210e0ff8e85d0a3efe39af55f6e2
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935053"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集並分析 Azure 認知搜尋的記錄資料

診斷或作業記錄可讓您深入瞭解 Azure 認知搜尋的詳細作業，而且對於監視服務和工作負載程式很有用。 在內部，某些系統資訊存在於後端很短的時間內，足以在您提出支援票證時進行調查和分析。 但是，如果您想要對運算元據進行自我導向，您應該設定診斷設定，以指定收集記錄資訊的位置。

您可以透過與 [Azure 監視器](../azure-monitor/index.yml)整合來啟用診斷記錄。 

當您設定診斷記錄時，系統會要求您指定儲存機制。 下表列舉用來收集和保存資料的選項。

| 資源 | 用於 |
|----------|----------|
| [傳送至 Log Analytics 工作區](../azure-monitor/learn/tutorial-resource-logs.md) | 事件和計量會傳送至 Log Analytics 工作區，您可以在入口網站中查詢該工作區，以傳回詳細資訊。 如需簡介，請參閱 [開始使用 Azure 監視器記錄](../azure-monitor/log-query/get-started-portal.md) |
| [使用 Blob 儲存體保存](../storage/blobs/storage-blobs-overview.md) | 事件和計量會封存至 Blob 容器，並儲存在 JSON 檔案中。 記錄可能會有相當細微的 (（小時/分鐘）) ，很適合用來研究特定事件，而不是用於開放式調查。 您可以使用 JSON 編輯器來查看原始記錄檔或 Power BI，以匯總記錄資料並將其視覺化。|
| [串流至事件中樞](../event-hubs/index.yml) | 事件和計量會串流至 Azure 事件中樞服務。 請選擇此選項作為非常大型記錄的替代資料收集服務。 |

## <a name="prerequisites"></a>必要條件

事先建立資源，讓您可以在設定診斷記錄時選取一或多個資源。

+ [建立 log analytics 工作區](../azure-monitor/learn/quick-create-workspace.md)

+ [建立儲存體帳戶](../storage/common/storage-account-create.md)

+ [建立事件中樞](../event-hubs/event-hubs-create.md)

## <a name="enable-data-collection"></a>啟用資料收集

診斷設定會指定如何收集記錄的事件和計量。

1. 在 [監視]**** 下方，選取 [診斷設定]****。

   ![診斷設定](./media/search-monitor-usage/diagnostic-settings.png "診斷設定")

1. 選取 [ **+ 新增診斷設定**]

1. 檢查 **Log Analytics**、選取您的工作區，然後選取 [ **insight-logs-operationlogs** ] 和 [ **AllMetrics**]。

   ![設定資料收集](./media/search-monitor-usage/configure-storage.png "設定資料收集")

1. 儲存設定。

1. 啟用記錄之後，請使用您的搜尋服務來開始產生記錄和計量。 在記錄的事件和計量變成可用之前，需要一些時間。

若為 Log Analytics，則在資料可供使用之前會有幾分鐘的時間，之後您就可以執行 Kusto 查詢來傳回資料。 如需詳細資訊，請參閱 [監視查詢要求](search-monitor-logs.md)。

針對 Blob 儲存體，它需要一小時的時間，容器才會出現在 Blob 儲存體中。 每個容器每小時會有一個 Blob。 只有在有活動可供記錄或測量時，才會建立容器。 將資料複製到儲存體帳戶時，資料會格式化為 JSON 並放在兩個容器中︰

+ insights-logs-operationlogs：適用於搜尋流量記錄
+ insights-metrics-pt1m：適用於計量

## <a name="query-log-information"></a>查詢記錄資訊

有兩個數據表包含 Azure 認知搜尋的記錄和度量： **AzureDiagnostics** 和 **AzureMetrics**。

1. 在 [ **監視**] 底下，選取 [ **記錄**]。

1. 在 [查詢] 視窗中輸入 **AzureMetrics** 。 執行此簡單查詢，以熟悉此資料表中所收集的資料。 在資料表中滾動以查看度量和值。 請注意頂端的記錄計數，如果您的服務已收集一段時間的計量，您可能會想要調整時間間隔以取得可管理的資料集。

   ![AzureMetrics 資料表](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 資料表")

1. 輸入下列查詢，以傳回表格式結果集。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 重複先前的步驟，從 **AzureDiagnostics** 開始傳回所有資料行以供參考，然後再按一個更選擇性的查詢來解壓縮更有趣的資訊。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics 資料表](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics 資料表")

## <a name="kusto-query-examples"></a>Kusto 查詢範例

如果您已啟用診斷記錄，您可以查詢 **AzureDiagnostics** ，以取得在服務上執行的作業清單和時機。 您也可以將活動相互關聯，以調查效能的變更。

#### <a name="example-list-operations"></a>範例：列出作業 

傳回作業清單和每個作業的計數。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>範例：將作業相互關聯

使查詢要求與索引作業相互關聯，並在一段時間內轉譯資料點，以查看作業是否一致。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

## <a name="logged-operations"></a>記錄的作業

Azure 監視器所捕捉的記錄事件包含與索引和查詢相關的事件。 Log Analytics 中的 **AzureDiagnostics** 資料表會收集與查詢和索引相關的運算元據。

| OperationName | 描述 |
|---------------|-------------|
| ServiceStats | 這項作業是 [取得服務統計資料](/rest/api/searchservice/get-service-statistics)的例行呼叫，可直接或隱含地在載入或重新整理入口網站時，填入入口網站的總覽頁面。 |
| 查詢。搜尋 |  針對索引查詢要求的詳細資訊，請參閱 [監視查詢](search-monitor-queries.md) 以取得已記錄查詢的相關資訊。|
| 索引編制索引。索引  | 這項作業是 [加入、更新或刪除檔](/rest/api/searchservice/addupdate-or-delete-documents)的呼叫。 |
| 指標。原型 | 這是 [匯入資料] 嚮導所建立的索引。 |
| 索引子。建立 | 透過 [匯入資料] 嚮導明確或隱含地建立索引子。 |
| 索引子。 Get | 每次執行索引子時，傳回索引子的名稱。 |
| 索引子。狀態 | 每次執行索引子時，傳回索引子的狀態。 |
| 資料來源。 Get | 每次執行索引子時，傳回資料來源的名稱。|
| 索引。 Get | 每當執行索引子時，傳回索引的名稱。 |

## <a name="log-schema"></a>記錄檔結構描述

如果您要建立自訂報表，包含 Azure 認知搜尋記錄資料的資料結構會符合下列架構。 針對 Blob 儲存體，每個 blob 都有一個名為「 **記錄** 」的根物件，其中包含記錄物件的陣列。 每個 Blob 都包含在同一小時內發生之所有作業的記錄。

下表是資源記錄通用的部分欄位清單。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| timeGenerated |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId |
| operationName |字串 |"Query.Search" |作業的名稱 |
| operationVersion |字串 |"2020-06-30" |使用的 api-version |
| category |字串 |"OperationLogs" |常數 |
| resultType |字串 |"Success" |可能的值：Success 或 Failure |
| resultSignature |int |200 |HTTP 結果碼 |
| durationMS |int |50 |作業的持續時間 (以毫秒為單位) |
| properties |物件 (object) |請參閱下表 |包含作業特定資料的物件 |

### <a name="properties-schema"></a>屬性結構描述

以下是 Azure 認知搜尋特有的屬性。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| Description_s |字串 |"GET /indexes('content')/docs" |作業的端點 |
| Documents_d |int |42 |處理的文件數目 |
| IndexName_s |字串 |「測試索引」 |與作業相關聯的索引名稱 |
| Query_s |字串 |"？ search = AzureSearch&$count = true&api 版本 = 2020-06-30" |查詢參數 |

## <a name="metrics-schema"></a>度量結構描述

系統會針對查詢要求捕獲計量，並以一分鐘的間隔來測量。 每個度量會顯示每分鐘的最小值、最大值和平均值。 如需詳細資訊，請參閱 [監視查詢要求](search-monitor-queries.md)。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
| metricName |字串 |"Latency" |度量的名稱 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| average |int |64 |度量時間間隔中原始樣本的平均值，以秒為單位或百分比，視計量而定。 |
| minimum |int |37 |度量時間間隔中原始範例的最小值（以秒為單位）。 |
| maximum |int |78 |度量時間間隔中原始樣本的最大值（以秒為單位）。  |
| total |int |258 |度量時間間隔中原始樣本的總計值（以秒為單位）。  |
| count |int |4 |在一分鐘的間隔內，從節點到記錄檔發出的度量數目。  |
| timegrain |字串 |"PT1M" |度量的時間細微性（以 ISO 8601 為單位）。 |

查詢的執行通常是以毫秒為單位，因此只會在 QPS 之類的度量中出現測量為秒的查詢。

針對 **每秒搜尋查詢** 度量，最小值是在該分鐘內註冊的每秒搜尋查詢最小值。 最大值依此類推。 平均值是一分鐘的彙總。 例如，在一分鐘內，您可能會有如下所示的模式：高負載的一秒，這是 SearchQueriesPerSecond 的最大值，之後是平均負載的58秒，最後一秒只有一個查詢，這是最小值。

針對 **節流的搜尋查詢百分比**、最小值、最大值、平均值和總計，全都具有相同的值：已節流的搜尋查詢百分比，從搜尋查詢總數（以一分鐘為單位）。

## <a name="view-raw-log-files"></a>查看原始記錄檔

Blob 儲存體是用來封存記錄檔。 您可以使用任何 JSON 編輯器來檢視記錄檔。 如果您沒有編輯器，建議您使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 入口網站中，開啟您的儲存體帳戶。 

2. 在左側導覽窗格中，按一下 [Blob]****。 您應該會看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 當記錄資料匯出至 Blob 儲存體時，Azure 認知搜尋會建立這些容器。

3. 在資料夾階層中一路往下點選，直到抵達 .json 檔案為止。  請使用操作功能表來下載檔案。

下載檔案之後，在 JSON 編輯器中開啟它以檢視內容。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請參閱搜尋服務監視的基本概念，以瞭解全面的監督功能。

> [!div class="nextstepaction"]
> [在 Azure 認知搜尋中監視作業和活動](search-monitor-usage.md)