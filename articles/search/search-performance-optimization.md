---
title: 效能級別
titleSuffix: Azure Cognitive Search
description: 學習調整 Azure 認知搜索性能和配置最佳比例的技術和最佳實踐。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212376"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>在 Azure 認知搜索上擴充性能

本文介紹了具有複雜可擴充性和可用性要求的高級方案的最佳做法。

## <a name="start-with-baseline-numbers"></a>從基線編號開始

在進行更大的部署工作之前，請確保您知道典型的查詢負載是什麼樣子。 以下準則可以説明您得出基線查詢編號。

1. 挑選完成一般搜尋要求所應花費的目標延遲 (或最大時間量)。

1. 使用真實資料集根據搜索服務創建和測試實際工作負載，以測量這些延遲率。

1. 從每秒少量查詢 （QPS） 開始，然後逐漸增加在測試中執行的數量，直到查詢延遲低於預定義目標。 這是很重要的效能評定，可協助您規劃應用程式將在使用量方面成長的規模。

1. 如果可能，請重複使用 HTTP 連接。 如果使用 Azure 認知搜索 .NET SDK，這意味著應重用實例或[搜索IndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)實例，如果使用 REST API，則應重用單個 HttpClient。

1. 更改查詢請求的實質，以便搜索在索引的不同部分進行。 變體很重要，因為如果持續執行相同的搜索請求，則緩存資料將開始使性能看起來比使用更不同的查詢集更好。

1. 更改查詢請求的結構，以便獲取不同類型的查詢。 並非所有搜索查詢都在同一級別執行。 例如，文檔查找或搜索建議通常比具有大量面面和篩選器的查詢快。 測試混合應包括各種查詢，其比率與生產中的預期大致相同。  

在創建這些測試工作負載時，Azure 認知搜索有一些特徵需要記住：

+ 一次推送過多的搜索查詢可能會使服務超載。 發生這種情況時，您將會看到 HTTP 503 回應碼。 為了避免在測試期間出現 503，請從各種搜索請求範圍開始，在添加更多搜索請求時查看延遲速率的差異。

+ Azure 認知搜索不會在後臺運行索引任務。 如果服務同時處理查詢和索引工作負荷，請通過將索引作業引入查詢測試，或探索在非尖峰時間運行索引作業的選項來考慮這一點。

> [!Tip]
> 您可以使用負載測試工具類比真實的查詢負載。 嘗試[使用 Azure DevOps 進行負載測試](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops)或使用這些[替代方法](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)之一。

## <a name="scale-for-high-query-volume"></a>高查詢量的縮放

當查詢時間過長或服務開始丟棄請求時，服務會負擔過重。 如果發生這種情況，您可以通過以下兩種方式之一解決問題：

+ **新增複本**  

  每個副本都是資料的副本，允許服務對多個副本載入平衡請求。  所有資料的負載平衡和複製都由 Azure 認知搜索管理，您可以隨時更改為服務分配的副本數。 您最多可在標準搜尋服務中配置 12 個複本，並在基本搜尋服務中配置 3 個複本。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整複本。

+ **在更高級別創建新服務**  

  Azure 認知搜索具有[多個層](https://azure.microsoft.com/pricing/details/search/)，每個層都提供不同級別的性能。 在某些情況下，您可能有如此多的查詢，以至於您所打開的層無法提供足夠的周轉，即使副本已滿。在這種情況下，請考慮遷移到性能更高的層，例如標準 S3 層，專為具有大量文檔和極高查詢工作負載的方案而設計。

## <a name="scale-for-slow-individual-queries"></a>縮放以進行慢速單個查詢

延遲率高的另一個原因是單個查詢完成時間過長。 在這種情況下，添加副本將不起作用。 可能有兩種可能的選項，包括以下內容：

+ **增加分區**

  分區將資料拆分到額外的計算資源中。 兩個分區將資料分成兩半，第三個分區將其拆分為三分之二，依此類推。 一個積極的副作用是，由於平行計算，較慢的查詢有時執行得更快。 我們注意到低選擇性查詢的並行化，例如匹配許多文檔的查詢，或提供大量文檔計數的方面。 由於需要大量計算來評分文檔的相關性，或計算文檔數，因此添加額外的分區有助於更快地完成查詢。  
   
  標準搜索服務中最多可以有 12 個分區，基本搜索服務中最多可以有 1 個分區。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整分割區。

+ **限制高基數位段**

  高基數位段由具有大量唯一值的可面欄位組成，因此計算結果時消耗大量資源。 例如，將產品識別碼 或"說明"欄位設置為可面/可篩選欄位將計為高基數，因為文檔到文檔的大多數值都是唯一的。 如果可能，請限制高基數欄位數目。

+ **增加搜索層**  

  向上移動到更高的 Azure 認知搜索層可能是提高慢速查詢性能的另一種方法。 每個較高層都提供更快的 CPU 和更多的記憶體，這兩者都對查詢性能產生積極影響。

## <a name="scale-for-availability"></a>擴展以尋求可用性

副本不僅有助於減少查詢延遲，還允許高可用性。 利用單一複本，您應該可以預期定期的停機時間，因為伺服器會在軟體更新之後重新開機，或針對其他將發生的維護事件重新開機。 因此，請務必考慮應用程式是否需要搜尋 (查詢) 及寫入 (編製事件的索引) 的高可用性。 Azure 認知搜索在所有付費搜索產品上提供 SLA 選項，具有以下屬性：

+ 針對唯讀工作負載 (查詢)，需有 2 個複本才能達到高可用性

+ 三個或更多副本，用於高可用性讀寫工作負載（查詢和索引）

有關此的更多詳細資訊，請訪問 Azure[認知搜索服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於副本是資料的副本，因此具有多個副本允許 Azure 認知搜索對一個副本執行電腦重新開機和維護，而其他副本的查詢執行將繼續。 相反，如果拿走副本，則假定這些副本是未充分利用的資源，則會導致查詢性能下降。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>擴展地理分佈工作負載和異地冗余

對於地理分佈工作負載，遠離主機資料中心的使用者的延遲率更高。 一個緩解措施是在離這些使用者較近的區域提供多個搜索服務。

Azure 認知搜索目前不提供跨區域地理複製 Azure 認知搜索索引的自動化方法，但可以使用一些技術使此過程易於實現和管理。 我們將在後續小節中加以概述。

地理分佈的搜索服務集的目標是在兩個或多個區域中提供兩個或多個索引，其中使用者路由到 Azure 認知搜索服務，該服務提供最低延遲，如以下示例所示：

   ![依區域交叉分析服務][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>使資料跨多個服務保持同步

有兩個選項用於保持分散式搜索服務同步，包括使用[Azure 認知搜索索引子](search-indexer-overview.md)或推送 API（也稱為[Azure 認知搜索 REST API）。](https://docs.microsoft.com/rest/api/searchservice/)  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引子更新多個服務上的內容

如果已在一個服務上使用索引子，則可以在第二個服務上配置第二個索引子以使用相同的資料來源物件，從同一位置提取資料。 每個區域中的每個服務都有自己的索引子和目標索引（您的搜索索引不共用，這意味著資料是重複的），但每個索引子引用相同的資料來源。

下面是該體系結構外觀的高級視覺化。

   ![具有分散式索引子和服務組合的單一資料來源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST API 在多個服務上推送內容更新

如果使用 Azure 認知搜索 REST API[推送 Azure 認知搜索索引中的內容](https://docs.microsoft.com/rest/api/searchservice/update-index)，則可以在需要更新時將所有搜索服務推送更改來保持各種搜索服務同步。 在代碼中，請確保處理對一個搜索服務的更新失敗但對其他搜索服務成功的情況。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理器

[Azure 流量管理器](../traffic-manager/traffic-manager-overview.md)允許您將請求路由到多個地理位置網站，這些網站然後由多個搜索服務備份。 流量管理器的一個優點是，它可以探測 Azure 認知搜索以確保它可用，並在停機時將使用者路由到備用搜索服務。 此外，如果要通過 Azure 網站路由搜索請求，Azure 流量管理器允許您載入網站已啟動但 Azure 認知搜索未結餘額的情況。 以下範例為運用流量管理員的架構。

   ![利用中央流量管理員依區域交叉分析服務][3]

## <a name="next-steps"></a>後續步驟

要瞭解有關每個定價層和服務限制的更多詳細資訊，請參閱[服務限制](search-limits-quotas-capacity.md)。 有關有關分區和副本組合的詳細資訊，請參閱[計畫容量](search-capacity-planning.md)。

有關本文中討論的技術的性能和演示的討論，請觀看以下視頻：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
