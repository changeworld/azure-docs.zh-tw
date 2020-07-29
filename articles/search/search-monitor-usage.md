---
title: 監視作業和活動
titleSuffix: Azure Cognitive Search
description: 啟用記錄、取得查詢活動計量、資源使用量，以及來自 Azure 認知搜尋服務的其他系統資料。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 421fddb819d4d396d3ab8890789e58ccb935cbc0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85806806"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>監視 Azure 認知搜尋的作業和活動

本文概述 Azure 認知搜尋的監視概念和工具。 若要進行整體監視，您可以使用內建功能和附加元件服務的組合，例如 Azure 監視器。

您可以完全追蹤下列各項：

* 服務：健全狀況/可用性和服務設定的變更。
* 儲存體：已使用和可用，每個內容類型的計數相對於服務層所允許的配額。
* 查詢活動：磁片區、延遲和節流或卸載的查詢。 記錄的查詢要求需要[Azure 監視器](#add-azure-monitor)。
* 編制索引活動：需要 Azure 監視器的[診斷記錄](#add-azure-monitor)。

搜尋服務不支援每個使用者驗證，因此不會在記錄檔中找到任何身分識別資訊。

## <a name="built-in-monitoring"></a>內建監視

內建監視是指搜尋服務所記錄的活動。 除了診斷之外，此監視層級不需要進行任何設定。

Azure 認知搜尋會以為期30天的排程來維護內部資料，以報表服務健康狀態和查詢計量，您可以在入口網站中找到，或透過這些[REST api](#monitoring-apis)。

下列螢幕擷取畫面可協助您在入口網站中尋找監視資訊。 一旦您開始使用服務，資料就會變成可用。 入口網站頁面每隔幾分鐘就會重新整理一次。

* [**監視**中] 索引標籤，在主要的 [總覽] 頁面上，顯示查詢量、延遲，以及服務是否承受壓力。
* 左側流覽窗格中的 [**活動記錄**] 會連接到 Azure Resource Manager。 活動記錄會報告 Resource Manager 所採取的動作：服務可用性和狀態、容量變更（複本和分割區），以及 API 金鑰相關活動。
* 進一步**監視**設定會提供可設定的警示、計量和診斷記錄。 當您需要時，建立這些專案。 收集並儲存資料之後，您就可以查詢或視覺化資訊以取得見解。

![搜尋服務中的 Azure 監視器整合](./media/search-monitor-usage/azure-monitor-search.png
 "搜尋服務中的 Azure 監視器整合")

> [!NOTE]
> 由於入口網站頁面每隔幾分鐘就會重新整理一次，因此回報的數位是近似值，其目的是讓您大致瞭解系統服務要求的程度。 實際的計量（例如每秒的查詢數（QPS））可能會高於或低於頁面上顯示的數位。 如果 precision 是需求，請考慮使用 Api。

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>適用于監視的 Api

您可以使用下列 Api 來抓取入口網站的 [監視和使用方式] 索引標籤中找到的相同資訊。

* [取得服務統計資料](/rest/api/searchservice/get-service-statistics)
* [取得索引統計資料](/rest/api/searchservice/get-index-statistics)
* [取得檔計數](/rest/api/searchservice/count-documents)
* [取得索引子狀態](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>活動記錄和服務健全狀況

入口網站中的 [[**活動記錄**](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)] 頁面會根據服務健康狀態的變更，收集 Azure Resource Manager 和報告的資訊。 您可以監視活動記錄，以瞭解與服務健康狀態相關的重大、錯誤和警告狀況。

常見專案包括 API 金鑰的參考-一般語音總機，例如*取得管理金鑰*和*取得查詢金鑰*。 這些活動會指出使用管理金鑰（建立或刪除物件）或查詢金鑰所提出的要求，但不會顯示要求本身。 如需這項資料細微性的資訊，您必須設定診斷記錄。

您可以從左側導覽窗格、從頂端視窗命令列中的 [通知] 或從 [診斷並解決問題]**** 頁面，存取 [活動記錄]****。

### <a name="monitor-storage-in-the-usage-tab"></a>在 [使用方式] 索引標籤中監視存放裝置

若要在入口網站中進行視覺化監視，[**使用量**] 索引標籤會顯示相對於服務層級所設定之目前[限制](search-limits-quotas-capacity.md)的資源可用性。 如果您要完成有關[要針對生產環境工作負載使用哪個定價層](search-sku-tier.md)或是否要[調整作用中複本和分割區的數量](search-capacity-planning.md)的決策，這些計量可透過顯示資源消耗速度及目前設定處理現有負載的情況，協助您進行這些決策。

以下是一個免費服務的圖例，其上限為每個類型 3 個物件且儲存體為 50 MB。 「基本」或「標準」服務的上限較高，且如果您增加分割區計數，儲存體上限也會按比例增加。

![相對於層級限制的使用狀態](./media/search-monitor-usage/usage-tab.png
 "相對於層級限制的使用狀態")

> [!NOTE]
> 與存放裝置相關的警示目前無法使用;儲存體耗用量不會匯總或登入 Azure 監視器中的**AzureMetrics**資料表。 若要取得儲存體警示，您必須[建立自訂解決方案](../azure-monitor/insights/solutions-creating.md)來發出資源相關通知，其中您的程式碼會檢查儲存體大小並處理回應。

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>使用 Azure 監視器的附加元件監視

許多服務（包括 Azure 認知搜尋）都會與[Azure 監視器](https://docs.microsoft.com/azure/azure-monitor/)整合，以取得額外的警示、計量和記錄診斷資料。 

如果您想要控制資料收集和儲存，請啟用搜尋服務的[診斷記錄](search-monitor-logs.md)。 Azure 監視器所捕捉的記錄事件會儲存在**AzureDiagnostics**資料表中，並由與查詢和索引相關的運算元據組成。

Azure 監視器提供了數個儲存體選項，而您的選擇會決定您可以如何使用資料：

* 如果您想要將 Power BI 報表中的[記錄資料視覺化](search-monitor-logs-powerbi.md)，請選擇 [Azure Blob 儲存體]。
* 如果您想要透過 Kusto 查詢流覽資料，請選擇 [Log Analytics]。

Azure 監視器有自己的帳單結構，而且本節中所參考的診斷記錄具有相關聯的成本。 如需詳細資訊，請參閱[Azure 監視器中的使用量和估計成本](../azure-monitor/platform/usage-estimated-costs.md)。

## <a name="monitor-user-access"></a>監視使用者存取

因為搜尋索引是較大用戶端應用程式的元件，所以沒有任何內建的方法可以控制或監視索引的每個使用者存取。 假設要求是來自用戶端應用程式，就是系統管理員或查詢要求。 管理員讀寫作業包括建立、更新、刪除整個服務中的物件。 唯讀作業是針對檔集合的查詢，範圍限定為單一索引。 

因此，您會在活動記錄中看到的內容是使用系統管理金鑰或查詢金鑰的呼叫參考。 適當的金鑰會包含在來自用戶端程式代碼的要求中。 服務未配備處理身分識別權杖或模擬。

當每個使用者授權的商務需求都存在時，建議與 Azure Active Directory 整合。 您可以使用 $filter 和使用者身分識別，來修剪使用者不應該看到的檔[搜尋結果](search-security-trimming-for-azure-search-with-aad.md)。 

沒有任何方法可將此資訊與包含 $filter 參數的查詢字串分開記錄。 如需報告查詢字串的詳細資訊，請參閱[監視查詢](search-monitor-queries.md)。

## <a name="next-steps"></a>後續步驟

具有 Azure 監視器的順暢是監督任何 Azure 服務的必要條件，包括 Azure 認知搜尋等資源。 如果您不熟悉 Azure 監視器，請花時間查看與資源相關的文章。 除了教學課程以外，下列文章是很好的開端。

> [!div class="nextstepaction"]
> [使用 Azure 監視器監視 Azure 資源](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)
