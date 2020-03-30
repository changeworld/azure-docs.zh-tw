---
title: Azure Cosmos DB 遍及各區的情形
description: 本文說明 Azure Cosmos DB 及不同雲端環境遍及各區的情形。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753218"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 遍及各區的情形

Azure Cosmos DB 是 Azure 中的基礎服務，預設情況下，始終在 Azure 可用的所有區域可用。 Azure 目前已在全球 [54 個區域](https://azure.microsoft.com/global-infrastructure/regions/)中提供使用。 

[![Azure 宇宙資料庫可用的區域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

在提供給客戶的五個不同 Azure 雲端環境中，全都有提供 Cosmos DB：

* **Azure 公用**雲端，全球皆有提供。

* **Azure China 21Vianet**通過微軟與中國最大的互聯網供應商之一 21Vianet 之間的獨特合作關係提供。

* **Azure Germany** 在資料信任者模型下提供服務，以確保 Deutsche Telecom 子公司 T-Systems International GmbH 以德國資料信任者的形式，掌管留在德國的客戶資料。

* **Azure Government** 在北美洲的四個區域中提供給 US Gov 機構和其合作夥伴使用。 

* **Azure 國防部政府 （DoD）** 在美國兩個地區向美國國防部提供。

## <a name="regional-presence-with-global-distribution"></a>透過全球發佈遍及各區

預設情況下，Azure Cosmos DB 公開的所有 API（包括 SQL、MongoDB、Cassandra、Gremlin 和表）在所有 Azure 區域都可用。 例如，Azure Cosmos DB 不僅在所有全域 Azure 區域中，而且在中國、德國、政府和國防部 （DoD） 區域等主權雲中公開蒙戈DB和 Cassandra API。

Azure Cosmos DB 是一個[全域分佈](distribute-data-globally.md)的資料庫服務。 您可以將任意數目的 Azure 區域與您的 Azure Cosmos 帳戶相關聯，而且您的資料會自動且透明地複寫。 您可以隨時在 Azure Cosmos 帳戶中新增或移除區域。 Azure Cosmos DB 具有周全的全球發佈功能和多重主要複寫通訊協定，可在第 99 個百分位數提供不到 10 毫秒的讀取和寫入延遲、具有 99.999 的讀取和寫入可用性，並可跨與您 Azure Cosmos 帳戶相關聯的所有區域，彈性調整針對其中的讀取和寫入所佈建的輸送量。 Azure Cosmos DB 也會提供五個具有完善定義的一致性模型，您可以選擇對資料套用特定的一致性模型。 最後，Azure Cosmos DB 是業內唯一提供全面[服務等級協定 （SLA） 的](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)資料庫服務，包括預配輸送量、第 99 個百分位數的延遲、高可用性和一致性。 上述功能在所有 Azure 雲中都可用。

## <a name="next-steps"></a>後續步驟

現在，您可以通過以下文章瞭解 Azure Cosmos DB 的核心概念：

* [全球資料發佈](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帳戶](manage-account.md)
* [佈建 Azure Cosmos 容器和資料庫的輸送量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
