---
title: 使用 Azure Cosmos DB 適用于 MongoDB 的 API 的遷移後優化步驟
description: 本檔提供從 MongoDB 到 Azure Cosmos DB 的 Mongo DB APi 的遷移後優化技術。
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
ms.author: jasonh
ms.openlocfilehash: 6733e0f6447d055da8d349940bc7c7665e003e33
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92475595"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 適用于 MongoDB 的 API 時的遷移後優化步驟

將 MongoDB 資料庫中儲存的資料遷移至 Azure Cosmos DB 的 MongoDB 版 API 之後，您就可以連線到 Azure Cosmos DB 並管理這些資料。 本指南提供您在移轉後應考慮進行的步驟。 如需遷移步驟，請參閱將 [Mongodb 遷移至 Azure Cosmos DB 適用于 mongodb 的 API 教學](../dms/tutorial-mongodb-cosmos-db.md) 課程。

在本指南中，您將了解如何：

- [連接您的應用程式](#connect-your-application)
- [將索引編制原則優化](#optimize-the-indexing-policy)
- [設定適用于 MongoDB 的 Azure Cosmos DB API 的全域散發](#globally-distribute-your-data)
- [設定一致性層級](#set-consistency-level)

> [!NOTE]
> 在應用層級上，唯一強制的遷移後步驟是將應用程式中的連接字串變更為指向新的 Azure Cosmos DB 帳戶。 所有其他的遷移步驟都是建議的優化。
>

## <a name="connect-your-application"></a>連接您的應用程式

1. 在新的視窗中登入 [Azure 入口網站](https://www.portal.azure.com/)
2. 從 [Azure 入口網站](https://www.portal.azure.com/)的左窗格中，開啟 [ **所有資源** ] 功能表，並尋找您已遷移資料的 Azure Cosmos DB 帳戶。
3. 開啟 [ **連接字串** ] 分頁。 右窗格中有您成功連接到您的帳戶所需的所有資訊。
4. 使用應用程式設定 (中的連線資訊，或) 其他相關位置，以反映應用程式中 Azure Cosmos DB 適用于 MongoDB 的 API 連接。
:::image type="content" source="./media/mongodb-post-migration/connection-string.png" alt-text="螢幕擷取畫面：顯示連接字串的設定。":::

如需詳細資訊，請參閱將 [MongoDB 應用程式連接到 Azure Cosmos DB](connect-mongodb-account.md) 頁面。

## <a name="optimize-the-indexing-policy"></a>將索引編制原則優化

依預設，所有資料欄位都會自動編制索引，以將資料移轉至 Azure Cosmos DB。 在許多情況下，此預設索引編制原則是可接受的。 一般而言，移除索引會優化寫入要求並具有預設的索引編制原則 (例如，自動編制索引) 將讀取要求優化。

如需編制索引的詳細資訊，請參閱 [Azure Cosmos DB 適用于 MongoDB 的 API 中的資料編制索引](mongodb-indexing.md) ，以及 [Azure Cosmos DB 文章中的索引編制](index-overview.md) 。

## <a name="globally-distribute-your-data"></a>全域散發您的資料

全球所有 [Azure 區域](https://azure.microsoft.com/regions/#services)都有提供 Azure Cosmos DB。 選取 Azure Cosmos DB 帳戶的預設一致性層級之後，您可以根據您的全域散發需求) ，將一或多個 Azure 區域 (關聯。 針對高可用性和商務持續性，我們一律建議至少在2個區域中執行。 您可以參閱 [Azure Cosmos DB 中的優化多重區域部署成本](optimize-cost-regions.md)的秘訣。

若要全域散發您的資料，請參閱 [全域散發 Azure Cosmos DB 的 MONGODB API 的資料](tutorial-global-distribution-mongodb.md)。

## <a name="set-consistency-level"></a>設定一致性層級

Azure Cosmos DB 提供五個定義完善的 [一致性層級](consistency-levels.md)。 若要閱讀 MongoDB 與 Azure Cosmos DB 一致性層級之間對應的相關資訊，請參閱 [一致性層級和 Azure Cosmos DB api](./consistency-levels.md)。 預設一致性層級是會話一致性層級。 變更一致性層級是選擇性的，您可以針對您的應用程式進行優化。 若要使用 Azure 入口網站來變更一致性層級：

1. 移至 [設定] 下的 [ **預設一致性** ] 分頁。
2. 選取您的 [一致性層級](consistency-levels.md)

大部分的使用者會將其一致性層級保留在預設的會話一致性設定中。 不過，有 [各種一致性層級的可用性和效能權衡取捨](./consistency-levels.md)。

## <a name="next-steps"></a>後續步驟

* [將 MongoDB 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 連接到 Azure Cosmos DB 帳戶](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 適用於 MongoDB 的 API 來設定全域散發讀取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 適用於 MongoDB 的 API 讓資料過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)