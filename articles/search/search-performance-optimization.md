---
title: 效能級別
titleSuffix: Azure Cognitive Search
description: 瞭解微調 Azure 認知搜尋效能和設定最佳規模的技術和最佳作法。
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 7c2857de0613be400f83544e1dabe079b7497bbd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77212376"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure 認知搜尋的效能調整

本文會針對具有擴充性和可用性的複雜需求的先進案例，說明其最佳作法。

## <a name="start-with-baseline-numbers"></a>開始使用基準編號

在進行較大規模的部署工作之前，請確定您知道一般查詢負載的樣子。 下列指導方針可協助您達到基準查詢編號。

1. 挑選完成一般搜尋要求所應花費的目標延遲 (或最大時間量)。

1. 針對您的搜尋服務，使用實際的資料集來建立及測試實際的工作負載，以測量這些延遲率。

1. 從每秒的查詢數（QPS）開始，然後逐漸增加在測試中執行的數目，直到查詢延遲降到預先定義的目標以下為止。 這是很重要的效能評定，可協助您規劃應用程式將在使用量方面成長的規模。

1. 如果可能，請重複使用 HTTP 連接。 如果您使用 Azure 認知搜尋 .NET SDK，這表示您應該重複使用實例或[SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient)實例，而且如果您使用 REST API，就應該重複使用單一 HttpClient。

1. 會改變查詢要求的實質，讓搜尋發生在索引的不同部分。 差異很重要，因為如果您持續執行相同的搜尋要求，快取資料將會開始，使效能看起來會比使用更不同的查詢集更好。

1. 會改變查詢要求的結構，讓您取得不同類型的查詢。 並非每個搜尋查詢都會在相同層級執行。 例如，檔查閱或搜尋建議的速度通常會比具有大量 facet 和篩選器的查詢更快。 測試撰寫應該包含各種查詢，大致上與在生產環境中預期的比率相同。  

建立這些測試工作負載時，Azure 認知搜尋有一些特性要牢記在心：

+ 您可以多次發送搜尋查詢來多載您的服務。 發生這種情況時，您將會看到 HTTP 503 回應碼。 若要在測試期間避免503，請從各種搜尋要求範圍開始，以在新增更多搜尋要求時查看延遲率的差異。

+ Azure 認知搜尋不會在背景中執行索引工作。 如果您的服務同時處理查詢和編制索引工作負載，請將索引編制作業引入查詢測試中，或探索在離峰時間執行索引作業的選項，以將其納入考慮。

> [!Tip]
> 您可以使用負載測試工具模擬實際的查詢負載。 嘗試[使用 Azure DevOps 進行負載測試，](https://docs.microsoft.com/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops)或使用其中一種[替代方案](https://docs.microsoft.com/azure/devops/test/load-test/overview?view=azure-devops#alternatives)。

## <a name="scale-for-high-query-volume"></a>高查詢量的規模

當查詢花費的時間太長，或服務開始卸載要求時，服務就會過重。 如果發生這種情況，您可以透過下列兩種方式的其中一種來解決問題：

+ **新增複本**  

  每個複本都是您的資料複本，可讓服務針對多個複本進行要求的負載平衡。  所有的負載平衡和資料複寫都是由 Azure 認知搜尋管理，您可以隨時更改為服務配置的複本數目。 您最多可在標準搜尋服務中配置 12 個複本，並在基本搜尋服務中配置 3 個複本。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整複本。

+ **在較高的層級建立新的服務**  

  Azure 認知搜尋有[數個層級](https://azure.microsoft.com/pricing/details/search/)，每個都提供不同層級的效能。 在某些情況下，您可能會有這麼多查詢，因為您所在的層無法提供足夠的週期，即使是在超量而複本時也是一樣。在此情況下，請考慮移至較高的執行層，例如標準 S3 層，其設計適用于具有大量檔的案例，以及非常高的查詢工作負載。

## <a name="scale-for-slow-individual-queries"></a>針對慢速個別查詢進行調整

高延遲率的另一個原因是單一查詢花太多時間來完成。 在此情況下，加入複本將不會有説明。 可能有下列兩個可能的選項會有説明：

+ **增加資料分割**

  分割區會在額外的計算資源中分割資料。 兩個分割區會將資料分成一半，而第三個分割區會將它分割成三分之一，依此類推。 其中一個正面的副作用是，速度較慢的查詢有時會因為平行運算而執行得更快。 我們已在低選擇性的查詢上（例如符合許多檔的查詢），或提供大量檔計數的 facet 來說明平行處理。 由於需要進行大量計算來評分檔的相關性，或計算檔數目，因此加入額外的資料分割可協助查詢更快完成。  
   
  標準搜尋服務中最多可以有12個數據分割，而在基本搜尋服務中可能會有1個分割區。 您可以從 [Azure 入口網站](search-create-service-portal.md)或 [PowerShell](search-manage-powershell.md) 來調整分割區。

+ **限制高基數位段**

  較高的基數位段包含具有大量唯一值的 facetable 或可篩選欄位，因此會在計算結果時耗用大量資源。 例如，將 [產品識別碼] 或 [描述] 欄位設定為 [facetable]/[可篩選] 會計算為 [高基數]，因為從檔到檔的大部分值都是唯一的。 如果可能，請限制高基數欄位數目。

+ **增加搜尋層**  

  若要提升至較高的 Azure 認知搜尋層，您可以使用另一種方式來改善緩慢查詢的效能。 每個較高層級提供更快的 Cpu 和更多記憶體，這兩者都對查詢效能有正面的影響。

## <a name="scale-for-availability"></a>適用于可用性的調整

複本不僅有助於減少查詢延遲，也可以允許高可用性。 利用單一複本，您應該可以預期定期的停機時間，因為伺服器會在軟體更新之後重新開機，或針對其他將發生的維護事件重新開機。 因此，請務必考慮應用程式是否需要搜尋 (查詢) 及寫入 (編製事件的索引) 的高可用性。 Azure 認知搜尋提供具有下列屬性的所有付費搜尋供應專案的 SLA 選項：

+ 針對唯讀工作負載 (查詢)，需有 2 個複本才能達到高可用性

+ 適用于讀寫工作負載（查詢和編制索引）的三個或多個複本以提供高可用性

如需這種情況的詳細資訊，請流覽[Azure 認知搜尋服務層級協定](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。

由於複本是您的資料複本，因此有多個複本可讓 Azure 認知搜尋對一個複本執行機器重新開機和維護，而在其他複本上繼續執行查詢。 相反地，如果您將複本帶掉，則會產生查詢效能降低的情況，假設這些複本是使用量過低的資源。

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>針對異地分散的工作負載和異地冗余進行調整

對於地理位置分散的工作負載而言，位於主機資料中心以外的使用者將會有較高的延遲率。 其中一個緩和措施就是在區域中布建多個搜尋服務，並更接近這些使用者。

Azure 認知搜尋目前不提供自動化方法來跨區域異地複寫 Azure 認知搜尋索引，但有一些技術可以用來讓此程式易於執行和管理。 我們將在後續小節中加以概述。

地理位置分散的搜尋服務集合的目標是在兩個或多個區域中有兩個或更多可用的索引，其中使用者會路由傳送至 Azure 認知搜尋服務，以提供此範例中所示的最低延遲：

   ![依區域交叉分析服務][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>將資料同步處理到多個服務

有兩個選項可讓您的分散式搜尋服務保持同步，其中包括使用[Azure 認知搜尋索引子](search-indexer-overview.md)或推送 API （也稱為[azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice/)）。  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>使用索引子來更新多個服務上的內容

如果您已經在一個服務上使用索引子，您可以在第二個服務上設定第二個索引子，以便使用相同的資料來源物件，從相同的位置提取資料。 每個區域中的每個服務都有自己的索引子和目標索引（您的搜尋索引不會共用，這表示資料會重複），但每個索引子都會參考相同的資料來源。

以下是該架構外觀的高階視覺效果。

   ![具有分散式索引子和服務組合的單一資料來源][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>使用 REST Api 在多個服務上推送內容更新

如果您使用 Azure 認知搜尋 REST API 在[Azure 認知搜尋索引中推送內容](https://docs.microsoft.com/rest/api/searchservice/update-index)，您可以在需要更新時，將變更推送到所有搜尋服務，藉以讓各種搜尋服務保持同步。 在您的程式碼中，請務必處理某個搜尋服務的更新失敗，但對其他搜尋服務成功的情況。

## <a name="leverage-azure-traffic-manager"></a>利用 Azure 流量管理員

[Azure 流量管理員](../traffic-manager/traffic-manager-overview.md)可讓您將要求路由傳送至多個地理位置的網站，然後由多個搜尋服務支援。 流量管理員的其中一個優點是，它可以探查 Azure 認知搜尋以確保其可供使用，並在發生停機時將使用者路由傳送至替代的搜尋服務。 此外，如果您透過 Azure 網站路由搜尋要求，Azure 流量管理員可讓您在網站已啟動但不是 Azure 認知搜尋的情況下，進行負載平衡。 以下範例為運用流量管理員的架構。

   ![利用中央流量管理員依區域交叉分析服務][3]

## <a name="next-steps"></a>後續步驟

若要深入瞭解定價層和各服務的限制，請參閱[服務限制](search-limits-quotas-capacity.md)。 如需深入瞭解分割區和複本組合的詳細資訊，請參閱[針對容量進行規劃](search-capacity-planning.md)。

如需本文所討論之技術的效能和示範討論，請觀看下列影片：

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
