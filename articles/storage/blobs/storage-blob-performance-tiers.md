---
title: 區塊 blob 儲存體效能層級— Azure 儲存體
description: 討論 Azure 區塊 blob 儲存體的 premium 和 standard 效能層級之間的差異。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "74270215"
---
# <a name="performance-tiers-for-block-blob-storage"></a>區塊 blob 儲存體的效能層級

當企業部署效能敏感的雲端原生應用程式時，請務必在不同的效能層級上具有符合成本效益的資料儲存選項。

Azure 區塊 blob 儲存體提供兩個不同的效能層級：

- **Premium**：已針對高交易速率和單一數位一致儲存體延遲進行優化
- **標準**：已針對高容量和高輸送量進行優化

下列考慮適用于不同的效能層級：

| 區域 |標準效能  |Premium 效能  |
|---------|---------|---------|
|區域可用性     |   所有區域      | 在[選取區域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)中       |
|支援的[儲存體帳戶類型](../common/storage-account-overview.md#types-of-storage-accounts)     |     一般用途 v2、BlobStorage、一般用途 v1    |    BlockBlobStorage     |
|支援[高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，超過 4 MiB PutBlock 或 PutBlob 大小     |    是，大於 256 KiB PutBlock 或 PutBlob 大小    |
|備援性     |     請參閱[儲存體帳戶的類型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前僅支援本機-多餘儲存體（LRS）和區域備援儲存體（ZRS）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>區域冗余儲存體（ZRS）可用於 premium 效能區塊 blob 儲存體帳戶的選取區域。</div>

關於成本，premium 效能會針對具有高交易率的應用程式提供優化的定價，以協助降低這些工作負載的[總儲存成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。

## <a name="premium-performance"></a>Premium 效能

Premium 性能區塊 blob 儲存體可透過高效能硬體提供資料。 資料會儲存在固態硬碟（Ssd）上，其已針對低延遲而優化。 相較于傳統硬碟，Ssd 提供更高的輸送量。

Premium 效能儲存體適用于需要快速且一致回應時間的工作負載。 最適合執行許多小型交易的工作負載。 範例工作負載包括：

- **互動式工作負載**。 這些工作負載需要立即更新和使用者意見反應，例如電子商務和對應應用程式。 例如，在電子商務應用程式中，可能不會快取較不常用的專案。 不過，他們必須立即向客戶顯示，視需要而定。

- **分析**。 在 IoT 案例中，可能會每秒將許多較小的寫入作業推送到雲端。 大量的資料可能會在中進行匯總，以供分析之用，然後幾乎立即刪除。 Premium 區塊 blob 儲存體的高內嵌功能讓此類型的工作負載更有效率。

- **人工智慧/機器學習服務（AI/ML）**。 AI/ML 會處理不同資料類型（例如視覺效果、語音和文字）的耗用量和處理。 這種高效能運算類型的工作負載會處理大量資料，需要快速回應和有效率的資料分析內嵌時間。

- **資料轉換**。 需要對資料進行持續編輯、修改和轉換的進程需要立即更新。 如需精確的資料標記法，此資料的取用者必須立即看到這些變更反映出來。

## <a name="standard-performance"></a>標準效能

標準效能支援不同的[存取層](storage-blob-storage-tiers.md)，以最符合成本效益的方式儲存資料。 它已針對大型資料集上的高容量和高輸送量進行優化。

- **備份和損毀修復資料集**。 標準效能儲存體提供符合成本效益的層級，讓它成為短期和長期嚴重損壞修復資料集、次要備份和合規性資料封存的絕佳使用案例。

- **媒體內容**。 影像和影片通常會在第一次建立和儲存時經常存取，但此內容類型在較舊的情況下使用的頻率較低。 標準效能儲存體提供適當的媒體內容需求層級。 

- **大量資料處理**。 這些類型的工作負載適用于標準儲存體，因為它們需要符合成本效益的高輸送量儲存體，而不是一致的低延遲。 會暫存大型的原始資料集以供處理，最後再遷移至較少的層級。

## <a name="migrate-from-standard-to-premium"></a>從標準遷移至 premium

您無法將現有的標準效能儲存體帳戶轉換成具有 premium 效能的區塊 blob 儲存體帳戶。 若要遷移至 premium 效能儲存體帳戶，您必須建立 BlockBlobStorage 帳戶，並將資料移轉至新帳戶。 如需詳細資訊，請參閱[建立 BlockBlobStorage 帳戶](storage-blob-create-account-block-blob.md)。

若要在儲存體帳戶之間複製 blob，您可以使用最新版本的[AzCopy](../common/storage-use-azcopy-blobs.md)命令列工具。 其他工具（例如 Azure Data Factory）也適用于資料移動和轉換。

## <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則：

- **Premium**：資料在其生命週期結束時過期。
- **標準**：將資料轉換到最佳存取層，並在其生命週期結束時使資料過期。

若要深入瞭解，請參閱[管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。

您無法在經常性、非經常性和封存層之間移動儲存于 premium 區塊 blob 儲存體帳戶中的資料。 不過，您可以從區塊 blob 儲存體帳戶將 blob 複製到*不同*帳戶中的經常性存取層。 若要將資料複製到不同的帳戶，請使用[Put Block FROM URL](/rest/api/storageservices/put-block-from-url) API 或[AzCopy v10](../common/storage-use-azcopy-v10.md)。 **來自 URL API 的 Put 區塊**會同步複製伺服器上的資料。 只有在所有資料從源伺服器位置移至目的地位置之後，呼叫才會完成。

## <a name="next-steps"></a>後續步驟

評估 GPv2 和 Blob 儲存體帳戶中的經常性存取、非經常性存取和封存。

- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
