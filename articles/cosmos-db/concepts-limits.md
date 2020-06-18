---
title: Azure Cosmos DB 服務配額
description: Azure Cosmos DB 不同資源類型的服務配額和預設限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 7ef7a55c81441077d2217ccfc41a2a9c9578eefe
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/20/2020
ms.locfileid: "83697558"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure Cosmos DB 服務配額

本文概述提供給 Azure Cosmos DB 中不同資源的預設配額。

## <a name="storage-and-throughput"></a>儲存體和輸送量

在訂用帳戶下建立 Azure Cosmos DB 帳戶之後，即可透過[建立資料庫、容器和項目](databases-containers-items.md)來管理帳戶中的資料。 您可在容器層級或資料庫層級依據[要求單位 (RU/秒或 RU)](request-units.md) 佈建輸送量。 下表列出每個容器/資料庫的儲存體和輸送量限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的 RU 數上限 ([專用輸送量佈建模式](databases-containers-items.md#azure-cosmos-containers)) | 預設為 1,000,000。 您可藉由[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加此值 |
| 每個資料庫的 RU 數上限 ([共用輸送量佈建模式](databases-containers-items.md#azure-cosmos-containers)) | 預設為 1,000,000。 您可藉由[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加此值 |
| 每個 (邏輯) 分割區索引鍵的 RU 數上限 | 10,000 |
| 每個 (邏輯) 分割區索引鍵在所有項目上的儲存體上限| 20 GB |
| 相異 (邏輯) 分割區索引鍵的數目上限 | 無限制 |
| 每個容器的儲存體上限 | 無限制 |
| 每個資料庫的儲存體上限 | 無限制 |
| 每個帳戶的附件大小上限 (附件功能即將停用) | 2 GB |
| 每 1 GB 所需的 RU 數下限 | 10 RU/秒 |

> [!NOTE]
> 若要了解管理工作負載 (所含分割區索引鍵需要更高儲存體或輸送量限制) 的最佳做法，請參閱[建立綜合分割區索引鍵](synthetic-partition-keys.md)。
>

Cosmos 容器 (或共用輸送量資料庫) 的最小輸送量必須為 400 RU/秒。 當容器成長時，支援的最小輸送量也取決於下列因素：

* 您曾在容器上佈建的最大輸送量。 例如，如果輸送量已增加到 50,000 RU/秒，則最低的可能佈建輸送量會是 500 RU/秒
* 容器中的目前儲存體 (以 GB 為單位)。 例如，如果容器具有 100 GB 的儲存體，則最低的可能佈建輸送量會是 1000 RU/秒
* 共用輸送量資料庫上最小輸送量也取決於先前在共用輸送量資料庫中建立的容器總數 (以每個容器 100 RU/秒為單位)。 例如，如果已在共用輸送量資料庫中建立五個容器，則輸送量必須至少為 500 RU/秒

您可從 Azure 入口網站或 SDK 擷取容器或資料庫的目前和最小輸送量。 如需詳細資訊，請參閱[在容器和資料庫中佈建輸送量](set-throughput.md)。 

> [!NOTE]
> 在某些情況下，您能夠將輸送量降低到低於10%。 使用 API 來取得每個容器的 RU 數下限。
>

總而言之，以下是佈建的 RU 下限。 

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的 RU 數下限 ([專用輸送量佈建模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 每個資料庫的 RU 數下限 ([共用輸送量佈建模式](databases-containers-items.md#azure-cosmos-containers)) | 400 |
| 共用輸送量資料庫內每個容器的 RU 數下限 | 100 |

Cosmos DB 可透過 SDK 或入口網站，支援彈性調整每個容器或資料庫的輸送量 (RU)。 每個容器可在最小值與最大值之間，於 10 到 100 倍的調整範圍內，以同步方式立即進行調整。 如果要求的輸送量值超出範圍，則會以非同步方式執行調整。 視容器中所要求的輸送量和資料儲存體大小而定，非同步調整可能需要數分鐘到數小時的時間才能完成。  

## <a name="control-plane-operations"></a>控制平面作業

您可使用 Azure 入口網站、Azure PowerShell、Azure CLI 和 Azure Resource Manager 範本[佈建及管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)。 下表列出每個訂用帳戶、帳戶和作業數目的限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個訂用帳戶的資料庫帳戶數目上限 | 預設為 50。 您可藉由[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加此值|
| 區域性容錯移轉的數目上限 | 預設為 1/小時。 您可藉由[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加此值|

> [!NOTE]
> 區域性容錯移轉只適用於單一區域寫入帳戶。 多重區域寫入帳戶不需要變更寫入區域，對於變更寫入區域也沒有任何限制。

Cosmos DB 會自動定期備份資料。 如需備份保留間隔和時段的詳細資料，請參閱[在 Azure Cosmos DB 中進行線上備份及隨選資料還原](online-backup-and-restore.md)。

## <a name="per-account-limits"></a>每個帳戶的限制

| 資源 | 預設限制 |
| --- | --- |
| 資料庫的數目上限 | 無限制 |
| 每個資料庫 (具有共用輸送量) 的容器數目上限 |25 |
| 每個資料庫或帳戶 (具有專用輸送量) 的容器數目上限  |無限制 |
| 區域數目上限 | 無限制 (所有 Azure 區域) |

## <a name="per-container-limits"></a>每個容器的限制

Azure Cosmos 容器可根據所使用的 API，分別代表集合、資料表或圖形。 容器支援 [唯一索引鍵條件約束](unique-keys.md)、[預存程序、觸發程序和 UDF](stored-procedures-triggers-udfs.md)，以及[編製索引原則](how-to-manage-indexing-policy.md)的設定。 下表列出容器內設定特定的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 資料庫或容器名稱的長度上限 | 255 |
| 每個容器的預存程式數上限 | 100 <sup>*</sup>|
| 每個容器的 UDF 數上限 | 25 <sup>*</sup>|
| 編製索引原則中的路徑數目上限| 100 <sup>*</sup>|
| 每個容器的唯一索引鍵數目上限|10 <sup>*</sup>|
| 每個唯一索引鍵條件約束的路徑數目上限|16 <sup>*</sup>|

<sup>*</sup> 您可連絡 Azure 支援人員來增加任一項的每個容器限制。

## <a name="per-item-limits"></a>每個項目的限制

Azure Cosmos 項目可根據所使用的 API，分別代表集合中的文件、資料表中的資料列，或圖形中的節點或邊緣。 下表顯示 Cosmos DB 中每個項目的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 項目大小上限 | 2 MB (JSON 表示法的 UTF-8 長度) |
| 分割區索引鍵值的長度上限 | 2048 個位元組 |
| 識別碼值的長度上限 | 1023 個位元組 |
| 每個項目的屬性數目上限 | 沒有實際限制 |
| 巢狀深度上限 | 沒有實際限制 |
| 屬性名稱的長度上限 | 沒有實際限制 |
| 屬性值的長度上限 | 沒有實際限制 |
| 字串屬性值的長度上限 | 沒有實際限制 |
| 數值屬性值的長度上限 | IEEE754 雙精確度 64 位元 |

除了分割區索引鍵和識別碼值的長度限制，以及 2 MB 的整體大小限制以外，項目裝載 (例如屬性數目和巢狀深度) 沒有任何限制。 您可能必須針對具有大型或複雜項目結構的容器設定編製索引原則，以減少 RU 耗用量。 如需真實世界範例以及用來管理大型項目的模式，請參閱 [Cosmos DB 中的模型項目](how-to-model-partition-example.md)。

## <a name="per-request-limits"></a>每個要求的限制

Azure Cosmos DB 支援針對容器、項目及資料庫等資源執行 [CRUD 和查詢作業](https://docs.microsoft.com/rest/api/cosmos-db/)。 其也支援針對容器中具有相同分割區索引鍵的多個項目執行[交易式批次要求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 資源 | 預設限制 |
| --- | --- |
| 單一作業的最長執行時間 (例如預存程式執行或單一查詢頁面擷取)| 5 秒 |
| 要求大小上限 (例如，預存程式、CRUD)| 2 MB |
| 回應大小上限 (例如，編頁查詢) | 4 MB |
| 交易式批次中的作業數目上限 | 100 |

當查詢之類的作業達到執行逾時或回應大小限制之後，會將結果的頁面和接續權杖傳回給用戶端，以繼續執行。 單一查詢可跨頁面/接續執行的持續時間沒有實際限制。

Cosmos DB 使用 HMAC 進行授權。 您可使用主要金鑰或[資源權杖](secure-access-to-data.md)，對容器、分割區索引鍵或項目等資源進行更細緻的存取控制。 下表列出 Cosmos DB 中的授權權杖限制。

| 資源 | 預設限制 |
| --- | --- |
| 主要權杖到期時間上限 | 15 分鐘  |
| 資源權杖到期時間下限 | 10 分鐘  |
| 資源權杖到期時間上限 | 預設為 24 小時。 您可藉由[提出 Azure 支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加此值|
| 權杖授權的最大時鐘誤差| 15 分鐘 |

Cosmos DB 支援在寫入期間執行觸發程序。 服務對於每個寫入作業最多支援一個前置觸發程序，以及一個後置觸發程序。 

## <a name="limits-for-autoscale-provisioned-throughput"></a>自動調整佈建輸送量的限制

如需自動調整的輸送量和儲存體限制詳細說明，請參閱[自動調整](provision-throughput-autoscale.md#autoscale-limits)一文和[常見問題集](autoscale-faq.md#lowering-the-max-rus)。

| 資源 | 預設限制 |
| --- | --- |
| 系統可調整到的 RU/秒上限 |  `Tmax`，由使用者設定的自動調整 RU/秒上限|
| 系統可調整到的 RU/秒下限 | `0.1 * Tmax`|
| 系統調整到的目前 RU/秒  |  `0.1*Tmax <= T <= Tmax`，依使用量而定|
| 每小時的計費 RU/秒下限| `0.1 * Tmax` <br></br>計費是以每小時為基礎執行，我們會根據系統在該小時內調整到的最高 RU/秒或 `0.1*Tmax` (以較高者為准) 來收取費用。 |
| 容器的自動調整最大 RU/秒下限  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100)`，四捨五入為最接近的 1000 RU/秒 |
| 資料庫的自動調整最大 RU/秒下限  |  `MAX(4000, highest max RU/s ever provisioned / 10, current storage in GB * 100,  4000 + (MAX(Container count - 25, 0) * 1000))`，四捨五入為最接近的 1000 RU/秒。 <br></br>請注意：如果資料庫有 25 個以上的容器，則系統會為每個額外的容器以 1000 RU/秒遞增自動調整最大 RU/秒下限。 例如，如果您有 30 個容器，則可設定的最低自動調整最大 RU/秒為 9000 RU/秒 (在 900 - 9000 RU/秒之間調整)。

## <a name="sql-query-limits"></a>SQL 查詢限制

Cosmos DB 支援使用 [SQL](how-to-sql-query.md) 來查詢項目。 下表描述查詢陳述式中的限制，例如在子句數目或查詢長度方面。

| 資源 | 預設限制 |
| --- | --- |
| SQL 查詢的長度上限| 256 KB |
| 每個查詢的 JOIN 數上限| 5 <sup>*</sup>|
| 每個查詢的 UDF 數上限| 10 <sup>*</sup>|
| 每個多邊形的點數上限| 4096 |
| 每個容器的包含路徑數上限| 500 |
| 每個容器的排除路徑數上限| 500 |
| 複合式索引中的屬性數上限| 8 |

<sup>*</sup> 您可連絡 Azure 支援人員來增加這些 SQL 查詢限制。

## <a name="mongodb-api-specific-limits"></a>MongoDB API 特定限制

Cosmos DB 針對依據 MongoDB 撰寫的應用程式支援 MongoDB 有線通訊協定。 您可在[支援的 MongoDB 功能和語法](mongodb-feature-support.md)中找到所支援命令和通訊協定版本。

下表列出 MongoDB 功能支援特定的限制。 針對 SQL (核心) API 所提及的其他服務限制也適用於 MongoDB API。

| 資源 | 預設限制 |
| --- | --- |
| MongoDB 查詢記憶體大小上限 (這項限制僅適用於 3.2 伺服器版本) | 40 MB |
| MongoDB 作業的最長執行時間| 30 秒 |
| 伺服器端連線關閉的閒置連線逾時 * | 30 分鐘 |

\* 我們建議用戶端應用程式將驅動程式設定中的閒置連線逾時設定為 2-3 分鐘，因為 [Azure LoadBalancer 的預設逾時為 4 分鐘](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。  此逾時可確保用戶端電腦與 Azure Cosmos DB 之間的中繼負載平衡器不會關閉閒置的連線。

## <a name="try-cosmos-db-free-limits"></a>免費試用 Cosmos DB 限制

下表列出[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) 試用版的限制。

| 資源 | 預設限制 |
| --- | --- |
| 試用期間 | 30天 (到期後可要求新的試用版) <br> 到期後，即會刪除儲存的資訊。 |
| 每個訂用帳戶的容器數上限 (SQL、Gremlin、資料表 API) | 1 |
| 每個訂用帳戶的容器數上限 (MongoDB API) | 3 |
| 每個容器的最大輸送量 | 5000 |
| 每個共用輸送量資料庫的最大輸送量 | 20000 |
| 每個帳戶的總儲存體上限 | 10 GB |

試用 Cosmos DB 僅支援「美國中部」、「北歐」和「東南亞」區域中的「全域散發」。 無法為試用 Azure Cosmos DB 的帳戶建立 Azure 支援票證。 不過，支援可提供給具有現有支援方案的訂閱者。

## <a name="free-tier-account-limits"></a>免費層帳戶限制
下表列出 [Azure Cosmos DB 免費試用帳戶](optimize-dev-test.md#azure-cosmos-db-free-tier)的限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個 Azure 訂用帳戶的免費層帳戶數目 | 1 |
| 免費層折扣期間 | 帳戶的存留期。 必須在帳戶建立期間選擇加入。 |
| 免費的 RU/秒上限 | 400 RU/秒 |
| 免費的儲存體上限 | 5 GB |
| 共用輸送量資料庫的數目上限 | 5 |
| 共用輸送量資料庫中的容器數目上限 | 25 <br>在免費層帳戶中，具有最多 25 個容器的共用輸送量資料庫其 RU/秒下限為 400 個 RU/秒。 |

  除了上述內容以外，[每個帳戶的限制](#per-account-limits)也適用於免費層帳戶。

## <a name="next-steps"></a>後續步驟

深入了解 Cosmos DB 的[全域散發](distribute-data-globally.md)和[資料分割](partitioning-overview.md)及[所佈建輸送量](request-units.md)等核心概念。

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
* [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
