---
title: 尋找 Azure Cosmos DB 中資料表 API 查詢的要求單位 (RU) 費用
description: 瞭解如何針對針對 Azure Cosmos 容器執行的資料表 API 查詢，尋找要求單位 (RU) 費用。 您可以使用 Azure 入口網站、.NET、JAVA、Python 和 Node.js 語言來尋找 RU 費用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: beca38e46dd2223211fb403d1e1734a5d406075b
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283950"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>找出 Azure Cosmos DB 中執行之作業的要求單位費用資料表 API

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。

所有資料庫作業的成本會由 Azure Cosmos DB 進行正規化，並以要求單位 (簡稱 RU) 表示。 您可以將 ru 視為效能貨幣，以抽象化執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源（例如 CPU、IOPS 和記憶體）。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、點讀取或查詢，成本一律會以 ru 來測量。 若要深入瞭解，請參閱 [要求單位和它的考慮](request-units.md) 文章。

本文提供不同的方式，讓您可以針對 Azure Cosmos DB 資料表 API 中的容器執行的任何作業，找到 [要求單位](request-units.md) (RU) 耗用量。 如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](find-request-unit-charge-mongodb.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [Gremlin api](find-request-unit-charge-gremlin.md)和 [SQL API](find-request-unit-charge.md) 文章，以找出 RU/秒費用。

## <a name="use-the-net-sdk"></a>使用 .NET SDK

目前，對於資料表作業傳回 RU 費用的唯一 SDK 是 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)。 `TableResult` 物件會公開您對於 Azure Cosmos DB 資料表 API 使用時由 SDK 填入的 `RequestCharge` 屬性：

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

如需詳細資訊，請參閱 [快速入門：使用 .NET SDK 和 Azure Cosmos DB 建立資料表 API 應用程式](create-table-dotnet.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)