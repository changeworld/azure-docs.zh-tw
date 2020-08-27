---
title: 監視作業和活動
titleSuffix: Azure Cognitive Search
description: 啟用記錄、取得查詢活動度量、資源使用量，以及 Azure 認知搜尋服務中的其他系統資料。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d47f6c20246e3210b58dbc9c802a11c866ae305e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935002"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>監視 Azure 認知搜尋的作業和活動

本文概述 Azure 認知搜尋的監視概念和工具。 若要進行全面監視，您可以使用內建功能和附加元件服務的組合（例如 Azure 監視器）。

您可以完全追蹤下列各項：

* 服務：健康情況/可用性和服務設定的變更。
* 儲存體：已使用和可用，且每個內容類型的計數相對於服務層所允許的配額。
* 查詢活動：大量、延遲和節流或已捨棄的查詢。 記錄的查詢要求需要 [Azure 監視器](#add-azure-monitor)。
* 索引編制活動：需要 Azure 監視器的 [診斷記錄](#add-azure-monitor) 。

搜尋服務不支援每一使用者的驗證，因此在記錄中找不到任何身分識別資訊。

## <a name="built-in-monitoring"></a>內建監視

內建監視是指搜尋服務所記錄的活動。 除了診斷之外，此監視層級不需要進行任何設定。

Azure 認知搜尋會針對服務健康狀態和查詢計量（您可以在入口網站中找到），或透過這些 [REST api](#monitoring-apis)，在輪流的30天排程上維護內部資料。

下列螢幕擷取畫面可協助您在入口網站中找出監視資訊。 當您開始使用服務之後，資料就會變成可用。 入口網站頁面會每隔幾分鐘重新整理一次。

* [**監視**] 索引標籤的主要 [總覽] 頁面會顯示查詢量、延遲，以及服務是否受到壓力。
* 左側導覽窗格中的 [**活動記錄**] 會連接到 Azure Resource Manager。 活動記錄會報告 Resource Manager 所採取的動作：服務可用性和狀態、容量 (複本) 和資料分割的變更，以及 API 金鑰相關活動。
* **監視** 設定（由下往下）提供可設定的警示、計量和診斷記錄。 在需要時建立這些專案。 收集並儲存資料之後，您就可以查詢資訊，或將資訊視覺化以取得見解。

![搜尋服務中的 Azure 監視器整合](./media/search-monitor-usage/azure-monitor-search.png
 "搜尋服務中的 Azure 監視器整合")

> [!NOTE]
> 因為入口網站頁面會每隔幾分鐘重新整理一次，所以回報的數位是近似值，目的是要讓您大致瞭解系統服務要求的程度。 實際的計量（例如每秒查詢數 (QPS) 可能會高於或低於頁面上顯示的數位。 如果需要精確度，請考慮使用 Api。

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>適用于監視的 Api

您可以使用下列 Api 來取得在入口網站的 [監視和使用方式] 索引標籤中找到的相同資訊。

* [取得服務統計資料](/rest/api/searchservice/get-service-statistics)
* [取得索引統計資料](/rest/api/searchservice/get-index-statistics)
* [取得檔計數](/rest/api/searchservice/count-documents)
* [取得索引子狀態](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>活動記錄和服務健康狀態

入口網站中的 [ [**活動記錄**](../azure-monitor/platform/activity-log.md#view-the-activity-log) ] 頁面會收集 Azure Resource Manager 的資訊，以及有關服務健康狀態變更的報告。 您可以監視活動記錄，以瞭解與服務健康狀態相關的重大、錯誤和警告狀況。

常見的專案包含 API 金鑰的參考-一般語音總機，例如 *取得管理金鑰* 和 *取得查詢金鑰*。 這些活動指出使用管理金鑰所提出的要求 (建立或刪除物件) 或查詢金鑰，但不顯示要求本身。 如需此資料細微性的資訊，您必須設定診斷記錄。

您可以從左側導覽窗格、從頂端視窗命令列中的 [通知] 或從 [診斷並解決問題]**** 頁面，存取 [活動記錄]****。

### <a name="monitor-storage-in-the-usage-tab"></a>在 [使用方式] 索引標籤中監視儲存體

針對入口網站中的視覺監視，[ **使用量** ] 索引標籤會顯示相對於服務層級所加諸目前 [限制](search-limits-quotas-capacity.md) 的資源可用性。 如果您要完成有關[要針對生產環境工作負載使用哪個定價層](search-sku-tier.md)或是否要[調整作用中複本和分割區的數量](search-capacity-planning.md)的決策，這些計量可透過顯示資源消耗速度及目前設定處理現有負載的情況，協助您進行這些決策。

以下是一個免費服務的圖例，其上限為每個類型 3 個物件且儲存體為 50 MB。 「基本」或「標準」服務的上限較高，且如果您增加分割區計數，儲存體上限也會按比例增加。

![相對於層級限制的使用量狀態](./media/search-monitor-usage/usage-tab.png
 "相對於層級限制的使用量狀態")

> [!NOTE]
> 目前無法使用與儲存體相關的警示;儲存體耗用量不會匯總或記錄到 Azure 監視器中的 **AzureMetrics** 資料表。 若要取得儲存體警示，您需要 [建立自訂解決方案](../azure-monitor/insights/solutions.md) 來發出資源相關通知，您的程式碼會在其中檢查儲存體大小並處理回應。

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Azure 監視器的附加元件監視

許多服務（包括 Azure 認知搜尋）會與 [Azure 監視器](../azure-monitor/index.yml) 整合，以取得其他警示、計量和記錄診斷資料。 

如果您想要控制資料收集和儲存，請啟用搜尋服務的[診斷記錄](search-monitor-logs.md)。 Azure 監視器所捕捉的記錄事件會儲存在 **AzureDiagnostics** 資料表中，並包含與查詢和索引相關的運算元據。

Azure 監視器提供數個儲存體選項，而您的選擇會決定您可以使用資料的方式：

* 如果您想要將 Power BI 報表中的 [記錄資料視覺化](search-monitor-logs-powerbi.md) ，請選擇 [Azure Blob 儲存體]。
* 如果您想要透過 Kusto 查詢來流覽資料，請選擇 Log Analytics。

Azure 監視器有自己的計費結構，而且此區段中所參考的診斷記錄會有相關聯的成本。 如需詳細資訊，請參閱 [Azure 監視器中的使用量和估計成本](../azure-monitor/platform/usage-estimated-costs.md)。

## <a name="monitor-user-access"></a>監視使用者存取

由於搜尋索引是較大用戶端應用程式的元件，因此沒有內建方法可控制或監視每位使用者對索引的存取。 系統會假設要求是來自用戶端應用程式，也就是系統管理員或查詢要求。 系統管理員讀寫作業包括建立、更新、刪除整個服務中的物件。 唯讀作業是針對檔集合進行查詢，範圍設定為單一索引。 

因此，您會在活動記錄中看到的參考是使用系統管理金鑰或查詢金鑰的呼叫。 適當的金鑰會包含在源自用戶端程式代碼的要求中。 服務沒有處理身分識別權杖或模擬的能力。

當每個使用者授權都有商務需求時，建議與 Azure Active Directory 整合。 您可以使用 $filter 和使用者身分識別，來修剪使用者不應該看到的檔 [搜尋結果](search-security-trimming-for-azure-search-with-aad.md) 。 

沒有任何方法可以從包含 $filter 參數的查詢字串分開記錄這項資訊。 如需報告查詢字串的詳細資訊，請參閱 [監視查詢](search-monitor-queries.md) 。

## <a name="next-steps"></a>後續步驟

流暢度與 Azure 監視器對於監督任何 Azure 服務（包括 Azure 認知搜尋之類的資源）而言很重要。 如果您不熟悉 Azure 監視器，請花時間複習與資源相關的文章。 除了教學課程之外，下列文章也是不錯的開端。

> [!div class="nextstepaction"]
> [使用 Azure 監視器監視 Azure 資源](../azure-monitor/insights/monitor-azure-resource.md)