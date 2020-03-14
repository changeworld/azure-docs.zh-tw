---
title: Azure Cosmos DB 服務配額
description: Azure Cosmos DB 不同資源類型的服務配額和預設限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 9e5b3e633bf35a25a68eb513d0ec691c3254bde4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79246912"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服務配額

本文概述提供給 Azure Cosmos DB 中不同資源的預設配額。

## <a name="storage-and-throughput"></a>儲存體和輸送量

在您的訂用帳戶下建立 Azure Cosmos 帳戶之後，您可以藉由[建立資料庫、容器和專案](databases-containers-items.md)來管理您帳戶中的資料。 您可以在容器層級或資料庫層級布建輸送量，以[要求單位（RU/秒或 RU）](request-units.md)為依據。 下表列出每個容器/資料庫的儲存體和輸送量限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的最大 ru 數（[專用輸送量布建模式](databases-containers-items.md#azure-cosmos-containers)） | 預設為1000000。 您可以藉由提出[Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它 |
| 每個資料庫最多 ru （已布[建的共用輸送量模式](databases-containers-items.md#azure-cosmos-containers)） | 預設為1000000。 您可以藉由提出[Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它 |
| 每個（邏輯）分割區索引鍵的最大 ru | 10,000 |
| 每個（邏輯）分割區索引鍵的所有專案儲存體上限| 20 GB |
| 相異（邏輯）分割區索引鍵的最大數目 | 無限制 |
| 每個容器的儲存體上限 | 無限制 |
| 每個資料庫的儲存體上限 | 無限制 |
| 每個帳戶的附件大小上限（附件功能正在進行折舊） | 2 GB |
| 每 1 GB 所需的最小 ru 數 | 10 RU/秒 |

> [!NOTE]
> 若要深入瞭解管理具有資料分割索引鍵的工作負載的最佳作法，其儲存體或輸送量需要較高的限制，請參閱[建立綜合分割](synthetic-partition-keys.md)區索引鍵。
>

Cosmos 容器（或共用輸送量資料庫）的最小輸送量必須為 400 ru。 當容器成長時，支援的最低輸送量也取決於下列因素：

* 您可以在容器上設定的最小輸送量取決於容器上所布建的最大輸送量。 例如，如果您的輸送量已增加到 10000 ru，則最低可能的布建輸送量會是 1000 ru
* 共用輸送量資料庫上的最小輸送量也取決於您先前在共用輸送量資料庫中建立的容器總數（以每個容器 100 ru 為單位）。 例如，如果您已在共用輸送量資料庫中建立五個容器，則輸送量至少必須為 500 ru

您可以從 Azure 入口網站或 Sdk 抓取容器或資料庫的目前和最小輸送量。 如需詳細資訊，請參閱[在容器和資料庫上](set-throughput.md)布建輸送量。 

> [!NOTE]
> 在某些情況下，您可以將輸送量降低到低於10%。 使用 API 來取得每個容器的最小 ru 數目。
>

總而言之，以下是布建的最小 RU 限制。 

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的最小 ru 數（[專用輸送量布建模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每個資料庫的最小 ru （已布[建的共用輸送量模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 共用輸送量資料庫內每個容器的最小 ru | 100 |

Cosmos DB 可透過 Sdk 或入口網站，為每個容器或資料庫支援彈性調整輸送量（ru）。 每個容器可以在10到100次的縮放範圍內，以同步方式和最大值為限時，立即進行調整。 如果要求的輸送量值超出範圍，則會以非同步方式執行調整。 視容器中所要求的輸送量和資料儲存體大小而定，非同步調整可能需要數分鐘到數小時才能完成。  

## <a name="control-plane-operations"></a>控制平面作業

您可以使用 Azure 入口網站、Azure PowerShell、Azure CLI 和 Azure Resource Manager 範本來布建[和管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)。 下表列出每個訂用帳戶、帳戶和作業數目的限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個訂閱的資料庫帳戶數目上限 | 預設為50。 您可以藉由提出[Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|
| 區域容錯移轉的最大數目 | 預設為 1/小時。 您可以藉由提出[Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|

> [!NOTE]
> 區域容錯移轉只適用于單一區域寫入帳戶。 多重區域寫入帳戶不需要或有變更寫入區域的任何限制。

Cosmos DB 會自動定期備份您的資料。 如需備份保留間隔和 windows 的詳細資訊，請參閱[Azure Cosmos DB 中的線上備份和隨選資料還原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每個帳戶的限制

| 資源 | 預設限制 |
| --- | --- |
| 資料庫的最大數目 | 無限制 |
| 每個資料庫中具有共用輸送量的容器數目上限 |25 |
| 每個資料庫或具有專用輸送量之帳戶的容器數目上限  |無限制 |
| 區域數目上限 | 無限制（所有 Azure 區域） |

## <a name="per-container-limits"></a>每個容器的限制

根據您使用的 API 而定，Azure Cosmos 容器可以代表集合、資料表或圖形。 容器支援唯一索引[鍵限制](unique-keys.md)式、[預存程式、觸發程式和 udf](stored-procedures-triggers-udfs.md)的設定，以及[索引編制原則](how-to-manage-indexing-policy.md)。 下表列出容器內設定特有的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 資料庫或容器名稱的最大長度 | 255 |
| 每個容器的預存程式數上限 | 100 <sup>*</sup>|
| 每個容器的最大 Udf | 25 <sup>*</sup>|
| 索引編制原則中的路徑數目上限| 100 <sup>*</sup>|
| 每個容器的唯一索引鍵數目上限|10 <sup>*</sup>|
| 每個唯一索引鍵條件約束的路徑數目上限|16 <sup>*</sup>|

<sup>*</sup>您可以聯絡 Azure 支援，以增加每個容器的限制。

## <a name="per-item-limits"></a>每個專案的限制

根據您使用的 API 而定，Azure Cosmos 專案可以代表集合中的檔、資料表中的資料列，或是圖形中的節點或邊緣。 下表顯示 Cosmos DB 中每個專案的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 專案的大小上限 | 2 MB （UTF-8 長度的 JSON 標記法） |
| 資料分割索引鍵值的最大長度 | 2048個位元組 |
| 識別碼值的最大長度 | 1023個位元組 |
| 每個專案的屬性數目上限 | 沒有實際限制 |
| 最大的嵌套深度 | 沒有實際限制 |
| 屬性名稱的最大長度 | 沒有實際限制 |
| 屬性值的最大長度 | 沒有實際限制 |
| 字串屬性值的最大長度 | 沒有實際限制 |
| 數值屬性值的最大長度 | IEEE754 雙精確度64位 |

除了資料分割索引鍵和識別碼值的長度限制，以及 2 MB 的整體大小限制以外，專案裝載沒有任何限制，例如屬性數目和嵌套深度。 您可能必須針對具有大型或複雜專案結構的容器設定編制索引原則，以減少 RU 耗用量。 如需實際範例和用來管理大型專案的模式，請參閱[Cosmos DB 中的模型專案](how-to-model-partition-example.md)。

## <a name="per-request-limits"></a>每個要求的限制

Azure Cosmos DB 支援對容器、專案和資料庫等資源[進行 CRUD 和查詢作業](https://docs.microsoft.com/rest/api/cosmos-db/)。 它也支援針對在容器中具有相同分割區索引鍵的多個專案進行[交易式批次要求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 資源 | 預設限制 |
| --- | --- |
| 單一作業的最長執行時間（例如預存程式執行或單一查詢頁面抓取）| 5 秒 |
| 要求大小上限（例如，預存程式、CRUD）| 2 MB |
| 回應大小上限（例如，分頁查詢） | 4 MB |
| 交易式批次中的最大作業數目 | 100 |

當查詢之類的作業達到執行超時或回應大小限制之後，它會將結果的頁面和接續 token 傳回給用戶端，以繼續執行。 單一查詢可以跨頁面/接續執行的持續時間沒有實際限制。

Cosmos DB 使用 HMAC 進行授權。 您可以使用主要金鑰或[資源權杖](secure-access-to-data.md)，對容器、資料分割索引鍵或專案等資源進行更細緻的存取控制。 下表列出 Cosmos DB 中的授權權杖限制。

| 資源 | 預設限制 |
| --- | --- |
| 主要權杖到期時間上限 | 15 分鐘  |
| 資源權杖到期時間下限 | 10分鐘  |
| 資源權杖到期時間上限 | 預設為24小時。 您可以藉由提出[Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|
| 權杖授權的最大時鐘誤差| 15 分鐘 |

Cosmos DB 支援在寫入期間執行觸發程式。 服務最多支援一個預先觸發程式，以及一個每個寫入作業的後續觸發程式。 

## <a name="autopilot-mode-limits"></a>Autopilot 模式限制

請參閱[Autopilot](provision-throughput-autopilot.md#autopilot-limits)文章，以瞭解 Autopilot 模式中的輸送量和儲存體限制。

## <a name="sql-query-limits"></a>SQL 查詢限制

Cosmos DB 支援使用[SQL](how-to-sql-query.md)查詢專案。 下表描述查詢語句中的限制，例如子句數目或查詢長度。

| 資源 | 預設限制 |
| --- | --- |
| SQL 查詢的最大長度| 256 KB <sup>*</sup>|
| 每個查詢的聯結數上限| 5 <sup>*</sup>|
| 每個查詢的最大 And| 2000 <sup>*</sup>|
| 每個查詢的最大 Or| 2000 <sup>*</sup>|
| 每個查詢的 Udf 上限| 10 <sup>*</sup>|
| 運算式中的每個引數數目上限| 6000 <sup>*</sup>|
| 每個多邊形的點數上限| 4096 <sup>*</sup>|

<sup>*</sup>您可以透過聯絡 Azure 支援人員來增加任何這些 SQL 查詢限制。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特定限制

Cosmos DB 針對以 MongoDB 撰寫的應用程式支援 MongoDB 有線通訊協定。 您可以在[支援的 MongoDB 功能和語法](mongodb-feature-support.md)中找到支援的命令和通訊協定版本。

下表列出 MongoDB 功能支援的特定限制。 針對 SQL （核心） API 所提及的其他服務限制也適用于 MongoDB API。

| 資源 | 預設限制 |
| --- | --- |
| MongoDB 查詢記憶體大小上限 | 40 MB |
| MongoDB 作業的執行時間上限| 30 秒 |
| 伺服器端連接結束的閒置連接逾時 * | 30 分鐘 |

\* 我們建議用戶端應用程式將驅動程式設定中的閒置連接逾時設為2-3 分鐘，因為[Azure LoadBalancer 的預設超時時間為4分鐘](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。  此超時時間可確保用戶端電腦與 Azure Cosmos DB 之間的中繼負載平衡器不會關閉閒置的連線。

## <a name="try-cosmos-db-free-limits"></a>試用 Cosmos DB 免費限制

下表列出免費試用版試用[Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)的限制。

| 資源 | 預設限制 |
| --- | --- |
| 試用期間 | 30天（可更新任意次數） |
| 每個訂用帳戶的容器上限（SQL、Gremlin、資料表 API） | 1 |
| 每個訂用帳戶的容器上限（MongoDB API） | 3 |
| 每個容器的最大輸送量 | 5000 |
| 每個共用輸送量資料庫的最大輸送量 | 20000 |
| 每個帳戶的總儲存空間上限 | 10 GB |

試用 Cosmos DB 僅支援「美國中部」、「北歐」和「東南亞」區域中的「全域散發」。 無法為試用 Azure Cosmos DB 帳戶建立 Azure 支援票證。 不過，支援是提供給具有現有支援方案的訂閱者。

## <a name="free-tier-account-limits"></a>免費層帳戶限制
下表列出[Azure Cosmos DB 免費層帳戶](optimize-dev-test.md#azure-cosmos-db-free-tier)的限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的免費層帳戶數目 | 1 |
| 免費層折扣的持續時間 | 帳戶的存留期。 在帳戶建立期間，必須加入宣告。 |
| 免費的 RU/秒上限 | 400 RU/秒 |
| 免費的儲存體上限 | 5 GB |
| 共用輸送量資料庫的最大數目 | 5 |
| 共用輸送量資料庫中的容器數目上限 | 25 <br>在免費層帳戶中，最多可有25個容器的共用輸送量資料庫的最小 RU/秒為 400 RU/秒。 |

  除了上述以外，[每個帳戶的限制](#per-account-limits)也適用于免費層帳戶。

## <a name="next-steps"></a>後續步驟

深入瞭解 Cosmos DB 的核心概念[全域散發](distribute-data-globally.md)和[分割](partitioning-overview.md)和布[建的輸送量](request-units.md)。

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
* [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
