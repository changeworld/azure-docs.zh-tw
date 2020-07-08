---
title: 在 Azure Cosmos DB 中使用資料庫、容器和專案
description: 本文說明如何在 Azure Cosmos DB 中建立和使用資料庫、容器和專案。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/24/2020
ms.reviewer: sngun
ms.openlocfilehash: 257d7a2e374867f6ff14aeaa633d95521b7ca39e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85114753"
---
# <a name="work-with-databases-containers-and-items-in-azure-cosmos-db"></a>在 Azure Cosmos DB 中使用資料庫、容器和專案

在您的 Azure 訂用帳戶下建立[Azure Cosmos DB 帳戶](account-overview.md)之後，您可以藉由建立資料庫、容器和專案來管理您帳戶中的資料。 本文會說明每個實體。 

下圖顯示 Azure Cosmos DB 帳戶中不同實體的階層：

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos 帳戶項目" border="false":::

## <a name="azure-cosmos-databases"></a>Azure Cosmos 資料庫

您可以在您的帳戶下建立一或多個 Azure Cosmos 資料庫。 資料庫類似于命名空間。 資料庫是一組 Azure Cosmos 容器的管理單位。 下表說明 Azure Cosmos 資料庫如何對應至各種 API 特定實體：

| Azure Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 資料庫 | 資料庫 | keyspace | 資料庫 | 資料庫 | NA |

> [!NOTE]
> 使用資料表 API 帳戶，當您建立第一個資料表時，系統會自動在您的 Azure Cosmos 帳戶中建立預設的資料庫。

### <a name="operations-on-an-azure-cosmos-database"></a>Azure Cosmos 資料庫中的作業

您可以使用 Azure Cosmos Api 與 Azure Cosmos 資料庫互動，如下表所述：

| 操作 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
|列舉所有資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | Yes | NA | NA |
|讀取資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | Yes | NA | NA |
|建立新的資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | Yes | NA | NA |
|更新資料庫| 是 | 是 | 是 (資料庫會對應至 keyspace) | Yes | NA | NA |


## <a name="azure-cosmos-containers"></a>Azure Cosmos 容器

Azure Cosmos 容器是布建的輸送量和儲存體的擴充性單位。 容器會以水平方式分割，然後跨多個區域複寫。 您新增至容器的項目和您在容器上佈建的輸送量，會自動依據分割區索引鍵分布在一組邏輯分割區之間。 若要深入瞭解分割區和分割區索引鍵，請參閱[分割區資料](partition-data.md)。 

當您建立 Azure Cosmos 容器時，您可以使用下列其中一種模式來設定輸送量：

* **專用布建的輸送量模式**：在容器上布建的輸送量是專為該容器保留，並受到 sla 的支援。 若要深入瞭解，請參閱[如何在 Azure Cosmos 容器上](how-to-provision-container-throughput.md)布建輸送量。

* **共用布建的輸送量模式**：這些容器會與相同資料庫中的其他容器共用布建的輸送量（不包括已設定專用布建輸送量的容器）。 換句話說，在資料庫上布建的輸送量會在所有「共用輸送量」容器之間共用。 若要深入瞭解，請參閱[如何在 Azure Cosmos 資料庫上](how-to-provision-database-throughput.md)布建輸送量。

> [!NOTE]
> 只有在建立資料庫和容器時，才可以設定共用和專用輸送量。 若要在建立容器後從專用輸送量模式切換為共用輸送量模式 (反之亦然)，您必須建立新的容器，並將資料遷移至新的容器。 您可以使用 Azure Cosmos DB 變更摘要] 功能來遷移資料。

不論您是使用專用或共用布建的輸送量模式來建立容器，Azure Cosmos 容器都可以調整彈性。

Azure Cosmos 容器是與結構描述無關的項目容器。 容器中的專案可以有任意架構。 例如，代表人員的專案和代表汽車的專案可以放在*相同的容器*中。 根據預設，您新增至容器的所有專案都會自動編制索引，而不需要明確的索引或架構管理。 您可以藉由在容器上設定[編制索引原則](index-overview.md)來自訂索引行為。 

您可以在 Azure Cosmos 容器中的選取專案上設定[存留時間（TTL）](time-to-live.md) ，或針對整個容器，以適當的方式從系統中清除這些專案。 Azure Cosmos DB 會在專案過期時自動予以刪除。 它也保證在容器上執行的查詢不會傳回固定系結內已過期的專案。 若要深入瞭解，請參閱在[您的容器上設定 TTL](how-to-time-to-live.md)。

您可以使用[變更](change-feed.md)摘要來訂閱針對容器的每個邏輯分割區所管理的作業記錄。 變更摘要會提供在容器上執行之所有更新的記錄，以及專案的前後影像。 如需詳細資訊，請參閱[使用變更摘要來建立回應式應用程式](serverless-computing-database.md)。 您也可以使用容器上的變更摘要原則，設定變更摘要的保留期間。

您可以為您的 Azure Cosmos 容器註冊[預存程式、觸發程式、使用者定義函數（udf）](stored-procedures-triggers-udfs.md)和[合併程式](how-to-manage-conflicts.md)。

您可以在 Azure Cosmos 容器上指定[唯一索引鍵條件約束](unique-keys.md)。 您可以建立唯一索引鍵原則，以確保每個邏輯分割區索引鍵一或多個值的唯一性。 如果您使用唯一索引鍵原則建立容器，則可以建立沒有新的或已更新的專案，其值會複製唯一索引鍵條件約束所指定的值。 若要進一步了解，請參閱[唯一索引鍵條件約束](unique-keys.md)。

Azure Cosmos 容器專門用於 API 特定的實體，如下表所示：

| Azure Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 容器 | 容器 | Table | 集合 | 圖形 | 資料表 |

> [!NOTE]
> 建立容器時，請確定您不會建立兩個名稱相同但大小寫不同的容器。 這是因為 Azure 平台的某些部分不會區分大小寫，而這可能導致在具有這類名稱的容器上發生遙測和動作的混淆/衝突。

### <a name="properties-of-an-azure-cosmos-container"></a>Azure Cosmos 容器的屬性

Azure Cosmos 容器具有一組系統定義的屬性。 根據您使用的 API 而定，某些屬性可能不會直接公開。 下表描述系統定義的屬性清單：

| 系統定義的屬性 | 系統產生或使用者可設定 | 目的 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_掉 | 系統產生的 | 容器的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 系統產生的 | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|\_終端 | 系統產生的 | 容器的上次更新日期時間戳記 | 是 | 否 | 否 | 否 | 否 |
|\_供電 | 系統產生的 | 容器的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 使用者可設定 | 使用者定義的容器唯一名稱 | Yes | Yes | Yes | Yes | Yes |
|indexingPolicy | 使用者可設定 | 提供變更索引路徑、索引類型和索引模式的功能 | 是 | 否 | 否 | 否 | 是 |
|timeToLive | 使用者可設定 | 提供在設定的時間週期後，自動從容器中刪除專案的功能。 如需詳細資訊，請參閱生存[時間](time-to-live.md)。 | 是 | 否 | 否 | 否 | 是 |
|changeFeedPolicy | 使用者可設定 | 用來讀取容器中對項目所做的變更。 如需詳細資訊，請參閱[變更](change-feed.md)摘要。 | 是 | 否 | 否 | 否 | 是 |
|uniqueKeyPolicy | 使用者可設定 | 用來確保邏輯分割區中一個或多個值的唯一性。 如需詳細資訊，請參閱[唯一索引鍵條件約束](unique-keys.md)。 | 是 | 否 | 否 | 否 | 是 |

### <a name="operations-on-an-azure-cosmos-container"></a>Azure Cosmos 容器中的作業

當您使用任何 Azure Cosmos Api 時，Azure Cosmos 容器支援下列作業：

| 操作 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 列舉資料庫中的容器 | Yes | 是 | 是 | 是 | NA | NA |
| 讀取容器 | Yes | 是 | 是 | 是 | NA | NA |
| 建立新容器 | Yes | 是 | 是 | 是 | NA | NA |
| 更新容器 | Yes | 是 | 是 | 是 | NA | NA |
| 刪除容器 | Yes | 是 | 是 | 是 | NA | NA |

## <a name="azure-cosmos-items"></a>Azure Cosmos 項目

Azure Cosmos 項目可根據所使用的 API，分別代表集合中的文件、資料表中的資料列，或圖形中的節點或邊緣。 下表顯示 API 特定實體與 Azure Cosmos 專案的對應：

| Cosmos 實體 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- |
|Azure Cosmos 項目 | Document | 資料列 | Document | 節點或邊緣 | 項目 |

### <a name="properties-of-an-item"></a>項目的屬性

每個 Azure Cosmos 專案都有下列系統定義的屬性。 根據您使用的 API 而定，其中有些可能不會直接公開。

| 系統定義的屬性 | 系統產生或使用者可設定| 目的 | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- | --- |
|\_掉 | 系統產生的 | 專案的唯一識別碼 | 是 | 否 | 否 | 否 | 否 |
|\_etag | 系統產生的 | 適用於開放式並行存取控制的實體標記 | 是 | 否 | 否 | 否 | 否 |
|\_終端 | 系統產生的 | 上次更新專案的時間戳記 | 是 | 否 | 否 | 否 | 否 |
|\_供電 | 系統產生的 | 項目的可定址 URI | 是 | 否 | 否 | 否 | 否 |
|id | 之前或之後 | 邏輯分割區中使用者定義的唯一名稱。 | Yes | Yes | Yes | Yes | Yes |
|任意使用者定義的屬性 | 使用者定義 | 以 API 原生標記法表示的使用者定義屬性（包括 JSON、BSON 和 CQL） | Yes | Yes | Yes | Yes | Yes |

> [!NOTE]
> 屬性的唯一性 `id` 只會在每個邏輯分割區內強制執行。 多個檔可以具有 `id` 具有不同分割區索引鍵值的相同屬性。

### <a name="operations-on-items"></a>項目上的作業

Azure Cosmos 專案支援下列作業。 您可以使用任何 Azure Cosmos Api 來執行作業。

| 操作 | Azure CLI | SQL API | Cassandra API | 適用於 MongoDB 的 Azure Cosmos DB API | Gremlin API | 資料表 API |
| --- | --- | --- | --- | --- | --- | --- |
| 插入、取代、刪除、更新插入、讀取 | 否 | 是 | Yes | Yes | Yes | 是 |

## <a name="next-steps"></a>後續步驟

瞭解這些工作和概念：

* [在 Azure Cosmos 資料庫上佈建輸送量](how-to-provision-database-throughput.md)
* [在 Azure Cosmos 容器上佈建輸送量](how-to-provision-container-throughput.md)
* [使用邏輯分割區](partition-data.md)
* [在 Azure Cosmos 容器上設定 TTL](how-to-time-to-live.md)
* [使用變更摘要來建立回應式應用程式](change-feed.md)
* [在您的 Azure Cosmos 容器上設定唯一索引鍵條件約束](unique-keys.md)
