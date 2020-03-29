---
title: 遷移後優化步驟，使用 Azure Cosmos DB 的 MongoDB API
description: 此文檔提供從 MongoDB 到 Azure Cosmos DB 的 Mongo DB APi 的遷移後優化技術。
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: lbosq
ms.openlocfilehash: ce33651aae64d0a90264dde6da64b4044c6ce132
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063598"
---
# <a name="post-migration-optimization-steps-when-using-azure-cosmos-dbs-api-for-mongodb"></a>使用 Azure Cosmos DB 的蒙戈DB API 時，遷移後優化步驟

將蒙戈DB資料庫中存儲的資料移轉到蒙哥DB的 Azure Cosmos DB API 後，可以連接到 Azure Cosmos DB 並管理資料。 本指南提供了遷移後應考慮的步驟。 有關遷移步驟，請參閱[遷移蒙戈DB 到 Azure Cosmos DB 的 API 教程](../dms/tutorial-mongodb-cosmos-db.md)。

在本指南中，您將了解如何：

- [連接應用程式](#connect-your-application)
- [優化索引策略](#optimize-the-indexing-policy)
- [為蒙戈DB配置 Azure Cosmos DB 的 API 的全域分佈](#globally-distribute-your-data)
- [設置一致性級別](#set-consistency-level)

> [!NOTE]
> 應用程式級別上唯一的強制遷移後步驟是更改應用程式中的連接字串以指向新的 Azure Cosmos DB 帳戶。 建議優化所有其他遷移步驟。
>

## <a name="connect-your-application"></a>連接應用程式

1. 在[Azure 門戶](https://www.portal.azure.com/)中的新視窗符號中
2. 從[Azure 門戶](https://www.portal.azure.com/)中，在左側窗格中打開 **"所有資源**"功能表，並查找已將資料移轉到的 Azure Cosmos DB 帳戶。
3. 打開**連接字串**邊欄選項卡。 右窗格中有您成功連接到您的帳戶所需的所有資訊。
4. 使用應用程式佈建（或其他相關位置）中的連接資訊來反映應用中的 MongoDB 連接的 Azure Cosmos DB API。
![連接字串](./media/mongodb-post-migration/connection-string.png)

有關詳細資訊，請參閱[將 MongoDB 應用程式連接到 Azure Cosmos DB](connect-mongodb-account.md)頁面。

## <a name="optimize-the-indexing-policy"></a>優化索引策略

預設情況下，在將資料移轉到 Azure Cosmos DB 期間，所有資料欄位都會自動編制索引。 在許多情況下，此預設索引策略是可以接受的。 通常，刪除索引會優化寫入請求，並且讓預設索引策略（即自動索引）優化讀取請求。

有關索引的詳細資訊，請參閱 Azure [Cosmos DB 的 MongoDB API 中的資料索引](mongodb-indexing.md)以及[Azure Cosmos DB 文章中的索引](index-overview.md)。

## <a name="globally-distribute-your-data"></a>全球分佈您的資料

全球所有 [Azure 區域](https://azure.microsoft.com/regions/#services)都有提供 Azure Cosmos DB。 為 Azure Cosmos DB 帳戶選擇預設一致性級別後，可以關聯一個或多個 Azure 區域（具體取決於全域分發需求）。 為了高可用性和業務連續性，我們總是建議在至少 2 個區域運行。 您可以查看在[Azure Cosmos DB 中優化多區域部署成本的](optimize-cost-regions.md)提示。

要全域分發資料，請參閱在[Azure Cosmos DB 的 MongoDB API 上全域分發資料](tutorial-global-distribution-mongodb.md)。

## <a name="set-consistency-level"></a>設置一致性級別

Azure 宇宙 DB 提供了 5 個定義良好的[一致性級別](consistency-levels.md)。 要閱讀有關 MongoDB 和 Azure Cosmos DB 一致性級別之間的映射，請閱讀[一致性級別和 Azure Cosmos DB API](consistency-levels-across-apis.md)。 預設一致性級別是會話一致性級別。 更改一致性級別是可選的，您可以針對應用對其進行優化。 要使用 Azure 門戶更改一致性級別，請使用以下功能：

1. 轉到"設置"下的 **"預設一致性**"邊欄選項卡。
2. 選擇您的[一致性級別](consistency-levels.md)

大多數使用者將其一致性級別保留在預設會話一致性設置中。 但是，[對於不同的一致性級別，存在可用性和性能權衡](consistency-levels-tradeoffs.md)。

## <a name="next-steps"></a>後續步驟

* [將 MongoDB 應用程式連線至 Azure Cosmos DB](connect-mongodb-account.md)
* [使用 Studio 3T 連接到 Azure 宇宙資料庫帳戶](mongodb-mongochef.md)
* [如何使用 Azure Cosmos DB 適用於 MongoDB 的 API 來設定全域散發讀取](mongodb-readpreference.md)
* [使用 Azure Cosmos DB 適用於 MongoDB 的 API 讓資料過期](mongodb-time-to-live.md)
* [Azure Cosmos DB 中的一致性層級](consistency-levels.md)
* [Azure Cosmos DB 中的編製索引](index-overview.md)
* [在 Azure 宇宙資料庫中請求單位](request-units.md)