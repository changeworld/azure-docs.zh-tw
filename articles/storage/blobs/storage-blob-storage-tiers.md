---
title: blob 的熱、酷和存檔存取層 - Azure 儲存
description: Azure 儲存帳戶的熱、酷和存檔訪問層。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/23/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: c803d489b70cda6910865f6096d21c2021c4ae3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393693"
---
# <a name="azure-blob-storage-hot-cool-and-archive-access-tiers"></a>Azure Blob 儲存體︰經常性存取、非經常性存取和封存存取層

Azure 儲存提供了不同的存取層,這允許您以最具成本效益的方式存儲 Blob 物件資料。 可用的存取層包括:

- **熱**- 針對存儲頻繁存取的數據進行優化。
- **酷**- 針對存儲不常存取和存儲至少 30 天的數據進行優化。
- **存檔**- 針對存儲很少存取和儲存的資料(具有靈活的延遲要求)(按小時順序)進行優化。

以下注意事項適用於不同的存取層:

- 只能在帳戶級別設置熱訪問層和冷訪問層。 存檔訪問層在帳戶級別不可用。
- 在上載期間或上載後,可以在 blob 級別設置熱、酷和存檔層。
- 冷存取層中的數據可以容忍稍低的可用性,但仍需要高耐用性、檢索延遲和輸送量特徵(類似於熱數據)。 對於冷數據,與熱數據相比,可用性服務級別協定 (SLA) 稍低一些,訪問成本更高,這是降低存儲成本的可接受權衡。
- 存檔存儲將數據存儲離線,提供最低的儲存成本,但也提供最高的數據補充和存取成本。

儲存在雲端的資料迅速成長。 若要為不斷擴充的儲存體需求來管理成本，根據存取頻率和計劃性保留期來組織您的資料，進而將成本最佳化很有幫助。 存儲在雲中的數據可能因雲在雲生命週期內生成、處理和訪問的方式而異。 有些資料在其存留期內會積極地存取及修改。 有些資料在其存留期的早期存取頻率很高，但存取頻率隨著資料老化而大幅下滑。 某些數據在雲中保持空閒狀態,很少(如果有的話)在存儲后訪問這些數據。

每個數據訪問方案都受益於針對特定訪問模式優化的不同訪問層。 借助熱、酷和存檔存取層,Azure Blob 儲存可滿足使用單獨定價模型的不同存取層的需求。

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>支援階層處理的儲存體帳戶

熱儲存、冷和存檔之間的物件儲存資料分層僅在 Blob 存儲和通用 v2 (GPv2) 帳戶中支援。 通用 v1 (GPv1) 帳戶不支援分層。 客戶可以通過 Azure 門戶輕鬆地將其現有的 GPv1 或 Blob 儲存帳戶轉換為 GPv2 帳戶。 GPv2 為 Blob、檔和佇列提供了新的定價和功能。 某些功能和降價僅在 GPv2 帳戶中提供。 全面審核定價后,使用 GPv2 帳戶進行評估。 某些工作負載在 GPv2 上可能比 GPv1 更昂貴。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

Blob 存儲和 GPv2 帳戶在帳戶級別公開**訪問層**屬性。 此屬性允許您為在物件級別未顯式設置它的任何 Blob 指定預設存取層。 對於在物件級別設置層的物件,帳戶層將不適用。 存檔層只能在對象級別應用。 您可以隨時在這些訪問層之間切換。

## <a name="hot-access-tier"></a>經常性存取層

熱存取層的存儲成本高於冷層和存檔層,但存取成本最低。 熱存取層的範例使用專案包括:

- 正在使用中或預期要頻繁存取(讀取和寫入)的數據。
- 用於處理並最終遷移到酷訪問層的數據。

## <a name="cool-access-tier"></a>非經常性存取層

與熱存儲相比,冷存取層具有更低的儲存成本和更高的存取成本。 這一層適用於將保留在非經常性存取層至少 30 天的資料。 冷存取層的範例使用專案包括:

- 短期備份和災害復原資料集。
- 不再經常檢視，但存取時應立即可用的較舊媒體內容。
- 當收集較多資料以供未來處理時，需要以符合成本效益方式預存的大型資料集。 (例如**，科學資料、來自製造工廠的原始遙測資料等長期儲存)

## <a name="archive-access-tier"></a>封存存取層

存檔訪問層具有最低的存儲成本。 但與熱層和冷層相比,它的數據檢索成本更高。 存檔層中的數據可能需要幾個小時才能檢索。 數據必須在存檔層中保留至少 180 天,否則需要提前刪除費用。

當 Blob 處於存檔儲存中時,Blob 數據處於離線狀態,無法讀取、覆蓋或修改。 要讀取或下載存檔中的 Blob,必須首先將其重新凍結到連線層。 不能在存檔存儲中拍攝 Blob 的快照。 不過，Blob 中繼資料會保持連線且可以取得，讓您可列出 Blob 和其屬性。 對於存檔中的 Blob,唯一有效的操作是 GetBlob 屬性、GetBlob元資料、清單 Blob、SetBlobTier、CopyBlob 和 DeleteBlob。 有關詳細資訊[,請參閱存檔層中的重新凍結 blob 資料](storage-blob-rehydration.md)。

封存取層的範例使用專案包括:

- 長期備份、次要備份和封存資料集
- 即使已處理成最終可用格式，但還是需要保存的原始資料。
- 需要儲存一段時間且幾乎不曾存取的相容性和封存資料。

## <a name="account-level-tiering"></a>帳戶級分層

所有三個訪問層中的 Blob 可以共存於同一帳戶中。 沒有任何顯式分配的層的任何 Blob 都從帳戶訪問層設置中推斷該層。 如果存取層來自帳戶,您將看到 **「存取層推斷**blob」屬性設定為「true」,**並且「存取層**blob」屬性與帳戶層匹配。 在 Azure 門戶中,_存取層推斷_屬性將顯示 blob 存取層為 **「熱」(推斷)** 或 **「酷」(推斷)。**

更改帳戶存取層適用於存儲在帳戶中未設置顯式層的所有_訪問層推斷_物件。 如果將帳戶層從熱級別切換為冷,則對於沒有 GPv2 帳戶中設置層的所有 Blob,將收取寫入操作(每 10,000 個)的費用。 此更改在 Blob 儲存帳戶中不收取任何費用。 如果在 Blob 儲存或 GPv2 帳戶中從冷到熱,則讀取操作(每 10,000)和數據檢索(每 GB)都將向您收費。

## <a name="blob-level-tiering"></a>Blob 層級的階層處理

Blob 級分層允許您使用[「放置 Blob」](/rest/api/storageservices/put-blob)或[「放置封鎖清單」](/rest/api/storageservices/put-block-list)操作將資料上載到您選擇的存取層,並使用[「設定 Blob 層](/rest/api/storageservices/set-blob-tier)」操作或[生命週期管理](#blob-lifecycle-management)功能在物件等級更改資料層。 您可以將資料上載到所需的訪問層,然後隨著使用模式的變化,可以輕鬆地更改熱、酷或存檔層之間的 Blob 訪問層,而無需在帳戶之間移動數據。 所有層更改請求都立即發生,熱和冷之間的層更改是暫態的。 但是，從封存解除凍結 Blob 可能需要數小時的時間。

最後一個 blob 層變更的時間會透過 [存取層變更時間]**** blob 屬性公開。 在熱層或冷層中覆蓋 Blob 時,新建立的 Blob 將繼承被覆蓋的 Blob 層,除非在創建時顯式設置新的 Blob 存取層。 如果 Blob 位於存檔層中,則無法覆蓋它,因此在這種情況下不允許上載相同的 Blob。 

> [!NOTE]
> 封存儲存體與 Blob 層級階層處理只支援區塊 blob。 當前也不能更改具有快照的塊 Blob 的層。

### <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob Storage 生命週期管理提供了一個豐富的、基於規則的策略,您可以使用該策略將數據轉換為最佳存取層,並在資料生命週期結束時過期。 若要深入了解，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。  

> [!NOTE]
> 存儲在區塊 Blob 儲存帳戶中的資料(進階性能)目前無法使用 Set Blob 層或使用 Azure [Blob](/rest/api/storageservices/set-blob-tier)儲存生命週期管理分層到熱、酷或存檔。
> 要移動資料,必須使用[Put Block 從 URL API](/rest/api/storageservices/put-block-from-url)或支援此 API 的 AzCopy 版本,將 Blob 從塊 Blob 儲存帳戶同步複製到其他帳戶中的熱存取層。
> *Put Block From URL* API 會同步複製伺服器上的資料，這表示只有將所有資料從原始伺服器位置移動到目標位置後，才會完成呼叫。

### <a name="blob-level-tiering-billing"></a>Blob 層級的階層處理計費

當 Blob 上載或移動到熱、酷或存檔層時,在層更改時立即按相應的速率收費。

當 Blob 移至存取頻率較低的階層 (經常性存取 -> 非經常性存取、經常性存取 -> 封存，或非經常性存取 -> 封存) 時，此作業會在寫入目的地階層時計費，且適用目的地階層的寫入作業 (每 10,000 個) 和資料寫入 (每 GB) 費用。

當 Blob 移至較暖層(存檔>酷、存檔>熱或冷>熱時),該操作將計費為從源層讀取,其中應用源層的讀取操作(每 10,000)和數據檢索(每 GB)費用。 可能也適用任何移出非經常性存取或封存層之 blob 的提早刪除費用。 [存檔中的補水數據](storage-blob-rehydration.md)需要時間,數據將收取存檔價格,直到數據連線恢復,Blob 層更改為熱或冷。 下表匯總了如何計費層更改:

| | **寫入費用 (作業 + 存取)** | **讀取費用 (作業 + 存取)**
| ---- | ----- | ----- |
| **SetBlobTier 方向** | 熱>酷,<br> 熱>存檔,<br> 酷>存檔 | 存檔>酷,<br> 存檔->热,<br> 冷>熱

### <a name="cool-and-archive-early-deletion"></a>非經常性存取和封存提前刪除

移動到冷層(僅限 GPv2 帳戶)的任何 Blob 都需遵守 30 天的冷卻早期刪除期。 移動到存檔層的任何 Blob 都受存檔早期刪除期 180 天的約束。 此費用按比例計算。 例如,如果 Blob 移至存檔,然後在 45 天後刪除或移動到熱層,則您需要支付相當於將該 Blob 儲存在存檔中的 135 天(180 減 45 日)的早期刪除費用。

如果存取層沒有變化,則可以使用 blob 屬性 **「上次修改**」來計算早期刪除。 否則,您可以在上次修改訪問層時通過查看 blob 屬性:**訪問層更改時間**來冷卻或存檔。 如需 Blob 屬性的詳細資訊，請參閱[取得 Blob 屬性](https://docs.microsoft.com/rest/api/storageservices/get-blob-properties)。

## <a name="comparing-block-blob-storage-options"></a>比較區塊來圖

下表顯示了高級性能塊 Blob 儲存以及熱、酷和存檔存取層的比較。

|                                           | **卓越的效能**   | **熱層** | **冷層**       | **存檔層**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **可用性**                          | 99.9%                     | 99.9%        | 99%                 | 離線           |
| **可用性** <br> **(RA-GRS 讀取)**  | N/A                       | 99.99%       | 99.9%               | 離線           |
| **使用費用**                         | 更高的儲存成本,更低的訪問和事務成本 | 儲存成本較高，存取和交易成本較低 | 儲存成本較低，存取和交易成本較高 | 儲存成本最低，存取和交易成本最高 |
| **最小物件大小**                   | N/A                       | N/A          | N/A                 | N/A               |
| **最小儲存持續時間**              | N/A                       | N/A          | 30 天<sup>1</sup> | 180 天
| **Latency** <br> **(距第一位元組時間)** | 單位數毫秒 | 毫秒 | 毫秒        | 小時<sup>2</sup> |

<sup>GPv2</sup>帳戶上冷層中的 1 個物件至少保留期為 30 天。 Blob 存儲帳戶對於酷層沒有最短的保留期。

<sup>2</sup>存檔儲存當前支援 2 個重新水合優先順序(高優先順序和標準優先順序),這些優先順序提供不同的檢索延遲。 有關詳細資訊,請參閱[存檔層中的重新凍結 blob 資料](storage-blob-rehydration.md)。

> [!NOTE]
> Blob 儲存帳戶支援與通用 v2 儲存帳戶相同的性能和可擴充性目標。 如需詳細資訊，請參閱 [Blob 儲存體的延展性和效能目標](scalability-targets.md)。

## <a name="quickstart-scenarios"></a>快速入門案例

在本節中,使用 Azure 門戶和電源shell演示了以下方案:

- 如何變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層。
- 如何在 GPv2 或 Blob 儲存體帳戶中變更 Blob 的存取層。

### <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存體帳戶的預設帳戶存取層

# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 門戶中,搜索並選擇 **「所有資源**」。

1. 選取您的儲存體帳戶。

1. 在 **「設定」** 中,選擇 **「設定**」以查看和更改帳戶設定。

1. 根據需要選擇正確的存取層:將**存取層**設置為 **「酷」** 或 **「熱**」。

1. 按一下頂端的 [儲存] **** 。

![變更儲存帳戶層](media/storage-tiers/account-tier.png)

# <a name="powershell"></a>[電源殼](#tab/azure-powershell)
以下 PowerShell 文本可用於更改帳戶層。 必須`$rgName`使用資源組名稱初始化該變數。 必須`$accountName`使用存儲帳戶名稱初始化該變數。 
```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```
---

### <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>變更 GPv2 或 Blob 儲存帳號中的 Blob 層
# <a name="portal"></a>[入口網站](#tab/azure-portal)
1. 登入 [Azure 入口網站](https://portal.azure.com)。

1. 在 Azure 門戶中,搜索並選擇 **「所有資源**」。

1. 選取您的儲存體帳戶。

1. 選擇容器,然後選擇 Blob。

1. 在**Blob 屬性**中,選擇 **「更改層**」 。

1. 選擇 **"熱****"、"酷**"或 **"存檔**"存取層。 如果 blob 當前處於存檔中,並且要重新水化到聯機層,也可以選擇 **「標準**」或「**高**」的補水優先順序。

1. 選擇 **「在底部保存**」。

![變更儲存帳戶層](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[電源殼](#tab/azure-powershell)
以下 PowerShell 文稿可用於更改 Blob 層。 必須`$rgName`使用資源組名稱初始化該變數。 必須`$accountName`使用存儲帳戶名稱初始化該變數。 必須`$containerName`使用容器名稱初始化該變數。 必須`$blobName`使用 blob 名稱初始化該變數。 
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

所有存儲帳戶都使用基於每個 Blob 層的 Blob 儲存的定價模型。 請注意下列計費考量：

- **存儲成本**:除了存儲的數據量外,存儲數據的成本也因訪問層而異。 每 GB 的成本會隨著儲存層存取頻率降低而減少。
- **資料存取成本**：資料存取費用會隨著儲存層存取頻率降低而增加。 對於冷和存檔存取層中的數據,讀取需要支付每 GB 的數據存取費用。
- **事務成本**:所有層的按事務費用都會隨著層變冷而增加。
- **異地複寫資料傳輸成本**︰此費用適用於已設定異地複寫的帳戶，包括 GRS 和 RA-GRS。 異地複寫資料傳輸會產生每 GB 費用。
- **輸出資料傳輸成本**︰輸出資料傳輸 (從 Azure 區域傳出的資料) 會產生每 GB 頻寬使用量費用，與一般用途的儲存體帳戶一致。
- **更改存取層**:更改帳戶存取層將導致_存取層推斷_的 blob 的層變更費用儲存在沒有顯式層集的帳戶中。 有關更改單個 Blob 的存取層的資訊,請參考 Blob[等級的分層計費](#blob-level-tiering-billing)。

> [!NOTE]
> 有關塊 Blob 定價的詳細資訊,請參閱[Azure 儲存定價](https://azure.microsoft.com/pricing/details/storage/blobs/)頁。 如需輸出資料傳輸費用的詳細資訊，請參閱[資料傳輸定價詳細資料](https://azure.microsoft.com/pricing/details/data-transfers/)頁面。

## <a name="faq"></a>常見問題集

**如果我想要將我的資料進行階層處理，應使用 Blob 儲存體或 GPv2 帳戶？**

建議您 GPv2，而不要使用 Blob 儲存體帳戶進行階層處理。 GPv2 支援 Blob 儲存體帳戶支援的所有功能，以及其他許多功能。 Blob 儲存體與 GPv2 之間的價格幾乎相同，但是 GPv2 帳戶將提供一些新功能和降價。 GPv1 帳戶不支援分層。

GPv1 與 GPv2 帳戶之間的價格結構不同，客戶在決定使用 GPv2 帳戶之前，應該仔細評估兩者。 您可以透過簡單的單鍵程序，輕鬆地將現有的 Blob 儲存體或 GPv1 帳戶轉換為 GPv2。 如需詳細資訊，請參閱 [Azure 儲存體帳戶概觀](../common/storage-account-overview.md)。

**是否可以將物件存儲在同一帳戶的所有三個(熱、酷和存檔)訪問層中?**

是。 在帳戶級別設置**的 Access Tier**屬性是預設帳戶層,適用於該帳戶中的所有物件,而不設置顯式設置層。 Blob 級分層允許您在物件級別設置訪問層,而不考慮帳戶上的訪問層設置。 同一帳戶中可能存在三個訪問層中的任何一個(熱、酷或存檔)。

**是否可以更改 Blob 或 GPv2 儲存帳戶的預設存取層?**

可以,您可以通過在儲存帳戶上設置**Access 層**屬性來更改預設帳戶層。 變更帳號層適用於帳戶中儲存的所有沒有顯示式層集的物件(例如,**熱(推斷)** 或**酷(推斷)**。 將帳戶層從熱到冷產生所有 Blob 的寫入操作(每 10,000 個),並且從冷到熱切換會產生 Blob 存儲和 GPv2 帳戶中所有 Blob 的讀取操作(每 10,000 個)和數據檢索(每 GB)的費用。

**是否可以將預設帳戶訪問層設置為存檔?**

否。 只能將熱訪問層和冷存取層設置為預設帳戶訪問層。 封存只能設定於物件層級。 在 Blob 上載時,無論預設帳戶層如何,您所選擇的訪問層都指定為熱、酷或存檔。 此功能允許您將資料直接寫入存檔層,以便從在 Blob 儲存中創建數據的那一刻起實現成本節約。

**熱、酷和存檔存取層在哪些區域可用?**

熱和酷訪問層以及 blob 級分層在所有區域都可用。 封存儲存體一開始只在精選區域中提供。 如需完整清單，請參閱 [Azure 依區域提供的產品](https://azure.microsoft.com/regions/services/)。

**冷存取層中的 blob 的行為與熱存取層中的 blob 的行為不同嗎?**

熱存取層中的 Blob 與 GPv1、GPv2 和 Blob 儲存帳戶中的 Blob 具有相同的延遲。 冷存取層中的 Blob 與 GPv1、GPv2 和 Blob 儲存帳戶中的 Blob 具有類似的延遲(以毫秒為單位)。 存檔存取層中的 Blob 在 GPv1、GPv2 和 Blob 儲存帳戶中具有幾個小時的延遲。

冷存取層中的 Blob 的可用性服務等級 (SLA) 略低於存儲在熱存取層中的 Blob。 如需詳細資訊，請參閱[儲存體 SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_2/)。

**熱層、冷層和存檔層之間的操作是相同的嗎?**

經常性存取與非經常性存取之間的所有作業都完全一致。 所有有效的存檔操作(包括 GetBlob 屬性、GetBlobMetadata、ListBlob、SetBlobTier 和 DeleteBlob)都 100% 與熱和酷一致。 在重新水化之前,在存檔層中無法讀取或修改 Blob 資料;存檔時僅支援 Blob 元數據讀取操作。

**當將 Blob 從存檔層重新加水到熱層或冷層時,我如何知道何時完成補液?**

在補液期間,可以使用 get blob 屬性操作輪詢**存檔狀態**屬性並確認層更改何時完成。 視目的地層而定，狀態會顯示為 "rehydrate-pending-to-hot" 或 "rehydrate-pending-to-cool"。 完成後,將刪除存檔狀態屬性,並且 Access **Tier** blob 屬性反映新的熱層或冷層。 有關詳細資訊[,請參閱存檔層中的重新凍結 blob 資料](storage-blob-rehydration.md)。

**設定 blob 的存取層之後，何時會開始以適當的費率計費？**

每個 Blob 一律根據 Blob [存取層]**** 屬性所指定的存取層計費。 為 Blob 設置新的連線層時 **,"Access Tier"** 屬性將立即反映所有轉換的新層。 但是,將 Blob 從離線存檔層重新加水到熱層或冷層可能需要幾個小時。 在這種情況下,您需要以存檔費率計費,直到補液完成,此時**Access Tier**屬性將反映新層。 重新水化到連線層後,您將以熱或冷速率為該 blob 收費。

**如何確定在刪除或移出冷層或存檔層時,我是否會產生提前刪除費用?**

在 30 天前和 180 天前遭到刪除或移出非經常性存取 (僅限 GPv2 帳戶) 或封存層的任何 Blob，分別會產生按比例計算的提早刪除費用。 您可以通過檢查 **「存取層更改時間」blob**屬性來確定 Blob 在冷層或存檔層中的時間,該屬性提供上次層更改的戳記。 如果 Blob 的層從未更改,則可以檢查 **「上次修改的**Blob」屬性。 有關詳細資訊,請參閱[「冷卻」和「存檔早期刪除](#cool-and-archive-early-deletion)」。

**哪些 Azure 工具和 SDK 支援 blob 級分層和存檔存儲?**

Azure 入口網站、PowerShell 和 CLI 工具，以及 .NET、Java、Python 和 Node.js 用戶端程式庫全都支援 blob 層級的階層處理和封存儲存體。  

**我可以在熱、酷和歸檔層中存儲多少數據?**

數據存儲以及其他限制設置在帳戶級別,而不是每個訪問層。 您可以選擇在一個層或所有三個層中使用所有限制。 有關詳細資訊,請參閱[標準儲存帳戶的可伸縮性和性能目標](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)。

## <a name="next-steps"></a>後續步驟

評估 GPv2 與 Blob 儲存帳戶中的熱、酷和存檔

- [依區域檢查熱、冷和存檔可用性](https://azure.microsoft.com/regions/#services)
- [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)
- [瞭解從存檔層中重新補充 blob 資料](storage-blob-rehydration.md)
- [確定進階效能是否有利於你的應用程式](storage-blob-performance-tiers.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [依區域檢視 Blob 儲存和 GPv2 帳號中的熱、酷和存檔定價](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
