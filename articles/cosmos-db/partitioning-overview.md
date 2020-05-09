---
title: Azure Cosmos DB 中的資料分割
description: 瞭解 Azure Cosmos DB 中的資料分割、選擇資料分割索引鍵時的最佳作法，以及如何管理邏輯分割區
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: a9368e67abf3c45981cf1f85fe46a2a2799a6877
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864329"
---
# <a name="partitioning-in-azure-cosmos-db"></a>Azure Cosmos DB 中的資料分割

Azure Cosmos DB 使用資料分割來調整資料庫中的個別容器，以符合您應用程式的效能需求。 在資料分割中，容器中的專案會分割成不同的子集，稱為*邏輯*分割區。 邏輯分割區是根據與容器中每個專案相關聯的資料*分割索引鍵*的值來組成。 邏輯分割區中的所有專案都具有相同的資料分割索引鍵值。

例如，容器會保留專案。 每個專案都有`UserID`屬性的唯一值。 如果`UserID`作為容器中專案的分割區索引鍵，而且有1000個唯一`UserID`值，則會為容器建立1000個邏輯分割區。

除了用來決定專案邏輯分割區的分割區索引鍵之外，容器中的每個專案都有一個*專案識別碼*（在邏輯分割區內是唯一的）。 結合資料分割索引鍵和*專案識別碼*會建立專案的*索引*，以唯一識別該專案。

[選擇分割](partitioning-overview.md#choose-partitionkey)區索引鍵是一項重要決策，會影響應用程式的效能。

## <a name="managing-logical-partitions"></a>管理邏輯分割區

Azure Cosmos DB 透明且自動地管理實體分割區上邏輯分割區的位置，以有效率地滿足容器的擴充性和效能需求。 隨著應用程式的輸送量和儲存體需求增加，Azure Cosmos DB 會移動邏輯分割區，以自動將負載分散到更多的實體分割區上。 您可以深入瞭解[實體](partition-data.md#physical-partitions)分割區。

Azure Cosmos DB 使用以雜湊為基礎的資料分割，將邏輯磁碟分割分散到實體資料分割。 Azure Cosmos DB 會雜湊專案的分割區索引鍵值。 雜湊的結果會決定實體分割區。 然後，Azure Cosmos DB 會將分割區索引鍵雜湊的索引鍵空間平均分配給實體磁碟分割。

只有單一邏輯分割區中的專案可以使用交易（在預存程式或觸發程式中）。

您可以深入瞭解[Azure Cosmos DB 如何管理](partition-data.md)資料分割。 （您不需要瞭解內部詳細資料來建立或執行您的應用程式，但在這裡為好奇的讀者加入）。

## <a name="choosing-a-partition-key"></a><a id="choose-partitionkey"></a>選擇分割區索引鍵

選取資料分割索引鍵是 Azure Cosmos DB 中簡單但重要的設計選擇。 一旦您選取資料分割索引鍵，就無法就地變更。 如果您需要變更分割區索引鍵，您應該使用新的所需的分割區索引鍵，將您的資料移至新的容器。

針對**所有**容器，您的資料分割索引鍵應該：

* 屬性，其值不會變更。 如果屬性是您的分割區索引鍵，您就無法更新該屬性的值。
* 具有較高的基數。 換句話說，屬性應該有範圍廣泛的可能值。
* 將要求單位（RU）耗用量和資料儲存體平均分散到所有邏輯分割區。 這可確保每個實體磁碟分割上的 RU 耗用量和儲存體散發。

如果您在 Azure Cosmos DB 中需要[多個專案的 ACID 交易](database-transactions-optimistic-concurrency.md#multi-item-transactions)，您將需要使用[預存程式或觸發程式](how-to-write-stored-procedures-triggers-udfs.md#stored-procedures)。 所有以 JavaScript 為基礎的預存程式和觸發程式的範圍都是單一邏輯分割區。

## <a name="partition-keys-for-read-heavy-containers"></a>大量讀取容器的分割區索引鍵

對於大部分的容器而言，在挑選分割區索引鍵時，您只需要考慮上述準則。 不過，對於大型的大量讀取容器，您可能會想要在查詢中選擇經常顯示為篩選的分割區索引鍵。 查詢可以透過在篩選器述詞中包含分割區索引鍵，[有效地路由傳送至相關的實體](how-to-query-container.md#in-partition-query)分割區。

如果大部分的工作負載要求都是查詢，而且大部分的查詢在相同的屬性上都有相等的篩選準則，則此屬性可以是不錯的資料分割索引鍵選擇。 例如，如果您經常執行篩選的查詢`UserID`，然後選取`UserID`做為分割區索引鍵，就會減少[跨分割區查詢](how-to-query-container.md#avoiding-cross-partition-queries)的數目。

不過，如果您的容器很小，您可能沒有足夠的實體分割區，而需要擔心跨分割區查詢的效能影響。 Azure Cosmos DB 中的大部分小型容器只需要一個或兩個實體分割區。

如果您的容器可能會成長到多個實體分割區，則您應該確定挑選分割區索引鍵，以最小化跨分割區查詢。 當下列其中一項為真時，您的容器將需要多個實體分割區：

* 您的容器將會布建超過 30000 RU
* 您的容器將會儲存超過 100 GB 的資料

## <a name="using-item-id-as-the-partition-key"></a>使用專案識別碼做為分割區索引鍵

如果您的容器具有具有各種可能值的屬性，可能是很好的分割區索引鍵選擇。 這類屬性的其中一個可能範例就是*專案識別碼*。 對於較小的大量讀取容器或任何大小的大量寫入容器，*專案識別碼*自然是資料分割索引鍵的絕佳選擇。

系統屬性*專案識別碼*保證存在於您 Cosmos 容器中的每個專案。 您可以有其他屬性，代表專案的邏輯識別碼。 在許多情況下，這些都是很好的資料分割索引鍵選擇，原因與*專案識別碼*相同。

*專案識別碼*是很好的分割區索引鍵選擇，原因如下：

* 有各種可能的值（每個專案一個唯一的*專案識別碼*）。
* 因為每個專案都有唯一的*專案識別碼*，所以*專案識別碼*會執行相當大的作業，以平均平衡 RU 耗用量和資料存放區。
* 您可以輕鬆地執行有效的點讀取，因為如果您知道專案的*專案識別碼*，一律會知道它的分割區索引鍵。

選取*專案識別碼*做為分割區索引鍵時要考慮的事項包括：

* 如果*專案*識別碼是資料分割索引鍵，它會成為整個容器中的唯一識別碼。 您將無法擁有具有重複*專案識別碼*的專案。
* 如果您有大量[實體](partition-data.md#physical-partitions)分割區的讀取繁重容器，當查詢具有具有*專案識別碼*的等號比較篩選器時，將會更有效率。
* 您無法跨多個邏輯分割區執行預存程式或觸發程式。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[Azure Cosmos DB 中的資料分割和水準調整](partition-data.md)。
* 瞭解[Azure Cosmos DB 中布建的輸送量](request-units.md)。
* 深入瞭解[Azure Cosmos DB 中的全域散發](distribute-data-globally.md)。
