---
title: 收集記錄資料
titleSuffix: Azure Cognitive Search
description: 藉由啟用診斷設定來收集和分析記錄資料，然後使用 Kusto 查詢語言來流覽結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 2849dc94f1c45dda3da09120adebba6e004eb96b
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211174"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集並分析 Azure 認知搜尋的記錄資料

診斷或作業記錄可讓您深入瞭解 Azure 認知搜尋的詳細作業，並且適用于監視服務和工作負載進程。 就內部而言，記錄會短暫地存在於後端，足以在您提出支援票證時應付調查和分析。 不過，如果您想要讓運算元據具有自我方向，您應該設定診斷設定來指定收集記錄資訊的位置。 

設定記錄適用于診斷和保留作業歷程記錄。 啟用記錄功能之後，您可以執行查詢或建立結構化分析的報表。

下表列舉用來收集和保存資料的選項。

| 資源 | 用於 |
|----------|----------|
| [傳送至 Log Analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 記錄的事件和查詢計量（根據下列架構）。 事件會記錄到 Log Analytics 工作區。 使用 Log Analytics，您可以執行查詢以傳回詳細資訊。 如需詳細資訊，請參閱[開始使用 Azure 監視器記錄](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [使用 Blob 儲存體封存](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 記錄的事件和查詢計量（根據下列架構）。 事件會記錄至 Blob 容器並儲存在 JSON 檔案中。 記錄可以很細微（以小時/分鐘為單位），適用于研究特定事件，但不適合用于開放式調查。 使用 JSON 編輯器來查看記錄檔。|
| [串流至事件中樞](https://docs.microsoft.com/azure/event-hubs/) | 根據本文中記載的結構描述，記錄事件和查詢計量。 請選擇此選項作為非常大型記錄的替代資料收集服務。 |

Azure 監視器記錄和 Blob 儲存體皆以免費服務的形式提供，因此您可以在 Azure 訂用帳戶的存留期免費試用。 Application Insights 可供免費註冊和使用，只要應用程式資料大小在特定限制範圍內即可 (如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/monitor/))。

## <a name="prerequisites"></a>Prerequisites

如果您使用 Log Analytics 或 Azure 儲存體，您可以事先建立資源。

+ [建立 log analytics 工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ 如果您需要記錄保存，請[建立儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)。

## <a name="create-a-log"></a>建立記錄檔

診斷設定會定義資料收集。 設定會指定收集的方式和內容。 

1. 在 [監視] 下方，選取 [診斷設定]。

   ![診斷設定](./media/search-monitor-usage/diagnostic-settings.png "診斷設定")

1. 選取 [ **+ 新增診斷設定**]

1. 選擇您想要匯出的資料︰記錄、計量或兩者。 您可以在儲存體帳戶、log analytics 工作區中收集資料，或將它串流至事件中樞。

   建議使用 Log analytics，因為您可以在入口網站中查詢工作區。

   如果您也使用 Blob 儲存體，則會在匯出記錄資料時，視需要建立容器和 blob。

   ![設定資料收集](./media/search-monitor-usage/configure-storage.png "設定資料收集")

1. 儲存設定。

1. 藉由建立或刪除物件（建立記錄事件）和提交查詢（產生計量）來進行測試。 

在 Blob 儲存體中，只有在有要記錄或測量的活動時，才會建立容器。 將資料複製到儲存體帳戶時，資料會格式化為 JSON 並放在兩個容器中︰

* insights-logs-operationlogs：適用於搜尋流量記錄
* insights-metrics-pt1m：適用於計量

**這需要一小時的時間，容器才會出現在 Blob 儲存體中。每個容器都有一個 blob （每小時）。**

記錄會每隔一小時封存一次活動發生的時間。 下列路徑是一個在 12 2020 年1月于上午9:00 建立的記錄檔範例。 其中每個 `/` 都是資料夾： `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2020/m=01/d=12/h=09/m=00/name=PT1H.json`

## <a name="log-schema"></a>記錄檔結構描述

包含 Azure 認知搜尋記錄資料的資料結構符合下列架構。 

對於 Blob 儲存體，每個 blob 都有一個名為**記錄**的根物件，其中包含記錄檔物件的陣列。 每個 Blob 都包含在同一小時內發生之所有作業的記錄。

下表是診斷記錄常見的部分欄位清單。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| timeGenerated |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId |
| operationName |字串 |"Query.Search" |作業的名稱 |
| operationVersion |字串 |"2019-05-06" |使用的 api-version |
| category |字串 |"OperationLogs" |常數 |
| resultType |字串 |"Success" |可能的值：Success 或 Failure |
| resultSignature |int |200 |HTTP 結果碼 |
| durationMS |int |50 |作業的持續時間 (以毫秒為單位) |
| properties |物件 (object) |請參閱下表 |包含作業特定資料的物件 |

### <a name="properties-schema"></a>屬性結構描述

下列屬性專屬於 Azure 認知搜尋。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| Description_s |字串 |"GET /indexes('content')/docs" |作業的端點 |
| Documents_d |int |42 |處理的文件數目 |
| IndexName_s |字串 |「測試-索引」 |與作業相關聯的索引名稱 |
| Query_s |字串 |"？ search = AzureSearch & $count = true & api 版本 = 2019-05-06" |查詢參數 |

## <a name="metrics-schema"></a>度量結構描述

系統會針對查詢要求捕捉計量，並以一分鐘的間隔進行測量。 每個度量會顯示每分鐘的最小值、最大值和平均值。 如需詳細資訊，請參閱[監視查詢要求](search-monitor-queries.md)。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
| metricName |字串 |"Latency" |度量的名稱 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| average |int |64 |度量時間間隔中原始樣本的平均值（以秒或百分比為單位），視度量而定。 |
| minimum |int |37 |度量時間間隔中原始樣本的最小值（以秒為單位）。 |
| maximum |int |78 |度量時間間隔中原始樣本的最大值，單位（秒）。  |
| total |int |258 |度量時間間隔中原始樣本的總計值，單位（秒）。  |
| count |int |4 |一分鐘內從節點發出到記錄的計量數目。  |
| timegrain |字串 |"PT1M" |標準的時間細微性（以 ISO 8601 為單位）。 |

查詢通常會以毫秒執行，因此只有測量為秒的查詢才會出現在 QPS 之類的度量中。

針對**每秒的搜尋查詢**計數，最小值是在該分鐘內註冊的每秒搜尋查詢的最低值。 最大值依此類推。 平均值是一分鐘的彙總。 例如，在一分鐘內，您可能會有如下的模式：一秒的高負載，這是最大值的 SearchQueriesPerSecond，後面加上58秒的平均負載，最後一秒只有一個查詢（這是最小值）。

對於**節流的搜尋查詢百分比**、最小值、最大值、平均值和總計，全都具有相同的值 . 已節流的搜尋查詢百分比，從搜尋查詢的總數（以一分鐘為單位）。

## <a name="view-log-files"></a>查看記錄檔

Blob 儲存體是用來封存記錄檔。 您可以使用任何 JSON 編輯器來檢視記錄檔。 如果您沒有編輯器，建議您使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 入口網站中，開啟您的儲存體帳戶。 

2. 在左側導覽窗格中，按一下 [Blob]。 您應該會看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 當記錄資料匯出到 Blob 儲存體時，Azure 認知搜尋會建立這些容器。

3. 在資料夾階層中一路往下點選，直到抵達 .json 檔案為止。  請使用操作功能表來下載檔案。

下載檔案之後，在 JSON 編輯器中開啟它以檢視內容。

## <a name="next-steps"></a>後續步驟

如果您尚未這麼做，請參閱搜尋服務監視的基本概念，以瞭解完整範圍的監督功能。

> [!div class="nextstepaction"]
> [監視 Azure 認知搜尋中的作業和活動](search-monitor-usage.md)