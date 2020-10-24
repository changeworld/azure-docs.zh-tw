---
title: 擁有權 (TCO) 的擁有權總成本 Azure Cosmos DB
description: 本文會比較 Azure Cosmos DB 與 IaaS 和內部部署資料庫的擁有權總成本
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.reviewer: sngun
ms.openlocfilehash: a058e7a23c36dcaca16c3cce1be1ca826d472bc0
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476989"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>擁有權 (TCO) 的擁有權總成本 Azure Cosmos DB

Azure Cosmos DB 採用更細緻的多租用戶和資源控管設計。 這項設計允許 Azure Cosmos DB以極低的成本運作，並協助使用者儲存。 目前，Azure Cosmos DB 在單一電腦上支援超過 280 多個客戶工作負載，密度持續增加，且叢集內有數千個客戶工作負載。 它會跨叢集中的不同電腦以及跨資料中心內的多個叢集，負載平衡客戶工作負載的複本。 若要深入瞭解，請參閱 [Azure Cosmos DB：推送全域散發資料庫的](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/)領域。 由於資源控管、多租用戶和 Azure 基礎結構之其餘部分的原生整合，Azure Cosmos DB 平均比在 IaaS 上執行的 MongoDB、Cassandra 或其他 OSS NoSQL 便宜 4 到 6 倍，且比在內部部署執行的資料庫引擎便宜最高 10 倍。 請參閱 [NoSQL 資料庫雲端服務的擁有權總成本](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)的相關文件。

OSS NoSQL 資料庫解決方案 (例如 Apache Cassandra、MongoDB、HBase)，引擎是針對內部部署環境所設計。 當作為受控服務提供時，它們相當於使用租用戶資料庫來管理已佈建的叢集和監視支援的 Resource Manager 範本。 OSS NoSQL 架構需要大量的作業額外負荷，並且查找專門知識可能既困難又昂貴。 另一方面，Azure Cosmos DB 是完全受控的雲端服務，它允許開發人員專注於商務創新，而不是管理和維護資料庫基礎結構上。

與雲端原生資料庫服務 Azure Cosmos DB 不同，OSS NoSQL 資料庫引擎不是以資源控管或更細緻的多租用戶作為基礎架構原則而設計和建置的。 OSS NoSQL 資料庫引擎 (例如 Cassandra 與 MongoDB) 做出了一個基本假設，即執行它們的虛擬機器的所有資源都可供其使用。 如果資源數量低於特定臨界值，則許多這些資料庫引擎無法運作。 例如，針對小型的 VM 執行個體，它們可用於廠商建議的組態，通常建議具有更高成本的大規模 VM。 因此，您無法裝載 OSS NoSQL 或任何其他內部部署資料庫引擎，並使用以耗用量為基礎的收費模型（例如每秒要求數或取用的儲存體）來提供此功能。

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Azure Cosmos DB 的擁有權總成本

Azure Cosmos DB 的無伺服器佈建模型不需要過度佈建的資料庫基礎結構。 提供 Azure Cosmos DB 資源，而不需要特殊組態或授權。 因此，相較於 OSS NoSQL 資料庫，Azure Cosmos DB 支援的應用程式能夠以節省高達 70% 的擁有權總成本執行。 如需即時範例，請參閱[客戶使用案例](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc)。 Azure Cosmos DB 定價模型的其他優點包括：

* **價格的絕佳價值：** 市場分析師、客戶和合作夥伴已確認 Azure Cosmos DB 提供的所有功能都有更高的價值，相較于客戶在自己或透過其他廠商自行實行這些解決方案時，可獲得的價格更低。 這類的資料庫功能包括：全域散發、多重區域寫入、定義完善且直覺的一致性模型，Azure Cosmos DB 不需要任何複雜性、額外負荷或停機，就能大幅簡化自動編制索引。

* **不需要進行 NoSQL DevOps 管理：** 使用 Azure Cosmos DB 不需要採用 DevOps 來管理部署、執行維護、調整或修補。 您可以執行透過裝載於內部部署或雲端基礎結構上的 OSS NoSQL 叢集執行的所有工作負載。

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Azure Cosmos DB 的擁有權成本" border="false":::

* **彈性規模的能力：** Azure Cosmos DB 輸送量可以擴大和縮小，讓您在非尖峰時間降低擁有權的成本。 部署在雲端基礎結構上的 OSS NoSQL 叢集提供的彈性有限，且根據定義，內部部署不具有彈性。 在 Azure Cosmos DB 中，如果您佈建更多的輸送量，則可確保您的輸送量呈線性成長。 這項保證由財務 SLA 和任何規模的第 99 個百分位數所支援。

* 規模**經濟：** 受控服務（例如 Azure Cosmos DB）會與大量節點搭配運作，以原生方式與網路、儲存體和計算進行整合。 由於 Azure Cosmos DB 的大型規模，您可以節省成本。

* **針對雲端優化：** Azure Cosmos DB 是以更精細的多租使用者和效能隔離，從頭開始設計。 這樣可以跨叢集和資料中心，以最佳方式放置、執行和平衡數千個租用戶和其工作負載。 相比之下，目前世代的 OSS NoSQL 資料庫會在內部部署環境運行，假設整個虛擬機器執行單一租用戶的工作負載。 這些資料庫也不是為了充分利用雲端提供者的基礎結構和硬體而設計的。 例如，OSS NoSQL 資料庫引擎不會知道虛擬機器與例行的映像升級之間的差異，或者該進階磁碟其實已經三向複寫。 它無法利用這些優勢，並將優勢和節省量傳遞給客戶。

* **您可以用小時支付：** 針對大規模的工作負載，需要在任何時間點進行調整，您只需支付該小時的費用。 應用程式上的工作負載通常在一年中的不同時間，以及查詢的資料上有所不同。 使用 Azure Cosmos DB，您可以根據需要相應增加或減少，並僅依需求支付。 使用內部部署或 IaaS 裝載的系統時，您無法符合此模型，因為沒有任何方法可將硬體解除委任。 在這種情況下，Azure Cosmos DB 平均可以節省 10 至 14 倍。

* **您可免費取得許多功能：** 在 Azure Cosmos DB 中，相較于替代資料庫服務，寫入工作負載的成本相當實惠。 此外，Azure Cosmos DB 還提供諸如[自動編製索引](index-policy.md)、[存留時間 (TTL)](time-to-live.md)、[變更摘要](change-feed.md)等功能，而不需要任何額外的費用，其他資料庫服務通常會收費。

* **針對各種工作負載使用統一的貨幣：** 不同于替代供應專案，在 Azure Cosmos DB 中，您不需要將工作負載分割（例如，在讀取和寫入中）。 或者為每個工作負載類型佈建輸送量，即讀取輸送量與寫入輸送量。 在 Azure Cosmos DB 中，使用統一且標準化的貨幣 (要求單位或 RU/秒) 保留佈建的輸送量。Azure Cosmos DB 不會強制您為工作負載指派優先順序、執行容量計劃或單獨為每種類型的容量支付。 這種方法可讓您在不同的作業和工作負載類型之間輕鬆地交換相同的 RU/秒。

* **不需要布建 vm 來調整規模：** 如果您想要調整規模，大部分的運算元據庫都需要您使用大型虛擬機器，以避免有雜訊的鄰近專案，以及鬆散的資源管理。 這樣會將成本的負擔和預付承諾量放在客戶身上。 使用 Azure Cosmos DB，您可以從小規模著手，順暢地擴展到大規模工作負載大小，並且不會出現任何停機或對資料可用性的影響。

* **您可以利用布建的輸送量達到上限：** 藉由 Azure Cosmos DB 中的子核心多工，您可以讓布建的輸送量與 IaaS 裝載的選項或協力廠商供應專案的範圍更加飽和。 這種方法比替代解決方案節省更多。

* **Azure Cosmos DB 與其他 Azure 服務的深入整合。** Azure Cosmos DB 與網路功能、計算、Azure Functions (無伺服器)、Azure IoT 和其他 Azure 服務進行原生整合。 透過這項整合，您可以取得最佳效能，全球資料複寫速度的強大保證。 協力廠商解決方案將無法比擬，或通常會收取額外費用以提供這類功能。

* **根據預設，您會自動取得高可用性，而且至少有10-20 個容錯網域：** Azure Cosmos DB 支援跨容錯網域散發工作負載，這是高可用性的關鍵功能。 它為世界上任何地方的第 99 百分位數讀寫提供 99.999 的高可用性。 透過自己或透過協力廠商解決方案實作此類服務的成本會比較高。

* **您可以自動取得所有的企業功能，不需要額外收費。** Azure Cosmos DB 提供最完整的合規性認證、安全性和靜態與動態加密，無需額外費用 (相較於我們的競爭對手)。 您可以在世界上任何地方自動取得區域可用性。 您可以跨任意數量的 Azure 區域延伸資料庫，並隨時新增或移除區域。

* **使用保留容量最多可省下65% 的成本：** Azure Cosmos DB [保留容量](cosmos-db-reserved-capacity.md) 可協助您透過預先支付一年或三年的 Azure Cosmos DB 資源來節省成本。 透過一年或三年的預先承諾量，您可以大幅降低成本，相較於一般的定價可節省 20-65% 的折扣。 在任務關鍵性工作負載上，您可以在佈建容量方面取得更好的 SLA。

## <a name="next-steps"></a>後續步驟

* 深入了解 [Azure Cosmos DB 定價模型如何為客戶提供符合成本效益的解決方案](total-cost-ownership.md)
* 深入了解[最佳化開發與測試](optimize-dev-test.md)
* 深入了解[最佳化輸送量成本](optimize-cost-throughput.md)
* 深入了解[最佳化儲存體成本](optimize-cost-storage.md)
* 深入了解[最佳化讀取和寫入的成本](optimize-cost-reads-writes.md)
* 深入了解[最佳化查詢成本](./optimize-cost-reads-writes.md)
* 深入了解[最佳化多重區域 Cosmos 帳戶的成本](optimize-cost-regions.md)
* 深入了解 [NoSQL 資料庫雲端服務的擁有權總成本](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
