---
title: 使用內建索引子為大型資料集編制索引
titleSuffix: Azure Cognitive Search
description: 透過批次模式、資源，以及已排程、平行和分散式索引的技術，進行大型資料編制索引或大量計算索引編制的策略。
manager: liamca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/05/2020
ms.openlocfilehash: 80307c97464e61d7b7d338703de90d1199adc819
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927012"
---
# <a name="how-to-index-large-data-sets-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中為大型資料集編制索引

Azure 認知搜尋支援[兩種基本方法](search-what-is-data-import.md)以供您將資料匯入到搜尋索引中：以程式設計方式將資料*推送*至索引，或將 [Azure 認知搜尋索引子](search-indexer-overview.md)指向受支援的資料來源，以*提取*資料。

隨著資料磁片區的成長或處理需求的變更，您可能會發現簡單或預設的索引編制策略已不再可行。 針對 Azure 認知搜尋，有數種方法可以容納較大型的資料集，範圍從您如何建立資料上傳要求的結構，到使用適用于已排程和分散式工作負載的來源特定索引子。

相同的技巧也適用于長時間執行的進程。 尤其是， [平行索引編制](#parallel-indexing) 中所述的步驟有助於進行大量計算的索引編制，例如在 [AI 擴充管線](cognitive-search-concept-intro.md)中進行影像分析或自然語言處理。

下列各節將探索使用推送 API 和索引子來編制大量資料索引的技術。

## <a name="push-api"></a>推送 API

將資料推送至索引時，有幾個主要考慮會影響推送 API 的索引速度。 下一節將概述這些因素。 

除了本文中的資訊之外，您還可以利用「 [優化索引編制速度」教學](tutorial-optimize-indexing-push-api.md) 課程中的程式碼範例來深入瞭解。

### <a name="service-tier-and-number-of-partitionsreplicas"></a>服務層和資料分割/複本的數目

新增資料分割或增加搜尋服務的層級，將會增加索引速度。

新增額外複本也可能會增加索引速度，但不保證。 另一方面，其他複本會增加搜尋服務可以處理的查詢量。 複本也是取得 [SLA](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的重要元件。

新增資料分割/複本或升級至較高的階層之前，請考慮貨幣成本和配置時間。 新增資料分割可能會大幅增加編制索引速度，但新增/移除它們可能需要15分鐘到數小時的時間。 如需詳細資訊，請參閱 [調整容量](search-capacity-planning.md)的相關檔。

### <a name="index-schema"></a>索引架構

索引的架構在索引資料方面扮演著重要的角色。 將欄位加入欄位，並將其他屬性新增至這些欄位 (例如可搜尋、*可 facet*或可*篩選*) 兩者都會減少編制*索引的速度*。

一般來說，如果您想要使用欄位，建議您只將其他屬性加入欄位。

> [!NOTE]
> 若要保持檔案大小，請避免將不可查詢的資料新增至索引。 影像和其他二進位資料無法直接搜尋，而且不應該儲存於索引中。 若要將不可查詢的資料整合到搜尋結果，請定義不可搜尋的欄位，以儲存資源的 URL 參考。

### <a name="batch-size"></a>批次大小

若要為較大型的資料集編製索引，其中一個最簡單的機制是在單一要求中提交多個文件或記錄。 只要整個承載的大小在 16 MB 以內，要求便可以在大量上傳作業中處理多達 1000 個文件。 無論您是在 .NET SDK 中使用 [ [新增檔] REST API](/rest/api/searchservice/addupdate-or-delete-documents) 或 [索引方法](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) ，都適用這些限制。 針對任一個 API，您會將1000檔封裝在每個要求的主體中。

使用批次來編制檔索引，可大幅改善索引效能。 為您的資料決定最佳批次大小是將索引編製速度最佳化的關鍵要素。 影響最佳批次大小的兩個主要因素如下：
+ 索引的結構描述
+ 資料的大小

由於最佳批次大小取決於您的索引和您的資料，因此最佳做法是測試不同的批次大小，以判斷您案例的最快編制索引速度。 本 [教學](tutorial-optimize-indexing-push-api.md) 課程提供使用 .net SDK 測試批次大小的範例程式碼。 

### <a name="number-of-threadsworkers"></a>執行緒/背景工作數目

為了充分利用 Azure 認知搜尋的索引編製速度，您很可能必須使用多個執行緒，以將批次索引要求同時傳送給服務。  

最佳的執行緒數目取決於：

+ 搜尋服務的層級
+ 磁碟分割數目
+ 您的批次大小
+ 索引的結構描述

您可以修改此範例，並使用不同的執行緒計數進行測試，以判斷您所處情況的最佳執行緒計數。 不過，只要您有數個同時執行的執行緒，應該就能利用絕大多數的效率增益。 

> [!NOTE]
> 當您增加搜尋服務的層級或增加分割區時，您也應該增加平行線程數目。

當抵達搜尋服務的要求增加時，您可能會遇到 [HTTP 狀態碼](/rest/api/searchservice/http-status-codes)指出要求並未完全成功。 在索引編製期間，兩個常見的 HTTP 狀態碼如下：

+ **503 服務無法使用** - 此錯誤表示系統負載過重，因此目前無法處理要求。
+ **207多重狀態** - 此錯誤表示有些文件成功，但至少有一個文件失敗。

### <a name="retry-strategy"></a>重試策略 

如果發生失敗，系統應該就會使用[指數輪詢重試策略](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)來重試要求。

Azure 認知搜尋的 .NET SDK 會自動重試 503 和其他失敗的要求，但您必須實作自己的邏輯才能重試 207。 您也可以使用 [Polly](https://github.com/App-vNext/Polly) 等開放原始碼工具來實作重試策略。

### <a name="network-data-transfer-speeds"></a>網路資料傳送速率

在編制資料索引時，網路資料傳送速率可能是限制因素。 從 Azure 環境內編制資料索引是加速編制索引的簡單方法。

## <a name="indexers"></a>索引子

[索引子](search-indexer-overview.md) 可用來編目支援的 Azure 資料來源，以取得可搜尋的內容。 有幾個索引子功能雖然不是專門用於大規模的索引編製作業，但特別適用於因應較大型的資料集：

+ 排程器可讓您以固定間隔分拆索引編製作業，以將作業分散到不同時間進行。
+ 排程的索引編製作業可以在最後一個已知的停止點繼續進行。 如果資料來源未能在 24 小時內完整搜耙一遍，索引子會在第二天從上次停下的地方繼續開始索引編製作業。
+ 將資料分割成較小的個別資料來源可實現平行處理作業。 您可以將來源資料分割成較小的元件，例如 Azure Blob 儲存體中的多個容器，然後在 Azure 認知搜尋中建立對應的多個 [資料來源物件](/rest/api/searchservice/create-data-source) ，以平行方式進行編制索引。

> [!NOTE]
> 索引子是資料來源特有的，因此使用索引子方法只適用于 Azure 上的選取資料來源： [SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)、 [Blob 儲存體](search-howto-indexing-azure-blob-storage.md)、 [資料表儲存體](search-howto-indexing-azure-tables.md)、 [Cosmos DB](search-howto-index-cosmosdb.md)。

### <a name="batch-size"></a>批次大小

如同推送 API，索引子可讓您設定每個批次的專案數目。 針對以[建立索引子 REST API](/rest/api/searchservice/Create-Indexer) 為基礎的索引子，您可以設定 `batchSize` 引數以自訂此設定，以便更加符合資料的特性。 

預設批次大小是資料來源特有的。 Azure SQL Database 和 Azure Cosmos DB 的預設批次大小為1000。 相反地，Azure Blob 索引會將批次大小設定為10份檔，以辨識較大的平均檔案大小。 

### <a name="scheduled-indexing"></a>排程的索引編製

索引子排程是一種重要機制，可用來處理大型資料集以及緩慢執行的程序 (例如，認知搜尋管線中的影像分析)。 索引子處理會在 24 小時的期間內運作。 如果處理無法在 24 小時內完成，索引子排程的行為可能對您會有幫助。 

根據設計，排程的索引編製會在特定間隔啟動，且作業通常會在繼續執行下一個排程的間隔之前完成。 不過，如果處理未在間隔內完成，索引子就會停止 (因為其時間已用盡)。 在下一個間隔中，將會從上次中斷之處繼續進行處理，而系統會追蹤該發生點。 

實際上，對於跨數天的索引負載，您可以將索引子安排在 24 小時的排程上。 繼續在下一個 24 小時的週期內進行索引編製時，作業會從最後一個已知的正常文件重新開始。 如此，索引子將可順利在數天內處理待處理項目，直到所有未處理的文件都完成處理為止。 如需此方法的詳細資訊，請參閱[在 Azure Blob 儲存體中為大型資料集編製索引](search-howto-indexing-azure-blob-storage.md#indexing-large-datasets)。 如需有關一般設定排程的詳細資訊，請參閱 [建立索引子 REST API](/rest/api/searchservice/Create-Indexer) 或查看 [如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

<a name="parallel-indexing"></a>

### <a name="parallel-indexing"></a>平行索引

平行索引編製策略所根據的是以一致的方式為多個資料來源編製索引，在此策略中，每個資料來源定義都會指定資料的子集。 

若需要進行非例行性且需要密集計算的索引編製作業 (例如，對認知搜尋管線中的掃描文件進行 OCR、影像分析或自然語言處理)，平行索引編製策略往往是能在最短時間完成長時間執行程序的理想方法。 如果您可以消除或減少查詢要求，對不會同時處理查詢的服務使用平行索引編製策略，便會成為因應龐大且處理速度緩慢的內容的最佳策略選項。 

平行處理具有下列元素：

+ 將來源資料分置於多個容器中，或是相同容器內的多個虛擬資料夾中。 
+ 將每個小型資料集對應至其本身的 [資料來源](/rest/api/searchservice/create-data-source)（與其本身的 [索引子](/rest/api/searchservice/create-indexer)配對）。
+ 對於認知搜尋，請參考每個索引子定義中的相同[技能集](/rest/api/searchservice/create-skillset)。
+ 寫入相同的目標搜尋索引中。 
+ 將所有索引子排程在相同時間執行。

> [!NOTE]
> 在 Azure 認知搜尋中，您無法將個別的複本或資料分割指派給索引或查詢處理。 系統會決定資源的使用方式。 若要瞭解對查詢效能的影響，您可以在測試環境中嘗試平行編制索引，再將其轉換成生產環境。  

### <a name="how-to-configure-parallel-indexing"></a>如何設定平行索引編製

就索引子而言，處理容量大致上會以搜尋服務所使用的每個服務單位 (SU) 的一個索引子子系統為基礎。 在基本層或標準層（至少有兩個複本）上布建的 Azure 認知搜尋服務，可能會有多個並行索引子。 

1. 在 [Azure 入口網站](https://portal.azure.com)中，在搜尋服務儀表板的 [概觀]**** 頁面上查看 [定價層]****，以確認足供平行索引之用。 基本層和標準層都提供多個複本。

2. 您可以平行執行多個索引子，做為服務中的搜尋單位數目。 在 [**設定**  >  **規模**] 中，[增加複本](search-capacity-planning.md)或資料分割以進行平行處理：每個索引子工作負載一個額外的複本或資料分割。 現有的查詢磁碟區保留足夠的數目。 為了編製索引而犧牲查詢工作負載，不是理想的取捨。

3. 將資料分散到多個容器，而該層級的 Azure 認知搜尋索引子可以觸達。 這可以是 Azure SQL Database 中的多個資料表、Azure Blob 儲存體中的多個容器，或是多個集合。 為每個資料表或容器定義一個資料來源物件。

4. 建立和排程多個索引子，以平行方式執行：

   + 假設一項服務有六個複本。 設定六個索引子，分別對應至包含六分之一個資料集的資料來源，讓整個資料集分割到六個不同的去處。 

   + 將每個索引子指向相同的索引。 對於認知搜尋工作負載，將每個索引子指向相同的技能集。

   + 在每個索引子定義內，排程相同的執行階段執行模式。 例如，`"schedule" : { "interval" : "PT8H", "startTime" : "2018-05-15T00:00:00Z" }` 會對所有索引子建立 2018-05-15 的排程，以八小時的間隔執行。

在排定的時間，所有索引子都會開始執行、載入資料、套用擴充 (如果您設定了認知搜尋管線)，並寫入至索引。 Azure 認知搜尋不會鎖定索引以進行更新。 並行寫入會受到管理，如果特定寫入在第一次嘗試時不成功，將會重試。

> [!Note]
> 增加複本時，如果預計索引大小會大幅增加，請考慮增加分割區計數。 分割區會儲存配量的索引內容；您的分割區愈多，每個分割區所須儲存的配量就愈少。

## <a name="see-also"></a>請參閱

+ [索引子概觀](search-indexer-overview.md)
+ [在入口網站中編製索引](search-import-data-portal.md)
+ [Azure SQL Database 索引子](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
+ [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
+ [Azure Blob 儲存體索引子](search-howto-indexing-azure-blob-storage.md)
+ [Azure 資料表儲存體索引子](search-howto-indexing-azure-tables.md)
+ [Azure 認知搜尋中的安全性](search-security-overview.md)