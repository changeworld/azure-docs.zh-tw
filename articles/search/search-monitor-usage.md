---
title: 監控操作和活動
titleSuffix: Azure Cognitive Search
description: 從 Azure 認知搜索服務啟用日誌記錄、獲取查詢活動指標、資源使用方式和其他系統資料。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 353e00f902a7314e5e5b7c8ee03e8b925a510b26
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462321"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>監視 Azure 認知搜索的操作和活動

本文介紹了在工作負載級別（查詢和索引）的服務（資源）級別的監視，並建議一個監視使用者訪問的框架。

在一個範圍內，您將使用內置基礎結構和基礎服務（如 Azure 監視器）的組合，以及返回統計資訊、計數和狀態的服務 API 的組合。 瞭解功能範圍可以説明您構建回饋迴圈，以便在問題出現時解決問題。

## <a name="use-azure-monitor"></a>使用 Azure 監視器

許多服務（包括 Azure 認知搜索）都利用[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)來查找警報、指標和記錄診斷資料。 對於 Azure 認知搜索，內置監視基礎結構主要用於資源級監視（服務運行狀況）和[查詢監視](search-monitor-queries.md)。

以下螢幕截圖可説明您在門戶中查找 Azure 監視器功能。

+ **監視**選項卡位於主概述頁中，一目了然地顯示關鍵指標。
+ **活動日誌**（概述）下方報告資源級操作：服務運行狀況和 API 金鑰請求通知。
+ **監視**（在清單的進一步向下）提供可配置的警報、指標和診斷日誌。 在需要時創建這些。 收集和存儲資料後，您可以查詢或視覺化資訊以獲取見解。

![Azure 監視器在搜索服務中的集成](./media/search-monitor-usage/azure-monitor-search.png
 "Azure 監視器在搜索服務中的集成")

### <a name="precision-of-reported-numbers"></a>報告數位的精度

門戶頁面每隔幾分鐘刷新一次。 因此，門戶中報告的數位是近似值，旨在讓您大致瞭解系統對請求的服務情況。 實際指標（如每秒查詢次數 （QPS） 可能高於或低於頁面上顯示的數位。

## <a name="activity-logs-and-service-health"></a>活動日誌和服務運行狀況

[**活動日誌**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)從 Azure 資源管理器收集資訊，並報告對服務運行狀況的更改。 您可以監視活動日誌中有關與服務運行狀況相關的關鍵、錯誤和警告條件。

對於服務內任務（如查詢、索引或創建物件），您將看到一些通用的語音總機，如為每個請求*獲取管理金鑰*和*獲取查詢金鑰*，但看不到特定操作本身。 有關此顆粒的資訊，必須配置診斷日誌記錄。

您可以從左側導覽窗格、從頂端視窗命令列中的 [通知] 或從 [診斷並解決問題]**** 頁面，存取 [活動記錄]****。

## <a name="monitor-storage"></a>監控存儲

"概述"頁中內置的選項卡式頁面報告資源使用方式。 一旦開始使用服務，此資訊就會可用，無需配置，並且頁面每隔幾分鐘刷新一次。 

如果您要完成有關[要針對生產環境工作負載使用哪個定價層](search-sku-tier.md)或是否要[調整作用中複本和分割區的數量](search-capacity-planning.md)的決策，這些計量可透過顯示資源消耗速度及目前設定處理現有負載的情況，協助您進行這些決策。

與存儲相關的警報當前不可用;存儲消耗不會聚合或登錄到 Azure 監視器中的**Azure Metrics**表。 您需要[構建一個自訂解決方案](https://docs.microsoft.com/azure/azure-monitor/insights/solutions-creating)來發出與資源相關的通知，其中代碼檢查存儲大小並處理回應。 有關存儲指標的詳細資訊，請參閱[獲取服務統計資訊](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)。

對於門戶中的可視監視，"**使用方式"** 選項卡顯示資源可用性相對於服務層施加的當前[限制](search-limits-quotas-capacity.md)。 

以下是一個免費服務的圖例，其上限為每個類型 3 個物件且儲存體為 50 MB。 「基本」或「標準」服務的上限較高，且如果您增加分割區計數，儲存體上限也會按比例增加。

![相對於層限制的使用狀態](./media/search-monitor-usage/usage-tab.png
 "相對於層限制的使用狀態")

## <a name="monitor-workloads"></a>監控工作負載

記錄的事件包括與索引和查詢相關的事件。 日誌分析中的**Azure 診斷**表收集與查詢和索引相關的運算元據。

大多數記錄的資料用於唯讀操作。 對於日誌中未捕獲的其他創建-更新-刪除操作，可以查詢搜索服務以查找系統資訊。

| OperationName | 描述 |
|---------------|-------------|
| 服務統計 | 此操作是獲取[服務統計資訊](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)的常規調用，直接調用或隱式調用以在載入或刷新門戶概覽頁時填充該頁。 |
| 查詢.搜索 |  查詢針對索引的請求，請參閱[監視器查詢](search-monitor-queries.md)，瞭解有關記錄查詢的資訊。|
| 索引.索引  | 此操作是添加[、更新或刪除文檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)的調用。 |
| 指標。原型 | 這是由導入資料嚮導創建的索引。 |
| 索引子.創建 | 通過"導入資料"嚮導顯式或隱式創建索引子。 |
| 索引子.獲取 | 每當運行索引子時，都會返回索引子的名稱。 |
| 索引子.狀態 | 每當運行索引子時，返回索引子的狀態。 |
| 資料來源.獲取 | 每當運行索引子時，都會返回資料來源的名稱。|
| 索引.獲取 | 每當運行索引子時返回索引的名稱。 |

### <a name="kusto-queries-about-workloads"></a>關於工作負載的庫斯托查詢

如果啟用了日誌記錄，則可以查詢**Azure 診斷**，以查詢在服務上運行的動作清單以及何時運行的操作。 您還可以關聯活動以調查性能變化。

#### <a name="example-list-operations"></a>示例：列出操作 

返回動作清單和每個操作的計數。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>示例：關聯操作

將查詢請求與索引操作關聯，並在時間圖表上呈現資料點以查看操作一致。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>使用搜索 API

Azure 認知搜索 REST API 和 .NET SDK 都提供對服務指標、索引和索引子資訊和文檔計數的程式設計訪問。

+ [獲取服務統計資訊](/rest/api/searchservice/get-service-statistics)
+ [獲取索引統計資訊](/rest/api/searchservice/get-index-statistics)
+ [獲取文檔計數](/rest/api/searchservice/count-documents)
+ [獲取索引子狀態](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>監視使用者訪問

由於搜索索引是大型用戶端應用程式的元件，因此沒有用於控制或監視每個使用者訪問索引的內置方法。 假定請求來自用戶端應用程式，用於管理員或查詢請求。 管理員讀寫操作包括在整個服務中創建、更新和刪除物件。 唯讀操作是針對文件組合的查詢，範圍為單個索引。 

因此，您將在活動日誌中看到的是使用管理金鑰或查詢金鑰調用的引用。 來自用戶端代碼的請求中包含相應的金鑰。 該服務不具備處理標識權杖或類比的設備。

當每個使用者授權的業務需求確實存在時，建議與 Azure 活動目錄集成。 您可以使用$filter和使用者標識[來修剪](search-security-trimming-for-azure-search-with-aad.md)使用者不應看到的文檔的搜尋結果。 

無法將此資訊與包含$filter參數的查詢字串分開記錄。 有關報告查詢字串的詳細資訊[，請參閱監視器查詢](search-monitor-queries.md)。

## <a name="next-steps"></a>後續步驟

使用 Azure 監視器的流暢性對於監督任何 Azure 服務（包括 Azure 認知搜索等資源）至關重要。 如果您不熟悉 Azure 監視器，請花時間查看與資源相關的文章。 除了教程之外，以下文章是一個很好的開始位置。

> [!div class="nextstepaction"]
> [使用 Azure 監視器監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
