---
title: 有關 Azure 宇宙 DB 自動駕駛模式下輸送量的常見問題
description: 有關 Azure Cosmos 資料庫和容器的自動駕駛模式的常見問題
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483306"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>有關 Azure Cosmos DB 自動駕駛模式中預配輸送量的常見問題（預覽）
使用自動駕駛模式，Azure Cosmos DB 將根據使用方式自動管理和縮放容器或資料庫的 RU/s。 本文回答了有關自動駕駛模式的常見問題。 

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-autopilot-mode-supported-for-all-apis"></a>是否支援所有 API 的自動駕駛模式？
是的，支援所有 API 的自動駕駛模式：蒙戈DB的核心 （SQL）、格雷姆林、表、Cassandra 和 API。

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>是否支援多主帳戶的自動駕駛模式？
是，多主帳戶支援自動駕駛模式。 最大 RU/s 在添加到 Cosmos 帳戶的每個區域都可用。 

### <a name="what-is-the-pricing-for-autopilot"></a>自動駕駛儀的定價是多少？
有關詳細資訊，請參閱 Azure 宇宙資料庫[定價頁](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>如何為容器或資料庫啟用自動駕駛儀？
可以在使用 Azure 門戶創建的新容器和資料庫上啟用自動駕駛模式。 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>是否有 CLI 或 SDK 支援使用自動駕駛模式創建容器或資料庫？
目前，在預覽版本中，只能從 Azure 門戶創建具有自動駕駛模式的資源。 尚未提供對 CLI 和 SDK 的支援。

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>我可以在現有容器或資料庫上啟用自動駕駛儀嗎？
目前，您可以在創建新容器和資料庫時啟用自動駕駛儀。 在現有容器和資料庫上啟用自動駕駛模式的支援尚不可用。 可以使用[Azure 資料工廠](../data-factory/connector-azure-cosmos-db.md)將現有容器遷移到新容器或[更改源](change-feed.md)。 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>是否可以關閉容器或資料庫上的自動駕駛模式？
可以，您可以通過切換到預配輸送量的"手動"選項來關閉自動駕駛儀。 在預覽版本中，在從自動駕駛模式切換到手動模式後，不能為同一資源再次啟用自動駕駛儀。 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>共用輸送量資料庫是否支援自動駕駛模式？
是，共用輸送量資料庫支援自動駕駛模式。 要啟用此功能，請在創建資料庫時選擇自動駕駛模式和**預配輸送量**選項。 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>啟用自動駕駛儀時，每個共用輸送量資料庫允許的集合數是多少？
對於啟用了自動駕駛模式的共用輸送量資料庫，允許的集合數為：MIN（25，資料庫的最大 RU/s / 1000）。 例如，如果資料庫的最大輸送量為 20，000 RU/s，則資料庫可以具有 MIN（25，20，000 RU/s / 1000） = 20 個集合。 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>與每個最大 RU/s 選項關聯的存儲限制是多少？  
每個最大 RU/s 的存儲限制（以 GB 表示）：資料庫或容器的最大 RU/秒 / 100。 例如，如果最大 RU/s 為 20，000 RU/s，則資源可以支援 200 GB 的存儲。 有關可用的最大 RU/s 和存儲選項，請參閱[自動駕駛儀限制](provision-throughput-autopilot.md#autopilot-limits)文章。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果我超過與最大輸送量關聯的存儲限制，會發生什麼情況？
如果超過與資料庫或容器的最大輸送量關聯的存儲限制，Azure Cosmos DB 將自動將最大輸送量增加到支援該存儲級別的下一個最高層。 例如，假設資料庫或容器預配了 4000 RU/s 最大輸送量選項，該選項的存儲限制為 50 GB。 如果資源的存儲增加到 100 GB，則資料庫或容器的最大 RU/s 將自動增加到 20，000 RU/s，這最多可以支援 200 GB。 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>自動駕駛儀會根據流量峰值進行上下擴展的速度？
自動駕駛儀將根據傳入流量在最小和最大 RU/s 範圍內即時擴展或縮小 RU/s。 計費以 1 小時的細微性完成，其中在特定小時內向您收取最高的 RU/s 的費用。 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>是否可以為自動駕駛模式指定自訂最大輸送量 （RU/s） 值？
目前，在預覽版期間，您可以在[四個選項](provision-throughput-autopilot.md#autopilot-limits)之間進行最大輸送量 （RU/s） 選擇。

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>是否可以增加資料庫或容器上的最大 RU/s（移動到更高層）？ 
是。 從容器**的"縮放&設置"** 選項或資料庫**的"縮放**"選項中，可以為自動駕駛儀選擇更高的最大 RU/秒。 這是一個非同步擴展操作，可能需要一些時間才能完成（通常需要 4-6 小時，具體取決於所選 RU/s），因為服務會提供更多資源來支援更高規模。 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>是否可以降低資料庫或容器上的最大 RU/s（移動到較低層）？
是。 只要資料庫或容器的當前存儲低於與要縮減的最大 RU/s 層關聯的[存儲限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，就可以將最大 RU/s 減少到該層。 例如，如果選擇了 20，000 RU/s 作為最大 RU/s，則可以將最大 RU/s 縮減到 4000 RU/s（如果存儲容量小於 50 GB（與 4000 RU/s 關聯的存儲限制）。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/s 和物理分區之間的映射是什麼？
首次選擇最大 RU/s 時，Azure Cosmos DB 將預配：物理分區的最大 RU/s / 10，000 RU/s = = 。 每個[物理分區](partition-data.md#physical-partitions)最多支援 10，000 RU/s 和 50 GB 存儲。 隨著存儲大小的增加，Azure Cosmos DB 將自動拆分分區以添加更多物理分區來處理存儲增加，或者如果存儲[超過關聯限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，則增加最大 RU/s 層。 

資料庫或容器的 Max RU/s 在所有物理分區之間平均分配。 因此，任何單個物理分區可以縮放的總輸送量是：資料庫或容器的最大 RU/s / = 物理分區。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>如果傳入請求超過資料庫或容器的最大 RU/秒，會發生什麼情況？
如果總消耗的 RU/s 超過容器或資料庫的最大 RU/秒，則超過最大 RU/s 的請求將被限制並返回 429 狀態碼。 導致超過 100% 正常化利用率的請求也將受到限制。 正常化利用率定義為所有物理分區的 RU/s 利用率最大值。 例如，假設您的最大輸送量為 20，000 RU/s，並且您有兩個物理分區，P_1和P_2，每個分區能夠擴展到 10，000 RU/s。 在給定的秒中，如果P_1使用了 6000 RU，並且P_2 8000 RU，則標準化利用率為 MAX（6000 RU / 10，000 RU，8000 RU / 10，000 RU） = 0.8。

> [!NOTE]
> Azure Cosmos DB 用戶端 SDK 和資料導入工具（Azure 資料工廠、批量執行器庫）會自動重試 429s，因此偶爾 429s 可以。 持續高的 429s 可能表示您需要增加最大 RU/s 或查看[熱分區](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)的分區策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>啟用自動駕駛儀時仍可以看到 429s（限制/速率限制）嗎？ 
是。 可以在兩種情況下看到 429。 首先，當總消耗的 RU/s 超過容器或資料庫的最大 RU/秒時，服務將相應地限制請求。 

其次，如果存在熱分區，即邏輯分區鍵值比其他分區鍵值具有不成比例更高的請求量，則基礎物理分區可能超過其 RU/s 預算。 最佳做法是，為了避免熱分區，[請選擇一個好的分區鍵](partitioning-overview.md#choose-partitionkey)，從而實現存儲和輸送量的均勻分佈。 

例如，如果選擇 20，000 RU/s 最大輸送量選項，並且具有 200 GB 的存儲空間，並且具有四個物理分區，則每個物理分區可以自動縮放至 5000 RU/s。 如果特定邏輯分區鍵上有熱分區，則當其駐留在基礎物理分區超過 5000 RU/s（即超過 100% 正常化利用率）時，您將看到 429s。

## <a name="next-steps"></a>後續步驟

* 瞭解如何在[Azure Cosmos 容器或資料庫上啟用自動駕駛儀](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode)。
* 瞭解[自動駕駛儀的好處](provision-throughput-autopilot.md#benefits-of-autopilot-mode)。
* 瞭解有關[邏輯分區和物理分區](partition-data.md)的更多詳細資訊。
