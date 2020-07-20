---
title: 一致性層級與 Azure Cosmos DB API
description: 瞭解 Azure Cosmos DB 和 Apache Cassandra （MongoDB）中不同 Api 之間的一致性層級對應
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 2398e95d9a119fe24c97f3887d16aa5b86c6ac76
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85119302"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

Azure Cosmos DB 為熱門資料庫提供與有線通訊協定相容的 Api 原生支援。 這些包括 MongoDB、Apache Cassandra、Gremlin 和 Azure 資料表儲存體。 這些資料庫不提供精確定義的一致性模型或一致性層級的 SLA 支援保證。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 

使用 SQL API、Gremlin API 和資料表 API 時，會使用在 Azure Cosmos 帳戶上設定的預設一致性層級。 

使用 Cassandra API 或 Azure Cosmos DB 適用于 MongoDB 的 API 時，應用程式會分別取得 Apache Cassandra 和 MongoDB 提供的一組完整一致性層級，並提供更強的一致性和持久性保證。 本檔說明 Apache Cassandra 和 MongoDB 一致性層級的對應 Azure Cosmos DB 一致性層級。

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

與 Azure Cosmos DB 不同的是，Apache Cassandra 並不會以原生方式提供精確定義的一致性保證。  取而代之的是，Apache Cassandra 會提供寫入一致性層級和讀取一致性層級，以實現高可用性、一致性和延遲的取捨。 使用 Azure Cosmos DB 的 Cassandra API 時： 

* Apache Cassandra 的寫入一致性層級會對應至 Azure Cosmos 帳戶上設定的預設一致性層級。 無法針對每個要求變更寫入作業（CL）的一致性。

* Azure Cosmos DB 會將 Cassandra 用戶端驅動程式所指定的讀取一致性層級，動態對應至在讀取要求上動態設定的其中一個 Azure Cosmos DB 一致性層級。 

下表說明使用 Cassandra API 時，原生 Cassandra 一致性層級如何對應至 Azure Cosmos DB 的一致性層級：  

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" alt-text="Cassandra 一致性模型對應" lightbox="./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png" :::

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>MongoDB 與 Azure Cosmos DB 一致性層級之間的對應

不同于 Azure Cosmos DB，原生 MongoDB 不會提供精確定義的一致性保證。 取而代之的是，原生 MongoDB 可讓使用者設定下列一致性保證：寫入考慮、讀取考慮，以及 isMaster 指示詞，以將讀取作業導向至主要或次要複本，以達到所需的一致性層級。 

使用 Azure Cosmos DB 適用于 MongoDB 的 API 時，MongoDB 驅動程式會將您的寫入區域視為主要複本，而所有其他區域都是讀取複本。 您可以選擇與您的 Azure Cosmos 帳戶相關聯的區域作為主要複本。 

使用 Azure Cosmos DB 適用于 MongoDB 的 API 時：

* 寫入考慮會對應至 Azure Cosmos 帳戶上設定的預設一致性層級。
 
* Azure Cosmos DB 會將 MongoDB 用戶端驅動程式所指定的讀取考慮動態對應至在讀取要求上動態設定的其中一個 Azure Cosmos DB 一致性層級。  

* 您可以藉由將區域做為第一個可寫入的區域，將與您的 Azure Cosmos 帳戶相關聯的特定區域標注為「主要」。 

下表說明使用適用于 MongoDB 的 Azure Cosmos DB API 時，原生 MongoDB 寫入/讀取考慮如何對應至 Azure Cosmos 一致性層級：

:::image type="content" source="./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png" alt-text="MongoDB 一致性模型對應" lightbox= "./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png":::

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性。 查看下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [適用於 MongoDB 的 Azure Cosmos DB API 所支援的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)
