---
title: 適用于 blob 的經常性、非經常性和封存存取層-Azure 儲存體
description: 適用于 Azure 儲存體帳戶的經常性、非經常性和封存存取層。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: f2f6be1022a7100a23f49534f2c18fc951d56284
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389097"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob 儲存體：經常性、非經常性和封存存取層

Azure 儲存體提供不同的存取層，可讓您以最符合成本效益的方式儲存 blob 物件資料。 可用的存取層包括：

- **熱**優化，用於儲存經常存取的資料。
- 針對儲存不常存取且至少儲存30天的資料而優化。
- 封存 **-針對**儲存很少存取且至少儲存180天（有彈性的延遲需求，以小時為單位）的資料進行封存。

下列考慮適用于不同的存取層：

- 只有經常性存取和非經常性存取層可以在帳戶層級設定。 封存存取層無法在帳戶層級使用。
- 經常性存取、非經常性存取和封存層可以在上傳期間或上傳之後，于 blob 層級設定。
- 非經常性存取層中的資料可容忍稍微較低的可用性，但仍需要高持久性、抓取延遲和輸送量特性，類似于熱資料。 對於非經常性存取資料，相較于經常性資料，可用性服務等級協定（SLA）和更高的存取成本是可接受的取捨，以降低儲存成本。
- 封存儲存體會在離線時儲存資料，並提供最低的儲存成本，以及最高的資料解除凍結和存取成本。

儲存在雲端的資料迅速成長。 若要為不斷擴充的儲存體需求來管理成本，根據存取頻率和計劃性保留期來組織您的資料，進而將成本最佳化很有幫助。 儲存在雲端的資料可以根據其在其存留期內的產生、處理和存取方式而有所不同。 有些資料在其存留期內會積極地存取及修改。 有些資料在其存留期的早期存取頻率很高，但存取頻率隨著資料老化而大幅下滑。 有些資料在雲端維持閒置狀態，而且在儲存之後，幾乎不常存取。

這些資料存取案例中的每個都是從針對特定存取模式優化的不同存取層來獲益。 使用經常性、非經常性和封存存取層時，Azure Blob 儲存體可透過不同的計價模式，滿足這項差異化存取層的需求。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支援階層處理的儲存體帳戶

只有在 Blob 儲存體和一般用途 v2 （GPv2）帳戶中，才支援經常性、非經常性和封存之間的物件儲存體資料分層。 一般用途 v1 （GPv1）帳戶不支援分層。 客戶可以透過 Azure 入口網站，輕鬆地將其現有的 GPv1 或 Blob 儲存體帳戶轉換為 GPv2 帳戶。 GPv2 提供 blob、檔案和佇列的新定價和功能。 某些功能和價格削減僅在 GPv2 帳戶中提供。 全面審查定價之後，請評估使用 GPv2 帳戶。 在 GPv2 上的某些工作負載可能會比 GPv1 更高。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

Blob 儲存體和 GPv2 帳戶會在帳戶層級公開 [**存取層**] 屬性。 這個屬性可讓您為任何未在物件層級設定其明確設定的 blob，指定預設存取層。 對於已在物件層級設定階層的物件，將不會套用帳戶層。 封存層只能套用於物件層級。 您可以隨時在這些存取層之間切換。

## <a name="hot-access-tier"></a>經常性存取層

經常性存取層的儲存成本高於非經常性和封存層，但存取成本最低。 熱存取層的使用案例範例包括：

- 經常使用或預期存取（讀取和寫入）的資料。
- 針對處理和最終遷移至非經常性存取層而暫存的資料。

## <a name="cool-access-tier"></a>非經常性存取層

相較于經常性儲存體，非經常性存取層的儲存成本較低，且存取成本較高。 這一層適用於將保留在非經常性存取層至少 30 天的資料。 非經常性存取層的使用案例範例包括：

- 短期備份和災害復原資料集。
- 不再經常檢視，但存取時應立即可用的較舊媒體內容。
- 當收集較多資料以供未來處理時，需要以符合成本效益方式預存的大型資料集。 (例如，科學資料、來自製造工廠的原始遙測資料等長期儲存)

## <a name="archive-access-tier"></a>封存存取層

封存存取層具有最低的儲存成本。 但相較于經常性存取和非經常性存取層，其資料抓取成本會更高。 封存層中的資料可能需要幾個小時的時間才能取得。 資料必須保留在封存層中至少180天，或受限於提早刪除費用。

當 blob 位於封存儲存體時，blob 資料會離線，而且無法讀取、覆寫或修改。 若要讀取或下載封存中的 blob，您必須先將它解除凍結到線上層。 您無法在封存儲存體中拍攝 blob 的快照集。 不過，Blob 中繼資料會保持連線且可以取得，讓您可列出 Blob 和其屬性。 針對封存中的 blob，唯一有效的作業是 GetBlobProperties、GetBlobMetadata、ListBlobs、SetBlobTier、CopyBlob 和 DeleteBlob。 若要深入瞭解，請參閱[從封存層解除凍結 blob 資料](storage-blob-rehydration.md)。

封存存取層的使用案例範例包括：

- 長期備份、次要備份和封存資料集
- 即使已處理成最終可用格式，但還是需要保存的原始資料。
- 需要儲存一段時間且幾乎不曾存取的相容性和封存資料。

## <a name="account-level-tiering"></a>帳戶層級的階層處理

這三個存取層中的 blob 可以並存于相同的帳戶內。 任何沒有明確指派之階層的 blob，都會從 [帳戶存取層] 設定推斷該層。 如果存取層來自帳戶，您會看到 [推斷的**存取層**] blob 屬性設定為 "true"，而 [**存取層**] blob 屬性符合帳戶層。 在 Azure 入口網站中，[_推斷的存取層_] 屬性會顯示為 [經常性存取 **（推斷）** ] 或 [非經常性 **（推斷）** ]。

變更帳戶存取層會套用至未設定明確層集之帳戶中所儲存的所有_存取層已推斷_物件。 如果您將帳戶層從經常性存取切換至非經常性存取，則只需針對 GPv2 帳戶中沒有設定層的所有 blob，支付寫入作業（每10000個）的費用。 在 Blob 儲存體帳戶中，這項變更不會產生任何費用。 如果您在 Blob 儲存體或 GPv2 帳戶中從非經常性存取層切換至經常性存取，則會向您收取讀取作業（每10000）和資料抓取（每 GB）的費用。

## <a name="blob-level-tiering"></a>Blob 層級的階層處理

Blob 層級的階層處理可讓您使用[Put blob](/rest/api/storageservices/put-blob)或[Put 區塊清單](/rest/api/storageservices/put-block-list)作業，將資料上傳至您選擇的存取層，並使用[設定 Blob 層](/rest/api/storageservices/set-blob-tier)作業或[生命週期管理](#blob-lifecycle-management)功能，在物件層級變更您的資料層。 您可以將資料上傳到您所需的存取層，然後在使用模式變更時，輕鬆地在經常性、非經常性或封存層中變更 blob 存取層，而不必在帳戶之間移動資料。 所有層級的變更要求都會立即發生，而且經常性存取和非經常性存取層級的變更是瞬間的。 但是，從封存解除凍結 Blob 可能需要數小時的時間。

最後一個 blob 層變更的時間會透過 [存取層變更時間] blob 屬性公開。 當覆寫經常性存取或非經常性存取層中的 blob 時，新建立的 blob 會繼承已覆寫的 blob 層，除非在建立時明確設定新的 blob 存取層。 如果 blob 位於封存層中，則無法覆寫，因此在此情況下不允許上傳相同的 blob。 

> [!NOTE]
> 封存儲存體與 Blob 層級階層處理只支援區塊 blob。 您目前也無法變更具有快照集之區塊 blob 的層級。

### <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則，可讓您將資料轉換到最佳存取層，並在其生命週期結束時使資料過期。 若要深入了解，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。  

> [!NOTE]
> 儲存在區塊 blob 儲存體帳戶（Premium 效能）中的資料目前無法使用[設定 Blob 層](/rest/api/storageservices/set-blob-tier)或使用 Azure Blob 儲存體生命週期管理來分層至經常性存取、非經常性存取或封存。
> 若要移動資料，您必須使用[Put Block FROM URL API](/rest/api/storageservices/put-block-from-url)或支援此 API 的 AzCopy 版本，以同步方式將 blob 從區塊 blob 儲存體帳戶複製到不同帳戶中的經常性存取層。
> *Put Block From URL* API 會同步複製伺服器上的資料，這表示只有將所有資料從原始伺服器位置移動到目標位置後，才會完成呼叫。

### <a name="blob-level-tiering-billing"></a>Blob 層級的階層處理計費

將 blob 上傳或移至經常性存取、非經常性存取或封存層時，會在階層變更時立即依對應的費率收費。

當 Blob 移至存取頻率較低的階層 (經常性存取 -> 非經常性存取、經常性存取 -> 封存，或非經常性存取 -> 封存) 時，此作業會在寫入目的地階層時計費，且適用目的地階層的寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用。

當 blob 移至較低層（封存-> 非經常性存取、封存 > 經常性存取或 > 非經常性存取）時，作業會以從來源層讀取的方式計費，其中適用來源階層的讀取作業（每10000）和資料抓取（每 GB）費用。 可能也適用任何移出非經常性存取或封存層之 blob 的提早刪除費用。 [從封存解除凍結資料](storage-blob-rehydration.md)需要時間，而資料會以封存價格計費，直到資料在線上還原，以及 blob 層變更為經常性存取或非經常性存取為止。 下表摘要說明層變更的計費方式：

| | **寫入費用 (作業 + 存取)** | **讀取費用 (作業 + 存取)**
| ---- | ----- | ----- |
| **SetBlobTier 方向** | 經常性 > 很酷，<br> 熱 > 封存，<br> 非經常性 > 封存 | 封存-> 酷炫，<br> 封存-> 經常性存取、<br> 酷炫 > 熱

### <a name="cool-and-archive-early-deletion"></a>非經常性存取和封存提前刪除

移入非經常性存取層（僅限 GPv2 帳戶）的任何 blob，都受限於30天內的非經常性存取提早刪除期間。 移入封存層的任何 blob 都受限於180天的封存提早刪除期間。 此費用按比例計算。 例如，如果將 blob 移至封存，然後在45天后刪除或移至經常性存取層，則會向您收取較早的刪除費用，相當於在封存中儲存該 blob 的135（180減去45）天。

如果沒有存取層變更，您可以使用 [**上次修改時間**] blob 屬性來計算早期刪除。 否則，您可以藉由查看 blob 屬性：**存取層-變更時間**來使用上次修改存取層為非經常性或封存的時間。 如需 Blob 屬性的詳細資訊，請參閱[取得 Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比較區塊 blob 儲存體選項

下表顯示高階效能區塊 blob 儲存體和經常性存取、非經常性存取和封存存取層的比較。

|                                           | **Premium 效能**   | **經常性存取層** | **非經常性存取層**       | **封存層**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | 離線           |
| **可用性** <br> **(RA-GRS 讀取)**  | N/A                       | 99.99%       | 99.9%               | 離線           |
| **使用費用**                         | 儲存成本較高，存取和交易成本較低 | 儲存成本較高，存取和交易成本較低 | 儲存成本較低，存取和交易成本較高 | 儲存成本最低，存取和交易成本最高 |
| **最小物件大小**                   | N/A                       | N/A          | N/A                 | N/A               |
| **最小儲存持續時間**              | N/A                       | N/A          | 30天<sup>1</sup> | 180 天
| **延遲** <br> **(距第一位元組時間)** | 單一位數的毫秒數 | 毫秒 | 毫秒        | 時數<sup>2</sup> |

在 GPv2 帳戶的非經常性存取層中，有<sup>1</sup>個物件的保留期間至少為30天。 Blob 儲存體帳戶不會有非經常性存取層的最小保留期間。

<sup>2</sup>封存儲存體目前支援2個解除凍結優先順序（高和標準），可提供不同的抓取延遲。 如需詳細資訊，請參閱[從封存層解除凍結 blob 資料](storage-blob-rehydration.md)。

> [!NOTE]
> Blob 儲存體帳戶支援與一般用途 v2 儲存體帳戶相同的效能和擴充性目標。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](scalability-targets.md)。

## <a name="quickstart-scenarios"></a>快速入門案例

在本節中，會使用 Azure 入口網站和 powershell 來示範下列案例：

- 如何變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層。
- 如何在 GPv2 或 Blob 儲存體帳戶中變更 Blob 的存取層。

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 **所有資源**。

1. 選取您的儲存體帳戶。

1. 在 [設定] 中 **，選取 [** **設定**] 以查看並變更帳戶設定。

1. 依據您的需求選取正確的存取層：將**存取層**設定為 [非經常性 **] 或 [** **熱**]。

1. 按一下頂端的 [儲存] 。

![變更儲存體帳戶層](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
下列 PowerShell 腳本可以用來變更帳戶層。 `$rgName` 變數必須使用您的資源組名進行初始化。 `$accountName` 變數必須使用您的儲存體帳戶名稱進行初始化。 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶中的 blob 層
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 入口網站中，搜尋並選取 **所有資源**。

1. 選取您的儲存體帳戶。

1. 選取您的容器，然後選取您的 blob。

1. 在 [ **Blob 屬性**] 中，選取 [**變更層**]。

1. 選取 [**經常性**存取 **]、** [非經常性存取] 或 [封存] 存取層。 如果您的 blob 目前在封存中，而您想要解除凍結至線上層，您也可以選取 [**標準**] 或 [**高**] 的解除凍結優先權。

1. 選取底部的 [**儲存**]。

![變更儲存體帳戶層](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
下列 PowerShell 腳本可以用來變更 blob 層。 `$rgName` 變數必須使用您的資源組名進行初始化。 `$accountName` 變數必須使用您的儲存體帳戶名稱進行初始化。 `$containerName` 變數必須使用您的容器名稱進行初始化。 `$blobName` 變數必須使用您的 blob 名稱進行初始化。 
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
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```
---

## <a name="pricing-and-billing"></a>價格和計費

所有儲存體帳戶都會根據每個 blob 的層級，使用區塊 blob 儲存體的定價模型。 請注意下列計費考量：

- **儲存體成本**：除了儲存的資料量以外，儲存資料的成本會因存取層而異。 每 GB 的成本會隨著儲存層存取頻率降低而減少。
- **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 對於非經常性存取和封存存取層中的資料，您需支付讀取的每 gb 資料存取費用。
- **交易成本**：隨著層級變低，所有階層都有每筆交易的費用。
- **異地複寫資料傳輸成本**︰此費用適用於已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。
- **輸出資料傳輸成本**︰輸出資料傳輸 (從 Azure 區域傳出的資料) 會產生每 GB 頻寬使用量費用，與一般用途的儲存體帳戶一致。
- **變更存取層**：變更帳戶存取層將會導致存取層的階層變更費用，而_推斷出_的 blob 儲存在未設定明確層的帳戶中。 如需變更單一 blob 之存取層的相關資訊，請參閱[blob 層級](#blob-level-tiering-billing)的階層處理計費。

> [!NOTE]
> 如需有關區塊 blob 定價的詳細資訊，請參閱[Azure 儲存體定價](https://azure.microsoft.com/pricing/details/storage/blobs/)頁面。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)頁面。

## <a name="faq"></a>常見問題集

**如果我想要將我的資料進行階層處理，應使用 Blob 儲存體或 GPv2 帳戶？**

建議您 GPv2，而不要使用 Blob 儲存體帳戶進行階層處理。 GPv2 支援 Blob 儲存體帳戶支援的所有功能，以及其他許多功能。 Blob 儲存體與 GPv2 之間的價格幾乎相同，但是 GPv2 帳戶將提供一些新功能和降價。 GPv1 帳戶不支援分層。

GPv1 與 GPv2 帳戶之間的價格結構不同，客戶在決定使用 GPv2 帳戶之前，應該仔細評估兩者。 您可以透過簡單的單鍵程序，輕鬆地將現有的 Blob 儲存體或 GPv1 帳戶轉換為 GPv2。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

**我可以在同一個帳戶中，儲存所有三個（經常性存取、非經常性存取和封存）存取層中的物件嗎？**

是的。 在帳戶層級設定的 [**存取層**] 屬性是套用至該帳戶中所有物件的預設帳戶層（沒有明確設定的層級）。 Blob 層級的階層處理可讓您在物件層級上設定存取層，而不論帳戶上設定的存取層為何。 三個存取層（經常性、非經常性或封存）中的 blob 可能存在於相同的帳戶內。

**我可以變更 Blob 或 GPv2 儲存體帳戶的預設存取層嗎？**

是，您可以在儲存體帳戶上設定 [**存取層**] 屬性，以變更預設帳戶層級。 變更帳戶層會套用至帳戶中儲存但未設定明確層的所有物件（例如經常性存取 **（推斷）** 或非經常性存取 **（推斷）** ）。 將帳戶層從經常性存取切換至非經常性存取時，會產生所有 blob 的寫入作業（每10000個），而不需要 GPv2 帳戶中的設定層，而從非經常性存取切換至經常性存取會產生 Blob 儲存體中所有 blob 的讀取作業（每10000）和資料抓取（每 GB）費用和 GPv2 帳戶。

**是否可以將我的預設帳戶存取層設定為封存？**

No。 只有經常性存取和非經常性存取層可以設定為預設帳戶存取層。 封存只能設定於物件層級。 在 blob 上傳時，不論預設帳戶層為何，您都可以將選擇的存取層指定為經常性、非經常性或封存。 這項功能可讓您直接將資料寫入封存層，以在您于 blob 儲存體中建立資料的時間，實現節省成本。

**在哪些區域中可以使用經常性、非經常性和封存存取層？**

經常性存取和非經常性存取層以及 blob 層級的階層處理在所有區域都有提供。 封存儲存體一開始只在精選區域中提供。 如需完整清單，請參閱 [Azure 依區域提供的產品](https://azure.microsoft.com/regions/services/)。

**非經常性存取層中的 blob 與經常性存取層中的 blob 的行為有何不同？**

經常性存取層中的 blob 與 GPv1、GPv2 和 Blob 儲存體帳戶中的 blob 具有相同的延遲。 非經常性存取層中的 blob 與 GPv1、GPv2 和 Blob 儲存體帳戶中的 blob 有類似的延遲（以毫秒為單位）。 封存存取層中的 blob 在 GPv1、GPv2 和 Blob 儲存體帳戶中有數小時的延遲。

非經常性存取層中的 blob 與儲存在經常性存取層中的 blob 有稍微較低的可用性服務等級（SLA）。 如需詳細資訊，請參閱[儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。

**經常性存取、非經常性存取和封存層之間的作業是否相同？**

經常性存取與非經常性存取之間的所有作業都完全一致。 所有有效的封存作業（包括 GetBlobProperties、GetBlobMetadata、ListBlobs、SetBlobTier 和 DeleteBlob）都與經常性存取和非經常性存取100% 一致。 在封存層中，無法讀取或修改 Blob 資料，直到解除凍結;封存時僅支援 blob 中繼資料讀取作業。

**將 Blob 從封存層解凍至經常性存取或非經常性存取層時，我要如何知道解除凍結已完成？**

在解除凍結期間，您可以使用 [取得 blob 屬性] 作業來輪詢 [封存**狀態**] 屬性，並確認階層變更何時完成。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成時，系統會移除封存狀態屬性，而 [存取層] blob 屬性會反映新的經常性存取或非經常性存取層。 若要深入瞭解，請參閱[從封存層解除凍結 blob 資料](storage-blob-rehydration.md)。

**設定 blob 的存取層之後，何時會開始以適當的費率計費？**

每個 Blob 一律根據 Blob [存取層] 屬性所指定的存取層計費。 當您為 blob 設定新的線上層時，[**存取層**] 屬性會立即反映所有轉換的新層。 不過，將 blob 從離線封存層解除凍結至經常性存取或非經常性存取層可能需要數小時的時間。 在此情況下，您會以封存費率計費，直到解除凍結完成為止，此時 [**存取層**] 屬性會反映新的層級。 一旦解除凍結到線上層之後，就會以經常性存取或非經常性存取費率向您收取該 blob 的費用。

**如何? 判斷當您刪除或移出非經常性存取或封存層中的 blob 時，是否會產生提早刪除費用？**

在 30 天前和 180 天前遭到刪除或移出非經常性存取 (僅限 GPv2 帳戶) 或封存層的任何 Blob，分別會產生按比例計算的提早刪除費用。 檢查 [存取層變更時間] Blob 屬性 (該屬性提供最後一次存取層變更的戳記)，即可判斷 Blob 放在非經常性存取或封存層中的時間長度。 如果 blob 的層級永遠不會變更，您可以檢查**上次修改**的 blob 屬性。 如需詳細資訊，請參閱非經常性存取[和封存提前刪除](#cool-and-archive-early-deletion)。

**哪些 Azure 工具和 SDK 支援 blob 層級的階層處理和封存儲存體？**

Azure 入口網站、PowerShell 和 CLI 工具，以及 .NET、Java、Python 和 Node.js 用戶端程式庫全都支援 blob 層級的階層處理和封存儲存體。  

**我可以在經常性存取、非經常性存取和封存層中儲存多少資料？**

資料儲存體與其他限制會設定于帳戶層級，而不是每個存取層。 您可以選擇在一層或所有三個層級中使用您的所有限制。 如需詳細資訊，請參閱[標準儲存體帳戶的擴充性和效能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

評估 GPv2 和 Blob 儲存體帳戶中的經常性存取、非經常性存取和封存

- [依照區域檢查經常性存取、非經常性存取和封存的可用性](https://azure.microsoft.com/regions/#services)
- [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [判斷 premium 效能是否能讓您的應用程式獲益](storage-blob-performance-tiers.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
