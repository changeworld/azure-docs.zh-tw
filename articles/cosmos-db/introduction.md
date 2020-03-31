---
title: Azure Cosmos DB 簡介
description: 了解 Azure Cosmos DB。 這個全域散發的多模型資料庫是針對低延遲、彈性的可擴縮性和高可用性而建置的，並且提供 NoSQL 資料的原生支援。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/26/2020
ms.locfileid: "79215246"
---
# <a name="welcome-to-azure-cosmos-db"></a>歡迎使用 Azure Cosmos DB

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 應用程式需要即時回應尖峰時間內使用量的巨變、儲存不斷增加的資料量，並在毫秒內將這些資料提供給使用者。

Azure Cosmos DB 是 Microsoft 的全域分散式多模型資料庫服務。 透過按鈕點選，Cosmos DB 可讓您有彈性且獨立地跨任意數目的 Azure 區域調整輸送量和儲存體。 您可以彈性地調整輸送量和儲存體，並利用下列您喜歡的 API，快速進行單一位數毫秒的資料存取：SQL、MongoDB、Cassandra、資料表或 Gremlin。 Cosmos DB 針對輸送量、延遲、可用性和一致性的保證提供了完整的[服務等級協定](https://aka.ms/acdbsla) (SLA)，這是其他資料庫服務無法提供的。

您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，而不需要 Azure 訂用帳戶 (免費且無需承諾用量)，或是使用 [Azure Cosmos DB 免費層](optimize-dev-test.md#azure-cosmos-db-free-tier)來取得帳戶，其中前 400 RU/秒和 5 GB 的儲存體免費。

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB 是 Microsoft 的全球發佈資料庫服務，包含彈性的相應放大、低延遲保證、五個一致性模型，以及完整保證的 SLA](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>主要權益

### <a name="turnkey-global-distribution"></a>周全的全域發佈

Cosmos DB 可讓您在全球範圍內建置回應迅速且高可用性的應用程式。 無論使用者身在何處，Cosmos DB 都會明確地複寫您的資料，因此您的使用者可以與最接近他們的資料複本進行互動。

Cosmos DB 允許您隨時新增任何 Azure 區域到您的 Cosmos 帳戶，或者從中移除區域，只要按一下按鈕即可。 Cosmos DB 可以將您的資料順暢地複寫到與您的 Cosmos 帳戶相關聯的所有區域，同時由於該服務的「多路連接」  功能，您的應用程式可用性依然高度可用。 如需詳細資訊，請參閱[全域散發](distribute-data-globally.md)一文。

### <a name="always-on"></a>永遠開啟

藉由與 Azure 基礎結構的深度整合以及[明確的多重主機複寫](global-dist-under-the-hood.md)，Cosmos DB 為讀取和寫入提供了 [99.999% 高可用性](high-availability.md)。 Cosmos DB 還提供您以程式設計方式 (或透過入口網站) 叫用 Cosmos 帳戶的區域性容錯移轉功能。 此功能可協助確保您的應用程式可以在發生區域性災害時進行容錯移轉。

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>在全球輸送量與儲存體的彈性可擴縮性

Cosmos DB 的設計包括透明的水平分割和多重主要複寫，可為您在全球各地的寫入和讀取提供前所未有的彈性可擴縮性。 您可以透過使用單一 API 呼叫，在全球範圍內彈性地擴大數億個要求/秒，並僅支付您所需要的輸送量 (和儲存體)。 此功能可協助您應付工作負載中非預期的暴增，而無需針對尖峰過度佈建。 如需詳細資訊，請參閱 [Cosmos DB 中的分割區](partitioning-overview.md)、[容器和資料庫上佈建的輸送量](set-throughput.md)，以及[全域調整佈建的輸送量](scaling-throughput.md)。

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>保證在第 99 個百分位數低延遲 (全球)

使用 Cosmos DB，您可以建置具高回應能力的全球級應用程式。 Cosmos DB 利用其新的多重主要複寫通訊協定、無閂鎖和[最適合寫入的資料庫引擎](index-policy.md)，保證全球各地的讀取 (索引) 和寫入延遲低於 10 毫秒的時間達第 99 個百分位數。 此功能可以持續擷取資料，並為回應迅速的應用程式提供快速查詢。

### <a name="precisely-defined-multiple-consistency-choices"></a>精確定義的多重一致性選擇

建置 Cosmos DB 中的全域分散式應用程式時，您不再需要[在一致性、可用性、延遲和輸送量之間做出極端取捨](consistency-levels-tradeoffs.md)。 Cosmos DB 的多重主要複寫通訊協定謹慎設計成提供[五種定義完善的一致性選擇](consistency-levels.md) -       (強式、限定過期、工作階段、一致前置詞和最終)，以取得直覺式的程式設計模型，讓全域分散式應用程式具有低延遲和高可用性。

### <a name="no-schema-or-index-management"></a>不需要任何結構描述或索引管理

維持資料庫結構描述和索引與應用程式的結構描述同步，對於全域散發的應用程式來說特別麻煩。 有了 Cosmos DB，您不需要處理結構描述或索引管理。 資料庫引擎完全與結構描述無關。  由於不需要結構描述和索引管理，因此在移轉結構描述時也不必擔心應用程式停機。 Cosmos DB 會[自動編製所有資料的索引](index-policy.md)，並提供快速的查詢。

### <a name="battle-tested-database-service"></a>實戰測試資料庫服務

Cosmos DB 是 Azure 中的基本服務。 近十年來，許多 Microsoft 產品已針對全球範圍內的任務關鍵性應用程式採用 Cosmos DB，包括 Skype、Xbox、Office 365，Azure 等等。 現在，Cosmos DB 是 Azure 上成長最快速的服務之一，許多外部客戶和任務關鍵性應用程式皆有使用，這些外部客戶和應用程式需要彈性延展、周全的全域發佈、多重主機複寫，以實現讀取和寫入的低延遲和高可用性。

### <a name="ubiquitous-regional-presence"></a>遍及各區，無所不在

Cosmos DB 可在全球所有 Azure 區域使用，包括公用雲端中 54 個以上區域、[Azure 中國 21Vianet](https://www.azure.cn/en-us/)、Azure Germany、Azure Government 和 Azure Government for Department of Defense (DoD)。 請參閱 [Cosmos DB 遍及各區](regional-presence.md)。

### <a name="secure-by-default-and-enterprise-ready"></a>預設保護且適合企業使用

Cosmos DB 已通過[多種合規性標準認證](compliance.md)。 此外，Cosmos DB 中的所有資料在靜止和移動中都會加密。 Cosmos DB 會提供資料列層級的授權，並遵守嚴格的安全性標準。

### <a name="significant-tco-savings"></a>大幅節省 TCO

因為 Cosmos DB 是完全受控的服務，所以您不再需要管理和操作複雜的多重資料中心部署和資料庫軟體升級，支付支援、授權或作業費用，或必須為尖端工作負載佈建資料庫。 如需詳細資訊，請參閱[使用 Cosmos DB 將成本最佳化](total-cost-ownership.md)。

### <a name="industry-leading-comprehensive-slas"></a>領先業界之全方位 SLA

Cosmos DB 是第一且唯一提供[領先業界之全方位 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) 的服務，且具有達 99.999% 穩定正常運作時間、第 99 個百分位數的讀取與寫入延遲、保證的輸送量及一致性。

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>使用原生內建 Apache Spark 的全域散發作業分析和 AI

您可以對 Cosmos DB 中儲存的資料直接執行 [Spark](spark-connector.md)。 這項功能允許您在全球範圍內執行低延遲的作業分析，而不會影響針對 Cosmos DB 直接操作的交易式工作負載。 如需詳細資訊，請參閱[全域分散式作業分析](lambda-architecture.md)。

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>使用熱門的開放原始碼軟體 (OSS) API 開發 Cosmos DB 上的應用程式

Cosmos DB 提供了一系列 API，用來使用儲存在 Cosmos 資料庫中的資料。 根據預設，[您可以使用 SQL](how-to-sql-query.md) (核心 API) 來查詢 Cosmos 資料庫。 Cosmos DB 還可實作 [Cassandra](cassandra-introduction.md)、[MongoDB](mongodb-introduction.md)、[Gremlin](graph-introduction.md) 和 [Azure 表格儲存體](table-introduction.md)。 您可將常用的 NoSQL (例如 MongoDB、Cassandra、Gremlin) 的用戶端驅動程式 (和工具) 直接指向您的 Cosmos 資料庫。 藉由支援常用的 NoSQL API 的線路通訊協定，Cosmos DB 可讓您：

* 輕鬆地將應用程式移轉到 Cosmos DB，同時保留應用程式邏輯的重要部分。
* 維持您的應用程式可攜，並繼續維持雲端廠商中立。
* 取得全面受管的雲端服務，為常見的 NoSQL API 提供領先業界且有財務補償的 SLA。 
* 根據您的需要，彈性地調整針對資料庫佈建的輸送量和儲存體，並僅支付您所需要的儲存體與輸送量。 這會大量節省成本。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益於 Azure Cosmos DB 的解決方案

任何需要處理[全球](distribute-data-globally.md)各地大量資料、讀取和寫入 (各種資料的回應時間都近乎即時) 的 [Web、行動裝置、遊戲和 IoT 應用程式](use-cases.md)，都將受益於 Cosmos DB 所[保證的高可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db/)、高輸送量、低延遲度及可微調的一致性。 了解如何將 Azure CosmosDB 用來建置 [IoT 和遠距通訊](use-cases.md#iot-and-telematics)、[零售和行銷](use-cases.md#retail-and-marketing)、[遊戲](use-cases.md#gaming)和 [Web 與行動應用程式](use-cases.md#web-and-mobile-applications)。

## <a name="next-steps"></a>後續步驟

深入了解 Cosmos DB [周全的全域發佈](distribute-data-globally.md)和[分割區](partitioning-overview.md)及[佈建的輸送量](request-units.md)核心概念。

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

* [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
* [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
* [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
* [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
* [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)
