---
title: 收集日誌資料
titleSuffix: Azure Cognitive Search
description: 通過啟用診斷設置收集和分析日誌資料，然後使用 Kusto 查詢語言來探索結果。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462343"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>收集和分析 Azure 認知搜索的日誌資料

診斷或動作記錄提供 Azure 認知搜索的詳細操作的見解，並且可用於監視服務和工作負載過程。 就內部而言，記錄會短暫地存在於後端，足以在您提出支援票證時應付調查和分析。 但是，如果要對運算元據進行自向，則應配置診斷設置以指定收集日誌記錄資訊的位置。

設置日誌對於診斷和保留操作歷史記錄非常有用。 啟用日誌記錄後，可以執行查詢或生成報表以進行結構化分析。

下表列舉了用於收集和持久化資料的選項。

| 資源 | 用於 |
|----------|----------|
| [發送到日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | 事件和指標將發送到日誌分析工作區，可以在門戶中查詢該工作區以返回詳細資訊。 有關簡介，請參閱[使用 Azure 監視器日誌入門](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata) |
| [使用 Blob 存儲存檔](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | 事件和指標將存檔到 Blob 容器並存儲在 JSON 檔中。 日誌可以非常精細（按小時/分鐘）進行，可用於研究特定事件，但不適用於開放式調查。 使用 JSON 編輯器查看原始日誌檔或 Power BI 以聚合和視覺化日誌資料。|
| [流到事件中心](https://docs.microsoft.com/azure/event-hubs/) | 事件和指標將資料流到 Azure 事件中心服務。 請選擇此選項作為非常大型記錄的替代資料收集服務。 |

Azure 監視器日誌和 Blob 存儲都可以作為免費服務提供，因此您可以在 Azure 訂閱的存留期內免費試用它。 Application Insights 可供免費註冊和使用，只要應用程式資料大小在特定限制範圍內即可 (如需詳細資訊，請參閱[價格頁面](https://azure.microsoft.com/pricing/details/monitor/))。

## <a name="prerequisites"></a>Prerequisites

如果使用日誌分析或 Azure 存儲，則可以提前創建資源。

+ [創建日誌分析工作區](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [創建存儲帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>啟用資料收集

診斷設置指定如何收集記錄的事件和指標。

1. 在 [監視]**** 下方，選取 [診斷設定]****。

   ![診斷設置](./media/search-monitor-usage/diagnostic-settings.png "診斷設定")

1. 選擇 **+ 添加診斷設置**

1. 選中**日誌分析**，選擇工作區，然後選擇 **"動作記錄**"和 **"所有指標**"。

   ![配置資料收集](./media/search-monitor-usage/configure-storage.png "設定資料收集")

1. 保存設置。

1. 啟用日誌記錄後，使用搜索服務開始生成日誌和指標。 記錄的事件和指標可用需要時間。

對於日誌分析，資料可用需要幾分鐘時間，之後即可運行 Kusto 查詢以返回資料。 有關詳細資訊，請參閱[監視器查詢請求](search-monitor-logs.md)。

對於 Blob 存儲，容器在 Blob 存儲中顯示需要一個小時。 每個容器每小時會有一個 Blob。 只有在有活動可供記錄或測量時，才會建立容器。 將資料複製到儲存體帳戶時，資料會格式化為 JSON 並放在兩個容器中︰

+ insights-logs-operationlogs：適用於搜尋流量記錄
+ insights-metrics-pt1m：適用於計量

## <a name="query-log-information"></a>查詢日誌資訊

在診斷日誌中，兩個表包含 Azure 認知搜索的日誌和指標 **：Azure 診斷**和**AzureMetrics**。

1. 在 **"監視"** 下，選擇 **"日誌**"。

1. 在查詢視窗中輸入**Azure 指標**。 運行此簡單查詢以熟悉此表中收集的資料。 滾動表以查看指標和值。 請注意頂部的記錄計數，如果您的服務已收集指標一段時間，您可能需要調整時間間隔以獲得可管理的資料集。

   ![AzureMetrics 表](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics 表")

1. 輸入以下查詢以返回表格結果集。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. 重複前面的步驟，從**Azure 診斷**開始返回所有列以用於資訊目的，然後是更選擇性的查詢，以提取更有趣的資訊。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![Azure 診斷表](./media/search-monitor-usage/azurediagnostics-table.png "Azure 診斷表")

## <a name="log-schema"></a>記錄檔結構描述

包含 Azure 認知搜索日誌資料的資料結構符合以下架構。 

對於 Blob 存儲，每個 Blob 都有一個稱為**記錄**的根物件，其中包含日誌物件陣列。 每個 Blob 都包含在同一小時內發生之所有作業的記錄。

下表是診斷日誌記錄常見的欄位的部分清單。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| 時間生成 |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>  MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的 ResourceId |
| operationName |字串 |"Query.Search" |作業的名稱 |
| operationVersion |字串 |"2019-05-06" |使用的 api-version |
| category |字串 |"OperationLogs" |常數 |
| resultType |字串 |"Success" |可能的值：Success 或 Failure |
| resultSignature |int |200 |HTTP 結果碼 |
| durationMS |int |50 |作業的持續時間 (以毫秒為單位) |
| properties |物件 (object) |請參閱下表 |包含作業特定資料的物件 |

### <a name="properties-schema"></a>屬性結構描述

以下屬性特定于 Azure 認知搜索。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| Description_s |字串 |"GET /indexes('content')/docs" |作業的端點 |
| Documents_d |int |42 |處理的文件數目 |
| IndexName_s |字串 |"測試索引" |與作業相關聯的索引名稱 |
| Query_s |字串 |"搜索\Azure 搜索&$count_真正的&api 版本=2019-05-06" |查詢參數 |

## <a name="metrics-schema"></a>度量結構描述

捕獲查詢請求的指標，並每隔一分鐘測量一次。 每個度量會顯示每分鐘的最小值、最大值和平均值。 有關詳細資訊，請參閱[監視器查詢請求](search-monitor-queries.md)。

| 名稱 | 類型 | 範例 | 注意 |
| --- | --- | --- | --- |
| resourceId |字串 |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |您的資源識別碼 |
| metricName |字串 |"Latency" |度量的名稱 |
| time |Datetime |"2018-12-07T00:00:43.6872559Z" |作業的時間戳記 |
| average |int |64 |原始樣本的平均值（以指標時間間隔為單位）（單位以秒為單位）或百分比，具體取決於指標。 |
| minimum |int |37 |在指標時間間隔中原始樣本的最小值，單位（以秒為單位）。 |
| maximum |int |78 |在指標時間間隔中原始樣本的最大值，單位（以秒為單位）。  |
| total |int |258 |在指標時間間隔中原始樣本的總值，單位（以秒為單位）。  |
| count |int |4 |在一分鐘間隔內從節點向日志發出的指標數。  |
| timegrain |字串 |"PT1M" |ISO 8601 中指標的時間細微性。 |

查詢通常以毫秒為單位執行，因此只有以秒為單位的查詢才會顯示在指標中，如 QPS。

對於 **"每秒搜索查詢"** 指標，最小值是該分鐘中每秒註冊的搜索查詢的最低值。 最大值依此類推。 平均值是一分鐘的彙總。 例如，在一分鐘內，您可能有一個這樣的模式：一秒的高負載，即搜索查詢Per秒的最大負載，然後是 58 秒的平均負載，最後只有一秒的查詢，這是最小值。

對於**受限搜索查詢百分比**、最小、最大、平均值和總計，所有值都具有相同的值：從一分鐘內搜索查詢總數中限制的搜索查詢的百分比。

## <a name="view-raw-log-files"></a>查看原始日誌檔

Blob 存儲用於存檔日誌檔。 您可以使用任何 JSON 編輯器來檢視記錄檔。 如果您沒有編輯器，建議您使用 [Visual Studio Code](https://code.visualstudio.com/download)。

1. 在 Azure 入口網站中，開啟您的儲存體帳戶。 

2. 在左側導覽窗格中，按一下 [Blob]****。 您應該會看到 **insights-logs-operationlogs** 和 **insights-metrics-pt1m**。 當日志資料匯出到 Blob 存儲時，這些容器由 Azure 認知搜索創建。

3. 在資料夾階層中一路往下點選，直到抵達 .json 檔案為止。  請使用操作功能表來下載檔案。

下載檔案之後，在 JSON 編輯器中開啟它以檢視內容。

## <a name="next-steps"></a>後續步驟

如果您尚未這樣做，請查看搜索服務監視的基礎知識，以瞭解各種監督功能。

> [!div class="nextstepaction"]
> [監視 Azure 認知搜索中的操作和活動](search-monitor-usage.md)