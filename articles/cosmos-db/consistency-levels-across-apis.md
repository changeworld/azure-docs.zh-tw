---
title: 一致性層級與 Azure Cosmos DB API
description: 瞭解 Azure Cosmos DB 中不同 API 和 Apache Cassandra、MongoDB 中不同 API 之間的一致性級別映射
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: ef7d032d37105549ff7b05f85b953cd420954602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131463"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>一致性層級與 Azure Cosmos DB API

Azure Cosmos DB 為常用資料庫提供與有線協定相容 API 的本機支援。 其中包括蒙戈DB、阿帕奇卡珊多拉、格雷姆林和 Azure 表存儲。 這些資料庫不為一致性級別提供精確定義的一致性模型或 SLA 支援的保證。 它們通常只提供由 Azure Cosmos DB 所提供的五個一致性模型子集。 

使用 SQL API、Gremlin API 和表 API 時，將使用 Azure Cosmos 帳戶上配置的預設一致性級別。 

當為 MongoDB 使用 Cassandra API 或 Azure Cosmos DB 的 API 時，應用程式分別獲得 Apache Cassandra 和 MongoDB 提供的全套一致性級別，具有更強的一致性和持久性保證。 本文檔顯示了 Apache Cassandra 和 MongoDB 一致性級別的相應 Azure Cosmos DB 一致性級別。

## <a name="mapping-between-apache-cassandra-and-azure-cosmos-db-consistency-levels"></a><a id="cassandra-mapping"></a>Apache Cassandra 與 Azure Cosmos DB 一致性層級之間的對應

與 Azure Cosmos DB 不同，Apache Cassandra 不提供精確定義的一致性保證。  相反，Apache Cassandra 提供了寫入一致性級別和讀取一致性級別，以實現高可用性、一致性和延遲權衡。 使用 Azure 宇宙 DB 的卡珊多拉 API 時： 

* Apache Cassandra 的寫入一致性級別映射到 Azure Cosmos 帳戶上配置的預設一致性級別。 無法根據請求更改寫入操作 （CL） 的一致性。

* Azure Cosmos DB 會動態地將 Cassandra 用戶端驅動程式指定的讀取一致性級別映射到在讀取請求上動態配置的 Azure Cosmos DB 一致性級別之一。 

下表說明了在使用 Cassandra API 時，本機 Cassandra 一致性級別如何映射到 Azure Cosmos DB 的一致性級別：  

[![卡珊多拉一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a name="mapping-between-mongodb-and-azure-cosmos-db-consistency-levels"></a><a id="mongo-mapping"></a>蒙戈DB和 Azure 宇宙資料庫一致性級別之間的映射

與 Azure Cosmos DB 不同，本機 MongoDB 不提供精確定義的一致性保證。 相反，本機 MongoDB 允許使用者配置以下一致性保證：寫入問題、讀取問題和 isMaster 指令 - 將讀取操作定向到主副本或輔助副本，以實現所需的一致性級別。 

當使用 Azure Cosmos DB 的 API 進行 MongoDB 時，MongoDB 驅動程式將寫入區域視為主副本，所有其他區域都是讀取副本。 您可以選擇與 Azure Cosmos 帳戶關聯的區域作為主副本。 

在使用 Azure Cosmos DB 的蒙戈DB API 時：

* 寫入問題映射到 Azure Cosmos 帳戶上配置的預設一致性級別。
 
* Azure Cosmos DB 會動態地將 MongoDB 用戶端驅動程式指定的讀取問題映射到在讀取請求上動態配置的 Azure Cosmos DB 一致性級別之一。  

* 通過將區域作為第一個可寫區域，可以將與 Azure Cosmos 帳戶關聯的特定區域批為"主"。 

下表說明了在使用 Azure Cosmos DB 的 MongoDB 的 API 時，本機 MongoDB 寫入/讀取問題如何映射到 Azure Cosmos 一致性級別：

[![蒙戈DB一致性模型映射](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>後續步驟

深入了解 Azure Cosmos DB API 與開放原始碼 API 之間的一致性層級與相容性。 查看下列文章：

* [各種一致性層級的可用性和效能權衡取捨](consistency-levels-tradeoffs.md)
* [適用於 MongoDB 的 Azure Cosmos DB API 所支援的 MongoDB 功能](mongodb-feature-support.md)
* [Azure Cosmos DB Cassandra API 支援的 Apache Cassandra 功能](cassandra-support.md)
