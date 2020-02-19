---
title: 監視作業和活動
titleSuffix: Azure Cognitive Search
description: 啟用記錄、取得查詢活動計量、資源使用量，以及來自 Azure 認知搜尋服務的其他系統資料。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/15/2020
ms.openlocfilehash: 5846e9516548032595c1ce072d1dae8dcce9d39e
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443596"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>監視 Azure 認知搜尋的作業和活動

本文會在工作負載層級（查詢和索引編制）介紹服務（資源）層級的監視，並建議用來監視使用者存取的架構。

在整個頻譜中，您將使用內建基礎結構和基本服務（如 Azure 監視器）的組合，以及傳回統計資料、計數和狀態的服務 Api。 瞭解功能的範圍可以協助您建立意見反應迴圈，以便在發生問題時加以解決。

## <a name="use-azure-monitor"></a>使用 Azure 監視器

許多服務（包括 Azure 認知搜尋）都會運用警示、計量和記錄診斷資料的[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)。 針對「Azure 認知搜尋」，內建的監視基礎結構主要用於資源層級監視（服務健全狀況）和[查詢監視](search-monitor-queries.md)。

下列螢幕擷取畫面可協助您在入口網站中找出 Azure 監視器功能。

+ [**監視**] 索引標籤位於主要的 [總覽] 頁面上，一眼就會顯示重要計量。
+ **活動記錄**（僅在以下的總覽）報告資源層級動作：服務健康狀態和 API 金鑰要求通知。
+ 在清單中進一步**監視**，可提供可設定的警示、計量和診斷記錄。 當您需要時，建立這些專案。 收集並儲存資料之後，您就可以查詢或視覺化資訊以取得見解。

![搜尋服務中的 Azure 監視器整合](./media/search-monitor-usage/azure-monitor-search.png
 "搜尋服務中的 Azure 監視器整合")

### <a name="precision-of-reported-numbers"></a>報告數位的精確度

入口網站頁面每隔幾分鐘就會重新整理一次。 因此，入口網站中回報的數位是近似值，主要是為了讓您知道系統服務要求的程度。 實際的計量（例如每秒的查詢數（QPS））可能會高於或低於頁面上顯示的數位。

## <a name="activity-logs-and-service-health"></a>活動記錄和服務健全狀況

[**活動記錄**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)會收集服務健康狀態變更的 Azure Resource Manager 和報告資訊。 您可以監視活動記錄，以瞭解與服務健康狀態相關的重大、錯誤和警告狀況。

針對服務內工作（例如查詢、編制索引或建立物件），您會看到一般語音總機，例如*取得管理金鑰*，以及取得每個要求的*查詢金鑰*，而不是特定動作本身。 如需這項資料細微性的資訊，您必須設定診斷記錄。

您可以從左側導覽窗格、從頂端視窗命令列中的 [通知] 或從 [診斷並解決問題] 頁面，存取 [活動記錄]。

## <a name="monitor-storage"></a>監視儲存體

[總覽] 頁面內建的索引標籤式頁面會報告資源使用量。 此資訊會在您開始使用服務時立即可用，而不需要設定，而且每隔幾分鐘就會重新整理頁面。 

如果您要完成有關[要針對生產環境工作負載使用哪個定價層](search-sku-tier.md)或是否要[調整作用中複本和分割區的數量](search-capacity-planning.md)的決策，這些計量可透過顯示資源消耗速度及目前設定處理現有負載的情況，協助您進行這些決策。

與存放裝置相關的警示目前無法使用;儲存體耗用量不會匯總或登入 Azure 監視器中的**AzureMetrics**資料表。 您需要建立自訂解決方案來發出資源相關通知，您的程式碼會在其中檢查儲存體大小並處理回應。 如需儲存體計量的詳細資訊，請參閱[取得服務統計資料](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics#response)。

若要在入口網站中進行視覺化監視，[**使用量**] 索引標籤會顯示相對於服務層級所設定之目前[限制](search-limits-quotas-capacity.md)的資源可用性。 

以下是一個免費服務的圖例，其上限為每個類型 3 個物件且儲存體為 50 MB。 「基本」或「標準」服務的上限較高，且如果您增加分割區計數，儲存體上限也會按比例增加。

![相對於層級限制的使用狀態](./media/search-monitor-usage/usage-tab.png
 "相對於層級限制的使用狀態")

## <a name="monitor-workloads"></a>監視工作負載

記錄的事件包括與索引編制和查詢相關的事件。 Log Analytics 中的**AzureDiagnostics**資料表會收集與查詢和索引相關的運算元據。

大部分記錄的資料都是用於唯讀作業。 針對未在記錄檔中捕捉的其他建立-更新-刪除作業，您可以查詢搜尋服務中的系統資訊。

| OperationName | 描述 |
|---------------|-------------|
| ServiceStats | 這項作業是用來[取得服務統計資料](https://docs.microsoft.com/rest/api/searchservice/get-service-statistics)的例行呼叫，不論是在載入或重新整理時，直接或隱含地用來填入入口網站的 [總覽] 頁面。 |
| 查詢。搜尋 |  針對索引的查詢要求，請參閱[監視查詢](search-monitor-queries.md)以取得已記錄查詢的相關資訊。|
| 檢索。索引  | 此作業是[新增、更新或刪除檔](https://docs.microsoft.com/rest/api/searchservice/addupdate-or-delete-documents)的呼叫。 |
| 索引.生產 | 這是「匯入資料」 wizard 所建立的索引。 |
| 索引子。建立 | 透過 [匯入資料] wizard 明確或隱含地建立索引子。 |
| 索引子. Get | 每當執行索引子時，會傳回索引子的名稱。 |
| 索引子。狀態 | 每當執行索引子時，會傳回索引子的狀態。 |
| 資料來源. Get | 每當執行索引子時，會傳回資料來源的名稱。|
| 索引。 Get | 每當執行索引子時，會傳回索引的名稱。 |

### <a name="kusto-queries-about-workloads"></a>關於工作負載的 Kusto 查詢

如果您已啟用記錄功能，您可以查詢**AzureDiagnostics** ，以取得服務上執行的作業和時間的清單。 您也可以將活動相互關聯，以調查效能中的變更。

#### <a name="example-list-operations"></a>範例：清單作業 

傳回作業清單和每個作業的計數。

```
AzureDiagnostics
| summarize count() by OperationName
```

#### <a name="example-correlate-operations"></a>範例：相互關聯作業

將查詢要求與索引作業相互關聯，並在一段時間圖表上呈現資料點，以查看作業是否一致。

```
AzureDiagnostics
| summarize OperationName, Count=count()
| where OperationName in ('Query.Search', 'Indexing.Index')
| summarize Count=count(), AvgLatency=avg(DurationMs) by bin(TimeGenerated, 1h), OperationName
| render timechart
```

### <a name="use-search-apis"></a>使用搜尋 Api

Azure 認知搜尋 REST API 和 .NET SDK 都可讓您以程式設計方式存取服務計量、索引和索引子資訊，以及檔計數。

+ [取得服務統計資料](/rest/api/searchservice/get-service-statistics)
+ [取得索引統計資料](/rest/api/searchservice/get-index-statistics)
+ [取得檔計數](/rest/api/searchservice/count-documents)
+ [取得索引子狀態](/rest/api/searchservice/get-indexer-status)

## <a name="monitor-user-access"></a>監視使用者存取

因為搜尋索引是較大用戶端應用程式的元件，所以沒有任何內建的方法可以控制或監視索引的每個使用者存取。 假設要求是來自用戶端應用程式，就是系統管理員或查詢要求。 管理員讀寫作業包括建立、更新、刪除整個服務中的物件。 唯讀作業是針對檔集合的查詢，範圍限定為單一索引。 

因此，您會在活動記錄中看到的內容是使用系統管理金鑰或查詢金鑰的呼叫參考。 適當的金鑰會包含在來自用戶端程式代碼的要求中。 服務未配備處理身分識別權杖或模擬。

當每個使用者授權的商務需求都存在時，建議與 Azure Active Directory 整合。 您可以使用 $filter 和使用者身分識別，來修剪使用者不應該看到的檔[搜尋結果](search-security-trimming-for-azure-search-with-aad.md)。 

沒有任何方法可將此資訊與包含 $filter 參數的查詢字串分開記錄。 如需報告查詢字串的詳細資訊，請參閱[監視查詢](search-monitor-queries.md)。

## <a name="next-steps"></a>後續步驟

具有 Azure 監視器的順暢是監督任何 Azure 服務的必要條件，包括 Azure 認知搜尋等資源。 如果您不熟悉 Azure 監視器，請花時間查看與資源相關的文章。 除了教學課程以外，下列文章是很好的開端。

> [!div class="nextstepaction"]
> [使用 Azure 監視器監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
