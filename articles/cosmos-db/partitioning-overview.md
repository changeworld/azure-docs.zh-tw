---
title: Azure Cosmos DB 中的資料分割和水平調整
description: 瞭解 Azure Cosmos DB 中的分割、邏輯、實體分割區、選擇資料分割索引鍵時的最佳作法，以及如何管理邏輯分割區
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/12/2020
ms.openlocfilehash: 353abe5ac55e49e01f6a99f72307b8525a72fc00
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281123"
---
# <a name="partitioning-and-horizontal-scaling-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割和水平調整

Azure Cosmos DB 使用資料分割來調整資料庫中的個別容器，以符合您應用程式的效能需求。 在資料分割中，容器中的專案會分割成不同的子集，稱為 *邏輯*分割區。 邏輯分割區是根據與容器中每個專案相關聯的分割區索引 *鍵* 值所組成。 邏輯分割區中的所有專案都有相同的資料分割索引鍵值。

例如，容器會保存專案。 每個專案都有屬性的唯一值 `UserID` 。 如果 `UserID` 作為容器中專案的分割區索引鍵，而且有1000個唯一 `UserID` 值，就會為容器建立1000個邏輯分割區。

除了決定專案邏輯分割區的資料分割索引鍵之外，容器中的每個專案在邏輯分割區) 內都有唯一 (的 *專案識別碼* 。 結合資料分割索引鍵和 *專案識別碼* 會建立可唯一識別專案的專案 *索引*。 [選擇資料分割索引鍵](#choose-partitionkey) 是一項重要決策，會影響您的應用程式效能。

本文說明邏輯與實體分割區之間的關聯性。 它也會討論資料分割的最佳做法，並深入瞭解水準調整在 Azure Cosmos DB 中的運作方式。 您不需要瞭解這些內部詳細資料來選取您的分割區索引鍵，但我們已加以涵蓋，讓您清楚瞭解 Azure Cosmos DB 的運作方式。

## <a name="logical-partitions"></a>邏輯分割區

邏輯分割區是由一組具有相同分割區索引鍵的專案所組成。 例如，在包含食物營養相關資料的容器中，所有專案都包含一個 `foodGroup` 屬性。 您可以使用 `foodGroup` 做為容器的分割區索引鍵。 具有特定值的專案群組 `foodGroup` ，例如 `Beef Products` 、 `Baked Products` 和 `Sausages and Luncheon Meats` ，形成不同的邏輯分割區。 刪除基礎資料時，您不需要擔心刪除邏輯分割區。

邏輯資料分割也會定義資料庫交易的範圍。 您可以使用 [具有快照集隔離的交易](database-transactions-optimistic-concurrency.md)來更新邏輯資料分割內的專案。 將新專案新增至容器時，系統會以透明的方式建立新的邏輯分割區。

您容器中的邏輯分割區數目沒有任何限制。 每個邏輯分割區最多可儲存 20 gb 的資料。 良好的分割區索引鍵選項有各式各樣的可能值。 例如，在所有專案都包含屬性的容器中 `foodGroup` ，邏輯資料分割內的資料 `Beef Products` 最多可成長到 20 GB。 [選取](#choose-partitionkey) 具有各種可能值的分割區索引鍵，可確保容器能夠調整規模。

## <a name="physical-partitions"></a>實體分割區

容器的調整方式是在實體分割區之間分配資料和輸送量。 就內部而言，會有一或多個邏輯分割區對應至單一實體分割區。 通常較小的容器具有許多邏輯分割區，但它們只需要單一實體分割區。 與邏輯分割區不同的是，實體分割區是系統的內部執行，而且完全由 Azure Cosmos DB 管理。

您容器中的實體分割區數目取決於下列設定：

*  (每個個別實體分割區布建的輸送量數目，最多可提供每秒10000個要求單位的輸送量) 。
* 每個個別實體資料分割 (的資料儲存體總數最多可儲存50GB 的資料) 。

您容器中的實體磁碟分割總數沒有限制。 當您布建的輸送量或資料大小增加時，Azure Cosmos DB 將會分割現有的實體分割區，以自動建立新的實體分割區。 實體分割區分割不會影響應用程式的可用性。 分割實體分割區之後，單一邏輯分割區內的所有資料仍會儲存在相同的實體分割區上。 實體分割區分割只會建立邏輯分割區與實體分割區的新對應。

針對容器布建的輸送量會平均分配給實體分割區。 未平均分散要求的資料分割索引鍵設計可能會導致太多要求被導向至變成「經常性存取」的一小部分資料分割。 經常性存取分割區可讓布建的輸送量使用效率不佳，這可能會導致速率限制和更高的成本。

您可以在 Azure 入口網站的 [**計量**] 分頁的 [**儲存體**] 區段中，看到容器的實體分割區：

:::image type="content" source="./media/partitioning-overview/view-partitions-zoomed-out.png" alt-text="查看實體分割區的數目" lightbox="./media/partitioning-overview/view-partitions-zoomed-in.png" ::: 

在上述螢幕擷取畫面中，容器具有 `/foodGroup` 分割區索引鍵。 圖形中的三個橫條都代表一個實體資料分割。 在映射中，資料 **分割索引鍵範圍** 與實體分割區相同。 選取的實體分割區包含三個邏輯分割區： `Beef Products` 、 `Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 。

如果您布建每秒18000個要求單位的輸送量 (RU/s) ，則這三個實體分割區中的每一個都可以使用總布建輸送量的1/3。 在選取的實體分割區內，邏輯分割區索引鍵 `Beef Products` 、 `Vegetable and Vegetable Products` 和 `Soups, Sauces, and Gravies` 可以統稱為使用實體資料分割的6000布建 RU/秒。 因為布建的輸送量會平均分配在容器的實體分割區，所以請務必選擇 [正確的邏輯分割](#choose-partitionkey)區索引鍵，以平均分配輸送量耗用量的資料分割索引鍵。 

> [!NOTE]
> 如果您選擇將輸送量耗用量平均分配給邏輯分割區的分割區索引鍵，您將可確保跨實體分割區的輸送量耗用量已平衡。

## <a name="managing-logical-partitions"></a>管理邏輯磁碟分割

Azure Cosmos DB 以透明的方式自動管理實體分割區上的邏輯分割區位置，以有效率地滿足容器的擴充性和效能需求。 隨著應用程式的輸送量和儲存體需求增加，Azure Cosmos DB 會移動邏輯分割區，以自動將負載分散到更多的實體分割區。 您可以深入瞭解 [實體](partitioning-overview.md#physical-partitions)資料分割。

Azure Cosmos DB 會使用雜湊式資料分割，將邏輯分割區分散到多個實體分割區。 Azure Cosmos DB 雜湊專案的資料分割索引鍵值。 雜湊的結果會決定實體資料分割。 然後，Azure Cosmos DB 會在實體分割區平均配置分割區索引鍵雜湊的索引鍵空間。

在預存程式或觸發程式) 中 (的交易只能針對單一邏輯分割區中的專案進行。

您可以深入瞭解 [Azure Cosmos DB 管理](partitioning-overview.md)資料分割的方式。  (不需要瞭解建立或執行應用程式的內部詳細資料，但在這裡新增給好奇的讀者。 ) 

## <a name="replica-sets"></a>複本集

每個實體分割區都是由一組複本（也稱為 [*複本集*](global-dist-under-the-hood.md)）所組成。 每個複本集都會裝載資料庫引擎的實例。 複本集會讓儲存在實體資料分割內的資料持久、高度可用且一致。 組成實體分割區的每個複本都會繼承磁碟分割的儲存配額。 實體分割區的所有複本會共同支援配置給實體分割區的輸送量。 Azure Cosmos DB 會自動管理複本集。

通常較小的容器只需要單一實體分割區，但它們仍會有至少4個複本。

下圖顯示如何將邏輯分割區對應至全域散發的實體分割區：

:::image type="content" source="./media/partitioning-overview/logical-partitions.png" alt-text="查看實體分割區的數目" border="false":::

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>選擇分割區索引鍵

分割區索引鍵有兩個元件：資料 **分割索引鍵路徑** 和資料 **分割索引鍵值**。 例如，假設有一個專案 {"userId"： "Andrew"，"worksFor"： "Microsoft"} 如果您選擇 "userId" 作為分割區索引鍵，則下列兩個數據分割索引鍵元件如下：

* 分割區索引鍵路徑 (例如： "/userId" ) 。 分割區索引鍵路徑接受英數位元和底線 (_) 個字元。 您也可以使用標準路徑標記法 (/) 來使用嵌套物件。

* 分割區索引鍵值 (例如： "Andrew" ) 。 分割區索引鍵值可以是字串或數數值型別。

若要瞭解輸送量、儲存體和分割區索引鍵長度的限制，請參閱 [Azure Cosmos DB 服務配額](concepts-limits.md) 」一文。

選取分割區索引鍵是 Azure Cosmos DB 中簡單但重要的設計選擇。 當您選取分割區索引鍵之後，就無法就地變更。 如果您需要變更分割區索引鍵，則應該將您的資料移至新的容器，並使用新的所需分割區索引鍵。

對於 **所有** 容器而言，您的分割區索引鍵應該：

* 是具有不會變更之值的屬性。 如果屬性是您的分割區索引鍵，則無法更新該屬性的值。

* 具有較高的基數。 換句話說，屬性應該有各式各樣的可能值。

* 將要求單位 (RU 平均分配給所有邏輯分割區) 耗用量和資料儲存體。 這可確保甚至是在實體分割區上進行 RU 耗用量和儲存體散發。

如果您在 Azure Cosmos DB 中需要 [多專案 ACID 交易](database-transactions-optimistic-concurrency.md#multi-item-transactions) ，您必須使用 [預存程式或觸發程式](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)。 所有以 JavaScript 為基礎的預存程式和觸發程式的範圍都是單一邏輯分割區。

## <a name="partition-keys-for-read-heavy-containers"></a>大量讀取容器的資料分割索引鍵

對於大部分的容器而言，在挑選分割區索引鍵時，您只需要考慮上述準則。 不過，對於大型的大量讀取容器，您可能會想要選擇經常在查詢中顯示為篩選的資料分割索引鍵。 藉由在篩選述詞中包含分割區索引鍵，即可 [有效率地將查詢路由傳送至相關的實體](how-to-query-container.md#in-partition-query) 分割區。

如果您大部分的工作負載要求都是查詢，且大部分的查詢都有相同屬性的等號比較篩選，則這個屬性可以是不錯的資料分割索引鍵選擇。 例如，如果您經常執行篩選的查詢 `UserID` ，然後選取 `UserID` 做為分割區索引鍵，將會減少跨資料 [分割查詢](how-to-query-container.md#avoiding-cross-partition-queries)的數目。

但是，如果您的容器很小，您可能沒有足夠的實體分割區需要擔心跨資料分割查詢的效能影響。 Azure Cosmos DB 中的最小容器只需要一或兩個實體分割區。

如果您的容器可以成長到多個實體分割區，則您應該確定挑選分割區索引鍵，以最小化跨分割區查詢。 當下列任一項為真時，您的容器將需要一個以上的實體分割區：

* 您的容器會布建超過 30000 RU

* 您的容器將儲存超過 100 GB 的資料

## <a name="using-item-id-as-the-partition-key"></a>使用專案識別碼作為分割區索引鍵

如果您的容器具有的屬性具有各式各樣的可能值，則可能是很棒的資料分割索引鍵選擇。 這類屬性的其中一個可能範例是 *專案識別碼*。 對於小型的大量讀取容器或任何大小的大量寫入容器而言， *專案識別碼* 自然是分割區索引鍵的絕佳選擇。

您容器中的每個專案都有系統屬性 *專案識別碼* 。 您可以有其他屬性，代表專案的邏輯識別碼。 在許多情況下，這些也是絕佳的資料分割索引鍵選擇，原因與 *專案識別碼*相同。

*專案識別碼*是很棒的資料分割索引鍵選擇，原因如下：

* 有各種可能的值 (每個專案) 一個唯一的 *專案識別碼* 。
* 因為每個專案都有唯一的 *專案識別碼* ，所以 *專案識別碼* 會進行平均平衡 RU 耗用量和資料儲存體的絕佳作業。
* 您可以輕鬆地進行有效的點讀取，因為如果您知道專案的 *專案識別碼*，一律會知道專案的分割區索引鍵。

選取 *專案識別碼* 作為分割區索引鍵時，應考慮的一些事項包括：

* 如果 *專案識別碼* 是分割區索引鍵，它會成為整個容器的唯一識別碼。 您將無法擁有具有重複 *專案識別碼*的專案。
* 如果您有大量大量 [實體](partitioning-overview.md#physical-partitions)資料分割的大量讀取容器，則如果查詢具有具有 *專案識別碼*的等號比較篩選準則，則查詢會更有效率。
* 您無法跨多個邏輯分割區執行預存程式或觸發程式。

## <a name="next-steps"></a>後續步驟

* 瞭解 [Azure Cosmos DB 中布建的輸送量](request-units.md)。
* 瞭解 [Azure Cosmos DB 中的全域散發](distribute-data-globally.md)。
* 了解如何[在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)。
* 了解如何[在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)。
