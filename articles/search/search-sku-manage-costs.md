---
title: 預估成本
titleSuffix: Azure Cognitive Search
description: 瞭解計費事件、計價模式，以及管理執行認知搜尋服務成本的秘訣。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: 4ad362b983f81e2cdc10cdbccafd8dda951482d7
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539560"
---
# <a name="how-to-estimate-and-manage-costs-of-an-azure-cognitive-search-service"></a>如何預估和管理 Azure 認知搜尋服務的成本

在本文中，您將瞭解定價模型、計費事件，以及管理執行 Azure 認知搜尋服務成本的秘訣。

## <a name="pricing-model"></a>定價模式

Azure 認知搜尋中的擴充性架構是以彈性的複本和資料分割組合為基礎，因此您可以根據您是否需要更多查詢或索引編制電源來變更容量，並只針對所需的部分付費。

搜尋服務所使用的資源數量乘以服務層所建立的計費費率，會決定執行服務的成本。 成本和容量會緊密地系結。 估計成本時，瞭解執行索引和查詢工作負載所需的容量，可讓您最好瞭解預計的成本。

基於計費目的，有兩個簡單的公式要注意：

| Formula | 描述 |
|---------|-------------|
| **R x P = SU** | 使用的複本數目乘以使用的資料分割數目，等於服務所使用的 *搜尋單位* 數量 (SU) 。 SU 是資源的單位，它可以是分割區或複本。 |
| **SU * 帳單費率 = 每月費用** | Su 乘以您布建服務之階層的計費費率，是整體每月帳單的主要行列式。 某些功能或工作負載相依于其他 Azure 服務，這可能會增加您解決方案在訂用帳戶層級的成本。 下列可計費事件區段會識別可新增至帳單的功能。 |

每個服務都以一個 SU (一個複本乘以一個分割區) 作為最小值。 任何服務的最大值為 36 su。 您可以透過多種方式來達到此上限：6個分割區 x 6 個複本，或3個分割區 x 12 個複本（例如）。 使用的容量通常會低於總容量 (例如，3個複本、3個分割區服務，以9個 SUs) 計費。 請參閱 [分割區和複本組合](search-capacity-planning.md#chart) 圖表，以取得有效的組合。

每個 SU 的計費費率為每小時。 每一層都有逐漸較高的速率。 較高的階層具有較大和 speedier 的資料分割，這對該層的整體每小時費率都有貢獻。 您可以在 [ [定價詳細資料](https://azure.microsoft.com/pricing/details/search/) ] 頁面上查看每一層的費率。

大多數客戶只能線上提供總容量的一部分，其餘部分則保留。 針對計費，您上線的資料分割和複本數目（由 SU 公式計算）會決定您以小時為單位的費用。 

## <a name="billable-events"></a>計費事件

以 Azure 認知搜尋為基礎的解決方案可能會以下列方式產生成本：

+ [服務](#service-costs) 本身的成本，以基本費率以最少的設定來 (一個分割區和複本) 。 您可以將此視為執行服務的固定成本。

+ 新增容量 (複本或資料分割) ，其中的成本會隨著計費費率遞增。 如果高可用性是商務需求，您將需要3個複本。

+  (輸出資料傳輸) 的頻寬費用

+ 特定功能或功能所需的附加元件服務：

  + AI 擴充 (需要 [認知服務](https://azure.microsoft.com/pricing/details/cognitive-services/)) 
  + 知識存放區 (需要 [Azure 儲存體](https://azure.microsoft.com/pricing/details/storage/)) 
  + 增量擴充 (需要 [Azure 儲存體](https://azure.microsoft.com/pricing/details/storage/)，適用于 AI 擴充) 
  + 客戶管理的金鑰和雙重加密 (需要 [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)) 
  + 無網際網路存取模型的私人端點 (需要 [Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/)) 

### <a name="service-costs"></a>服務成本

不同于可「暫停」以避免產生費用的虛擬機器或其他資源，在專用的硬體上一律可使用 Azure 認知搜尋服務。 因此，建立服務是一個計費事件，會在您建立服務時啟動，並在您刪除服務時結束。 

最小的收費是 (一個複本 x 一個資料分割) 計費費率的第一個搜尋單位。 此最小值會在服務的存留期內修正，因為服務無法在低於此設定的任何時間執行。 

除了最小值之外，您還可以個別新增複本和資料分割。 透過複本和分割區增量增加的容量會根據下列公式增加您的帳單： **(複本 x 分割區 x 計費費率)**，而您要向其收費的費率取決於您選取的定價層。

當您估計搜尋解決方案的成本時，請記住，定價和容量不是線性的 (倍的容量會比將成本) 加倍。 如需公式運作方式的範例，請參閱 [如何配置複本和](search-capacity-planning.md#how-to-allocate-replicas-and-partitions)資料分割。

### <a name="bandwidth-charges"></a>頻寬費用

如果 Azure 資料來源與 Azure 認知搜尋位於不同的區域，則使用 [索引子](search-indexer-overview.md) 可能會影響計費。 在此案例中，將輸出資料從 Azure 資料來源移至 Azure 認知搜尋可能會產生費用。 如需詳細資訊，請參閱 Azure 資料平臺的定價頁面。

如果您在與資料相同的區域中建立 Azure 認知搜尋服務，則可以完全消除資料輸出費用。 以下是 [頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)的一些資訊：

+ 輸入資料： Microsoft 不會對 Azure 上的任何服務收取任何輸入資料的費用。 

+ 輸出資料：輸出資料是指查詢結果。 認知搜尋不會收取輸出資料的費用，但如果服務位於不同的區域，則可以從 Azure 傳出費用。

  這些費用實際上不是您 Azure 認知搜尋帳單的一部分。 這裡提及的是，因為如果您要將結果傳送給其他區域或非 Azure 應用程式，您可以看到這些成本反映在整體帳單中。

### <a name="ai-enrichment-with-cognitive-services"></a>使用認知服務的 AI 擴充

針對 [AI 擴充](cognitive-search-concept-intro.md)，您應該計畫在 S0 定價層中， [將可計費的 Azure 認知服務資源附加](cognitive-search-attach-cognitive-services.md)至 Azure 認知搜尋的相同區域中，以進行隨用隨付處理。 附加認知服務沒有相關聯的固定成本。 您只需支付所需的處理費用。

| 作業 | 計費影響 |
|-----------|----------------|
| 檔破解，文字解壓縮 | 免費 |
| 檔破解，映射解壓縮 | 根據從您的檔中解壓縮的影像數目計費。 在 [索引子](/rest/api/searchservice/create-indexer#indexer-parameters)設定中， **>imageaction** 是觸發影像解壓縮的參數。 如果 **>imageaction** 設為 "none" (預設) ，則不會向您收取映射解壓縮的費用。 映射解壓縮的速率記載于 Azure 認知搜尋的 [定價詳細資料](https://azure.microsoft.com/pricing/details/search/) 頁面上。|
| [內建認知技能](cognitive-search-predefined-skills.md) | 以與您直接使用認知服務來執行工作相同的費率計費。 |
| 自訂技能 | 自訂技能是您所提供的功能。 使用自訂技能的成本完全取決於自訂程式碼是否呼叫其他計量付費服務。 |

[增量擴充 (preview) ](cognitive-search-incremental-indexing-conceptual.md)功能可讓您提供快取，讓索引子更有效率地執行您在未來修改技能集時所需的認知技能，以節省您的時間和金錢。

## <a name="tips-for-managing-costs"></a>管理成本的秘訣

下列指導方針可協助您降低成本或更有效率地管理成本：

+ 在相同的區域中建立所有資源，或盡可能減少最少的區域，以最小化或消除頻寬費用。

+ 將所有服務合併成一個資源群組，例如 Azure 認知搜尋、認知服務，以及解決方案中使用的任何其他 Azure 服務。 在 Azure 入口網站中，尋找資源群組，並使用 **成本管理** 命令，深入瞭解實際和預估的費用。

+ 針對您的前端應用程式考慮 Azure Web 應用程式，讓要求和回應保持在資料中心界限內。

+ 擴大大量資源的作業（例如編制索引），然後針對一般查詢工作負載重新調整。 從 Azure 認知搜尋的最小設定開始， (由一個磁碟分割和一個複本) 組成的 SU，然後監視使用者活動，以找出表示需要更多容量的使用模式。 如果有可預測的模式，您可以將調整與活動同步處理 (您必須撰寫程式碼，才能將此) 自動化。

+ 成本管理內建在 Azure 基礎結構中。 查看 [帳單和成本管理](../cost-management-billing/cost-management-billing-overview.md) ，以取得有關追蹤成本、工具和 api 的詳細資訊。

不可能暫時關閉搜尋服務。 專用資源一律可運作，並配置給您的服務存留期專屬使用。 刪除服務是永久性的，也會刪除其相關聯的資料。

就服務本身而言，降低帳單的唯一方法是將複本和資料分割縮減為仍提供可接受效能和 [SLA 合規性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的層級，或在較低層建立服務， (S1 每小時費率低於 S2 或 S3 費率) 。 如果您在負載預測的下半部布建服務，則在超出服務時，您可以建立第二個較大的分層服務，在第二個服務上重建索引，然後刪除第一個服務。

## <a name="next-steps"></a>後續步驟

瞭解如何在您的 Azure 訂用帳戶中監視和管理成本。

> [!div class="nextstepaction"]
> [Azure 成本管理和計費文件](../cost-management-billing/cost-management-billing-overview.md)