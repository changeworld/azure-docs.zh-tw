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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "72753218"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Azure Cosmos DB 遍及各區的情形

Azure Cosmos DB 是 Azure 中的基礎服務，而且根據預設，在 Azure 可供使用的所有區域中，一律都可使用。 Azure 目前已在全球 [54 個區域](https://azure.microsoft.com/global-infrastructure/regions/)中提供使用。 

[![Azure Cosmos DB 可用的區域](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

在提供給客戶的五個不同 Azure 雲端環境中，全都有提供 Cosmos DB：

* **Azure 公用**雲端，全球皆有提供。

* 您可以透過 Microsoft 和世紀間的獨特合作關係來取得**Azure 中國的世紀**，其中一個國家/地區在中國最大的網際網路提供者之一。

* **Azure Germany** 在資料信任者模型下提供服務，以確保 Deutsche Telecom 子公司 T-Systems International GmbH 以德國資料信任者的形式，掌管留在德國的客戶資料。

* **Azure Government** 在北美洲的四個區域中提供給 US Gov 機構和其合作夥伴使用。 

* 美國國防部 **（DoD）的 Azure Government**適用于美國國防部的兩個地區。

## <a name="regional-presence-with-global-distribution"></a>透過全球發佈遍及各區

預設會在所有 Azure 區域中提供 Azure Cosmos DB 所公開的所有 Api （包括 SQL、MongoDB、Cassandra、Gremlin 和 Table）。 例如，您可以讓 MongoDB 和 Cassandra Api Azure Cosmos DB 只在所有全域 Azure 區域中公開，同時在主權雲端（例如中國、德國、政府和國防部（DoD）區域）中提供。

Azure Cosmos DB 是[全域散發](distribute-data-globally.md)的資料庫服務。 您可以將任意數目的 Azure 區域與您的 Azure Cosmos 帳戶相關聯，而且您的資料會自動且透明地複寫。 您可以隨時在 Azure Cosmos 帳戶中新增或移除區域。 Azure Cosmos DB 具有周全的全球發佈功能和多重主要複寫通訊協定，可在第 99 個百分位數提供不到 10 毫秒的讀取和寫入延遲、具有 99.999 的讀取和寫入可用性，並可跨與您 Azure Cosmos 帳戶相關聯的所有區域，彈性調整針對其中的讀取和寫入所佈建的輸送量。 Azure Cosmos DB 也會提供五個具有完善定義的一致性模型，您可以選擇對資料套用特定的一致性模型。 最後，Azure Cosmos DB 是業界唯一的資料庫服務，可提供完整的[服務等級協定（SLA）](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) ，涵蓋布建的輸送量、第99百分位數的延遲、高可用性和一致性。 上述功能適用于所有 Azure 雲端。

## <a name="next-steps"></a>後續步驟

您現在可以透過下列文章瞭解 Azure Cosmos DB 的核心概念：

* [全球資料發佈](distribute-data-globally.md)
* [如何管理 Azure Cosmos DB 帳戶](manage-account.md)
* [佈建 Azure Cosmos 容器和資料庫的輸送量](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
