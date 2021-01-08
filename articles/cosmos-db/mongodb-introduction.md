---
title: 適用於 MongoDB 的 Azure Cosmos DB API 簡介
description: 了解如何使用適用於 MongoDB 的 Azure Cosmos DB API 以 Azure Cosmos DB 來儲存及查詢大量資料。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 11/25/2020
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab335c8cc682f6b33dcb30509d682186a178c676
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861068"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Azure Cosmos DB API
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

[Azure Cosmos DB](introduction.md) 是 Microsoft 全域發佈的多模型資料庫服務，適用於任務關鍵性應用程式。 Azure Cosmos DB 提供[一站式全域散發](distribute-data-globally.md)、全球[彈性調整的輸送量和儲存體](partitioning-overview.md)、達到第 99 個百分位數的個位數毫秒延遲，以及保證的高可用性，全部都由[領先業界的 SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) \(英文\) 所支援。 Azure Cosmos DB 會[自動編製資料的索引](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)，您不需要處理結構描述和索引管理。 它是多重模型，支援文件、索引鍵/值、圖表和單欄式資料模型。 Azure Cosmos DB 服務為 Cassandra、MongoDB、Gremlin 和 Azure 表格儲存體等常見 NoSQL API 實作 Wire Protocol。 這可讓您使用熟悉的 NoSQL 用戶端驅動程式和工具來與您的 Cosmos 資料庫互動。

> [!NOTE]
> [無伺服器容量模式](serverless.md)現已可在 Azure Cosmos DB 的 MongoDB API 上使用。

## <a name="wire-protocol-compatibility"></a>Wire Protocol 相容性

Azure Cosmos DB 會實作 MongoDB 有線通訊協定。 此實作允許與原生 MongoDB 用戶端 SDK、驅動程式和工具的透明相容性。 Azure Cosmos DB 不裝載 MongoDB 資料庫引擎。 您可以在這裡找到 MongoDB 所支援功能的詳細資料： 
- [適用於 MongoDB 的 Azure Cosmos DB API (引擎版本 3.6)](mongodb-feature-support-36.md)
- [適用於 MongoDB 的 Azure Cosmos DB API (引擎版本 3.2)](mongodb-feature-support.md)

根據預設，如果新帳戶是使用適用於 MongoDB 的 Azure Cosmos DB API 來建立，則可以與 MongoDB 的 Wire Protocol 3.6 版相容。 任何了解此通訊協定版本的 MongoDB 用戶端驅動程式，應該都能原生地連線到 Cosmos DB。

:::image type="content" source="./media/mongodb-introduction/cosmosdb-mongodb.png" alt-text="適用於 MongoDB 的 Azure Cosmos DB API" border="false":::

## <a name="key-benefits"></a>主要權益

Cosmos DB 作為完全受控且全域散發之資料庫的主要優點如[這裡](introduction.md)所述。 此外，以原生方式實作熱門 NoSQL API 的 Wire Protocol，使 Cosmos DB 能提供下列優點：

* 輕鬆地將應用程式移轉到 Cosmos DB，同時保留應用程式邏輯的重要部分。
* 維持您的應用程式可攜，並繼續維持雲端廠商中立。
* 取得由 Cosmos DB 所提供領先業界且有財務補償適用於常見 NoSQL API 的 SLA。
* 根據您的需要，彈性地調整針對 Cosmos 資料庫佈建的輸送量和儲存體，並僅支付您所需要的儲存體與輸送量。 這會大量節省成本。
* 周全全球散發且具有多重區域寫入複寫。

## <a name="cosmos-dbs-api-for-mongodb"></a>適用於 MongoDB 的 Cosmos DB API

請遵循快速入門建立 Azure Cosmos 帳戶，然後遷移您現有的 MongoDB 應用程式以使用 Azure Cosmos DB，或建置一個新的應用程式：

* [移轉現有的 MongoDB Node.js Web 應用程式](create-mongodb-nodejs.md)。
* [使用適用於 MongoDB 的 Azure Cosmos DB API 和 .NET SDK 建置 Web 應用程式](create-mongodb-dotnet.md)
* [使用適用於 MongoDB 的 Azure Cosmos DB API 和 Java SDK 建置主控台應用程式](create-mongodb-java.md)

## <a name="next-steps"></a>後續步驟

以下是可讓您快速入門的一些指標：

* 遵循[將 MongoDB 應用程式連線到 Azure Cosmos DB](connect-mongodb-account.md) 教學課程，以了解如何取得您的帳戶連接字串資訊。
* 依照[使用 Studio 3T 搭配 Azure Cosmos DB](mongodb-mongochef.md) 教學課程，了解如何在 Studio 3T 中建立 Azure Cosmos DB 資料庫和 MongoDB 應用程式之間的連線。
* 遵循[將 MongoDB 資料匯入到 Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json) 教學課程，以將您的資料匯入 Cosmos 資料庫。
* 使用 [Robo 3T](mongodb-robomongo.md) 連線到 Cosmos 帳戶。
* 了解如何[設定全球分散式應用程式的讀取喜好設定](../cosmos-db/tutorial-global-distribution-mongodb.md)。
* 在我們的 [疑難排解指南](mongodb-troubleshoot.md)中尋找常見錯誤的解決方案


<sup>注意：本文描述 Azure Cosmos DB 的功能，該功能提供對 MongoDB 資料庫的 Wire Protocol 相容性。Microsoft 不會執行 MongoDB 資料庫來提供這項服務。Azure Cosmos DB 與 MongoDB, Inc. 沒有附屬關係</sup>