---
title: Azure 宇宙資料庫服務配額
description: Azure Cosmos DB 服務配額和不同資源類型的預設限制。
author: abhijitpai
ms.author: abpai
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: bd384f2b4dd879c30fafd2b765dd9ba0efb12447
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348399"
---
# <a name="azure-cosmos-db-service-quotas"></a>Azure 宇宙資料庫服務配額

本文概述提供給 Azure Cosmos DB 中不同資源的預設配額。

## <a name="storage-and-throughput"></a>存儲和輸送量

在訂閱下創建 Azure Cosmos 帳戶後，可以通過[創建資料庫、容器和項](databases-containers-items.md)來管理帳戶中的資料。 您可以在容器級或資料庫級別根據[請求單位（RU/s 或 RU）](request-units.md)預配輸送量。 下表列出了每個容器/資料庫的存儲和輸送量限制。

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的最大 R 最大 R（[專用輸送量預配模式](databases-containers-items.md#azure-cosmos-containers)） | 預設情況下為 1，000，000。 您可以通過提交 Azure[支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它 |
| 每個資料庫的最大 R 最大 R（[共用輸送量預配模式](databases-containers-items.md#azure-cosmos-containers)） | 預設情況下為 1，000，000。 您可以通過提交 Azure[支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它 |
| 每個（邏輯）分區鍵的最大 R 魯斯 | 10,000 |
| 每個（邏輯）分區鍵在所有專案上的最大存儲空間| 20 GB |
| 不同（邏輯）分區鍵的最大數量 | 無限制 |
| 每個容器的最大存儲空間 | 無限制 |
| 每個資料庫的最大存儲量 | 無限制 |
| 每個帳戶的最大附件大小（附件功能正在折舊） | 2 GB |
| 每 1 GB 所需的最小 R 機數 | 10 RU/s |

> [!NOTE]
> 要瞭解管理具有需要更高存儲或輸送量限制的分區金鑰的工作負載的最佳做法，請參閱[創建合成分區金鑰](synthetic-partition-keys.md)。
>

Cosmos 容器（或共用輸送量資料庫）的最小輸送量必須為 400 個 R。 隨著容器的增長，受支援的最小輸送量還取決於以下因素：

* 可以在容器上設置的最小輸送量取決於在容器上預配的最大輸送量。 例如，如果輸送量增加到 10000 個 R，則盡可能低的預配輸送量將是 1000 個 R
* 共用輸送量資料庫上的最小輸送量還取決於您在共用輸送量資料庫中創建的容器總數，每個容器的輸送量為 100 個 R。 例如，如果在共用輸送量資料庫中創建了五個容器，則輸送量必須至少為 500 個 R

可以從 Azure 門戶或 SDK 檢索容器或資料庫的當前和最低輸送量。 有關詳細資訊，請參閱[容器和資料庫上的預配輸送量](set-throughput.md)。 

> [!NOTE]
> 在某些情況下，您可能能夠將輸送量降低到 10% 以上。 使用 API 獲取每個容器的精確最小 R。
>

總之，以下是最小預配的 RU 限制。 

| 資源 | 預設限制 |
| --- | --- |
| 每個容器的最小 R 最大 （[專用輸送量預配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 每個資料庫的最小 R 最大 （[共用輸送量預配模式](databases-containers-items.md#azure-cosmos-containers)） | 400 |
| 共用輸送量資料庫中每個容器的最小 R0 | 100 |

Cosmos DB 支援通過 SDK 或閘戶對每個容器或資料庫的輸送量 （R） 進行彈性縮放。 每個容器可以在最小值和最大值之間同步和立即在 10 到 100 倍的縮放範圍內進行縮放。 如果請求的輸送量值超出範圍，則非同步執行縮放。 非同步擴展可能需要幾分鐘到幾小時才能完成，具體取決於容器中的請求輸送量和資料存儲大小。  

## <a name="control-plane-operations"></a>控制平面操作

您可以使用 Azure 門戶、Azure PowerShell、Azure CLI 和 Azure 資源管理器範本[預配和管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)。 下表列出了每個訂閱的限制、帳戶和運算元。

| 資源 | 預設限制 |
| --- | --- |
| 每個訂閱的最大資料庫帳戶數 | 預設情況下為 50。 您可以通過提交 Azure[支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|
| 區域容錯移轉的最大數量 | 預設情況下為 1/小時。 您可以通過提交 Azure[支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|

> [!NOTE]
> 區域容錯移轉僅適用于單個區域寫入帳戶。 多區域寫入帳戶不需要或對更改寫入區域沒有任何限制。

Cosmos DB 可定期自動備份資料。 有關備份保留間隔和視窗的詳細資訊，請參閱[Azure Cosmos DB 中的連線備份和按需資料還原](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)。

## <a name="per-account-limits"></a>每個帳戶限制

| 資源 | 預設限制 |
| --- | --- |
| 最大資料庫數 | 無限制 |
| 每個具有共用輸送量的資料庫的最大容器數 |25 |
| 每個具有專用輸送量的資料庫或帳戶的最大容器數  |無限制 |
| 最大區域數 | 無限制（所有 Azure 區域） |

## <a name="per-container-limits"></a>每個容器限制

根據所使用的 API，Azure Cosmos 容器可以表示集合、表或圖形。 容器支援[唯一的關鍵約束](unique-keys.md)、[預存程序、觸發器和 UDF](stored-procedures-triggers-udfs.md)以及[索引策略的配置](how-to-manage-indexing-policy.md)。 下表列出了特定于容器內配置的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 資料庫或容器名稱的最大長度 | 255 |
| 每個容器的最大預存程序 | 100<sup>*</sup>|
| 每個容器的最大 UDF | 25<sup>*</sup>|
| 索引策略中的最大路徑數| 100<sup>*</sup>|
| 每個容器的唯一鍵的最大數量|10<sup>*</sup>|
| 每個唯一鍵約束的最大路徑數|16<sup>*</sup>|

<sup>*</sup>您可以通過聯繫 Azure 支援來增加這些每個容器限制中的任何一個。

## <a name="per-item-limits"></a>每個專案限制

根據所使用的 API，Azure Cosmos 項可以表示集合中的文檔、表中的行或圖形中的節點或邊。 下表顯示了 Cosmos DB 中每個專案的限制。 

| 資源 | 預設限制 |
| --- | --- |
| 專案的最大大小 | 2 MB （UTF-8 長度的 JSON 表示） |
| 分區鍵值的最大長度 | 2048 位元組 |
| id 值的最大長度 | 1023 位元組 |
| 每個專案的最大屬性數 | 無實際限制 |
| 最大嵌套深度 | 無實際限制 |
| 屬性名稱的最大長度 | 無實際限制 |
| 屬性值的最大長度 | 無實際限制 |
| 字串屬性值的最大長度 | 無實際限制 |
| 數值屬性值的最大長度 | IEEE754 雙精度 64 位 |

除了分區鍵和 id 值的長度限制以及 2 MB 的總體大小限制外，對專案有效負載（如屬性數和嵌套深度）沒有限制。 您可能需要為具有大型或複雜物料結構的容器配置索引策略，以減少 RU 消耗。 有關實際示例以及管理大型專案的模式，請參閱[Cosmos DB 中的建模項](how-to-model-partition-example.md)。

## <a name="per-request-limits"></a>每個請求限制

Azure Cosmos DB 支援針對容器、項和資料庫等資源的[CRUD 和查詢操作](https://docs.microsoft.com/rest/api/cosmos-db/)。 它還支援針對容器中具有相同分區鍵的多個項的[事務批次處理請求](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.transactionalbatch)。

| 資源 | 預設限制 |
| --- | --- |
| 單個操作的最大執行時間（如預存程序執行或單個查詢頁檢索）| 5 秒 |
| 最大請求大小（例如，預存程序，CRUD）| 2 MB |
| 最大回應大小（例如，分頁查詢） | 4 MB |
| 事務批次處理中的最大運算元 | 100 |

查詢等操作達到執行超時或回應大小限制後，它將結果頁和延續權杖返回到用戶端以恢復執行。 單個查詢可以跨頁面/延續運行的持續時間沒有實際限制。

宇宙 DB 使用 HMAC 進行授權。 可以使用主金鑰或[資源權杖](secure-access-to-data.md)對容器、分區鍵或項等資源進行細細微性存取控制。 下表列出了 Cosmos DB 中授權權杖的限制。

| 資源 | 預設限制 |
| --- | --- |
| 最大主權杖到期時間 | 15 分鐘  |
| 最小資源權杖到期時間 | 10 分鐘  |
| 最大資源權杖到期時間 | 預設情況下為 24 小時。 您可以通過提交 Azure[支援票證](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)來增加它|
| 權杖授權的最大時鐘偏差| 15 分鐘 |

Cosmos DB 支援在寫入期間執行觸發器。 該服務最多支援一個預觸發和一個後觸發每個寫入操作。 

## <a name="autopilot-mode-limits"></a>自動駕駛模式限制

有關自動駕駛模式下的輸送量和存儲限制，請參閱[自動駕駛儀](provision-throughput-autopilot.md#autopilot-limits)一文。

## <a name="sql-query-limits"></a>SQL 查詢限制

Cosmos DB 支援使用[SQL](how-to-sql-query.md)查詢項。 下表描述了查詢語句中的限制，例如子句數或查詢長度。

| 資源 | 預設限制 |
| --- | --- |
| SQL 查詢的最大長度| 256 KB |
| 每個查詢的最大 JOINs| 5<sup>*</sup>|
| 每個查詢的最大 UDF| 10<sup>*</sup>|
| 每個多邊形的最大點數| 4096 |

<sup>*</sup>您可以通過聯繫 Azure 支援來增加這些 SQL 查詢限制。

## <a name="mongodb-api-specific-limits"></a>蒙戈DB API 特定限制

Cosmos DB 支援 MongoDB 有線協定，用於針對 MongoDB 編寫的應用程式。 您可以在[支援的 MongoDB 功能和語法](mongodb-feature-support.md)中找到受支援的命令和協定版本。

下表列出了特定于 MongoDB 功能支援的限制。 SQL（核心）API中提到的其他服務限制也適用于 MongoDB API。

| 資源 | 預設限制 |
| --- | --- |
| 最大蒙戈DB查詢記憶體大小 | 40 MB |
| 蒙戈DB操作的最大執行時間| 30 秒 |
| 伺服器端連接關閉的空閒連接逾時* | 30 分鐘 |

\*我們建議用戶端應用程式在驅動程式設置中將空閒連接逾時設置為 2-3 分鐘，因為[Azure LoadBalancer 的預設超時為 4 分鐘](../load-balancer/load-balancer-tcp-idle-timeout.md#tcp-idle-timeout)。  此超時將確保用戶端電腦和 Azure Cosmos DB 之間的中間負載平衡器不會關閉空閒連接。

## <a name="try-cosmos-db-free-limits"></a>嘗試宇宙 DB 自由限制

下表列出了["嘗試 Azure 宇宙資料庫免費](https://azure.microsoft.com/try/cosmosdb/)試用"的限制。

| 資源 | 預設限制 |
| --- | --- |
| 審判的持續時間 | 30 天（可續訂任意次數） |
| 每個訂閱的最大容器（SQL、Gremlin、表 API） | 1 |
| 每個訂閱的最大容器（MongoDB API） | 3 |
| 每個容器的最大輸送量 | 5000 |
| 每個共用輸送量資料庫的最大輸送量 | 20000 |
| 每個帳戶的最大總存儲量 | 10 GB |

嘗試 Cosmos DB 僅支援美國中部、北歐和東南亞地區的全球分佈。 無法為嘗試 Azure 宇宙資料庫帳戶創建 Azure 支援票證。 但是，為具有現有支援計畫的訂閱者提供支援。

## <a name="free-tier-account-limits"></a>免費套餐帳戶限制
下表列出了[Azure Cosmos DB 免費層帳戶的限制。](optimize-dev-test.md#azure-cosmos-db-free-tier)

| 資源 | 預設限制 |
| --- | --- |
| 每個 Azure 訂閱的免費層帳戶數 | 1 |
| 免費折扣的持續時間 | 帳戶的存留期。 必須在創建帳戶期間加入宣告。 |
| 最大 RU/s 免費 | 400 RU/秒 |
| 最大存儲空間免費 | 5 GB |
| 最大共用輸送量資料庫數 | 5 |
| 共用輸送量資料庫中的最大容器數 | 25 <br>在免費層帳戶中，最多 25 個容器的共用輸送量資料庫的最小 RU/s 為 400 RU/s。 |

  除上述情況外，[每個帳戶限制](#per-account-limits)也適用于免費套餐帳戶。

## <a name="next-steps"></a>後續步驟

閱讀更多關於 Cosmos DB 的核心概念[全域分發](distribute-data-globally.md)、[分區](partitioning-overview.md)和[預配輸送量](request-units.md)。

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
* [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure 宇宙 DB](https://azure.microsoft.com/try/cosmosdb/)
