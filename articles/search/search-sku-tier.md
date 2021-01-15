---
title: 選擇定價層
titleSuffix: Azure Cognitive Search
description: 您可以在下列層級布建 Azure 認知搜尋：免費、基本和標準，以及標準適用于各種資源設定和容量層級。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4c58968cb6a38a10433915ec8fa00336ccad301e
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/14/2021
ms.locfileid: "98216405"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>選擇 Azure 認知搜尋的定價層

當您 [建立搜尋服務](search-create-service-portal.md)時，您會選擇在服務存留期內固定的定價層。 您選取的層會決定：

+ 您可以建立的索引數量和其他物件 (上限) 
+ 分割區的大小和速度 (實體儲存體)
+ 可計費費率，每月固定成本，但如果您新增資料分割或複本，也會遞增成本

此外，有幾個高階 [功能](#premium-features) 會隨附于階層需求。

## <a name="tier-descriptions"></a>層描述

層級包括 **免費**、 **基本**、 **標準** 和 **儲存體優化**。 標準和儲存體優化可透過數個設定和容量提供。

下列來自 Azure 入口網站的螢幕擷取畫面顯示可在入口網站和 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)上找到的可用層（減去定價 (）。 

![Azure 認知搜尋的定價層](media/search-sku-tier/tiers.png "Azure 認知搜尋的定價層")

**Free** 可針對較小的專案建立有限的搜尋服務，例如執行教學課程和程式碼範例。 在內部，會在多個訂閱者之間共用複本和資料分割。 您無法調整免費服務或執行大量的工作負載。

**基本** 和 **標準** 是最常用的可計費層， **標準** 為預設值。 您可以透過控制項下的專用資源，部署較大型的專案、將效能優化，以及增加容量。

某些層級已針對特定類型的工作進行優化。 例如， **標準3高密度 (S3 HD)** 是適用于 s3 的 *主機模式* ，其中基礎硬體已針對大量較小的索引進行優化，且適用于多租使用者案例。 S3 HD 具有與 S3 相同的每單位費用，但硬體已針對大量較小的索引進行快速檔案讀取優化。

**儲存體優化** 層以比標準層更低的價格提供較大的儲存體容量。 主要的取捨是較高的查詢延遲，您應該針對特定的應用程式需求進行驗證。 若要深入瞭解此層的效能考慮，請參閱 [效能與優化考慮](search-performance-optimization.md)。

您可以在 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)、Azure 認知搜尋文章中的 [服務限制](search-limits-quotas-capacity.md) ，以及當您布建服務時的入口網站頁面上，找到各層的詳細資訊。

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>依層級的功能可用性

大部分的功能都可在所有層級上使用，包括免費層。 在少數情況下，您選擇的層級會影響您執行功能的能力。 下表說明與服務層相關的功能條件約束。

| 功能 | 限制 |
|---------|-------------|
| [索引](search-indexer-overview.md) | S3 HD 上無法使用索引子。  |
| [AI 擴充](search-security-manage-encryption-keys.md) | 在免費層上執行，但不建議使用。 |
| [客戶管理的加密金鑰](search-security-manage-encryption-keys.md) | 無法在免費層上使用。 |
| [IP 防火牆存取](service-configure-firewall.md) | 無法在免費層上使用。 |
| [私人端點 (與 Azure Private Link) 整合 ](service-create-private-endpoint.md) | 針對搜尋服務的輸入連線，在免費層中無法使用。 針對其他 Azure 資源的索引子的輸出連線，在免費或 S3 HD 上無法使用。 適用于使用技能集的索引子，不適用於 Free、Basic、S1 或 S3 HD。|

除非您提供足夠的容量，否則耗用大量資源的功能可能無法正常運作。 例如，除非資料集很小，否則 [AI 擴充](cognitive-search-concept-intro.md) 具有長時間執行的技能，可在免費服務上運作。

## <a name="billable-events"></a>計費事件

以 Azure 認知搜尋為基礎的解決方案可能會以下列方式產生成本：

+ [服務](#service-costs) 本身的成本，以基本費率以最少的設定來 (一個分割區和複本) 。 您可以將此視為執行服務的固定成本。

+ 新增容量 (複本或資料分割) ，其中的成本會隨著計費費率遞增

+  (輸出資料傳輸) 的頻寬費用

+ 特定功能或功能所需的附加元件服務：

  + AI 擴充 (需要 [認知服務](https://azure.microsoft.com/pricing/details/cognitive-services/)) 
  + 知識存放區 (需要 [Azure 儲存體](https://azure.microsoft.com/pricing/details/storage/)) 
  + 增量擴充 (需要 [Azure 儲存體](https://azure.microsoft.com/pricing/details/storage/)，適用于 AI 擴充) 
  + 客戶管理的金鑰和雙重加密 (需要 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)) 
  + 無網際網路存取模型的私人端點 (需要 [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/)) 

### <a name="service-costs"></a>服務成本

不同于可「暫停」以避免產生費用的虛擬機器或其他資源，在專用的硬體上一律可使用 Azure 認知搜尋服務。 因此，建立服務是一個計費事件，會在您建立服務時啟動，並在您刪除服務時結束。 

最小的收費是 (一個複本 x 一個資料分割) 計費費率的第一個搜尋單位。 此最小值會在服務的存留期內修正，因為服務無法在低於此設定的任何時間執行。 除了最小值之外，您還可以個別新增複本和資料分割。 透過複本和分割區增量增加的容量會根據下列公式來增加您的帳單： [ (複本 x 分割區 x 速率) ](#search-units)，也就是您所選的定價層的費用。

當您估計搜尋解決方案的成本時，請記住，定價和容量並非線性。  (倍的容量會比將成本加倍。 ) 如需公式運作方式的範例，請參閱 [如何配置複本和](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)資料分割。

### <a name="bandwidth-charges"></a>頻寬費用

如果 Azure 資料來源與 Azure 認知搜尋位於不同的區域，則使用 [索引子](search-indexer-overview.md) 可能會影響計費。 在此案例中，將輸出資料從 Azure 資料來源移至 Azure 認知搜尋可能會產生費用。 如需詳細資訊，請參閱 Azure 資料平臺的定價頁面。

如果您在與資料相同的區域中建立 Azure 認知搜尋服務，則可以完全消除資料輸出費用。 以下是 [頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)的一些資訊：

+ 輸入資料： Microsoft 不會對 Azure 上的任何服務收取任何輸入資料的費用。 

+ 輸出資料：輸出資料是指查詢結果。 認知搜尋不會收取輸出資料的費用，但如果服務位於不同的區域，則可以從 Azure 傳出費用。 這些費用實際上不是您 Azure 認知搜尋帳單的一部分。 這裡提及的是，因為如果您要將結果傳送給其他區域或非 Azure 應用程式，您可以看到這些成本反映在整體帳單中。

### <a name="ai-enrichment-with-cognitive-services"></a>使用認知服務的 AI 擴充

針對 [AI 擴充](cognitive-search-concept-intro.md)，您應該計畫在 S0 定價層中， [將可計費的 Azure 認知服務資源附加](cognitive-search-attach-cognitive-services.md)至 Azure 認知搜尋的相同區域中，以進行隨用隨付處理。 附加認知服務沒有相關聯的固定成本。 您只需支付所需的處理費用。

| 作業 | 計費影響 |
|-----------|----------------|
| 檔破解，文字解壓縮 | 免費 |
| 檔破解，映射解壓縮 | 根據從您的檔中解壓縮的影像數目計費。 在 [索引子](/rest/api/searchservice/create-indexer#indexer-parameters)設定中， **>imageaction** 是觸發影像解壓縮的參數。 如果 **>imageaction** 設為 "none" (預設) ，則不會向您收取映射解壓縮的費用。 映射解壓縮的速率記載于 Azure 認知搜尋的 [定價詳細資料](https://azure.microsoft.com/pricing/details/search/) 頁面上。|
| [內建認知技能](cognitive-search-predefined-skills.md) | 以與您直接使用認知服務來執行工作相同的費率計費。 |
| 自訂技能 | 自訂技能是您所提供的功能。 使用自訂技能的成本完全取決於自訂程式碼是否呼叫其他計量付費服務。 |

[增量擴充 (preview) ](cognitive-search-incremental-indexing-conceptual.md)功能可讓您提供快取，讓索引子更有效率地執行您在未來修改技能集時所需的認知技能，以節省您的時間和金錢。

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a> (R x P = SU) 的計費公式

若要瞭解 Azure 認知搜尋作業，最重要的計費概念是 *搜尋單位* (SU) 。 由於 Azure 認知搜尋相依于索引編制和查詢的複本和資料分割，因此只需要收取一或多個資料分割就不合理。 相反地，會將兩者合併來計費。

SU 是服務 *所用**複本* 和資料分割的乘積： **(R x P = SU)**。

每個服務都以一個 SU (一個複本乘以一個分割區) 作為最小值。 任何服務的最大值為 36 su。 您可以透過多種方式來達到此上限：6個分割區 x 6 個複本，或3個分割區 x 12 個複本（例如）。 使用的容量通常會低於總容量 (例如，3個複本、3個分割區服務，以9個 SUs) 計費。 請參閱 [分割區和複本組合](search-capacity-planning.md#chart) 圖表，以取得有效的組合。

每個 SU 的計費費率為每小時。 每一層都有逐漸較高的速率。 較高的階層具有較大和 speedier 的資料分割，這對該層的整體每小時費率都有貢獻。 您可以在 [ [定價詳細資料](https://azure.microsoft.com/pricing/details/search/) ] 頁面上查看每一層的費率。

大多數客戶只能線上提供總容量的一部分，其餘部分則保留。 針對計費，您上線的資料分割和複本數目（由 SU 公式計算）會決定您以小時為單位的費用。

## <a name="next-steps"></a>後續步驟

成本管理是容量規劃不可或缺的一部分。 在下一個步驟中，請繼續閱讀下列文章，以取得如何預估容量和管理成本的指引。

> [!div class="nextstepaction"]
> [如何在 Azure 認知搜尋中管理成本和預估容量](search-sku-manage-costs.md)