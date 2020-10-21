---
title: 在 Azure Cosmos DB 中尋找 Gremlin API 查詢的要求單位 (RU) 費用
description: 瞭解如何針對針對 Azure Cosmos 容器執行的 Gremlin 查詢，尋找要求單位 (RU) 收費。 您可以使用 Azure 入口網站、.NET、JAVA 驅動程式來尋找 RU 費用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 43685440de236d30698922b3b16a820eb935ae7c
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283958"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-gremlin-api"></a>針對 Azure Cosmos DB Gremlin API 中執行的作業尋找要求單位費用

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。

所有資料庫作業的成本會由 Azure Cosmos DB 進行正規化，並以要求單位 (簡稱 RU) 表示。 您可以將 ru 視為效能貨幣，以抽象化執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源（例如 CPU、IOPS 和記憶體）。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、點讀取或查詢，成本一律會以 ru 來測量。 若要深入瞭解，請參閱 [要求單位和它的考慮](request-units.md) 文章。

本文提供不同的方式，讓您可以針對 Azure Cosmos DB Gremlin API 中的容器執行的任何作業，找到 [要求單位](request-units.md) (RU) 耗用量。 如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](find-request-unit-charge-mongodb.md)、 [CASSANDRA API](find-request-unit-charge-cassandra.md)、 [SQL API](find-request-unit-charge.md)和 [資料表 API](find-request-unit-charge-table.md) 文章，以找出 RU/s 的費用。

Gremlin API 所傳回的標頭會對應至目前透過 Gremlin.NET 和 Java SDK 來呈現的自訂狀態屬性。 要求費用會提供在 `x-ms-request-charge` 索引鍵下方。 如果您使用的是 Gremlin API 時，會有多個選項可用來找出對 Azure Cosmos 容器的作業所產生的 RU 耗用量。

## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

1. 登入 [Azure 入口網站](https://portal.azure.com/)。

1. [建立新的 Azure Cosmos 帳戶](create-graph-gremlin-console.md#create-a-database-account)並為其提供資料，或選取已包含資料的現有帳戶。

1. 前往 [資料總管]**** 窗格，然後選取您要處理的容器。

1. 輸入有效的查詢，然後選取 [ **執行 Gremlin 查詢**]。

1. 選取下 [查詢統計資料]****，以顯示您執行的要求實際的要求費用。

:::image type="content" source="./media/find-request-unit-charge/portal-gremlin-query.png" alt-text="在 Azure 入口網站中取得 Gremlin 查詢要求費用的螢幕擷取畫面":::

## <a name="use-the-net-sdk-driver"></a>使用 .NET SDK 驅動程式

使用 [Gremlin.NET SDK](https://www.nuget.org/packages/Gremlin.Net/) 時，狀態屬性會提供在 `ResultSet<>` 物件的 `StatusAttributes` 屬性下方：

```csharp
ResultSet<dynamic> results = client.SubmitAsync<dynamic>("g.V().count()").Result;
double requestCharge = (double)results.StatusAttributes["x-ms-request-charge"];
```

如需詳細資訊，請參閱 [快速入門：使用 Azure Cosmos DB GREMLIN API 帳戶建立 .NET Framework 或 Core 應用程式](create-graph-dotnet.md)。

## <a name="use-the-java-sdk-driver"></a>使用 JAVA SDK 驅動程式

使用 [Gremlin Java SDK](https://mvnrepository.com/artifact/org.apache.tinkerpop/gremlin-driver) 時，可以對於 `ResultSet` 物件呼叫 `statusAttributes()` 方法來擷取狀態屬性：

```java
ResultSet results = client.submit("g.V().count()");
Double requestCharge = (Double)results.statusAttributes().get().get("x-ms-request-charge");
```

如需詳細資訊，請參閱 [快速入門：使用 JAVA SDK 在 Azure Cosmos DB 中建立圖形資料庫](create-graph-java.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)