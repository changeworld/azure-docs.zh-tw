---
title: 區塊 blob 儲存體效能層級-Azure 儲存體
description: 討論 Azure 區塊 blob 儲存體 premium 和 standard 效能層級之間的差異。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: 404999cfac5037702eb61fdf74b2c5245ce9eb30
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/24/2020
ms.locfileid: "95526114"
---
# <a name="performance-tiers-for-block-blob-storage"></a>區塊 Blob 儲存體的效能層級

當企業部署效能敏感性的雲端原生應用程式時，請務必在不同的效能層級擁有符合成本效益的資料儲存選項。

Azure 區塊 blob 儲存體提供兩個不同的效能層級：

- **Premium**：針對高交易率和單一位數的一致儲存延遲優化
- **標準**：針對高容量和高輸送量優化

下列考慮適用于不同的效能層級：

| 區域 |標準效能  |Premium 效能  |
|---------|---------|---------|
|區域可用性     |   所有區域      | 在[選取區域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)中       |
|支援的 [儲存體帳戶類型](../common/storage-account-overview.md#types-of-storage-accounts)     |     一般用途 v2、BlobStorage、一般用途 v1    |    BlockBlobStorage     |
|支援 [高輸送量區塊 blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，超過 4 MiB PutBlock 或 PutBlob 大小     |    是，超過 256 KiB PutBlock 或 PutBlob 大小    |
|備援     |     查看 [儲存體帳戶的類型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前僅支援本機冗余儲存體 (LRS) 和區域備援儲存體 (ZRS) <div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>區域冗余儲存體 (ZRS) 可在 premium 效能區塊 blob 儲存體帳戶的選取區域中使用。</div>

關於成本，premium 效能可針對具有高交易率的應用程式提供最佳的定價，以協助降低這些工作負載的 [總儲存成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/) 。

## <a name="premium-performance"></a>Premium 效能

Premium 效能區塊 blob 儲存體可透過高效能硬體提供資料。 資料會儲存在固態硬碟 (Ssd) ，以針對低延遲進行優化。 相較于傳統硬碟，Ssd 提供更高的輸送量。

Premium 效能儲存體適用于需要快速且一致回應時間的工作負載。 它最適合執行許多小型交易的工作負載。 範例工作負載包括：

- **互動式工作負載**。 這些工作負載需要立即更新和使用者意見反應，例如電子商務和地圖應用程式。 例如，在電子商務應用程式中，可能不會快取較少的經常流覽專案。 不過，它們必須立即隨選向客戶顯示。

- **分析**： 在 IoT 案例中，有許多較小的寫入作業可能會每秒推送至雲端。 大量資料可能會在中進行，並匯總以供分析之用，然後幾乎立即刪除。 高階區塊 blob 儲存體的高內嵌功能讓這種類型的工作負載更有效率。

- **人工智慧/機器學習 (AI/ML)**。 AI/ML 會處理不同資料類型的耗用量和處理，例如視覺效果、語音和文字。 這種高效能運算類型的工作負載處理大量資料，需要快速回應和有效率的資料分析內嵌時間。

- **資料轉換**。 需要持續編輯、修改和轉換資料的進程需要立即更新。 針對準確的資料標記法，此資料的取用者必須立即看到這些變更反映出來。

## <a name="standard-performance"></a>標準效能

標準效能支援不同的 [存取層](storage-blob-storage-tiers.md) ，以最符合成本效益的方式來儲存資料。 它已針對大型資料集的高容量和高輸送量進行優化。

- **備份和嚴重損壞修復資料集**。 標準效能儲存體可提供符合成本效益的層級，使其成為短期和長期嚴重損壞修復資料集、次要備份和合規性資料封存的最佳使用案例。

- **媒體內容**。 影像和影片通常會在第一次建立和儲存時經常存取，但此內容類型的使用方式較舊。 標準效能儲存體針對媒體內容需求提供適合的層級。 

- **大量資料處理**。 這些類型的工作負載適合標準儲存體，因為它們需要符合成本效益的高輸送量儲存體，而不是一致的低延遲。 大型的原始資料集會暫存以進行處理，最後遷移至較低的層級。

## <a name="migrate-from-standard-to-premium"></a>從標準遷移至 premium

您無法將現有的標準效能儲存體帳戶轉換為具有 premium 效能的區塊 blob 儲存體帳戶。 若要遷移至 premium 效能儲存體帳戶，您必須建立 BlockBlobStorage 帳戶，並將資料移轉至新的帳戶。 如需詳細資訊，請參閱 [建立 BlockBlobStorage 帳戶](storage-blob-create-account-block-blob.md)。

若要在儲存體帳戶之間複製 blob，您可以使用最新版的 [AzCopy](../common/storage-use-azcopy-blobs.md) 命令列工具。 其他工具（例如 Azure Data Factory）也適用于資料移動和轉換。

## <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 儲存體生命週期管理提供豐富、以規則為基礎的原則：

- **Premium**：在生命週期結束時使資料過期。
- **標準**：將資料轉換到最佳存取層，並在其生命週期結束時使資料過期。

若要深入瞭解，請參閱 [管理 Azure Blob 儲存體生命週期](storage-lifecycle-management-concepts.md)。

您無法在經常性存取層、非經常性存取層和封存層之間移動儲存在 premium 區塊 blob 儲存體帳戶中的資料。 不過，您可以從區塊 blob 儲存體帳戶將 blob 複製到 *不同* 帳戶中的經常性存取層。 若要將資料複製到不同的帳戶，請使用 [URL API 中的 Put 區塊](/rest/api/storageservices/put-block-from-url) 或 [AzCopy v10](../common/storage-use-azcopy-v10.md)。 **來自 URL API 的 Put 區塊** 會以同步方式將資料複製到伺服器上。 只有在將所有資料從原始伺服器位置移至目的地位置之後，才會完成呼叫。

## <a name="next-steps"></a>後續步驟

在 GPv2 和 Blob 儲存體帳戶中評估經常性存取、非經常性存取和封存。

- [瞭解如何從封存層解除凍結 blob 資料](storage-blob-rehydration.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](./monitor-blob-storage.md)
- [依照區域檢查 Blob 儲存體和 GPv2 帳戶中的經常性存取、非經常性存取和封存價格](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)