---
title: 使用 Azure Cosmos DB 全域散發資料
description: 了解如何從 Azure Cosmos DB (全域散發的多模型資料庫服務)，使用全域資料庫進行全球規模的異地複寫、多重主機、容錯移轉及資料復原。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982118"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>透過 Azure Cosmos DB 進行全域資料散發 - 概觀

當今的應用程式需要高度回應,並且始終在線。 為了達到低延遲和高可用性，這些應用程式的執行個體必須部署在接近使用者的資料中心內。 這些應用程式通常會部署在多個資料中心內，因此稱為全域散發的應用程式。 全域散發的應用程式需要全域散發的資料庫，這類資料庫可以透明方式複寫世界各地的資料，讓應用程式使用接近其使用者的資料複本。 

Azure Cosmos DB 是一個全域散發的資料庫服務，旨在提供低延遲性、有彈性的輸送量延展性、妥善定義以保有資料一致性的語法，以及高可用性。 簡而言之,如果應用程式需要保證在世界任何地方的快速回應時間,如果需要始終處於連線狀態,並且需要吞吐量和存儲的無限彈性可擴充性,則應在 Azure Cosmos DB 上構建應用程式。

您可以將資料庫設定為全域散發，並且在任何可用的 Azure 區域中使用。 要降低延遲,請將數據放置在靠近使用者位置的位置。 選擇所需的區域，取決於您應用程式能夠觸達的全域範圍以及使用者所在的位置。 Cosmos DB 透明地將資料複製到與您的 Cosmos 帳戶關聯的所有區域。 它會提供全域散發的 Azure Cosmos 資料庫和容器的單一系統映像，使您的應用程式可以在本機讀取和寫入。 

透過 Azure Cosmos DB，您可以隨時新增或移除任何與您帳戶相關聯的區域。 您不需暫停或重新部署應用程式，就能新增或移除區域。 由於服務本機提供的多宿主功能,它一直高度可用。

![高可用性的部署拓撲](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>全域散發的主要優點

**構建全域活動-活動應用。** 憑藉其新穎的多主機複製協定,每個區域都支援寫入和讀取。 多主機功能還支援:

- 無限的彈性寫入和讀取可擴充性。 
- 在世界各地具有 99.999% 的讀取和寫入可用性。
- 保證讀取和寫入會以第 99 百分位數且小於 10 毫秒的方式提供服務。

通過使用 Azure Cosmos DB 多宿主 API,應用程式知道最近的區域,並可以向該區域發送請求。 識別最靠近的區域不會變更任何組態。 當您向 Azure Cosmos 帳戶添加和刪除區域時,應用程式不需要重新部署或暫停,它仍隨時高度可用。

**建置高度回應的應用程式。** 應用程式可以針對為資料庫選擇的所有區域執行近乎即時的讀取和寫入。 Azure Cosmos DB 在內部處理區域之間的數據複製,並保證所選級別的一致性級別。

**建置高可用性應用程式。** 在全球多個區域運行資料庫可提高資料庫的可用性。 如果一個區域無法使用，其他區域就會自動處理應用程式的要求。 Azure Cosmos DB 可為多重區域資料庫提供達 99.999% 的讀取和寫入可用性。

**在區域中斷期間保持業務連續性。** Azure Cosmos DB 支援在區域中斷期間[自動容錯移轉](how-to-manage-database-account.md#automatic-failover)。 在區域性中斷期間，Azure Cosmos DB 會繼續維持其延遲性、可用性、一致性和輸送量 SLA。 為了幫助確保整個應用程式高度可用,Cosmos DB 提供了手動故障轉移 API 來類比區域中斷。 您可以使用此 API 來進行一般的業務持續性演練。

**全域擴展讀取和寫入輸送量。** 您可以使每個區域都能在世界各地進行可寫和彈性縮放讀取和寫入。 應用程式在 Azure Cosmos 資料庫或容器上配置的輸送量保證在與 Azure Cosmos 帳戶關聯的所有區域之間傳遞。 預配輸送量由[財務支援的 SL](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)保證。

**從多個定義良好的一致性模型中進行選擇。** Azure Cosmos DB 複寫通訊協定會提供五個定義完善、實用且直覺式的一致性模型。 每個模型都會在一致性與效能之間進行權衡取捨。 使用這些一致性模型來輕鬆建置全域散發的應用程式。

## <a name="next-steps"></a><a id="Next Steps"></a>後續步驟

從下列文章中深入了解全域散發：

* [全域散發 - 運作原理](global-dist-under-the-hood.md)
* [如何在應用程式中設定多主機](how-to-multi-master.md)
* [設定多路連接的用戶端](how-to-manage-database-account.md#configure-multiple-write-regions)
* [從您的 Azure Cosmos 帳戶新增或移除區域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [建立 SQL API 帳戶的自訂衝突解決原則](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Cosmos DB 的可程式設計一致性模型](consistency-levels.md)
* [為應用程式選擇正確的一致性層級](consistency-levels-choosing.md)
* [Azure Cosmos DB API 間的一致性層級](consistency-levels-across-apis.md)
* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)

