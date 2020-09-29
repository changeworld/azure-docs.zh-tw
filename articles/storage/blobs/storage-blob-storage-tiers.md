---
title: 適用于 blob 的經常性存取、非經常性存取層和封存存取層-Azure 儲存體
description: 瞭解 Azure Blob 儲存體的經常性存取、非經常性存取層和封存存取層。 查看支援階層處理的儲存體帳戶。 比較區塊 blob 儲存體選項。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/28/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 569e785cd8fc3ec4bbf9960cef63258e83496847
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460725"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層

Azure 儲存體提供不同的存取層，可讓您以最符合成本效益的方式儲存 Blob 物件資料。 可用的存取層包括：

- 用於儲存經常存取之資料的**熱**優化。
- 針對儲存不常存取且至少儲存30天的資料**進行非經常性**存取優化。
- 封存 **-已**優化，可用於儲存很少存取且至少儲存180天的資料，而且有彈性的延遲需求， (在時數) 的順序。

下列考量適用於不同的存取層：

- 只有經常性與非經常性可在帳戶層級設定。 封存存取層無法在帳戶層級使用。
- 經常性存取、非經常性存取與封存層可在上傳期間或上傳之後，於 Blob 層級設定。
- 非經常性存取層中資料可容忍稍低的可用性，但仍需要高持久性、擷取延遲與輸送量特性，類似於經常性存取資料。 為了降低儲存體成本，相較於經常性資料，非經常性存取資料稍低的可用性服務等級協定 (SLA) 與較高存取成本為可接受交易。
- 封存儲存體會在離線時儲存資料，並提供最低的儲存成本，以及最高的資料解除凍結與存取成本。

儲存在雲端的資料迅速成長。 若要為不斷擴充的儲存體需求來管理成本，根據存取頻率和計劃性保留期來組織您的資料，進而將成本最佳化很有幫助。 儲存在雲端的資料可依據其在其存留時間內所產生、處理及存取的方式而有所不同。 有些資料在其存留期內會積極地存取及修改。 有些資料在其存留期的早期存取頻率很高，但存取頻率隨著資料老化而大幅下滑。 在雲端中，有些資料維持閒置狀態，且在儲存之後幾乎很少存取。

每個資料存取案例都可受益于針對特定存取模式優化的不同存取層。 使用經常性存取、非經常性存取層和封存存取層，Azure Blob 儲存體可解決使用不同定價模式的不同存取層需要的這項需求。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支援階層處理的儲存體帳戶

經常性存取、非經常性存取和封存之間的物件儲存體資料階層處理只支援 Blob 儲存體和一般用途 v2 (GPv2) 帳戶。 一般用途 v1 (GPv1) 帳戶不支援階層處理。 客戶可以透過 Azure 入口網站，輕鬆地將現有的 GPv1 或 Blob 儲存體帳戶轉換為 GPv2 帳戶。 GPv2 提供 blob、檔案和佇列的新定價和功能。 部分功能和價格剪下只會在 GPv2 帳戶中提供。 全面檢查定價之後，請評估使用 GPv2 帳戶。 某些工作負載的 GPv2 成本可能比 GPv1 更高。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

Blob 儲存體和 GPv2 帳戶會在帳戶層級公開 **存取層** 屬性。 這個屬性可讓您針對任何未在物件層級明確設定的 blob，指定預設的存取層。 對於在物件層級設定階層的物件，帳戶層將不適用。 封存層只能套用於物件層級。 您可以隨時在這些存取層之間切換。

## <a name="hot-access-tier"></a>經常性存取層

經常性存取層的儲存成本高於非經常性存取層和封存存取層，但其存取成本最低。 經常性存取層的使用案例範例包括：

- 處於活躍使用狀態或預期會頻繁存取 (讀取和寫入) 的資料。
- 為了進行處理並最終移轉至非經常性存取層而暫存的資料。

## <a name="cool-access-tier"></a>非經常性存取層

相較於經常性儲存層，非經常性存取層的儲存成本較低，但存取成本較高。 這一層適用於將保留在非經常性存取層至少 30 天的資料。 非經常性存取層的使用案例範例包括：

- 短期備份和災害復原資料集。
- 不再經常檢視，但存取時應立即可用的較舊媒體內容。
- 當收集較多資料以供未來處理時，需要以符合成本效益方式預存的大型資料集。 (例如**，科學資料、來自製造工廠的原始遙測資料等長期儲存)

## <a name="archive-access-tier"></a>封存存取層

封存存取層的儲存成本最低。 但相較于經常性存取層和非經常性存取層，其資料抓取成本較高。 資料必須保留在封存層中至少180天，或受限於提早刪除費用。 封存層中的資料可能需要數小時的時間，才能根據解除凍結的優先順序取得。 針對小型物件，高優先順序解除凍結可能會在1小時內從封存中取出物件。 若要深入瞭解，請參閱 [解除凍結封存層的 blob 資料](storage-blob-rehydration.md) 。

當 blob 位於封存儲存體時，blob 資料會離線，而且無法讀取、覆寫或修改。 若要讀取或下載封存中的 blob，您必須先將它解除凍結到線上層。 您無法在封存儲存體中取得 blob 的快照集。 不過，blob 中繼資料會保持連線並可供使用，讓您可以列出 blob、其屬性、中繼資料和 blob 索引標記。 不允許在封存期間設定或修改 blob 中繼資料;不過，您可以設定和修改 blob 索引標記。 對於封存中的 blob，唯一有效的作業是 GetBlobProperties、GetBlobMetadata、SetBlobTags、GetBlobTags、FindBlobsByTags、ListBlobs、SetBlobTier、CopyBlob 和 DeleteBlob。

封存存取層的使用案例範例包括：

- 長期備份、次要備份和封存資料集
- 即使已處理成最終可用格式，但還是需要保存的原始資料。
- 需要儲存一段時間且幾乎不曾存取的相容性和封存資料。

> [!NOTE]
> ZRS、GZRS 或 RA GZRS 帳戶目前不支援封存層。

## <a name="account-level-tiering"></a>帳戶層級的分層

這三個存取層中的 blob 可以並存于同一個帳戶中。 任何未明確指派層的 blob 都會從帳戶存取層設定推斷階層。 如果存取層來自帳戶，您會看到 [ **存取層推斷** 的 blob] 屬性設定為 [true]，而且 **存取層** blob 屬性符合帳戶層。 在 Azure 入口網站中，會以 blob 存取層顯示 [ _存取層推斷_ ] 屬性，並將它視為經常性 ** (推斷) ** 或 [非經常性 ** (推斷]) **。

變更帳戶存取層會套用到儲存在未設定明確層的帳戶中的所有 _存取層推斷_ 物件。 如果您將帳戶層從經常性存取切換至非經常性存取層，您必須針對 GPv2 帳戶中沒有設定層的所有 blob，支付每個 10000) 的寫入作業 (費用。 Blob 儲存體帳戶中的這項變更不會產生任何費用。 如果您在 Blob 儲存體或 GPv2 帳戶中從非經常性存取切換至經常性存取，則會向您收取每個 10000) 的讀取作業 (，以及每 GB 的資料抓取 () 。

## <a name="blob-level-tiering"></a>Blob 層級的階層處理

Blob 層級的階層處理可讓您使用 [Put blob](/rest/api/storageservices/put-blob) 或 [put 區塊清單](/rest/api/storageservices/put-block-list) 作業，將資料上傳至您所選擇的存取層，並使用 [設定 Blob 層](/rest/api/storageservices/set-blob-tier) 作業或 [生命週期管理](#blob-lifecycle-management) 功能，在物件層級變更您的資料層。 您可以將資料上傳到您所需的存取層，然後在使用模式變更時，輕鬆地在經常性存取層、非經常性存取層或封存層之間變更 blob 存取層，而不必在帳戶之間移動資料。 所有層級的變更要求都會立即發生，經常性存取和非經常性存取之間的階層變更則是瞬間進行。 但是，從封存解除凍結 Blob 可能需要數小時的時間。

最後一個 blob 層變更的時間會透過 [存取層變更時間]**** blob 屬性公開。 覆寫經常性存取層或非經常性存取層中的 blob 時，除非在建立時明確設定新的 blob 存取層，否則新建立的 blob 會繼承已覆寫的 blob 層。 如果 blob 在封存層中，則無法覆寫，因此在此案例中不允許上傳相同的 blob。 

> [!NOTE]
> 封存儲存體與 Blob 層級階層處理只支援區塊 blob。

### <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則，可讓您用來將資料轉換到最佳存取層，並在其生命週期結束時使資料過期。 若要深入了解，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。  

> [!NOTE]
> 儲存在區塊 blob 儲存體帳戶中的資料 (Premium 效能) 目前無法使用 [Set Blob 層](/rest/api/storageservices/set-blob-tier) 或使用 Azure Blob 儲存體生命週期管理分層至經常性存取、非經常性存取或封存。
> 若要移動資料，您必須使用 [URL API 的 Put 區塊](/rest/api/storageservices/put-block-from-url) 或支援此 API 的 AzCopy 版本，將 blob 從區塊 blob 儲存體帳戶同步複製到不同帳戶中的經常性存取層。
> *Put Block From URL* API 會同步複製伺服器上的資料，這表示只有將所有資料從原始伺服器位置移動到目標位置後，才會完成呼叫。

### <a name="blob-level-tiering-billing"></a>Blob 層級的階層處理計費

將 blob 上傳或移至經常性存取層、非經常性存取層或封存層時，會在階層變更時以相對的費率收費。

當 Blob 移至存取頻率較低的階層 (經常性存取 -> 非經常性存取、經常性存取 -> 封存，或非經常性存取 -> 封存) 時，此作業會在寫入目的地階層時計費，且適用目的地階層的寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用。

將 blob 移至較高的層級時 (封存->非經常性存取、封存 >經常性存取或 >非經常性存取) 時，此作業會以從來源層讀取的計費方式計費，其中每個 10000 (的讀取作業) 和資料抓取 (每 GB) 收取的來源層費用。 可能也適用任何移出非經常性存取或封存層之 blob 的提早刪除費用。 [從封存解除凍結資料](storage-blob-rehydration.md) 需要一些時間，而且資料將會收取封存價格，直到資料在線上還原且 blob 層變更為經常性存取或非經常性存取為止。 下表摘要說明階層變更的計費方式：

| | **寫入費用 (作業 + 存取)** | **讀取費用 (作業 + 存取)**
| ---- | ----- | ----- |
| **SetBlobTier 方向** | 經常性 >很酷，<br> 熱 >封存、<br> 非經常性 >封存 | 封存->很酷<br> 封存->經常性存取，<br> 冷 >經常性存取

### <a name="cool-and-archive-early-deletion"></a>非經常性存取和封存提前刪除

移入非經常性存取層的任何 blob (GPv2 帳戶僅) 受限於30天的很酷的早期刪除期間。 移入封存層的任何 blob 都受限於180天的封存提早刪除期間。 此費用按比例計算。 例如，如果將 blob 移至封存，然後在45天后刪除或移至經常性存取層，將會向您收取相當於 135 (180 減去 45) 天將 blob 儲存在封存中的提早刪除費用。

如果沒有存取層變更，您可以使用 [ **上次修改時間**] blob 屬性來計算早期刪除。 否則，您可以在存取層上次修改為非經常性或封存的時候，透過查看 blob 屬性： **存取層變更時間**來使用。 如需 Blob 屬性的詳細資訊，請參閱[取得 Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比較區塊 blob 儲存體選項

下表說明 premium 效能區塊 blob 儲存體和經常性存取、非經常性存取層和封存存取層的比較。

|                                           | **Premium 效能**   | **經常性存取層** | **非經常性存取層**       | **封存層**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | 離線           |
| **可用性** <br> **(RA-GRS 讀取)**  | N/A                       | 99.99%       | 99.9%               | 離線           |
| **使用費用**                         | 儲存體成本較高、存取權和交易成本較高 | 儲存成本較高，存取和交易成本較低 | 儲存成本較低，存取和交易成本較高 | 儲存成本最低，存取和交易成本最高 |
| **最小物件大小**                   | N/A                       | N/A          | N/A                 | N/A               |
| **最小儲存持續時間**              | N/A                       | N/A          | 30天<sup>1</sup> | 180 天
| **延遲** <br> **(距第一位元組時間)** | 一位數毫秒 | 毫秒 | 毫秒        | 小時<sup>2</sup> |

在 GPv2 帳戶的非經常性存取層中，有<sup>1</sup>個物件的保留持續時間至少為30天。 Blob 儲存體帳戶不會有非經常性存取層的最小保留期限。

<sup>2</sup> 封存儲存體目前支援2個解除凍結優先順序（高和標準），以提供不同的抓取延遲。 如需詳細資訊，請參閱 [解除凍結封存層的 blob 資料](storage-blob-rehydration.md)。

> [!NOTE]
> Blob 儲存體帳戶支援與一般用途 v2 儲存體帳戶相同的效能和擴充性目標。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](scalability-targets.md)。

## <a name="quickstart-scenarios"></a>快速入門案例

在本節中，會使用 Azure 入口網站和 PowerShell 來示範下列案例：

- 如何變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層。
- 如何在 GPv2 或 Blob 儲存體帳戶中變更 Blob 的存取層。

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 [所有資源]。

1. 選取您的儲存體帳戶。

1. 在 [設定] 中 **，選取 [** 設定] 以查看及變更帳戶**設定**。

1. 針對您的需求選取正確的存取層：將**存取層**設定為 [非經常性存取 **] 或 [** 經常性存取 **]。**

1. 按一下頂端的 [ **儲存** ]。

![變更 Azure 入口網站中的預設帳戶層](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
您可以使用下列 PowerShell 腳本來變更帳戶層。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶中的 blob 層級
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 [所有資源]。

1. 選取您的儲存體帳戶。

1. 選取容器，然後選取您的 Blob。

1. 在 [Blob 屬性] 中，選取 [變更階層]。

1. 選取經常性**存取、非****經常性存取層**或封存存取層。 **Cool** 如果您的 blob 目前處於封存狀態，而且您想要解除凍結到線上層，您也可以選取 [ **標準** ] 或 [ **高**] 的解除凍結優先權。

1. 在底部選取 [儲存]。

![變更 Azure 入口網站中的 blob 層](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
您可以使用下列 PowerShell 腳本來變更 blob 層。 `$rgName` 變數必須以您的資源群組名稱進行初始化。 `$accountName` 變數必須以您的儲存體帳戶名稱進行初始化。 `$containerName` 變數必須以您的容器名稱進行初始化。 `$blobName` 變數必須以您的 Blob 名稱進行初始化。 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>價格和計費

所有儲存體帳戶會根據每個 blob 的層級，使用區塊 blob 儲存體的定價模型。 請注意下列計費考量：

- **儲存體成本**：除了儲存的資料量以外，儲存資料的成本會根據存取層而有所不同。 每 GB 的成本會隨著儲存層存取頻率降低而減少。
- **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 針對非經常性存取層和封存存取層中的資料，您需支付讀取的每 gb 資料存取費用。
- **交易成本**：當階層變低時，所有階層都有每筆交易費用。
- **異地複寫資料傳輸成本**︰此費用適用於已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。
- **輸出資料傳輸成本**︰輸出資料傳輸 (從 Azure 區域傳出的資料) 會產生每 GB 頻寬使用量費用，與一般用途的儲存體帳戶一致。
- **變更存取層**：變更帳戶存取層會導致 _存取層推斷_ 的 blob （儲存在未設定明確層的帳戶中）的層級變更費用。 如需變更單一 blob 存取層的詳細資訊，請參閱 [blob 層級](#blob-level-tiering-billing)的階層處理計費。

    在啟用版本控制時變更 blob 的存取層，或如果 blob 有快照集，可能會產生額外的費用。 如需有關啟用 blob 版本設定以及明確變更 blob 層級時如何計費的詳細資訊，請參閱 blob 版本設定檔中的 [定價和帳單](versioning-overview.md#pricing-and-billing) 。 如需有關 blob 有快照集且您明確地變更 blob 層的計費方式的詳細資訊，請參閱 blob 快照集檔中的 [定價和帳單](snapshots-overview.md#pricing-and-billing) 。

> [!NOTE]
> 如需區塊 blob 價格的詳細資訊，請參閱 [Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/) 頁面。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)頁面。

## <a name="faq"></a>常見問題集

**如果我想要將我的資料進行階層處理，應使用 Blob 儲存體或 GPv2 帳戶？**

建議您 GPv2，而不要使用 Blob 儲存體帳戶進行階層處理。 GPv2 支援 Blob 儲存體帳戶支援的所有功能，以及其他許多功能。 Blob 儲存體與 GPv2 之間的價格幾乎相同，但是 GPv2 帳戶將提供一些新功能和降價。 GPv1 帳戶不支援階層處理。

GPv1 與 GPv2 帳戶之間的價格結構不同，客戶在決定使用 GPv2 帳戶之前，應該仔細評估兩者。 您可以透過簡單的單鍵程序，輕鬆地將現有的 Blob 儲存體或 GPv1 帳戶轉換為 GPv2。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

**我可以將所有三 (經常性存取、非經常性存取和封存) 存取層中的物件儲存在相同的帳戶嗎？**

是。 在帳戶層級設定的 **存取層** 屬性是預設帳戶層，適用于該帳戶中的所有物件，而不含明確的設定層。 Blob 層級的階層處理可讓您在物件層級上設定存取層，而不管帳戶上的存取層設定為何。 任何三個存取層中的 blob (經常性存取、非經常性存取層或封存) 可能存在於同一個帳戶中。

**我可以變更 Blob 或 GPv2 儲存體帳戶的預設存取層嗎？**

是，您可以藉由在儲存體帳戶上設定 **存取層** 屬性來變更預設帳戶層級。 變更帳戶層級會套用至帳戶中儲存的所有物件，而這些物件並沒有明確的層級設定 (例如，經常性 ** (推斷) ** 或未 **推斷的 () **) 。 將帳戶層從經常性存取切換至非經常性存取時，會產生所有 blob 的寫入作業 (每 10000) ，而不需要 GPv2 帳戶中的設定層，且從非經常性存取會產生每個 10000) 的讀取 (作業，以及對 Blob 儲存體和 GPv2 帳戶中所有 blob 的資料抓取 (每 GB) 費用。

**我可以將我的預設帳戶存取層設定為封存嗎？**

否。 只有經常性存取和非經常性存取層可以設定為預設帳戶存取層。 封存只能設定於物件層級。 在 blob 上傳時，您可以將您選擇的存取層指定為經常性存取、非經常性存取層或封存（不論預設帳戶層為何）。 這項功能可讓您直接將資料寫入封存層，以在您于 blob 儲存體中建立資料時，實現節省成本。

**在哪些區域中可以使用經常性存取、非經常性存取層和封存存取層？**

經常性存取和非經常性存取層以及 blob 層級的階層處理適用于所有區域。 封存儲存體一開始只在精選區域中提供。 如需完整清單，請參閱 [Azure 依區域提供的產品](https://azure.microsoft.com/regions/services/)。

**經常性存取、非經常性存取層和封存存取層支援哪些冗余選項？**

經常性存取層和非經常性存取層都支援所有的重複選項。 封存層只支援 LRS、GRS 和 RA GRS。 封存層不支援 ZRS、GZRS 和 RA GZRS。

**非經常性存取層中的 blob 與經常性存取層中的 blob 的行為有何不同？**

經常性存取層中的 blob 與 GPv1、GPv2 和 Blob 儲存體帳戶中的 blob 有相同的延遲。 非經常性存取層中的 blob 與 GPv1、GPv2 和 Blob 儲存體帳戶中的 blob 有類似的延遲 (（以毫秒為) 單位）。 封存存取層中的 blob 在 GPv1、GPv2 和 Blob 儲存體帳戶中有數小時的延遲。

非經常性存取層中的 blob 具有比經常性存取層中儲存的 blob 更低的可用性服務等級 (SLA) 。 如需詳細資訊，請參閱[儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/)。

**經常性存取、非經常性存取層和封存層之間的作業是否相同？**

經常性存取與非經常性存取之間的所有作業都完全一致。 所有有效的封存作業（包括 GetBlobProperties、GetBlobMetadata、SetBlobTags、GetBlobTags、FindBlobsByTags、ListBlobs、SetBlobTier 和 DeleteBlob）都與經常性存取和非經常性存取100% 一致。 在封存層中，無法讀取或修改 Blob 資料，直到解除凍結;封存期間僅支援 blob 中繼資料讀取作業。 不過，您可以在封存時讀取、設定或修改 blob 索引標記。

**將 blob 從封存層解除凍結至經常性存取或非經常性存取層時，如何知道解除凍結何時完成？**

在解除凍結期間，您可以使用「取得 blob 屬性」作業來輪詢 [封存 **狀態** ] 屬性，並確認階層變更已完成。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成時，會移除封存狀態屬性，而 **存取層** blob 屬性會反映新的經常性存取層或非經常性存取層。 若要深入瞭解，請參閱 [解除凍結封存層的 blob 資料](storage-blob-rehydration.md) 。

**設定 blob 的存取層之後，何時會開始以適當的費率計費？**

每個 Blob 一律根據 Blob [存取層]**** 屬性所指定的存取層計費。 當您為 blob 設定新的線上層時，[ **存取層** ] 屬性會立即反映所有轉換的新層。 不過，將 blob 從離線封存層解除凍結至經常性存取或非經常性存取層可能需要數小時的時間。 在此情況下，您會以封存費率計費，直到解除凍結完成為止，此時 [ **存取層** ] 屬性會反映新的層級。 解除凍結至線上層之後，您就會以經常性或非經常性存取費率向您收取該 blob 的費用。

**如何? 判斷當刪除或移出非經常性存取或封存層中的 blob 時，是否會產生提早刪除費用？**

在 30 天前和 180 天前遭到刪除或移出非經常性存取 (僅限 GPv2 帳戶) 或封存層的任何 Blob，分別會產生按比例計算的提早刪除費用。 您可以藉由檢查 **存取層變更時間** blob 屬性（可提供最後一層變更的戳記），判斷 blob 處於非經常性存取層或封存層的時間長度。 如果 blob 的層級從未變更，您可以檢查 **上次修改過** 的 blob 屬性。 如需詳細資訊，請參閱非經常性存取 [和封存提早刪除](#cool-and-archive-early-deletion)。

**哪些 Azure 工具和 Sdk 支援 blob 層級的分層和封存儲存體？**

Azure 入口網站、PowerShell 和 CLI 工具，以及 .NET、Java、Python 和 Node.js 用戶端程式庫全都支援 blob 層級的階層處理和封存儲存體。  

**我可以將多少資料儲存在經常性存取層、非經常性存取層和封存層？**

系統會在帳戶層級（而不是每個存取層）設定資料儲存體和其他限制。 您可以選擇在一或多個階層中使用所有限制。 如需詳細資訊，請參閱 [標準儲存體帳戶的擴充性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

在 GPv2 和 Blob 儲存體帳戶中評估經常性存取、非經常性存取和封存

- [依區域檢查經常性存取、非經常性存取和封存的可用性](https://azure.microsoft.com/regions/#services)
- [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [判斷 premium 效能是否能讓您的應用程式受益](storage-blob-performance-tiers.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
