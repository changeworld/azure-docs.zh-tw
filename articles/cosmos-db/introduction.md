---
title: Azure Cosmos DB 簡介
description: 了解 Azure Cosmos DB。 這個全域散發的多模型資料庫是針對低延遲、彈性的延展性和高可用性而建置的，並且提供 NoSQL 資料的原生支援。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2020
ms.openlocfilehash: 2dd60d1962734f7a4264587fd1b5b0d2a03c9ff0
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359739"
---
# <a name="welcome-to-azure-cosmos-db"></a>歡迎使用 Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 應用程式需要即時回應尖峰時間內使用量的巨變、儲存不斷增加的資料量，並在毫秒內將這些資料提供給使用者。

Azure Cosmos DB 是適用於新式應用程式開發的完全受控 NoSQL 資料庫。 一位數毫秒的回應時間，以及自動和立即的擴充性，可保證在任何規模中的執行速度。 [SLA 支援](https://azure.microsoft.com/support/legal/sla/cosmos-db)的可用性和企業級安全性可確保提供商務持續性。 因為全球各地的多區域資料散發功能、開放原始碼 API 和適用於多種熱門語言的 SDK，讓應用程式開發的速度更快且更具生產力。 作為完全受控的服務，Azure Cosmos DB 利用自動管理、更新和修補，將資料庫管理權交到您手上。 Azure Cosmos DB 也會使用符合成本效益的無伺服器和自動調整選項來處理容量管理，以回應應用程式必須符合所需容量的訴求。

您可以[免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)，而不需要 Azure 訂用帳戶 (免費且無需承諾用量)，或是使用 [Azure Cosmos DB 免費層](optimize-dev-test.md#azure-cosmos-db-free-tier)來取得帳戶，其中前 400 RU/秒和 5 GB 的儲存體免費。

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

:::image type="content" source="./media/introduction/azure-cosmos-db.png" alt-text="Azure Cosmos DB 是適用於新式應用程式開發的完全受控 NoSQL 資料庫。" border="false":::

## <a name="key-benefits"></a>主要權益

### <a name="guaranteed-speed-at-any-scale"></a>在任何規模下都能保證提供的執行速度

取得無可匹敵的 [SLA 支援](https://azure.microsoft.com/support/legal/sla/cosmos-db)速度和輸送量、快速的全域存取，以及即時彈性。

- 具有快速讀取和寫入延遲功能的全域即時存取，以及都受到 [SLA 支援](https://azure.microsoft.com/support/legal/sla/cosmos-db)的輸送量和一致性
- 只要按一下按鈕，就能將多區域寫入和資料散發到任何 Azure 區域。
- 獨立且可在任何 Azure 區域間彈性縮放的儲存體和輸送量；即使在無法預測的流量高載期間，也適用於全球規模，沒有任何限制。

### <a name="simplified-application-development"></a>簡化的應用程式開發

透過開放原始碼 API、多個 SDK、無結構描述資料以及針對操作資料進行無 ETL 分析來快速建立。

- 與現代 (雲端原生) 應用程式開發中使用的重要 Azure 服務緊密整合，包括 Azure Functions、IoT 中樞、AKS (Azure Kubernetes Service)、App Service 等等。
- 從多個資料庫 API 中選擇，包括原生核心 (SQL) API、適用於 MongoDB 的 API、Cassandra API、Gremlin API 和資料表 API。
- 使用您選擇的語言，搭配適用於 .NET、JAVA、Node.js 和 Python 的 SDK，在 Core (SQL) API 上建立應用程式。 或是選擇其他任何資料庫 API 的驅動程式。
- 使用 Azure Synapse Analytics，針對儲存在 Azure Cosmos DB 中近乎即時的作業資料執行無 ETL 分析。
- 變更摘要可讓您輕鬆追蹤和管理資料庫容器的變更，並使用 Azure Functions 建立觸發的事件。
- 無論資料模型為何，Azure Cosmos DB 的無結構描述服務會自動為所有資料編製索引，以提供高速的快速查詢。

### <a name="mission-critical-ready"></a>任務關鍵性就緒

保證每個應用程式的商務持續性、99.999% 的可用性，以及企業級安全性。

- Azure Cosmos DB 提供一套完整的 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db)，包括領先業界的全球可用性。
- 使用自動資料複寫，輕鬆地將資料散發到任何 Azure 區域。 使用強式一致性時，多區域寫入或 RPO 0 可享有零停機。
- 使用自我管理金鑰，享有企業級的靜態加密。
- Azure 角色型存取控制可讓您的資料保持安全，並提供微調控制。

### <a name="fully-managed-and-cost-effective"></a>完全受控且符合成本效益

端對端資料庫管理，搭配無伺服器和自動調整，符合您的應用程式和 TCO 需求

- 完全受控的資料庫服務。 自動化、無需觸控、維護、修補及更新等功能，節省開發人員的時間和金錢。
- 符合成本效益的選項，適用於任何大小或規模可能發生的無法預測或偶一為之的工作負載，讓開發人員可以輕鬆地開始使用，而不需要規劃或管理容量。
- 無伺服器模型提供尖峰的工作負載自動化和回應式服務，以視需要管理流量高載。
- 自動調整佈建的輸送量，並在維護 [SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db) 的同時立即為無法預測的工作負載擴充容量。

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>受益於 Azure Cosmos DB 的解決方案

任何需要處理[全球](distribute-data-globally.md)各地大量資料、讀取和寫入 (各種資料的回應時間都近乎即時) 的 [Web、行動裝置、遊戲和 IoT 應用程式](use-cases.md)，都將受益於 Cosmos DB 所[保證的高可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db/)、高輸送量、低延遲度及可微調的一致性。 了解如何將 Azure CosmosDB 用來建置 [IoT 和遠距通訊](use-cases.md#iot-and-telematics)、[零售和行銷](use-cases.md#retail-and-marketing)、[遊戲](use-cases.md#gaming)和 [Web 與行動應用程式](use-cases.md#web-and-mobile-applications)。

## <a name="next-steps"></a>後續步驟

透過下列其中一個快速入門開始使用 Azure Cosmos DB：

- [開始使用 Azure Cosmos DB SQL API](create-sql-api-dotnet.md)
- [開始使用適用於 MongoDB 的 Azure Cosmos DB API](create-mongodb-nodejs.md)
- [開始使用 Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md)
- [開始使用 Azure Cosmos DB Gremlin API](create-graph-dotnet.md)
- [開始使用 Azure Cosmos DB 資料表 API](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [免費試用 Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)