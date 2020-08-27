---
title: 效能級別
titleSuffix: Azure Cognitive Search
description: 瞭解微調 Azure 認知搜尋效能和設定最佳規模的技巧和最佳作法。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5fd949466978714fe1dc0c4ccc67a3cb8f993314
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934951"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure 認知搜尋的效能調整規模

本文說明具有複雜需求的先進擴充性和可用性的最佳做法。

## <a name="start-with-baseline-numbers"></a>開始使用基準編號

在進行較大的部署工作之前，請確定您知道一般查詢負載的外觀。 下列指導方針可協助您達到基準查詢編號。

1. 挑選完成一般搜尋要求所應花費的目標延遲 (或最大時間量)。

1. 使用實際的資料集，針對您的搜尋服務建立及測試真實的工作負載，以測量這些延遲率。

1. 從每秒的查詢數目很少 (QPS) ，然後逐漸增加測試中所執行的數目，直到查詢延遲降到預先定義的目標底下為止。 這是很重要的效能評定，可協助您規劃應用程式將在使用量方面成長的規模。

1. 如果可能，請重複使用 HTTP 連接。 如果您使用 Azure 認知搜尋 .NET SDK，這表示您應該重複使用實例或 [SearchIndexClient](/dotnet/api/microsoft.azure.search.searchindexclient) 實例，如果您使用 REST API，則應該重複使用單一 HttpClient。

1. 改變查詢要求的實質，讓搜尋出現在索引的不同部分。 變化很重要，因為如果您持續執行相同的搜尋要求，快取資料將會開始使效能看起來比使用更不同的查詢集還好。

1. 變更查詢要求的結構，讓您取得不同類型的查詢。 並非每個搜尋查詢都在相同層級執行。 例如，檔查閱或搜尋建議的速度通常比具有大量 facet 和篩選準則的查詢更快。 測試混合應包含各種查詢，大致上與您預期在生產環境中的比率相同。  

建立這些測試工作負載時，有一些 Azure 認知搜尋的特性要牢記在心：

+ 您可以一次推送太多搜尋查詢來多載您的服務。 發生這種情況時，您將會看到 HTTP 503 回應碼。 若要在測試期間避免503，請從各種搜尋要求範圍開始，以查看當您新增更多搜尋要求時的延遲率差異。

+ Azure 認知搜尋不會在背景執行索引工作。 如果您的服務會同時處理查詢和編制索引工作負載，請將索引編制作業帶入查詢測試，或探索在離峰時段執行索引編制工作的選項，以將其納入考慮。

> [!Tip]
> 您可以使用負載測試工具模擬實際的查詢負載。 請嘗試 [使用 Azure DevOps 的負載測試，](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) 或使用其中一個 [替代方案](/azure/devops/test/load-test/overview?view=azure-devops#alternatives)。

## <a name="scale-for-high-query-volume"></a>高查詢量的調整

當查詢花費的時間太長，或服務開始卸載要求時，服務會被嚴重不足。 如果發生這種情況，您可以透過下列兩種方式之一來解決問題：

+ **新增複本**  

  每個複本都是您的資料複本，可讓服務針對多個複本進行負載平衡要求。  所有資料的負載平衡和複寫都是由 Azure 認知搜尋管理，而且您可以隨時變更為服務配置的複本數目。 您最多可在標準搜尋服務中配置 12 個複本，並在基本搜尋服務中配置 3 個複本。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整複本。

+ **在較高的層級建立新的服務**  

  Azure 認知搜尋有 [許多層](https://azure.microsoft.com/pricing/details/search/) 級，而且每一個層都提供不同的效能等級。 在某些情況下，您可能會有這麼多的查詢，也就是您所在的階層無法提供足夠的週期，即使是在超量複本時也一樣。在此情況下，請考慮移至較高階的執行層，例如標準 S3 層，其設計適用于具有大量檔的案例，以及極高的查詢工作負載。

## <a name="scale-for-slow-individual-queries"></a>針對緩慢的個別查詢進行調整

高延遲率的另一個原因是單一查詢花費的時間太長，無法完成。 在此情況下，新增複本將不會有説明。 可能有兩個可能的選項有助於包括下列各項：

+ **增加磁碟分割**

  分割區會跨額外的計算資源分割資料。 兩個數據分割會將資料分割成一半，第三個分割區則會將資料分割成三分之一，依此類推。 其中一個正面的副作用是，速度較慢的查詢有時會因為平行運算而執行得更快。 我們已注意到低選擇性查詢（例如符合許多檔的查詢）或提供大量檔計數的 facet。 由於需要進行大量計算以對檔的相關性進行評分，或計算檔數目，因此加入額外的資料分割有助於查詢更快速完成。  
   
  標準搜尋服務中最多可以有12個分割區，而基本搜尋服務中最多隻能有1個分割區。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整分割區。

+ **限制高基數位段**

  高基數位段包含具有大量唯一值的可 facet 或可篩選欄位，因此會在計算結果時耗用大量資源。 例如，將 [產品識別碼] 或 [描述] 欄位設定為可 facet/可篩選，將會計算為高基數，因為檔與檔中的大部分值都是唯一的。 如果可能，請限制高基數欄位數目。

+ **增加搜尋層**  

  移至較高的 Azure 認知搜尋層可以是改善緩慢查詢效能的另一種方式。 每個較高層級可提供更快速的 Cpu 和更多記憶體，這兩者都有對查詢效能的正面影響。

## <a name="scale-for-availability"></a>適用于可用性的調整

複本不僅有助於減少查詢延遲，也可以允許高可用性。 利用單一複本，您應該可以預期定期的停機時間，因為伺服器會在軟體更新之後重新開機，或針對其他將發生的維護事件重新開機。 因此，請務必考慮應用程式是否需要搜尋 (查詢) 及寫入 (編製事件的索引) 的高可用性。 Azure 認知搜尋在具有下列屬性的所有付費搜尋供應專案上提供 SLA 選項：

+ 針對唯讀工作負載 (查詢)，需有 2 個複本才能達到高可用性

+ 適用于高可用性之讀寫工作負載的三個或多個複本 (查詢和編制索引) 

如需詳細資訊，請流覽 [Azure 認知搜尋服務等級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於複本是您資料的複本，因此，擁有多個複本可讓 Azure 認知搜尋針對一個複本進行電腦重新開機和維護，而在其他複本上繼續執行查詢。 相反地，如果您將複本移出，則會產生查詢效能降低的情況，假設這些複本都是使用量過低的資源。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>針對地理位置分散的工作負載及異地冗余進行調整

針對地理位置分散的工作負載，位於主機資料中心以外的使用者將會有較高的延遲率。 其中一個緩和措施是在區域中布建多個搜尋服務，更接近這些使用者。

Azure 認知搜尋目前並不提供自動化的跨區域複寫 Azure 認知搜尋索引的方法，但是有一些技巧可以用來簡化和管理此程式。 我們將在後續小節中加以概述。

異地分散式搜尋服務的目標是要在兩個或多個區域中提供兩個以上的索引，其中使用者會路由至提供最低延遲的 Azure 認知搜尋服務，如下列範例所示：

   ![依區域交叉分析服務][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>跨多個服務保持資料同步

有兩個選項可讓您的分散式搜尋服務保持同步，這是由使用 [Azure 認知搜尋索引子](search-indexer-overview.md) 或推送 API (也稱為 [Azure 認知搜尋 REST API](/rest/api/searchservice/)) 。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引子來更新多個服務的內容

如果您已經在一個服務上使用索引子，您可以在第二個服務上設定第二個索引子，以使用相同的資料來源物件，從相同的位置提取資料。 每個區域中的每個服務都有自己的索引子和目標索引， (您的搜尋索引不會共用，這表示資料會) 複製，但每個索引子都會參考相同的資料來源。

以下是該架構外觀的高階視覺效果。

   ![具有分散式索引子和服務組合的單一資料來源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api 將內容更新推送至多個服務

如果您使用 Azure 認知搜尋 REST API 將 [內容推送 Azure 認知搜尋索引](/rest/api/searchservice/update-index)，您可以在需要更新時將變更推送至所有搜尋服務，藉此讓不同的搜尋服務保持同步。 在您的程式碼中，請務必處理某項搜尋服務的更新失敗，但其他搜尋服務會成功的情況。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理員

[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md) 可讓您將要求路由傳送至多個地理位置的網站，然後由多個搜尋服務提供支援。 流量管理員的其中一個優點是它可以探查 Azure 認知搜尋以確保它可供使用，並在發生停機時，將使用者路由傳送至替代的搜尋服務。 此外，如果您透過 Azure 網站路由傳送搜尋要求，Azure 流量管理員可讓您在網站已啟動但未 Azure 認知搜尋的情況下，進行負載平衡。 以下範例為運用流量管理員的架構。

   ![利用中央流量管理員依區域交叉分析服務][3]

## <a name="next-steps"></a>後續步驟

若要深入瞭解各項定價層和服務的限制，請參閱 [服務限制](search-limits-quotas-capacity.md)。 若要深入瞭解分割區和複本組合，請參閱 [容量規劃](search-capacity-planning.md) 。

如需本文所討論之技術的效能和示範的討論，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png