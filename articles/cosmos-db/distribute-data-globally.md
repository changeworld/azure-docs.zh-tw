---
title: 使用 Azure Cosmos DB 全域散發資料
description: 使用全域散發的多模型資料庫服務，瞭解如何使用全球資料庫 Azure Cosmos DB 全球規模的異地複寫、多重區域寫入、容錯移轉及資料復原。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seo-nov-2020
ms.openlocfilehash: 7fedd45585698aef9248318a1b055cb656f25d02
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335123"
---
# <a name="distribute-your-data-globally-with-azure-cosmos-db"></a>使用 Azure Cosmos DB 來全域散發資料
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

現今的應用程式需要具有快速回應能力，且一律保持線上狀態。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 這些應用程式通常會部署在多個資料中心內，因此稱為全域散發的應用程式。 全域散發的應用程式需要全域散發的資料庫，這類資料庫可以透明方式複寫世界各地的資料，讓應用程式使用接近其使用者的資料複本。 

Azure Cosmos DB 是一種全域散發的資料庫系統，可讓您從資料庫的本機複本讀取和寫入資料。 Azure Cosmos DB 會以透明方式將資料複寫到與您 Cosmos 帳戶相關聯的所有區域。 Azure Cosmos DB 是一個全域散發的資料庫服務，旨在提供低延遲性、有彈性的輸送量延展性、妥善定義以保有資料一致性的語法，以及高可用性。 簡單地說，如果您的應用程式需要在世界各地都有快速的回應時間，而且必須永遠保持上線狀態，而且需要無限制且彈性的輸送量和儲存體調整能力，您應該在 Azure Cosmos DB 上建立您的應用程式。

您可以將資料庫設定為全域散發，並且在任何可用的 Azure 區域中使用。 若要降低延遲，請將資料放在接近使用者的位置。 選擇所需的區域，取決於您應用程式能夠觸達的全域範圍以及使用者所在的位置。 Cosmos DB 會以透明方式將資料複寫到與您 Cosmos 帳戶相關聯的所有區域。 它會提供全域散發的 Azure Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 

透過 Azure Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您不需暫停或重新部署應用程式，就能新增或移除區域。 因為服務原生提供的多路連接功能，所以它會持續保持高可用性。

:::image type="content" source="./media/distribute-data-globally/deployment-topology.png" alt-text="高可用性的部署拓撲" border="false":::

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**建立全域主動-主動應用程式。** 在其新穎多重區域寫入複寫通訊協定的情況下，每個區域都支援寫入和讀取。 多區域寫入功能也可啟用：

- 無限制的彈性寫入和讀取的擴充性。
- 在世界各地具有 99.999% 的讀取和寫入可用性。
- 保證讀取和寫入會以第 99 百分位數且小於 10 毫秒的方式提供服務。

藉由使用 Azure Cosmos DB 多路連接 Api，您的應用程式會知道最接近的區域，而且可以將要求傳送至該區域。 識別最靠近的區域不會變更任何組態。 當您在 Azure Cosmos 帳戶中新增和移除區域時，您的應用程式不需要重新部署或暫停，它隨時都能持續保持高可用性。

**建置高度回應的應用程式。** 您的應用程式可以針對您為資料庫選擇的所有區域，執行近乎即時的讀取和寫入。 Azure Cosmos DB 在內部處理具有您所選層級一致性層級保證的區域之間的資料複寫。

**建置高可用性應用程式。** 在全球多個區域中執行資料庫可提高資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**在區域性中斷期間維護商務持續性。** Azure Cosmos DB 支援在區域中斷期間[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 在區域性中斷期間，Azure Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 為了協助確保您的整個應用程式具有高可用性，Cosmos DB 提供手動容錯移轉 API 來模擬區域性中斷。 您可以使用此 API 來進行一般的業務持續性演練。

**全域調整讀取和寫入輸送量。** 您可以讓每個區域都能成為可寫入，並彈性規模讀取和寫入到全球各地。 您的應用程式在 Azure Cosmos 資料庫或容器上設定的輸送量，會布建到與您的 Azure Cosmos 帳戶相關聯的所有區域。 已布建的輸送量是由 [財務支援的 sla](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)保證。

**從數個定義完善的一致性模型中選擇。** Azure Cosmos DB 複寫通訊協定會提供五個定義完善、實用且直覺式的一致性模型。 每個模型都會在一致性與效能之間進行權衡取捨。 使用這些一致性模型來輕鬆建置全域散發的應用程式。

## <a name="next-steps"></a><a id="Next Steps"></a>後續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [如何在應用程式中設定多重區域寫入](how-to-multi-master.md)
* [設定多路連接的用戶端](how-to-manage-database-account.md#configure-multiple-write-regions)
* [從您的 Azure Cosmos 帳戶新增或移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 中的可程式化一致性模型](consistency-levels.md)
* [為應用程式選擇正確的一致性層級](./consistency-levels.md)
* [Azure Cosmos DB API 間的一致性層級](./consistency-levels.md)
* [各種一致性層級的可用性和效能權衡取捨](./consistency-levels.md)