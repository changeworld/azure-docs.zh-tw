---
title: Azure Blob 儲存體經常性存取層、非經常性存取層和封存的存取層
description: 閱讀 Azure Blob 儲存體的經常性存取、非經常性存取層和封存存取層。 查看支援階層處理的儲存體帳戶。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 67534e70904c70f7bf9dda44502e723916bdce93
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98928804"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Azure Blob 儲存體經常性存取層、非經常性存取層和封存的存取層

Azure 儲存體提供不同的存取層，可讓您以最符合成本效益的方式儲存 blob 物件資料。 可用的存取層包括：

- 用於儲存經常存取之資料的 **熱** 優化。
- 針對儲存不常存取且至少儲存30天的資料 **進行非經常性** 存取優化。
- 封存 **-針對** 儲存很少存取且至少儲存180天（具有彈性延遲需求）的資料（以時數為單位）。

下列考量適用於不同的存取層：

- 您可以在上傳期間或之後，在 blob 上設定存取層。
- 只有經常性與非經常性可在帳戶層級設定。 封存存取層只能在 blob 層級設定。
- 非經常性存取層中的資料會有些許較低的可用性，但仍具有高耐久性、抓取延遲和輸送量特性，類似于經常性存取資料。 對於非經常性存取資料，相較于經常性存取資料，較低的可用性和更高的存取成本是可接受的低整體儲存體成本。 如需詳細資訊，請參閱[儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)。
- 封存存取層中的資料會離線儲存。 封存層提供最低的儲存成本，但也提供最高的存取成本和延遲。
- 經常性存取層和非經常性存取層都支援所有的重複選項。 封存層只支援 LRS、GRS 和 RA GRS。
- 資料儲存體限制是在帳戶層級設定，而不是針對每個存取層。 您可以選擇在一或多個階層中使用所有限制。

儲存在雲端的資料迅速成長。 若要為不斷擴充的儲存體需求來管理成本，根據存取頻率和計劃性保留期來組織您的資料，進而將成本最佳化很有幫助。 儲存在雲端的資料可依據其在其存留時間內所產生、處理及存取的方式而有所不同。 有些資料在其存留期內會積極地存取及修改。 有些資料在其存留期的早期存取頻率很高，但存取頻率隨著資料老化而大幅下滑。 在雲端中，有些資料維持閒置狀態，且在儲存之後幾乎很少存取。

每個資料存取案例都可受益于針對特定存取模式優化的不同存取層。 使用經常性存取、非經常性存取層和封存存取層，Azure Blob 儲存體可透過不同的定價模式來滿足差異化存取層的這項需求。

下列工具和用戶端程式庫全都支援 blob 層級的分層和封存儲存體。

- Azure 入口網站
- PowerShell
- Azure CLI 工具
- .NET 用戶端程式庫
- Java 用戶端程式庫
- Python 用戶端程式庫
- Node.js 用戶端程式庫

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支援階層處理的儲存體帳戶

Blob 儲存體和一般目的 v2 (GPv2) 帳戶可支援經常性存取、非經常性存取和封存之間的物件儲存體資料分層。 一般用途 v1 (GPv1) 帳戶不支援階層處理。 您可以透過 Azure 入口網站輕鬆地將現有的 GPv1 或 Blob 儲存體帳戶轉換為 GPv2 帳戶。 GPv2 提供 blob、檔案和佇列的新定價和功能。 某些功能和價格剪下只會在 GPv2 帳戶中提供。 某些工作負載的 GPv2 成本可能比 GPv1 更高。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

Blob 儲存體和 GPv2 帳戶會在帳戶層級公開 **存取層** 屬性。 這個屬性可讓您針對任何未在物件層級明確設定的 blob，指定預設的存取層。 針對已明確設定階層的物件，不會套用帳戶層。 封存層只能套用於物件層級。 您可以隨時在存取層之間切換。

使用 GPv2 而非 Blob 儲存體帳戶進行階層處理。 GPv2 支援 Blob 儲存體帳戶支援的所有功能，再加上許多其他功能。 Blob 儲存體與 GPv2 之間的價格幾乎完全相同，但某些新功能和價格切割僅適用于 GPv2 帳戶。

GPv1 與 GPv2 帳戶之間的價格結構不同，客戶在決定使用 GPv2 帳戶之前，應該仔細評估兩者。 您可以透過簡單的單鍵程序，輕鬆地將現有的 Blob 儲存體或 GPv1 帳戶轉換為 GPv2。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

## <a name="hot-access-tier"></a>經常性存取層

經常性存取層的儲存成本高於非經常性存取層和封存存取層，但其存取成本最低。 經常性存取層的使用案例範例包括：

- 使用中或預期會經常讀取和寫入的資料
- 暫存以進行處理和最終遷移至非經常性存取層的資料

## <a name="cool-access-tier"></a>非經常性存取層

相較於經常性儲存層，非經常性存取層的儲存成本較低，但存取成本較高。 這一層適用於將保留在非經常性存取層至少 30 天的資料。 非經常性存取層的使用案例範例包括：

- 短期備份和嚴重損壞修復
- 較舊的資料未頻繁使用，但在存取時預期會立即可用
- 需要以有效率的方式儲存成本的大型資料集，同時會收集更多資料，以供未來處理

## <a name="archive-access-tier"></a>封存存取層

封存存取層具有最低的儲存成本，但相較于經常性存取層和非經常性存取層，資料抓取成本較高。 資料必須保留在封存層中至少180天，或受限於提早刪除費用。 封存層中的資料可能需要數小時的時間，才能根據指定的解除凍結優先權取得。 針對小型物件，高優先順序解除凍結可能會在一小時內從封存中取出物件。 若要深入瞭解，請參閱 [解除凍結封存層的 blob 資料](storage-blob-rehydration.md) 。

當 blob 位於封存儲存體時，blob 資料會離線，而且無法讀取或修改。 若要讀取或下載封存中的 blob，您必須先將它解除凍結到線上層。 您無法在封存儲存體中取得 blob 的快照集。 不過，blob 中繼資料會保持連線並可供使用，讓您可以列出 blob、其屬性、中繼資料和 blob 索引標記。 不允許在封存期間設定或修改 blob 中繼資料。 不過，您可以設定和修改 blob 索引標記。 對於封存中的 blob，唯一有效的作業 [是取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)、 [取得 blob 中繼資料](/rest/api/storageservices/get-blob-metadata)、 [設定 Blob 標記](/rest/api/storageservices/set-blob-tags)、 [取得 blob 標記](/rest/api/storageservices/get-blob-tags)、 [依標記尋找 blob](/rest/api/storageservices/find-blobs-by-tags)、 [列出](/rest/api/storageservices/list-blobs)blob、 [設定 blob 層](/rest/api/storageservices/set-blob-tier)、 [複製 blob](/rest/api/storageservices/copy-blob)，以及 [刪除 blob](/rest/api/storageservices/delete-blob)。

封存存取層的使用案例範例包括：

- 長期備份、次要備份和封存資料集
- 原始 (原始) 資料必須保留，即使已處理成最終可用形式
- 需要儲存一段時間且幾乎不會被存取的合規性與封存資料

> [!NOTE]
> ZRS、GZRS 或 RA GZRS 帳戶不支援封存層。 如果儲存體帳戶包含封存層中的 blob，則不支援從 LRS 遷移至 GRS。

## <a name="account-level-tiering"></a>帳戶層級的分層

這三個存取層中的 blob 可以並存于同一個帳戶中。 任何未明確指派層的 blob 都會從帳戶存取層設定推斷階層。 如果存取層來自帳戶，您會看到 [ **存取層推斷** 的 blob] 屬性設定為 [true]，而且 **存取層** blob 屬性符合帳戶層。 在 Azure 入口網站中，會以 blob 存取層顯示 [ _存取層推斷_ ] 屬性，並將它視為經常性 **(推斷)** 或 [非經常性 **(推斷])**。

變更帳戶存取層會套用到儲存在未設定明確層的帳戶中的所有 _存取層推斷_ 物件。 如果您將帳戶層從經常性存取切換至非經常性存取層，您必須針對 GPv2 帳戶中沒有設定層的所有 blob，支付每個 10000) 的寫入作業 (費用。 Blob 儲存體帳戶中的這項變更不會產生任何費用。 如果您在 Blob 儲存體或 GPv2 帳戶中從非經常性存取切換至經常性存取，則會向您收取每個 10000) 的讀取作業 (，以及每 GB 的資料抓取 () 。

只有經常性存取和非經常性存取層可以設定為預設帳戶存取層。 封存只能設定於物件層級。 在 blob 上傳時，您可以將您選擇的存取層指定為經常性存取、非經常性存取層或封存（不論預設帳戶層為何）。 這項功能可讓您直接將資料寫入封存層，以在您于 blob 儲存體中建立資料時，實現節省成本。

## <a name="blob-level-tiering"></a>Blob 層級的階層處理

Blob 層級的階層處理可讓您使用 [Put blob](/rest/api/storageservices/put-blob) 或 [put 區塊清單](/rest/api/storageservices/put-block-list) 作業，將資料上傳至您所選擇的存取層，並使用 [設定 blob 層](/rest/api/storageservices/set-blob-tier) 作業或 [生命週期管理](#blob-lifecycle-management) 功能，在物件層級變更您的資料層。 您可以將資料上傳到您所需的存取層，然後在使用模式變更時，輕鬆地在經常性存取層、非經常性存取層或封存層之間變更 blob 存取層，而不必在帳戶之間移動資料。 所有層級的變更要求都會立即發生，經常性存取和非經常性存取之間的階層變更則是瞬間進行。 從封存層解除凍結 blob 可能需要數小時的時間。

最後一個 blob 層變更的時間會透過 [存取層變更時間] blob 屬性公開。 覆寫經常性存取層或非經常性存取層中的 blob 時，除非在建立時明確設定新的 blob 存取層，否則新建立的 blob 會繼承已覆寫的 blob 層。 如果 blob 在封存層中，則無法覆寫，因此在此案例中不允許上傳相同的 blob。

> [!NOTE]
> 封存儲存體與 Blob 層級階層處理只支援區塊 blob。

### <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則，可讓您用來將資料轉換到最佳存取層，並在其生命週期結束時使資料過期。 若要深入瞭解，請參閱 [自動化 Azure Blob 儲存體存取層以將成本優化](storage-lifecycle-management-concepts.md) 。

> [!NOTE]
> 儲存在區塊 blob 儲存體帳戶中的資料 (Premium 效能) 目前無法使用 [Set Blob 層](/rest/api/storageservices/set-blob-tier) 或使用 Azure Blob 儲存體生命週期管理分層至經常性存取、非經常性存取或封存。
> 若要移動資料，您必須使用 [URL API 的 Put 區塊](/rest/api/storageservices/put-block-from-url) 或支援此 API 的 AzCopy 版本，將 blob 從區塊 blob 儲存體帳戶同步複製到不同帳戶中的經常性存取層。
> **Put Block From URL** API 會同步複製伺服器上的資料，這表示只有將所有資料從原始伺服器位置移動到目標位置後，才會完成呼叫。

### <a name="blob-level-tiering-billing"></a>Blob 層級的階層處理計費

當 blob 在各層之間上傳或移動時，會在上傳或階層變更時以相對的費率收費。

當 Blob 移至存取頻率較低的階層 (經常性存取 -> 非經常性存取、經常性存取 -> 封存，或非經常性存取 -> 封存) 時，此作業會在寫入目的地階層時計費，且適用目的地階層的寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用。

將 blob 移至較高的層級時 (封存->非經常性存取、封存 >經常性存取或 >非經常性存取) 時，此作業會以從來源層讀取的計費方式計費，其中每個 10000 (的讀取作業) 和資料抓取 (每 GB) 收取的來源層費用。 移出非經常性存取層或封存層的任何 blob[提早刪除](#cool-and-archive-early-deletion)費用也適用。 [從封存層解除凍結資料](storage-blob-rehydration.md) 需要一些時間，而且資料將會收取封存價格，直到資料在線上還原且 blob 層變更為經常性存取或非經常性存取為止。

下表摘要說明層級變更的計費方式：

| | **(作業寫入費用 + 存取)** | **讀取 (操作 + 存取) 的費用** |
| ---- | ----- | ----- |
| **Set Blob Tier** | 經常性 > 酷<br> 熱 > 封存<br> 非經常性 > 封存 | 封存-> 酷炫<br> 封存-> 熱門<br> 冷 > 經常性存取

### <a name="cool-and-archive-early-deletion"></a>非經常性存取和封存提前刪除

移入非經常性存取層的任何 blob (GPv2 帳戶僅) 受限於30天的很酷的早期刪除期間。 移入封存層的任何 blob 都受限於180天的封存提早刪除期間。 此費用按比例計算。 例如，如果將 blob 移至封存，然後在45天后刪除或移至經常性存取層，將會向您收取相當於 135 (180 減去 45) 天將 blob 儲存在封存中的提早刪除費用。

在非經常性存取層和封存層之間移動時，有一些詳細資料：

1. 如果根據儲存體帳戶的預設存取層將 blob 推斷為非經常性存取層，且 blob 移至封存，則不會提早刪除費用。
1. 如果 blob 明確地移至非經常性存取層，然後移至封存，則會收取提早刪除費用。

如果沒有存取層變更，請使用 [ **上次修改** blob] 屬性來計算提早刪除時間。 否則，請在存取層上次修改為非經常性或封存的情況下使用，方法是查看 blob 屬性： **存取層-變更時間**。 如需 Blob 屬性的詳細資訊，請參閱[取得 Blob 屬性](/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比較區塊 blob 儲存體選項

下表說明 premium 效能區塊 blob 儲存體和經常性存取、非經常性存取層和封存存取層的比較。

|                                           | **Premium 效能**   | **經常性存取層** | **非經常性存取層**       | **封存層**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | 離線           |
| **可用性** <br> **(RA-GRS 讀取)**  | N/A                       | 99.99%       | 99.9%               | 離線           |
| **使用費用**                         | 儲存體成本較高、存取權和交易成本較高 | 儲存成本較高，存取和交易成本較低 | 儲存成本較低，存取和交易成本較高 | 儲存成本最低，存取和交易成本最高 |
| **最小儲存持續時間**              | N/A                       | N/A          | 30天<sup>1</sup> | 180 天
| **延遲** <br> **(距第一位元組時間)** | 一位數毫秒 | 毫秒 | 毫秒        | 小時<sup>2</sup> |

在 GPv2 帳戶的非經常性存取層中，有<sup>1</sup>個物件的保留持續時間至少為30天。 Blob 儲存體帳戶不會有非經常性存取層的最小保留期限。

<sup>2</sup> 封存儲存體目前支援兩種解除凍結優先順序（高和標準），以提供不同的抓取延遲和成本。 如需詳細資訊，請參閱 [解除凍結封存層的 blob 資料](storage-blob-rehydration.md)。

> [!NOTE]
> Blob 儲存體帳戶支援與一般用途 v2 儲存體帳戶相同的效能和擴充性目標。 如需詳細資訊，請參閱 [Blob 儲存體的擴充性和效能目標](scalability-targets.md)。

## <a name="pricing-and-billing"></a>價格和計費

所有儲存體帳戶會根據每個 blob 的層級，使用區塊 blob 儲存體的定價模型。 請注意下列計費考量：

- **儲存體成本**：除了儲存的資料量以外，儲存資料的成本會根據存取層而有所不同。 每 GB 的成本會隨著儲存層存取頻率降低而減少。
- **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 針對非經常性存取層和封存存取層中的資料，您需支付讀取的每 gb 資料存取費用。
- **交易成本**：當階層變低時，所有階層都有每筆交易費用。
- **異地複寫資料傳輸成本**：此費用只適用于已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。
- **輸出資料傳輸成本**︰輸出資料傳輸 (從 Azure 區域傳出的資料) 會產生每 GB 頻寬使用量費用，與一般用途的儲存體帳戶一致。
- **變更存取層**：變更帳戶存取層會導致所有未設定明確層的 blob，產生層級變更費用。 如需變更單一 blob 存取層的詳細資訊，請參閱 [blob 層級](#blob-level-tiering-billing)的階層處理計費。

    在啟用版本控制時變更 blob 的存取層，或如果 blob 有快照集，可能會產生額外的費用。 如需啟用版本設定之 blob 的相關資訊，請參閱 blob 版本設定檔中的 [定價和帳單](versioning-overview.md#pricing-and-billing) 。 如需具有快照集之 blob 的相關資訊，請參閱 blob 快照集檔中的 [定價和帳單](snapshots-overview.md#pricing-and-billing) 。

> [!NOTE]
> 如需區塊 blob 價格的詳細資訊，請參閱 [區塊 blob 定價](https://azure.microsoft.com/pricing/details/storage/blobs/)。 如需輸出資料傳輸費用的詳細資訊，請參閱 [頻寬定價詳細](https://azure.microsoft.com/pricing/details/bandwidth/) 資料頁面。

## <a name="availability"></a>可用性

不同的存取層以及 blob 層級的階層處理，可在選取的區域中使用。 如需完整清單，請參閱 [Azure 依區域提供的產品](https://azure.microsoft.com/global-infrastructure/services/?products=storage)。

## <a name="next-steps"></a>後續步驟

瞭解如何跨存取層管理 blob 和帳戶。

- [如何管理 Azure 儲存體帳戶中的 blob 層](manage-access-tier.md)
- [如何管理 Azure 儲存體帳戶的預設帳戶存取層](../common/manage-account-default-access-tier.md)
- [藉由自動化 Azure Blob 儲存體存取層來將成本優化](storage-lifecycle-management-concepts.md)
