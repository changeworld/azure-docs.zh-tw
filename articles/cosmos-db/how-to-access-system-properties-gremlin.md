---
title: 透過 Azure Cosmos DB 圖形存取系統文件屬性
description: 了解如何透過 Gremlin API 讀取和寫入 Cosmos DB 系統文件屬性
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: 61814082ebe9828a08da1e8786890b500c239082
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081836"
---
# <a name="system-document-properties"></a>系統文件屬性
[!INCLUDE[appliesto-gremlin-api](includes/appliesto-gremlin-api.md)]

Azure Cosmos DB [system properties](/rest/api/cosmos-db/databases) ```_ts``` ```_self``` ```_attachments``` ```_rid``` ```_etag``` 在每個檔上都有系統屬性，例如、、、和。 此外，Gremlin 引擎會在邊緣上新增 ```inVPartition``` 和 ```outVPartition``` 屬性。 根據預設，這些屬性都可以周遊。 不過，可以在 Gremlin 周遊中納入特定屬性或所有屬性。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

此屬性適用於開放式並行存取控制。 如果應用程式需要將作業分成幾個不同的周遊，它可以使用 eTag 屬性來避免並行寫入中的資料遺失。

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>存留時間 (TTL)

如果集合已啟用文件到期功能，且文件上已設定 ```ttl``` 屬性，則這個屬性可作為一般頂點或邊線屬性在 Gremlin 周遊中使用。 ```ProjectionStrategy``` 對於公開存留時間屬性不是必要的。

使用下列周遊建立的頂點會在 **123 秒** 後自動刪除。

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>下一步
* [Cosmos DB 開放式並行存取](faq.md#how-does-the-sql-api-provide-concurrency)
* Azure Cosmos DB 中的[存留時間 (TTL)](time-to-live.md)
