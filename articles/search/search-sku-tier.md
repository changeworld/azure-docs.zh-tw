---
title: 選擇定價層或 SKU
titleSuffix: Azure Cognitive Search
description: 您可以在下列 Sku 中布建 Azure 認知搜尋：免費、基本和標準，以及標準適用于各種資源設定和容量層級。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: ac08f2cee19b2d8860323c48d89205d5ca939157
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88922798"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>選擇 Azure 認知搜尋的定價層

當您建立 Azure 認知搜尋服務時，會在服務存留期內固定的定價層 (或 SKU) 上 [建立資源](search-create-service-portal.md) 。 層級包括免費、基本、標準和儲存體優化。 標準和儲存體優化可透過數個設定和容量提供。

大部分的客戶都是從免費層開始，以便評估服務。 評估之後，在開發和生產部署的其中一個較高層級建立第二個服務是很常見的。

## <a name="feature-availability-by-tier"></a>依層級的功能可用性

下表說明階層相關的功能條件約束。

| 功能 | 限制 |
|---------|-------------|
| [索引](search-indexer-overview.md) | S3 HD 上無法使用索引子。 |
| [AI 擴充](search-security-manage-encryption-keys.md) | 在免費層上執行，但不建議使用。 |
| [客戶管理的加密金鑰](search-security-manage-encryption-keys.md) | 無法在免費層上使用。 |
| [IP 防火牆存取](service-configure-firewall.md) | 無法在免費層上使用。 |
| [與 Azure Private Link 整合](service-create-private-endpoint.md) | 無法在免費層上使用。 |

大部分的功能都可在每個層級上使用，包括免費，但需要大量資源的功能才能正常運作，除非您提供足夠的容量。 例如，除非資料集很小，否則 [AI 擴充](cognitive-search-concept-intro.md) 具有長時間執行的技能，可在免費服務上運作。

## <a name="tiers-skus"></a> (Sku 的層級) 

層級的差異如下：

+ 您可以建立的索引和索引子數量
+ 分割區的大小和速度 (實體儲存體)

您選取的層會決定計費費率。 下列來自 Azure 入口網站的螢幕擷取畫面顯示可在入口網站和 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)上找到的可用層（減去定價 (）。 「**免費**」、「**基本**」和「**標準**」是最常見的層級。

**Free** 可針對較小的專案（包括快速入門和教學課程）建立有限的搜尋服務。 在內部，會在多個訂閱者之間共用複本和資料分割。 您無法調整免費服務或執行大量的工作負載。

**基本** 和 **標準** 是最常用的可計費層， **標準** 為預設值。 您可以使用控制項下的專用資源，部署較大型的專案、將效能優化，並設定容量。

![Azure 認知搜尋的定價層](media/search-sku-tier/tiers.png "Azure 認知搜尋的定價層")

某些層級已針對特定類型的工作進行優化。 例如， **標準3高密度 (S3 HD) ** 是適用于 s3 的 *主機模式* ，其中基礎硬體已針對大量較小的索引進行優化，且適用于多租使用者案例。 S3 HD 具有與 S3 相同的每單位費用，但硬體已針對大量較小的索引進行快速檔案讀取優化。

**儲存體優化** 層以比標準層更低的價格提供較大的儲存體容量。 主要的取捨是較高的查詢延遲，您應該針對特定的應用程式需求進行驗證。  若要深入瞭解此層的效能考慮，請參閱 [效能與優化考慮](search-performance-optimization.md)。

您可以在 [ [定價] 頁面](https://azure.microsoft.com/pricing/details/search/)、Azure 認知搜尋文章中的 [服務限制](search-limits-quotas-capacity.md) ，以及當您布建服務時的入口網站頁面上，找到各層的詳細資訊。

## <a name="billable-events"></a>計費事件

以 Azure 認知搜尋為基礎的解決方案可能會以下列方式產生成本：

+ 服務本身的成本，以最少的設定 (一個分割區和複本) 

+ 新增容量 (複本或資料分割) 

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

根據服務的位置而定，使用 [索引子](search-indexer-overview.md) 可能會影響計費。 如果您在與資料相同的區域中建立 Azure 認知搜尋服務，則可以完全消除資料輸出費用。 以下是 [頻寬定價頁面](https://azure.microsoft.com/pricing/details/bandwidth/)的一些資訊：

+ Microsoft 不會針對 Azure 上的任何服務或 Azure 認知搜尋的任何輸出資料收取任何輸入資料的費用。
+ 在 multiservice 解決方案中，如果所有服務都位於相同的區域中，則不會對網路上的資料收費。

如果服務位於不同的區域，則會收取輸出資料的費用。 這些費用實際上不是您 Azure 認知搜尋帳單的一部分。 這裡提及的是，因為如果您使用資料或 AI 擴充的索引子來從不同區域提取資料，您將會看到成本反映在整體帳單中。

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

SU 是服務*所用**複本*和資料分割的乘積： ** (R x P = SU) **。

每個服務都以一個 SU (一個複本乘以一個分割區) 作為最小值。 任何服務的最大值為 36 su。 您可以透過多種方式來達到此上限：6個分割區 x 6 個複本，或3個分割區 x 12 個複本（例如）。 使用的容量通常會低於總容量 (例如，3個複本、3個分割區服務，以9個 SUs) 計費。 請參閱 [分割區和複本組合](search-capacity-planning.md#chart) 圖表，以取得有效的組合。

每個 SU 的計費費率為每小時。 每一層都有逐漸較高的速率。 較高的階層具有較大和 speedier 的資料分割，這對該層的整體每小時費率都有貢獻。 您可以在 [ [定價詳細資料](https://azure.microsoft.com/pricing/details/search/) ] 頁面上查看每一層的費率。

大多數客戶只能線上提供總容量的一部分，其餘部分則保留。 針對計費，您上線的資料分割和複本數目（由 SU 公式計算）會決定您以小時為單位的費用。

## <a name="how-to-manage-costs"></a>如何管理成本

下列建議可協助您降低成本，或更有效地管理成本：

+ 在相同的區域中建立所有資源，或盡可能減少最少的區域，以最小化或消除頻寬費用。

+ 將所有服務合併成一個資源群組，例如 Azure 認知搜尋、認知服務，以及解決方案中使用的任何其他 Azure 服務。 在 Azure 入口網站中，尋找資源群組，並使用 **成本管理** 命令，深入瞭解實際和預估的費用。

+ 針對您的前端應用程式考慮 Azure Web 應用程式，讓要求和回應保持在資料中心界限內。

+ 擴大大量資源的作業（例如編制索引），然後針對一般查詢工作負載重新調整。 從 Azure 認知搜尋的最小設定開始， (由一個磁碟分割和一個複本) 組成的 SU，然後監視使用者活動，以找出表示需要更多容量的使用模式。 如果有可預測的模式，您可以將調整與活動同步處理 (您必須撰寫程式碼，才能將此) 自動化。

此外，請造訪 [帳單和成本管理](../cost-management-billing/manage/getting-started.md) ，以取得與消費相關的內建工具和功能。

不可能暫時關閉搜尋服務。 專用資源一律可運作，並配置給您的服務存留期專屬使用。 刪除服務是永久性的，也會刪除其相關聯的資料。

就服務本身而言，降低帳單的唯一方法是將複本和資料分割縮減為仍提供可接受效能和 [SLA 合規性](https://azure.microsoft.com/support/legal/sla/search/v1_0/)的層級，或在較低層建立服務， (S1 每小時費率低於 S2 或 S3 費率) 。 如果您在負載預測的下半部布建服務，則在超出服務時，您可以建立第二個較大的分層服務，在第二個服務上重建索引，然後刪除第一個服務。

## <a name="how-to-evaluate-capacity-requirements"></a>如何評估容量需求

在 Azure 認知搜尋中，容量會結構*replicas*化為複本*和資料*分割。

+ 複本是搜尋服務的實例。 每個複本都會裝載一個負載平衡的索引複本。 例如，具有六個複本的服務會在服務中載入每個索引的六個複本。

+ 分割區會儲存索引並自動分割可搜尋的資料。 兩個數據分割會將索引分割成半個，三個分割區分成三個，依此類推。 就容量而言，資料 *分割大小* 是各層之間的主要區別功能。

> [!NOTE]
> 所有標準和儲存體優化層都支援 [彈性的複本和](search-capacity-planning.md#chart) 資料分割組合，讓您可以藉由變更餘額來 [優化系統的速度或儲存體](search-performance-optimization.md) 。 基本層最多可為高可用性提供三個複本，但只有一個資料分割。 免費層不提供專屬資源：計算資源是由多個訂閱者所共用。

### <a name="evaluating-capacity"></a>評估容量

開始執行服務的容量和成本。 階層會限制兩個層級：儲存體和資源。 您應該考慮這兩者的原因，因為您最先達到的限制是有效的限制。

商務需求通常會規定您需要的索引數目。 例如，您可能需要大型檔儲存機制的全域索引。 或者，您可能需要多個根據區域、應用程式或業務小的索引。

若要判斷索引的大小，您必須[建置一個](search-what-is-an-index.md)索引。 其大小會以匯入的資料和索引設定為基礎，例如您是否啟用建議工具、篩選和排序。

針對全文檢索搜尋，主要資料結構是 [反向索引](https://en.wikipedia.org/wiki/Inverted_index) 結構，其具有與來源資料不同的特性。 對於反向索引，大小和複雜度是由內容所決定，而不一定是您饋送至其中的資料量。 具有高冗余性的大型資料來源可能會產生比包含高度變數內容的較小資料集更小的索引。 因此很少可以根據原始資料集的大小推斷索引大小。

> [!NOTE] 
> 雖然預估索引和儲存空間的未來需求可能會像是猜測，但還是值得一試。 如果層的容量太低，您將需要在較高的層級布建新的服務，然後 [重載您的索引](search-howto-reindex.md)。 沒有將服務從一個 SKU 就地升級到另一個 SKU。
>

### <a name="estimate-with-the-free-tier"></a>使用免費層進行評估

其中一個預估容量的方法是從免費層開始。 請記住，免費服務提供最多三個索引、50 MB 的儲存空間，以及2分鐘的索引時間。 使用這些條件約束來估計投影的索引大小可能會很困難，但這些步驟如下：

+ [建立免費服務](search-create-service-portal.md)。
+ 準備小型、具代表性的資料集。
+ [在入口網站中建立初始索引](search-get-started-portal.md) ，並記下其大小。 功能和屬性會影響儲存體。 例如，新增建議工具 (搜尋即輸入查詢) 將會增加儲存需求。 使用相同的資料集，您可能會嘗試在每個欄位上建立具有不同屬性的多個索引版本，以查看儲存需求的差異。 如需詳細資訊，請參閱 [建立基本索引中的「存放裝置含意](search-what-is-an-index.md#index-size)」。

有了粗略的估計值，您可以將兩個索引的數量加倍 (開發和生產) ，然後據以選擇您的定價層。

### <a name="estimate-with-a-billable-tier"></a>使用可計費層進行估計

專用資源可以容納較大型的取樣和處理時間，以在開發期間更實際估計索引數量、大小和查詢量。 某些客戶會直接使用計費層，然後在開發專案成熟時重新評估。

1. [查看每一層的服務限制](./search-limits-quotas-capacity.md#index-limits) ，判斷較低的層級是否可以支援您需要的索引數目。 在基本、S1 和 S2 層中，索引限制分別為15、50和200。 儲存體優化層有10個索引的限制，因為它的設計是為了支援少量的極大型索引。

1. [在可計費的定價層建立服務](search-create-service-portal.md)：

    + 如果您不確定預計的負載，請在 [基本] 或 [S1] 上啟動。
    + 如果您知道您將會有大規模的索引和查詢負載，請從 S2 或甚至 S3 開始。
    + 如果您要編制大量資料的索引，且查詢負載相對較低（如同內部商務應用程式），請從 L1 或 L2 的儲存體進行優化。

1. [建置初始索引](search-what-is-an-index.md)，以判斷來源資料轉譯為索引的情形。 這是唯一能預估索引大小的方式。

1. 在入口網站中[監視儲存體、服務限制、查詢量和延遲](search-monitor-usage.md)。 入口網站會顯示每秒查詢數、節流查詢和搜尋延遲。 所有這些值都可以協助您決定是否選取了正確的層級。 

索引編號和大小對您的分析同樣重要。 這是因為最大限制是透過儲存體 (分割區的充分利用) 或依資源 (索引、) 索引子等等的最大限制來達到，視何者先達到。 入口網站可協助您追蹤這兩項，在 [概觀] 頁面上並排顯示目前的使用量和最大限制。

> [!NOTE]
> 如果檔包含無關的資料，則可以擴大儲存體需求。 在理想情況下，檔只包含搜尋體驗所需的資料。 二進位資料無法搜尋，而且應該個別儲存 (可能在 Azure 資料表或 blob 儲存體) 中。 接著，應該在索引中加入欄位，以保存外部資料的 URL 參考。 如果您要在單一) 要求中大量上傳多份檔，則個別檔的大小上限為 16 MB (或較少。 如需詳細資訊，請參閱 [Azure 認知搜尋中的服務限制](search-limits-quotas-capacity.md)。
>

**查詢量的考量**

每秒查詢數 (QPS) 是效能調整期間的重要計量，但如果您在一開始就預期會有大量的查詢量，通常只會考慮階層。

標準層可以提供複本和資料分割的平衡。 您可以藉由新增負載平衡的複本或加入資料分割以進行平行處理，來增加查詢週期。 然後，您可以在布建服務之後調整效能。

如果您預期從一開始就會有高度持續的查詢磁片區，您應該考慮較高的標準層，並支援更強大的硬體。 然後，您可以讓分割區和複本離線，或甚至切換至較低層的服務（如果沒有發生這些查詢磁片區）。 如需如何計算查詢輸送量的詳細資訊，請參閱 [Azure 認知搜尋效能和優化](search-performance-optimization.md)。

儲存體優化層適用于大型資料工作負載，當查詢延遲需求較不重要時，支援更多的整體可用索引儲存體。 您仍應使用額外的複本來進行負載平衡，並使用額外的資料分割來進行平行處理。 然後，您可以在布建服務之後調整效能。

**服務等級協定**

免費層和預覽功能不提供 [服務等級協定 (sla) ](https://azure.microsoft.com/support/legal/sla/search/v1_0/)。 在所有可計費層中，SLA 會在您為您的服務佈建足夠的備援性時生效。 您需要有兩個以上的複本，查詢 (讀取) Sla。 您需要有三個以上的複本，以便查詢和編制索引 (讀寫) Sla。 磁碟分割數目不會影響 Sla。

## <a name="tips-for-tier-evaluation"></a>定價層評估秘訣

+ 允許計量以查詢為依據，並收集在上班時間 (查詢的使用模式資料，在離峰時段編制索引) 。 使用此資料來通知服務布建決策。 雖然不是每小時或每日的頻率，您可以動態調整分割區和資源，以配合查詢磁片區中的規劃變更。 如果層級保有足夠的時間足以保證採取動作，您也可以容納未計畫但持續的變更。

+ 請記住，在布建下的唯一缺點是，如果實際需求大於您的預測，您可能必須卸載服務。 為了避免服務中斷，您會在較高的層級建立新的服務，並並存執行，直到所有應用程式和要求都以新端點為目標為止。

## <a name="next-steps"></a>後續步驟

從免費層開始，然後使用您的資料子集來建立初始索引，以瞭解其特性。 Azure 認知搜尋中的資料結構是反向索引結構。 反向索引的大小和複雜性取決於內容。 請記住，重複性高的內容所產生的索引，往往會比差異性高的內容所產生的索引小。 因此，內容特性（而不是資料集大小）會決定索引儲存體需求。

當您有索引大小的初始估計值之後，請在本文討論的其中一個層級上布建可 [計費服務](search-create-service-portal.md) ：基本、標準或儲存體優化。 放寬對資料大小調整的任何人為限制，並 [重建您的索引](search-howto-reindex.md) ，以包含您想要搜尋的所有資料。

視需要[配置資料分割和複本](search-capacity-planning.md)，以取得所需的效能和規模。

如果效能和容量都沒問題，您就完成了。 否則，請在更符合需求的不同定價層重新建立搜尋服務。

> [!NOTE]
> 如果您有任何問題，請張貼至 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) 或 [聯絡 Azure 支援](https://azure.microsoft.com/support/options/)。