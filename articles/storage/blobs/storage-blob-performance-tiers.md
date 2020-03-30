---
title: 阻止 Blob 存儲性能層 = Azure 存儲
description: 討論 Azure 塊 Blob 存儲的高級性能和標準性能層之間的區別。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/12/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: clausjor
ms.openlocfilehash: ff82986b27d038c536872b07e1308b0d48fadaef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74270215"
---
# <a name="performance-tiers-for-block-blob-storage"></a>塊 Blob 存儲的性能層

隨著企業部署性能敏感的雲原生應用程式，在不同性能級別擁有經濟高效的資料存儲選項非常重要。

Azure 塊 Blob 存儲提供兩個不同的性能層：

- **高級**： 針對高事務速率和單位一致存儲延遲進行了優化
- **標準**：針對高容量和高輸送量進行了優化

以下注意事項適用于不同的性能層：

| 區域 |標準性能  |卓越的性能  |
|---------|---------|---------|
|區域可用性     |   所有區域      | 在[選定區域](https://azure.microsoft.com/global-infrastructure/services/?products=storage)       |
|受支援的[存儲帳戶類型](../common/storage-account-overview.md#types-of-storage-accounts)     |     通用 v2， Blob 存儲， 通用 v1    |    塊 Blob 存儲     |
|支援[高輸送量塊 Blob](https://azure.microsoft.com/blog/high-throughput-with-azure-blob-storage/)     |    是，在大於 4 MiB PutBlock 或 PutBlob 大小     |    是，在大於 256 KiB PutBlock 或 PutBlob 大小    |
|備援性     |     請參閱[存儲帳戶的類型](../common/storage-account-overview.md#types-of-storage-accounts)   |  目前僅支援本地冗余存儲 （LRS） 和區域重新集中存儲 （ZRS）<div role="complementary" aria-labelledby="zone-redundant-storage"><sup>1</sup></div>     |

<div id="zone-redundant-storage"><sup>1</sup>區域冗余存儲 （ZRS） 在高級性能塊 Blob 存儲帳戶的選定區域中可用。</div>

在成本方面，高級性能為具有高事務速率的應用程式提供優化的定價，以説明降低這些工作負載[的總存儲成本](https://azure.microsoft.com/blog/reducing-overall-storage-costs-with-azure-premium-blob-storage/)。

## <a name="premium-performance"></a>卓越的性能

高性能塊 Blob 存儲使資料可通過高性能硬體提供。 資料存儲在固態磁碟機 （SSD） 上，這些磁碟機針對低延遲進行了優化。 與傳統硬碟相比，SSD 提供更高的輸送量。

高級性能存儲非常適合需要快速一致的回應時間的工作負載。 它最適合執行許多小事務的工作負載。 工作負荷示例包括：

- **互動式工作負載**。 這些工作負載需要即時更新和使用者回饋，例如電子商務和映射應用程式。 例如，在電子商務應用程式中，不太頻繁查看的專案可能不會緩存。 但是，必須按需向客戶立即顯示它們。

- **分析**. 在 IoT 方案中，可能會每秒將許多較小的寫入操作推送到雲中。 可能會獲取大量資料，為分析目的進行聚合，然後幾乎立即刪除。 高級塊 Blob 存儲的高引入功能使其高效地用於此類工作負載。

- **人工智慧/機器學習 （AI/ML）**. AI/ML 處理不同資料類型（如視覺物件、語音和文本）的消耗和處理。 這種高性能計算類型的工作負載處理大量資料，這些資料需要快速回應和高效的引入時間進行資料分析。

- **資料轉換**。 需要持續編輯、修改和轉換資料的過程需要即時更新。 為了準確進行資料表示，此資料的消費者必須立即看到這些更改的反映。

## <a name="standard-performance"></a>標準性能

標準性能支援不同的[訪問層](storage-blob-storage-tiers.md)以最具成本效益的方式存儲資料。 它針對大型資料集的高容量和高輸送量進行了優化。

- **備份和災害復原資料集**。 標準性能存儲提供經濟高效的層級，使其成為短期和長期災害復原資料集、輔助備份和合規性資料存檔的完美用例。

- **媒體內容**. 首次創建和存儲時，通常經常訪問圖像和視頻，但此內容類型在變老時使用的頻率較低。 標準性能存儲提供適合媒體內容需求的層。 

- **批量資料處理**。 這些類型的工作負載適用于標準存儲，因為它們需要經濟高效的高輸送量存儲，而不是一致的低延遲。 大型原始資料集將暫存處理，並最終遷移到較冷層。

## <a name="migrate-from-standard-to-premium"></a>從標準遷移到高級

不能將現有標準性能存儲帳戶轉換為具有高級性能的塊 Blob 存儲帳戶。 要遷移到高級性能存儲帳戶，必須創建一個 BlockBlob 存儲帳戶，並將資料移轉到新帳戶。 有關詳細資訊，請參閱[創建塊 Blob 存儲帳戶](storage-blob-create-account-block-blob.md)。

要在存儲帳戶之間複製 Blob，可以使用最新版本的[AzCopy](../common/storage-use-azcopy-blobs.md)命令列工具。 其他工具（如 Azure 資料工廠）也可用於資料移動和轉換。

## <a name="blob-lifecycle-management"></a>Blob 生命週期管理

Blob 存儲生命週期管理提供了豐富的、基於規則的策略：

- **高級**：在其生命週期結束時過期資料。
- **標準**：將資料轉換為最佳訪問層，並在其生命週期結束時過期資料。

要瞭解更多資訊，請參閱[管理 Azure Blob 存儲生命週期](storage-lifecycle-management-concepts.md)。

不能在熱、酷和存檔層之間移動存儲在高級塊 Blob 存儲帳戶中的資料。 但是，您可以將 Blob 從塊 Blob 存儲帳戶複製到*其他*帳戶中的熱訪問層。 要將資料複製到其他帳戶，請使用["從 URL API 放置塊"](/rest/api/storageservices/put-block-from-url)或["AzCopy v10](../common/storage-use-azcopy-v10.md)"。 **從 URL 的放置塊**API 同步複製伺服器上的資料。 只有在所有資料從原始伺服器位置移動到目標位置後，呼叫才會完成。

## <a name="next-steps"></a>後續步驟

評估 GPv2 和 Blob 存儲帳戶中的熱、酷和存檔。

- [瞭解從存檔層中重新補充 blob 資料](storage-blob-rehydration.md)
- [啟用 Azure 儲存體計量以評估您目前的儲存體帳戶使用量](../common/storage-enable-and-view-metrics.md)
- [按區域查看 Blob 存儲和 GPv2 帳戶中的熱、酷和存檔定價](https://azure.microsoft.com/pricing/details/storage/)
- [檢查資料傳輸價格](https://azure.microsoft.com/pricing/details/data-transfers/)
