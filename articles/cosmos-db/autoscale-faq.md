---
title: Azure Cosmos DB 中自動調整佈建輸送量的常見問題集
description: 關於為 Azure Cosmos DB 資料庫和容器自動調整佈建輸送量的常見問題集
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/10/2020
ms.openlocfilehash: b398f739189232f39a2fee06fc6e6ff0d53348f0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83656620"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>關於 Azure Cosmos DB 中自動調整佈建輸送量的常見問題集

透過自動調整佈建輸送量，Azure Cosmos DB 會根據使用量，自動管理及調整資料庫或容器的 RU/秒。 本文提供關於自動調整常見問題的解答。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="what-is-the-difference-between-autopilot-and-autoscale-in-azure-cosmos-db"></a>Azure Cosmos DB 中的「自動試驗」和「自動調整」之間有何差異？
「自動調整」或「自動調整佈建輸送量」是功能的更新名稱，先前稱為「自動試驗」。 在目前的自動調整版本中，我們已新增功能，包括設定自訂最大 RU/秒和程式設計支援的功能。 

### <a name="what-happens-to-databases-or-containers-created-in-the-previous-autopilot-tier-model"></a>在先前的自動試驗階層模型中建立的資料庫或容器會發生什麼事？
使用先前的階層模型建立的資源會自動支援新的自動調整自訂最大 RU/秒模型。 該階層上限會變成新的 RU/秒上限，而這會產生相同的調整範圍。 

例如，如果您先前選取的階層可在 400 到 4000 RU/秒之間調整，則資料庫或容器現在會顯示最大 RU/秒為 4000 RU/秒，可在 400 到 4000 RU/秒之間調整。 從這裡，您可以將最大 RU/秒變更為自訂值，以符合您的工作負載。 

### <a name="how-quickly-will-autoscale-scale-up-and-down-based-on-spikes-in-traffic"></a>自動調整根據流量尖峰進行擴大和縮小的速度為何？
使用自動調整時，系統會根據傳入流量，在 `0.1 * Tmax` 到 `Tmax` 的範圍內，上下調整輸送量 (RU/秒) `T`。 因為縮放比例是自動且即時的，所以您可以隨時取用到佈建的最大值 `Tmax`，而不會發生延遲。 

### <a name="how-do-i-determine-what-rus-the-system-is-currently-scaled-to"></a>如何判斷系統目前調整的 RU/秒？
使用 [Azure 監視器計量](how-to-choose-offer.md#measure-and-monitor-your-usage)，監視佈建的最大 RU/秒，和系統調整到的目前輸送量 (RU/秒)。 

### <a name="what-is-the-pricing-for-autoscale"></a>自動調整的定價為何？
每小時，系統會收取在一小時內擴充到最高輸送量 `T` 的費用。 如果您的資源在一小時內沒有任何要求，或調整未超過 `0.1 * Tmax`，則會向您收取最少 `0.1 * Tmax` 的費用。 請參閱 Azure Cosmos DB [定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)，以取得詳細資料。 

### <a name="how-does-autoscale-show-up-on-my-bill"></a>我的帳單上會如何顯示自動調整？
在單一主機帳戶中，每 100 RU/秒的自動調整費率是標準 (手動) 佈建輸送量的 1.5 倍。 您會在帳單上看到現有的標準佈建輸送量計量。 此計量的數量將會乘以1.5。 例如，如果系統在一小時內擴充到的最高 RU/秒是 6000 RU/秒，則對於該小時的計量，會向您收取 60 * 1.5 = 90 個單位。

在多重主機帳戶中，每 100 RU/秒的自動調整費率與標準 (手動) 佈建多重主機輸送量相同。 您會在帳單上看到現有的多重主機計量。 由於費率相同，如果您使用自動調整，則會看到與標準輸送量相同的數量。

### <a name="does-autoscale-work-with-reserved-capacity"></a>自動調整是否會使用保留容量？
是。 當您購買單一主機保留容量時，自動調整資源的保留折扣會依照 1.5 * [特定區域的比例](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md#reservation-discount-per-region)，套用至您的計量使用量。 

多重主機保留容量的運作方式與自動調整和標準 (手動) 佈建輸送量相同。 請參閱 [Azure Cosmos DB 保留容量](cosmos-db-reserved-capacity.md)

### <a name="does-autoscale-work-with-free-tier"></a>自動調整是否會使用免費層？
是。 在免費層中，您可以在容器上使用自動調整輸送量。 尚未提供具有自訂最大 RU/秒的自動調整共用輸送量資料庫。 請參閱[免費層如何與自動調整搭配運作](understand-your-bill.md#billing-examples-with-free-tier-accounts)。

### <a name="is-autoscale-supported-for-all-apis"></a>所有 API 是否皆支援自動調整？
是，所有 API 皆支援自動調整：核心 (SQL)、Gremlin、資料表、Cassandra，以及適用於 MongoDB 的 API。

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>多重主機帳戶是否支援自動調整？
是。 在新增至 Azure Cosmos DB 帳戶的每個區域中，都可以使用最大 RU/秒。 

### <a name="how-do-i-enable-autoscale-on-new-databases-or-containers"></a>如何在新資料庫或容器上啟用自動調整？
請參閱[如何啟用自動調整](how-to-provision-autoscale-throughput.md)這篇文章。

### <a name="can-i-enable-autoscale-on-an-existing-database-or-a-container"></a>如何在現有的資料庫或容器上啟用自動調整？
是。 您也可以視需要，在自動調整和標準 (手動) 佈建輸送量之間切換。 目前，對於所有 API，您只能使用 [Azure 入口網站](how-to-provision-autoscale-throughput.md#enable-autoscale-on-existing-database-or-container)來執行下列作業。

### <a name="how-does-the-migration-between-autoscale-and-standard-manual-provisioned-throughput-work"></a>如何在自動調整和標準 (手動) 佈建輸送量之間切換？
從概念上來說，變更輸送量類型是兩階段的程序。 首先，您會傳送要求，將輸送量設定變更為使用自動調整或手動佈建的輸送量。 在這兩種情況下，系統會根據目前的輸送量設定和儲存體，自動判斷並設定初始 RU/秒的值。 在此步驟中，不會接受使用者提供的 RU/秒值。 然後，更新完成後，您可以[變更 RU/秒](#can-i-change-the-max-rus-on-the-database-or-container)，以符合您的工作負載。 

**從標準 (手動) 佈建輸送量移轉至自動調整**

對於容器，使用下列公式來估計初始自動調整的最大 RU/秒：``MAX(4000, current manual provisioned RU/s, maximum RU/s ever provisioned / 10, storage in GB * 100)``，四捨五入到最接近的 1000 RU/秒。 實際的初始自動調整最大 RU/秒可能會根據您的帳戶設定而有所不同。

範例 #1：假設您有一個容器，其中有 10,000 RU/秒的手動佈建輸送量，以及 25 GB 的儲存體。 當您啟用自動調整時，初始自動調整最大 RU/秒為：10,000 RU/秒，將在 1000 - 10,000 RU/秒之間進行調整。 

範例 #2：假設您有一個容器，其中有 50,000 RU/秒的手動佈建輸送量，以及 2500 GB 的儲存體。 當您啟用自動調整時，初始自動調整最大 RU/秒為：250,000 RU/秒，將在 25,000 - 250,000 RU/秒之間進行調整。 

**從自動調整移轉至標準 (手動) 佈建輸送量**

初始手動佈建的輸送量會等於目前自動調整最大 RU/秒。 

範例：假設您有一個自動調整資料庫或容器，其最大 RU/秒為 20,000 RU/秒 (在 2000 - 20,000 RU/秒之間調整)。 當您更新為使用手動佈建的輸送量時，初始輸送量會是 20,000 RU/秒。 

### <a name="is-there-azure-cli-or-powershell-support-to-manage-databases-or-containers-with-autoscale"></a>是否有 Azure CLI 或 PowerShell 支援以自動調整來管理資料庫或容器？
目前您只能從 Azure 入口網站、.NET V3 SDK、Java V4 SDK 以及 Azure Resource Manager，使用自動調整來建立及管理資源。 尚未提供 Azure CLI、PowerShell 和其他 SDK 中的支援。

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>共用的輸送量資料庫是否支援自動調整？
是，共用的輸送量資料庫支援自動調整。 若要啟用此功能，請在建立資料庫時，選取自動調整和 [佈建輸送量] 選項。 

### <a name="what-is-the-number-of-allowed-containers-per-shared-throughput-database-when-autoscale-is-enabled"></a>啟用自動調整時，每個共用輸送量資料庫允許的容器數目為何？
Azure Cosmos DB 在共用輸送量資料庫中可強制執行最多 25 個容器，其適用於具有自動調整或標準 (手動) 輸送量的資料庫。 

### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>與每個最大 RU/秒選項相關聯的儲存體限制為何？  
每個最大 RU/秒的儲存體限制 (GB) 為：資料庫或容器的最大 RU/秒 / 100。 例如，如果最大 RU/秒為 20,000 RU/秒，則資源可以支援 200 GB 的儲存體。 如需可用的最大 RU/秒和儲存體選項，請參閱[自動調整限制](provision-throughput-autoscale.md#autoscale-limits)一文。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果超過與我的最大輸送量相關聯的儲存體限制，會發生什麼事？
如果超過與資料庫或容器的最大輸送量相關聯的儲存體限制，Azure Cosmos DB 會自動將最大輸送量增加到可支援該儲存體層級的第二高 RU/秒。

例如，如果您從 50,000 RU/秒的最大 RU/秒開始 (在 5000 - 50,000 RU/秒之間調整)，您最多可以儲存 500 GB 的資料。 如果超過 500 GB (例如，儲存體現在是 600 GB)，新的最大 RU/秒將會是 60,000 RU/秒 (在 6000 - 60,000 RU/秒之間調整)。

### <a name="can-i-change-the-max-rus-on-the-database-or-container"></a>我是否可以變更資料庫或容器上的最大 RU/秒？ 
是。 請參閱這篇[文章](how-to-provision-autoscale-throughput.md)，了解如何變更最大 RU/秒。 當您根據要求的值變更最大 RU/秒時，這可能是非同步作業，需要一些時間才能完成 (最多可能 4-6 小時，視選取的 RU/秒而定)

#### <a name="increasing-the-max-rus"></a>增加最大 RU/秒
當您傳送要求以增加最大 RU/秒 `Tmax` 時，根據選取的最大 RU/秒而定，服務會佈建更多資源，以支援更高的最大 RU/秒。 發生這種情況時，您現有的工作負載和作業不會受到影響。 系統會在先前的 `0.1*Tmax` 到 `Tmax` 之間，繼續調整您的資料庫或容器，直到 `0.1*Tmax_new` 到 `Tmax_new` 的新調整範圍準備就緒。

#### <a name="lowering-the-max-rus"></a>降低最大 RU/秒
當您降低最大 RU/秒時，您可以將其設為的最小值為：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，四捨五入到最接近的 1000 RU/秒。 

範例 #1：假設您有一個自動調整容器，其最大 RU/秒為 20,000 RU/秒 (在 2000 - 20,000 RU/秒之間調整)，儲存體為 50 GB。 您可以設定最大 RU/秒的最低、最小值為：MAX(4000, 20,000 / 10, **50 * 100**) = 5000 RU/秒 (在 500 - 5000 RU/秒之間調整)。

範例 #2：假設您有一個自動調整容器，其最大 RU/秒為 100,000 RU/秒，儲存體為 100 GB。 現在，您將最大 RU/秒擴大到N 150,000 RU/秒 (在 15,000 - 150,000 RU/秒之間調整)。 您現在可以設定最大 RU/秒的最低、最小值為：MAX(4000, **150,000 / 10**, 100 * 100) = 15,000 RU/秒 (在 1500 - 15,000 RU/秒之間調整)。 

對於共用的輸送量資料庫，當您降低最大 RU/秒時，您可以設定的最小值為：`MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，四捨五入到最接近的 1000 RU/秒。  

上述公式和範例涉及到您可以設定的最小自動調整 RU/秒，而且有別於 `0.1 * Tmax` 到 `Tmax` 的系統自動調整範圍。 無論最大 RU/秒為何，系統一律會在 `0.1 * Tmax` 到 `Tmax` 之間調整。 

### <a name="how-does-ttl-work-with-autoscale"></a>TTL 如何與自動調整搭配使用？
使用自動調整時，TTL 作業不會影響 RU/秒的縮放比例。 由於 TTL 而耗用的任何 RU 不屬於自動調整容器的計費 RU/秒。 

例如，假設您有一個 400 – 4000 RU/秒的自動調整容器：
- 第 1 小時：T=0：容器沒有使用量 (無 TTL 或工作負載要求)。 可計費的 RU/秒為 400 RU/秒。
- 第 1 小時：T=1：已啟用 TTL。
- 第 1 小時：T=2：容器開始取得要求，這會在 1 秒內耗用 1000 RU。 另外還會發生 200 RU 的 TTL。 可計費的 RU/秒仍為 1000 RU/秒。 無論 TTL 是在何時發生，都不會影響自動調整縮放比例邏輯。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒與實體分割區之間的對應為何？
當您第一次選取最大 RU/秒時，Azure Cosmos DB 將會佈建：最大 RU/秒 / 10,000 RU/秒 = 實體分割區數目。 每個[實體分割區](partition-data.md#physical-partitions)最多可支援 10,000 RU/秒和 50 GB 的儲存體。 隨著儲存體大小的增加，如果儲存體[超過相關限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，Azure Cosmos DB 會自動分割分割區，以新增更多的實體分割區來處理儲存體的增加，或增加最大 RU/秒。 

資料庫或容器的最大 RU/秒會平均分散到所有實體分割區。 因此，任何單一實體分割區可以調整的輸送量總計為：資料庫或容器的最大 RU/秒 / 實體分割區數目。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>當傳入要求超過資料庫或容器的最大 RU/秒時，會發生什麼事？
如果整體耗用的 RU/秒超過資料庫或容器的最大 RU/秒，則超過最大 RU/秒的要求會受到節流，並傳回 429 狀態碼。 導致超過 100% 標準化使用率的要求也會受到節流。 標準化使用率定義為所有實體分割區之間的 RU/秒使用率上限。 例如，假設您的最大輸送量是 20,000 RU/秒，而且您有兩個實體分割區，P_1 和 P_2，每個都能夠調整為 10,000 RU/秒。 在指定的秒數內，如果 P_1 已使用 6000 RU，P_2 已使用 8000 RU，則標準化使用率為 MAX(6000 RU / 10,000 RU，8000 RU / 10,000 RU) = 0.8。

> [!NOTE]
> Azure Cosmos DB 用戶端 SDK 和資料匯入工具 (Azure Data Factory，大量執行工具程式庫) 會在出現 429s 時自動重試，因此，偶爾出現 429s 也沒關係。 持續大量出現 429s 可能表示您需要增加最大 RU/秒，或查看[常用分割區](#autoscale-rate-limiting)的分割區策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a> 啟用自動調整時，是否仍可看見 429s (節流/速率限制)？ 
是。 在兩種情況下，您可能會看見 429s。 第一種情況是，當整體耗用的 RU/秒超過資料庫或容器的最大 RU/秒時，服務將會視情況節流要求。 

第二種情況是，如果有常用分割區 (亦即，邏輯分割區索引鍵值與其他分割區索引鍵值相比之下，要求量較高)，則基礎實體分割區可能超過其 RU/秒預算。 最佳做法是避免常用分割區，[選擇良好的分割區索引鍵](partitioning-overview.md#choose-partitionkey)，讓儲存體和輸送量能夠平均分佈。 

例如，如果您選取 20,000 RU/秒最大輸送量選項，並且有 200 GB 的儲存體，四個實體分割區，則每個實體分割區最多可自動調整至 5000 RU/秒。 如果特定邏輯分割區索引鍵上有常用分割區，當其所在的基礎實體分割區超過 5000 RU/秒時 (亦即超過 100% 的標準化使用率)，就會看到 429s。


## <a name="next-steps"></a>後續步驟

* 了解如何[在 Azure Cosmos DB 資料庫或容器上啟用自動調整](how-to-provision-autoscale-throughput.md)。
* 了解[使用自動調整佈建輸送量的好處](provision-throughput-autoscale.md#benefits-of-autoscale)。
* 深入了解[邏輯和實體分割區](partition-data.md)。
                        