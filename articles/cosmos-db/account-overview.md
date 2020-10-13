---
title: 使用 Azure Cosmos DB 帳戶
description: 本文說明如何建立及使用 Azure Cosmos 帳戶。 它也會顯示 Azure Cosmos 帳戶中的元素階層
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/09/2020
ms.reviewer: sngun
ms.openlocfilehash: d01164552b1f3f10f592cedf80eafe57566bf09c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91661069"
---
# <a name="work-with-azure-cosmos-account"></a>使用 Azure Cosmos 帳戶

Azure Cosmos DB 是完全受控的平台即服務 (PaaS)。 若要開始使用 Azure Cosmos DB，首先您應在 Azure 訂用帳戶中建立 Azure Cosmos 帳戶。 您的 Azure Cosmos 帳戶包含唯一的 DNS 名稱，且您可以使用 Azure 入口網站、Azure CLI 或使用不同的語言特定 SDK 來管理帳戶。 如需詳細資訊，請參閱[如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)。

Azure Cosmos 帳戶是全域散發和高可用性的基本單位。 若要將您的資料和輸送量全域散發至多個 Azure 區域，您可以隨時在 Azure Cosmos 帳戶中新增和移除 Azure 區域。 您可以設定 Azure Cosmos 帳戶，使其包含單一或多個寫入區域。 如需詳細資訊，請參閱[如何在 Azure Cosmos 帳戶中新增和移除 Azure 區域](how-to-manage-database-account.md)。 您可以在 Azure Cosmos 帳戶上設定[預設一致性](consistency-levels.md)層級。 Azure Cosmos DB 會提供涵蓋輸送量、第 99 百分位的延遲，一致性和高可用性的完整 SLA。 如需詳細資訊，請參閱 [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)。

若要安全地管理 Azure Cosmos 帳戶中所有資料的存取權，您可以使用與您的帳戶相關聯的 [主要金鑰](secure-access-to-data.md) 。 若要更安全地存取您的資料，您可以在 Azure Cosmos 帳戶上設定 [VNET 服務端點](vnet-service-endpoint.md) 和 [IP 防火牆](firewall-support.md) 。 

## <a name="elements-in-an-azure-cosmos-account"></a>Azure Cosmos 帳戶中的元素

Azure Cosmos 容器是擴充性的基礎單位。 您在容器上可佈建的輸送量 (RU/秒) 和儲存體幾乎不受限制。 Azure Cosmos DB 會使用您指定的邏輯分割區索引鍵以透明的方式分割您的容器，以彈性地調整您佈建的輸送量和儲存體。 如需詳細資訊，請參閱[使用 Azure Cosmos 容器和項目](databases-containers-items.md)。

目前，您最多可以在 Azure 訂用帳戶下建立50個 Azure Cosmos 帳戶 (這是可透過支援要求) 增加的軟限制。 單一 Azure Cosmos 帳戶可管理的資料量和佈建的輸送量幾乎不受限制。 若要管理您的資料和佈建的輸送量，您可以在您的帳戶下建立一或多個 Azure Cosmos 資料庫，並且可在該資料庫內建立一或多個容器。 下圖顯示 Azure Cosmos 帳戶中的元素階層：

:::image type="content" source="./media/account-overview/hierarchy.png" alt-text="Azure Cosmos 帳戶的階層" border="false":::

## <a name="next-steps"></a>後續步驟

瞭解如何管理您的 Azure Cosmos 帳戶和其他概念：

* [如何管理 Azure Cosmos 帳戶](how-to-manage-database-account.md)
* [全球發佈](distribute-data-globally.md)
* [一致性層級](consistency-levels.md)
* [使用 Azure Cosmos 容器和項目](databases-containers-items.md)
* [Azure Cosmos 帳戶的 VNET 服務端點](vnet-service-endpoint.md)
* [Azure Cosmos 帳戶的 IP 防火牆](firewall-support.md)
* [如何在 Azure Cosmos 帳戶中新增和移除 Azure 區域](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
