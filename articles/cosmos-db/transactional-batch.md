---
title: 使用 .NET SDK Azure Cosmos DB 中的交易式批次作業
description: 瞭解如何在 Azure Cosmos DB .NET SDK 中使用 TransactionalBatch，以執行成功或失敗的點作業群組。
author: stefArroyo
ms.author: esarroyo
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/27/2020
ms.openlocfilehash: 9f6692db2da3722507136a468d1dcbdc2985e73f
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347552"
---
# <a name="transactional-batch-operations-in-azure-cosmos-db-using-the-net-sdk"></a>使用 .NET SDK Azure Cosmos DB 中的交易式批次作業
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

交易式批次描述一組需要成功或失敗的點運算，以及容器中的相同分割區索引鍵。 在 .NET SDK 中， `TransactionalBatch` 類別是用來定義此批次作業。 如果所有作業都依照交易式批次作業中的描述順序成功，就會認可交易。 但是，如果有任何作業失敗，則會回復整個交易。

## <a name="whats-a-transaction-in-azure-cosmos-db"></a>Azure Cosmos DB 中的交易

一般資料庫中的交易可以定義為以單一工作邏輯單位執行的一連串作業。 每一筆交易都提供 ACID (不可部分完成性、一致性、隔離、持久性) 屬性保證。

* 不可部分完成 **性保證在** 交易內完成的所有作業都會被視為單一單位，而且全部都已認可或全都不是。
* **一致性** 可確保資料在交易之間一律處於有效的狀態。
* **隔離** 可保證不會有兩個交易互相干擾。許多商業系統都會提供多個可以根據應用程式需求使用的隔離等級。
* **持久性** 可確保資料庫中所認可的任何變更一律會存在。
Azure Cosmos DB 針對相同[邏輯分割](partitioning-overview.md)區索引鍵內的作業[，支援具有快照隔離的完整 ACID 相容交易](database-transactions-optimistic-concurrency.md)。

## <a name="transactional-batch-operations-vs-stored-procedures"></a>交易式批次作業與預存程式

Azure Cosmos DB 目前支援預存程式，這也會提供作業的交易式範圍。 不過，交易式批次作業提供下列優點：

* **Language 選項** –您所使用的 SDK 和語言支援交易式批次，而預存程式則需要以 JavaScript 撰寫。
* 程式 **代碼版本控制**–版本控制應用程式程式碼，並將其上架到 CI/CD 管線，比協調預存程式的更新和確定在正確時間進行變換更為自然。 它也可讓您更輕鬆地回復變更。
* **效能** –相較于預存程式執行，減少對等作業的延遲，最多可達30%。
* **內容序列化** ：交易式批次內的每個作業都可以利用其承載的自訂序列化選項。

## <a name="how-to-create-a-transactional-batch-operation"></a>如何建立交易式批次作業

建立交易式批次作業時，您會從容器實例開始，然後呼叫 `CreateTransactionalBatch` ：

```csharp
string partitionKey = "The Family";
ParentClass parent = new ParentClass(){ Id = "The Parent", PartitionKey = partitionKey, Name = "John", Age = 30 };
ChildClass child = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatch batch = container.CreateTransactionalBatch(new PartitionKey(parent.PartitionKey)) 
  .CreateItem<ParentClass>(parent)
  .CreateItem<ChildClass>(child);
```

接下來，您必須 `ExecuteAsync` 在批次上呼叫：

```csharp
TransactionalBatchResponse batchResponse = await batch.ExecuteAsync();
```

一旦收到回應，請檢查是否成功，並將結果解壓縮：

```csharp
using (batchResponse)
{
  if (batchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = batchResponse.GetOperationResultAtIndex<ParentClass>(0);
    ParentClass parentClassResult = parentResult.Resource;
    TransactionalBatchOperationResult<ChildClass> childResult = batchResponse.GetOperationResultAtIndex<ChildClass>(1);
    ChildClass childClassResult = childResult.Resource;
  }
}
```

如果發生失敗，失敗的作業會有其對應錯誤的狀態碼。 所有其他作業都會有424狀態碼 (失敗的相依性) 。 在下列範例中，作業會失敗，因為它會嘗試建立已存在 (409 HttpStatusCode 的專案。衝突) 。 狀態碼可讓一個識別交易失敗的原因。

```csharp
// Parent's birthday!
parent.Age = 31;
// Naming two children with the same name, should abort the transaction
ChildClass anotherChild = new ChildClass(){ Id = "The Child", ParentId = parent.Id, PartitionKey = partitionKey };
TransactionalBatchResponse failedBatchResponse = await container.CreateTransactionalBatch(new PartitionKey(partitionKey))
  .ReplaceItem<ParentClass>(parent.Id, parent)
  .CreateItem<ChildClass>(anotherChild)
  .ExecuteAsync();

using (failedBatchResponse)
{
  if (!failedBatchResponse.IsSuccessStatusCode)
  {
    TransactionalBatchOperationResult<ParentClass> parentResult = failedBatchResponse.GetOperationResultAtIndex<ParentClass>(0);
    // parentResult.StatusCode is 424
    TransactionalBatchOperationResult<ChildClass> childResult = failedBatchResponse.GetOperationResultAtIndex<ChildClass>(1);
    // childResult.StatusCode is 409
  }
}
```

## <a name="how-are-transactional-batch-operations-executed"></a>交易式批次作業的執行方式

`ExecuteAsync`呼叫方法時，會將物件中的所有作業 `TransactionalBatch` 分組，並序列化為單一承載，並以單一要求的形式傳送至 Azure Cosmos DB 服務。

服務會接收要求並執行交易式範圍內的所有作業，並使用相同的序列化通訊協定傳迴響應。 這項回應可能是成功或失敗，並會針對每個作業提供個別的作業回應。

SDK 會公開回應以驗證結果，並選擇性地將每個內部操作結果解壓縮。

## <a name="limitations"></a>限制

目前有兩個已知的限制：

* Azure Cosmos DB 要求大小限制會限制承載的大小 `TransactionalBatch` 不超過 2 MB，而執行時間上限為5秒。
* 目前的限制為每個100作業， `TransactionalBatch` 以確保效能符合預期且在 sla 內。

## <a name="next-steps"></a>後續步驟

* 深入瞭解[您可以使用 TransactionalBatch 進行的](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/TransactionalBatch)作業

* 請流覽我們的 [範例](sql-api-dotnet-v3sdk-samples.md) ，以取得更多使用 COSMOS DB .net SDK 的方法
