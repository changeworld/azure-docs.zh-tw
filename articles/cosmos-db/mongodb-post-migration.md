---
title: 使用適用于 MongoDB 的 Azure Cosmos DB API 進行遷移後的優化步驟
description: 本檔提供從 MongoDB 到 Azure Cosmos DB 的 Mongo DB APi 的遷移後優化技術。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: 055604c399aa1641e823f24af499102bfff3ed0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263087"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用適用于 MongoDB 的 Azure Cosmos DB API 時的遷移後優化步驟

將儲存在 MongoDB 資料庫中的資料移轉至 Azure Cosmos DB 的 MongoDB API 之後，您就可以連接到 Azure Cosmos DB 並管理資料。 本指南提供您在遷移後應考慮的步驟。 如需遷移步驟，請參閱將[Mongodb 遷移至 Azure Cosmos DB 的 MONGODB API 教學](../dms/tutorial-mongodb-cosmos-db.md)課程。

在本指南中，您將了解如何：

- [連接您的應用程式](#connect-your-application)
- [優化索引編制原則](#optimize-the-indexing-policy)
- [設定適用于 MongoDB 的 Azure Cosmos DB API 的全域散發](#globally-distribute-your-data)
- [設定一致性層級](#set-consistency-level)

> [!NOTE]
> 在應用層級上，唯一強制的後續遷移步驟是將應用程式中的連接字串變更為指向新的 Azure Cosmos DB 帳戶。 所有其他的遷移步驟都是建議的優化做法。
>

## <a name="connect-your-application"></a>連接您的應用程式

1. 在新視窗中登入[Azure 入口網站](https://www.portal.azure.com/)
2. 從[Azure 入口網站](https://www.portal.azure.com/)的左窗格中，開啟 [**所有資源**] 功能表，並尋找您已遷移資料的 Azure Cosmos DB 帳戶。
3. 開啟 [**連接字串**] 分頁。 右窗格中有您成功連接到您的帳戶所需的所有資訊。
4. 使用應用程式設定中的連線資訊（或其他相關位置），在您的應用程式中反映 Azure Cosmos DB 的 MongoDB API 連線。
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="連接字串":::

如需詳細資訊，請參閱將[MongoDB 應用程式連接到 Azure Cosmos DB](connect-mongodb-account.md)頁面。

## <a name="optimize-the-indexing-policy"></a>優化索引編制原則

根據預設，所有資料欄位都會在將資料移轉至 Azure Cosmos DB 時自動編制索引。 在許多情況下，這是可接受的預設索引編制原則。 一般而言，移除索引會將寫入要求優化並具有預設的索引編制原則（亦即，自動編制索引），以優化讀取要求。

如需編制索引的詳細資訊，請參閱[Azure Cosmos DB 的 MONGODB API 中的資料索引編制](mongodb-indexing.md)，以及[Azure Cosmos DB 文章中的索引編制](index-overview.md)。

## <a name="globally-distribute-your-data"></a>全域散發您的資料

全球所有 [Azure 區域](https://azure.microsoft.com/regions/#services)都有提供 Azure Cosmos DB。 選取 Azure Cosmos DB 帳戶的預設一致性層級之後，您可以建立一或多個 Azure 區域的關聯（視您的全域散發需求而定）。 為了達到高可用性和商務持續性，我們一律建議在至少2個區域中執行。 您可以[在 Azure Cosmos DB 中查看優化多重區域部署成本](optimize-cost-regions.md)的秘訣。

若要全域散發您的資料，請參閱[在 Azure Cosmos DB 適用于 MongoDB 的 API 上全域散發資料](tutorial-global-distribution-mongodb.md)。

## <a name="set-consistency-level"></a>設定一致性層級

Azure Cosmos DB 提供5個定義完善的[一致性層級](consistency-levels.md)。 若要閱讀 MongoDB 與 Azure Cosmos DB 一致性層級之間對應的相關資訊，請參閱[一致性層級和 Azure Cosmos DB api](consistency-levels-across-apis.md)。 預設一致性層級是會話一致性層級。 變更一致性層級是選擇性的，您可以針對應用程式將其優化。 若要使用 Azure 入口網站來變更一致性層級：

1. 移至 [設定] 底下的 [**預設一致性**] 分頁。
2. 選取您的[一致性層級](consistency-levels.md)

大部分的使用者會將其一致性層級保持在預設的會話一致性設定。 不過，有[各種一致性層級的可用性和效能取捨](consistency-levels-tradeoffs.md)。

## <a name="next-steps"></a>後續步驟

* [將 MongoDB 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 連接到 Azure Cosmos DB 帳戶](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 適用於 MongoDB 的 API 來設定全域散發讀取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 適用於 MongoDB 的 API 讓資料過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
