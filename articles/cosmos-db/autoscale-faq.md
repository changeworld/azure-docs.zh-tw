---
title: 在 Azure Cosmos DB 中自動調整布建輸送量的常見問題
description: 關於自動調整已布建的 Azure Cosmos DB 資料庫和容器輸送量的常見問題
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791777"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>關於自動調整布建輸送量的常見問題 Azure Cosmos DB

透過自動調整布建的輸送量，Azure Cosmos DB 會根據使用量，自動管理及調整容器或資料庫的 RU/秒。 本文會回答關於自動調整的常見問題。

## <a name="frequently-asked-questions"></a>常見問題集

### <a name="is-autoscale-supported-for-all-apis"></a>所有 Api 都支援自動調整嗎？
是，所有 Api 都支援自動調整： Core （SQL）、Gremlin、Table、Cassandra 和適用于 MongoDB 的 API。

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>多宿主帳戶是否支援自動調整？
是，多宿主帳戶支援自動調整。 在新增至 Cosmos 帳戶的每個區域中，都可以使用最大 RU/秒。 

### <a name="what-is-the-pricing-for-autoscale-"></a>自動調整的定價為何？
如需詳細資訊，請參閱 Azure Cosmos DB[定價頁面](https://azure.microsoft.com/pricing/details/cosmos-db/)。 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>如何? 為我的容器或資料庫啟用自動調整規模？
您可以在使用 Azure 入口網站建立的新容器和資料庫上啟用自動調整。

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>是否有 CLI 或 SDK 支援，可以使用自動調整布建的輸送量來建立容器或資料庫？
目前，您只能使用 Azure 入口網站的自動調整布建輸送量來建立資源。 尚未提供 CLI 和 SDK 的支援。

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>我可以在現有的容器或資料庫上啟用自動調整嗎？
目前，您可以在建立新容器和資料庫時，啟用自動調整。 尚未提供在現有容器和資料庫上啟用自動調整的支援。 您可以使用[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)或[變更](change-feed.md)摘要，將現有容器遷移至新的容器。 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>我可以關閉容器或資料庫的自動調整嗎？
是，您可以藉由切換至布建輸送量的 [標準（手動）] 選項來關閉自動調整。 在目前版本中，從自動調整切換至標準布建輸送量之後，您就無法再次針對相同的資源啟用自動調整。 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>共用輸送量資料庫是否支援自動調整？
是，共用輸送量資料庫支援自動調整。 若要啟用這項功能，請在建立資料庫時選取 [自動調整] 和 [布建**輸送量**] 選項。 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>啟用自動調整時，每個共用輸送量資料庫允許的集合數目是多少？
對於已啟用自動調整的共用輸送量資料庫，允許的集合數目為： MIN （25，最大 RU/秒的資料庫/1000）。 例如，如果資料庫的最大輸送量是 20000 RU/秒，資料庫可以有 MIN （25，20000 RU/s/1000） = 20 個集合。 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>與每個最大 RU/秒選項相關聯的儲存體限制為何？  
每個最大 RU/秒的儲存體限制（GB）為：資料庫或容器/100 的最大 RU/秒。 例如，如果 RU/秒的最大值為 20000 RU/秒，則資源可以支援 200 GB 的儲存體。 如需可用的最大 RU/秒和儲存體選項，請參閱[自動調整限制](provision-throughput-autoscale.md#autoscale-limits)一文。 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>如果我超過我的最大輸送量相關聯的儲存體限制，會發生什麼事？
如果超過與資料庫或容器的最大輸送量相關聯的儲存體限制，Azure Cosmos DB 會自動將最大輸送量增加到可支援該儲存體層級的下一個最高層。 例如，假設資料庫或容器布建了 4000 RU/秒的最大輸送量選項，其儲存體限制為 50 GB。 如果資源的儲存體增加到 100 GB，則資料庫或容器的最大 RU/秒將會自動增加到 20000 RU/秒，最多可支援 200 GB。 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>根據流量尖峰，會如何快速相應增加和減少？
透過自動調整，您可以根據傳入流量，在最小和最大 RU/秒範圍內，立即相應增加或相應減少 RU/秒。 計費是以1小時的資料細微性完成，在這段時間內，您需支付一小時的最高 RU/秒費用。

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>我可以為自動調整指定自訂的最大輸送量（RU/秒）值嗎？
目前，您可以在[四個選項](provision-throughput-autoscale.md#autoscale-limits)中選取 [最大輸送量（RU/秒）]。

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>我可以在資料庫或容器上增加最大 RU/秒（移到較高的層級）嗎？ 
可以。 從您的容器的 [**調整 & 設定**] 選項或您的資料庫的 [**調整**] 選項，您可以針對 [自動調整] 選取較高的 RU/秒。 這是非同步相應增加作業，可能需要一些時間才能完成（通常是4-6 小時，視選取的 RU/秒而定），因為服務會布建更多資源以支援更高的規模。 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>可以在資料庫或容器上減少最大 RU/秒（移至較低層級）嗎？
可以。 只要資料庫或容器的目前儲存體低於與您想要相應減少的最大 RU/秒層相關聯的[儲存體限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)，您就可以將最大 ru/秒降到該階層。 例如，如果您選取了 20000 RU/秒做為最大 RU/秒，如果您的儲存體少於 50 GB （與 4000 RU/秒相關聯的儲存體限制），則可以將最大 RU/秒相應減少為 4000 RU/秒。

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>最大 RU/秒與實體分割區之間的對應為何？
當您第一次選取最大 RU/秒時，Azure Cosmos DB 將會布建：最大 RU/秒/10000 RU/秒 = # 個實體分割區。 每個[實體分割](partition-data.md#physical-partitions)區最多可支援 10000 RU/秒和 50 GB 的儲存空間。 隨著儲存體大小的增加，Azure Cosmos DB 會自動分割磁碟分割，以新增更多的實體分割區來處理儲存體的增加，或增加最大 RU/秒層（如果儲存體[超過相關聯的限制](#what-is-the-storage-limit-associated-with-each-max-rus-option)）。 

資料庫或容器的最大 RU/秒會平均分散到所有實體分割區。 因此，任何單一實體分割區可以調整的總輸送量為：資料庫或容器/# 實體磁碟分割的最大 RU/秒。 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>當連入要求超過資料庫或容器的最大 RU/秒時，會發生什麼事？
如果整體耗用的 RU/秒超過容器或資料庫的最大 RU/秒，則超過最大 RU/秒的要求會受到節流，並傳回429狀態碼。 導致超過100% 正規化使用率的要求也會進行節流。 正規化使用率會定義為所有實體分割區的 RU/秒使用率上限。 例如，假設您的最大輸送量為 20000 RU/秒，而且您有兩個實體分割區，P_1 和 P_2，每個都能夠調整為 10000 RU/秒。 在給定的秒中，如果 P_1 使用 6000 ru，而 P_2 8000 ru，則正規化的使用率為 MAX （6000 RU/10000 RU，8000 RU/10000 RU） = 0.8。

> [!NOTE]
> Azure Cosmos DB 用戶端 Sdk 和資料匯入工具（Azure Data Factory、大量執行程式程式庫）會在429s 上自動重試，因此偶爾的429s 就沒問題。 持續大量的429s 可能表示您需要增加 RU/秒的最大值，或針對[熱資料分割](#autoscale-rate-limiting)審查資料分割策略。

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>啟用自動調整時，仍然可以看到429s （節流/速率限制）嗎？ 
可以。 在兩種情況下，您可能會看到429s。 第一，當整體取用的 RU/秒超過容器或資料庫的最大 RU/秒時，服務會據以節流要求。 

第二，如果有熱資料分割（亦即，相較于其他分割區索引鍵值的要求數量過多的邏輯分割區索引鍵值），則基礎實體分割區可能會超過其 RU/秒的預算。 最佳做法是避免經常性資料[分割，選擇良好](partitioning-overview.md#choose-partitionkey)的資料分割索引鍵，以平均分配儲存體和輸送量。 

例如，如果您選取 [20000 RU/秒的最大輸送量] 選項，而且200有4個實體分割區，則每個實體磁碟分割最多可以自動縮放 5000 RU/秒。 如果特定邏輯分割區索引鍵上有熱磁碟分割，當其所在的基礎實體分割區超過 5000 RU/秒時，即會看到429s，亦即超過100% 的正規化使用率。

## <a name="next-steps"></a>後續步驟

* 瞭解如何[在 Azure Cosmos 容器或資料庫上啟用自動](provision-throughput-autoscale.md#create-db-container-autoscale)調整。
* 瞭解[使用自動調整布建輸送量的優點](provision-throughput-autoscale.md#autoscale-benefits)。
* 深入瞭解[邏輯和實體](partition-data.md)分割區。
