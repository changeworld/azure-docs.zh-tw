---
title: 變更摘要提取模型
description: 了解如何使用 Azure Cosmos DB 變更摘要提取模型來讀取變更摘要，以及提取模型與變更摘要處理器之間的差異
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 09/09/2020
ms.reviewer: sngun
ms.openlocfilehash: b056c12f51c6e36a806f2bba0f5efe9ea9498798
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90015631"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>Azure Cosmos DB 中的變更摘要提取模型

您可以透過變更摘要提取模型，依自己的步調使用 Azure Cosmos DB 變更摘要。 如同您已可以透過[變更摘要處理器](change-feed-processor.md)進行的動作，您可以使用變更摘要提取模型，平行處理多個變更摘要取用者之間的變更。

> [!NOTE]
> 變更摘要提取模型目前僅適用於 [Azure Cosmos DB .NET SDK 中的預覽](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.13.0-preview)。 預覽尚未提供給其他 SDK 版本使用。

## <a name="comparing-with-change-feed-processor"></a>與變更摘要處理器比較

許多案例都可以使用[變更摘要處理器](change-feed-processor.md)或提取模型來處理變更摘要。 提取模型的接續權杖和變更摘要處理器的租用容器，都是變更摘要中最後處理項目 (或項目批次) 的「書籤」。

不過，您無法將接續權杖轉換成租用容器 (反之亦然)。

> [!NOTE]
> 在大部分情況下，當您需要從變更摘要讀取時，最簡單的選項是使用 [變更摘要處理器](change-feed-processor.md)。

在這些情況下，您應該考慮使用提取模型：

- 讀取特定分割區索引鍵的變更
- 控制用戶端接收變更以進行處理的步調
- 執行變更摘要中現有資料的一次性讀取 (例如，執行資料移轉) 

以下是變更摘要處理器與提取模型之間的一些主要差異：

|功能  | 變更摘要處理器| 提取模型 |
| --- | --- | --- |
| 追蹤處理變更摘要的目前時間點 | 租用 (儲存在 Azure Cosmos DB 容器中) | 接續權杖 (儲存在記憶體中或以手動方式保存) |
| 重新執行過去變更的能力 | 是，使用推送模型 | 是，使用提取模型|
| 輪詢未來的變更 | 根據使用者指定的 `WithPollInterval` 自動檢查變更 | 手動 |
| 處理整個容器的變更 | 是，且自動平行處理來自相同容器的多個執行緒/機器| 是，且使用 FeedTokens 手動平行處理 |
| 只處理單一分割區索引鍵的變更 | 不支援 | 是|
| 支援層級 | 正式推出 | 預覽 |

## <a name="consuming-an-entire-containers-changes"></a>使用整個容器的變更

您可以使用提取模型建立 `FeedIterator` 來處理變更摘要。 當您一開始建立時 `FeedIterator` ，您必須指定所需的值，此 `ChangeFeedStartFrom` 值包含讀取變更的起始位置以及所需的值 `FeedRange` 。 `FeedRange`是資料分割索引鍵值的範圍，並使用特定的來指定將從變更摘要讀取的專案 `FeedIterator` 。

您可以選擇性地指定 `ChangeFeedRequestOptions` 來設定 `PageSizeHint` 。 `PageSizeHint`是將在單一頁面中傳回的最大專案數。

`FeedIterator` 有兩種類別。 除了下列傳回物件的範例以外，您也可以使用 `Stream` 支援來取得回應。 串流可讓您讀取資料，而不需要先將其還原序列化，並儲存在用戶端資源上。

以下是取得傳回實體物件之 `FeedIterator` 的範例，在此案例中為 `User` 物件：

```csharp
FeedIterator<User> InteratorWithPOCOS = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());
```

以下是取得傳回 `Stream` 之 `FeedIterator` 的範例：

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Beginning());
```

如果您未提供 `FeedRange` 至 `FeedIterator` ，您可以依照自己的步調處理整個容器的變更摘要。 以下是從目前時間開始讀取所有變更的範例：

```csharp
FeedIterator iteratorForTheEntireContainer = container.GetChangeFeedStreamIterator<User>(ChangeFeedStartFrom.Now());

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>使用分割區索引鍵的變更

在某些情況下，您可能只想要處理特定分割區索引鍵的變更。 您可以取得特定分割區索引鍵的 `FeedIterator`，並使用與您對整個容器相同的方式來處理變更。

```csharp
FeedIterator<User> iteratorForPartitionKey = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(FeedRange.FromPartitionKey(new PartitionKey("PartitionKeyValue"))));

while (iteratorForThePartitionKey.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForThePartitionKey.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="using-feedrange-for-parallelization"></a>使用 FeedRange 進行平行處理

在[變更摘要處理器](change-feed-processor.md)中，工作會自動分散至多個取用者。 在變更摘要提取模型中，您可以使用 `FeedRange` 來平行處理變更摘要的處理。 `FeedRange` 代表分割區索引鍵值的範圍。

以下範例說明如何取得容器的範圍清單：

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

當您取得容器的 FeedRanges 清單時，您每個[實體分割區](partition-data.md#physical-partitions)會取得一個 `FeedRange`。

使用 `FeedRange`，您就可以建立 `FeedIterator`，將變更摘要的處理平行處理至多部電腦或執行緒。 不同于上一個範例，示範如何取得 `FeedIterator` 整個容器或單一分割區索引鍵的，您可以使用 FeedRanges 取得多個可平行處理變更摘要的 FeedIterators。

在您想要使用 FeedRanges 的案例中，您需要有可取得 FeedRanges 的協調器處理，並將其散發到這些電腦。 此散發可能是：

* 使用 `FeedRange.ToJsonString` 並散發此字串值。 取用者可以使用此值搭配 `FeedRange.FromJsonString`
* 如果是內含式散發，則傳遞 `FeedRange` 物件參考。

以下範例示範如何使用兩個平行讀取的假設個別電腦，從容器的變更摘要開頭進行讀取：

機器 1：

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[0]));
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

機器 2：

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning(ranges[1]));
while (iteratorB.HasMoreResults)
{
   FeedResponse<User> users = await iteratorB.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="saving-continuation-tokens"></a>儲存接續權杖

您可以建立接續權杖來儲存 `FeedIterator` 的位置。 接續權杖是一個字串值，會持續追蹤您 FeedIterator 上次處理的變更。 這會讓 `FeedIterator` 可在此時間點之後繼續進行。 下列程式碼會在建立容器之後，讀取變更摘要。 在沒有其他的變更可用之後，其會保存接續權杖，讓變更摘要耗用量可以在稍後繼續。

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.Beginning());

string continuation = null;

while (iterator.HasMoreResults)
{
   FeedResponse<User> users = await iterator.ReadNextAsync();
   continuation = users.ContinuationToken;

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}

// Some time later
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(ChangeFeedStartFrom.ContinuationToken(continuation));
```

只要 Cosmos 容器仍然存在，FeedIterator 的接續權杖就永遠不會過期。

## <a name="next-steps"></a>後續步驟

* [變更摘要的概觀](change-feed.md)
* [使用變更摘要處理器](change-feed-processor.md)
* [觸發 Azure Functions](change-feed-functions.md)