---
title: 使用 Azure Cosmos DB 全域散發資料
description: 了解如何從 Azure Cosmos DB (全域散發的多模型資料庫服務)，使用全域資料庫進行全球規模的異地複寫、多重主機、容錯移轉及資料復原。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "80982118"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>透過 Azure Cosmos DB 進行全域資料散發 - 概觀

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 這些應用程式通常會部署在多個資料中心內，因此稱為全域散發的應用程式。 全域散發的應用程式需要全域散發的資料庫，這類資料庫可以透明方式複寫世界各地的資料，讓應用程式使用接近其使用者的資料複本。 

Azure Cosmos DB 是一個全域散發的資料庫服務，旨在提供低延遲性、有彈性的輸送量延展性、妥善定義以保有資料一致性的語法，以及高可用性。 簡單地說，如果您的應用程式需要在世界各地保證快速回應時間，而且必須隨時上線，而且需要無限制且彈性的輸送量和儲存體調整能力，您應該在 Azure Cosmos DB 上建立您的應用程式。

您可以將資料庫設定為全域散發，並且在任何可用的 Azure 區域中使用。 若要降低延遲，請將資料放在您的使用者所在的位置附近。 選擇所需的區域，取決於您應用程式能夠觸達的全域範圍以及使用者所在的位置。 Cosmos DB 會以透明方式將資料複寫至與您的 Cosmos 帳戶相關聯的所有區域。 它會提供全域散發的 Azure Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 

透過 Azure Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您不需暫停或重新部署應用程式，就能新增或移除區域。 因為服務原本就提供了多路連接功能，所以它一直都是高可用性。

![高可用性的部署拓撲](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**建立全域主動-主動應用程式。** 透過其 novel 多宿主複寫通訊協定，每個區域都支援寫入和讀取。 多宿主功能也會啟用：

- 無限制的彈性寫入和讀取擴充性。 
- 在世界各地具有 99.999% 的讀取和寫入可用性。
- 保證讀取和寫入會以第 99 百分位數且小於 10 毫秒的方式提供服務。

藉由使用 Azure Cosmos DB 多路連接 Api，您的應用程式會知道最接近的區域，並可將要求傳送至該區域。 識別最靠近的區域不會變更任何組態。 當您在 Azure Cosmos 帳戶中新增和移除區域時，您的應用程式不需要重新部署或暫停，它會持續保持高度可用狀態。

**建置高度回應的應用程式。** 您的應用程式可以對您為資料庫選擇的所有區域執行近乎即時的讀取和寫入。 Azure Cosmos DB 在內部處理區域之間的資料複寫，其一致性層級保證您所選取的層級。

**建置高可用性應用程式。** 在全球多個區域中執行資料庫可提高資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**維護區域中斷期間的業務持續性。** Azure Cosmos DB 支援在區域中斷期間[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 在區域性中斷期間，Azure Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 為了協助確保您的整個應用程式具有高可用性，Cosmos DB 提供手動容錯移轉 API 來模擬區域性中斷。 您可以使用此 API 來進行一般的業務持續性演練。

**全域調整讀取和寫入輸送量。** 您可以讓每個區域都可寫入，並彈性調整所有世界各地的讀取和寫入。 您的應用程式在 Azure Cosmos 資料庫或容器上設定的輸送量，保證會在與您的 Azure Cosmos 帳戶相關聯的所有區域中提供。 已布建的輸送量會由[財務支援的 sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)保證。

**從數個定義完善的一致性模型中進行選擇。** Azure Cosmos DB 複寫通訊協定會提供五個定義完善、實用且直覺式的一致性模型。 每個模型都會在一致性與效能之間進行權衡取捨。 使用這些一致性模型來輕鬆建置全域散發的應用程式。

## <a name="next-steps"></a><a id="Next Steps"></a>後續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [如何在您的應用程式中設定多宿主](how-to-multi-master.md)
* [設定多路連接的用戶端](how-to-manage-database-account.md#configure-multiple-write-regions)
* [從您的 Azure Cosmos 帳戶新增或移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 中的可程式化一致性模型](consistency-levels.md)
* [為應用程式選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB API 間的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)

