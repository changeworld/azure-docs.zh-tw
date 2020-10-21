---
title: 在 Azure Cosmos DB 中尋找 Cassandra API 查詢的要求單位 (RU) 費用
description: 瞭解如何針對針對 Azure Cosmos 容器執行的 Cassandra 查詢，尋找要求單位 (RU) 收費。 您可以使用 Azure 入口網站、.NET 和 JAVA 驅動程式來尋找 RU 費用。
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: a0451e76ad8ce65486534bfbc68ae1b71adc9098
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/20/2020
ms.locfileid: "92283957"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-cassandra-api"></a>找出 Azure Cosmos DB 中執行之作業的要求單位費用 Cassandra API

Azure Cosmos DB 支援許多 API，例如 SQL、MongoDB、Cassandra、Gremlin 和資料表。 每個 API 都有它自己的一組資料庫作業。 這些作業的範圍可從簡單的點讀取和寫入到複雜查詢。 每個資料庫作業都會根據作業的複雜度來取用系統資源。

所有資料庫作業的成本會由 Azure Cosmos DB 進行正規化，並以要求單位 (簡稱 RU) 表示。 您可以將 ru 視為效能貨幣，以抽象化執行 Azure Cosmos DB 所支援之資料庫作業所需的系統資源（例如 CPU、IOPS 和記憶體）。 無論您使用哪種 API 與 Azure Cosmos 容器互動，成本始終由 RU 測量。 無論資料庫作業是寫入、點讀取或查詢，成本一律會以 ru 來測量。 若要深入瞭解，請參閱 [要求單位和它的考慮](request-units.md) 文章。

本文提供不同的方式，讓您可以針對 Azure Cosmos DB Cassandra API 中的容器執行的任何作業，找到 [要求單位](request-units.md) (RU) 耗用量。 如果您使用不同的 API，請參閱 [適用于 MongoDB 的 API](find-request-unit-charge-mongodb.md)、 [SQL API](find-request-unit-charge.md)、 [Gremlin API](find-request-unit-charge-gremlin.md)和 [資料表 API](find-request-unit-charge-table.md) 文章，以找出 RU/秒費用。

對 Azure Cosmos DB 的 Cassandra API 執行作業時，RU 費用會在傳入的裝載中以名為 `RequestCharge` 的欄位傳回。 有多個選項可供擷取 RU 費用。

## <a name="use-the-net-sdk"></a>使用 .NET SDK

使用 [.NET SDK](https://www.nuget.org/packages/CassandraCSharpDriver/) 時，可以擷取 `RowSet` 物件的 `Info` 屬性下的傳入承載：

```csharp
RowSet rowSet = session.Execute("SELECT table_name FROM system_schema.tables;");
double requestCharge = BitConverter.ToDouble(rowSet.Info.IncomingPayload["RequestCharge"].Reverse().ToArray(), 0);
```

如需詳細資訊，請參閱 [快速入門：使用 .NET SDK 和 Azure Cosmos DB 來建立 Cassandra 應用程式](create-cassandra-dotnet.md)。

## <a name="use-the-java-sdk"></a>使用 Java SDK

使用 [Java SDK](https://mvnrepository.com/artifact/com.datastax.cassandra/cassandra-driver-core) 時，可以對 `ResultSet` 物件呼叫 `getExecutionInfo()` 方法來擷取傳入承載：

```java
ResultSet resultSet = session.execute("SELECT table_name FROM system_schema.tables;");
Double requestCharge = resultSet.getExecutionInfo().getIncomingPayload().get("RequestCharge").getDouble();
```

如需詳細資訊，請參閱 [快速入門：使用 JAVA SDK 和 Azure Cosmos DB 來建立 Cassandra 應用程式](create-cassandra-java.md)。

## <a name="next-steps"></a>後續步驟

若要了解如何最佳化您的 RU 耗用量，請參閱下列文章：

* [Azure Cosmos DB 中的要求單位和輸送量](request-units.md)
* [在 Azure Cosmos DB 中最佳化已佈建的輸送量成本](optimize-cost-throughput.md)
* [在 Azure Cosmos DB 中最佳化查詢成本](optimize-cost-queries.md)