---
title: 變更摘要提取模型
description: 瞭解如何使用 Azure Cosmos DB 變更摘要提取模型來讀取變更摘要，以及提取模型和變更摘要處理器之間的差異
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/10/2020
ms.reviewer: sngun
ms.openlocfilehash: 0e6e243ceb73ca2a1180e59ba6c6b4095ed6069a
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116708"
---
# <a name="change-feed-pull-model-in-azure-cosmos-db"></a>變更 Azure Cosmos DB 中的摘要提取模型

有了變更摘要提取模型，您就可以依自己的步調使用 Azure Cosmos DB 變更摘要。 您可以使用變更摘要[處理器](change-feed-processor.md)來進行，您可以使用變更摘要提取模型，平行處理多個變更摘要取用者之間的變更。

> [!NOTE]
> 變更摘要提取模型目前僅在[Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)中為預覽狀態。 預覽尚未提供給其他 SDK 版本使用。

## <a name="consuming-an-entire-containers-changes"></a>使用整個容器的變更

您可以 `FeedIterator` 使用提取模型來建立來處理變更摘要。 當您一開始建立時 `FeedIterator` ，可以在中指定選擇性的 `StartTime` `ChangeFeedRequestOptions` 。 若未指定，則 `StartTime` 會是目前的時間。

`FeedIterator`有兩種類別。 除了下列傳回實體物件的範例以外，您也可以透過支援取得回應 `Stream` 。 資料流程可讓您讀取資料，而不需要先將它還原序列化，並儲存在用戶端資源上。

以下是取得傳回 `FeedIterator` 實體物件（在此案例中為物件）的範例 `User` ：

```csharp
FeedIterator<User> iteratorWithPOCOS = container.GetChangeFeedIterator<User>();
```

以下是取得會傳回之的範例 `FeedIterator` `Stream` ：

```csharp
FeedIterator iteratorWithStreams = container.GetChangeFeedStreamIterator();
```

使用 `FeedIterator` ，您可以依自己的步調輕鬆地處理整個容器的變更摘要。 以下是範例：

```csharp
FeedIterator<User> iteratorForTheEntireContainer= container.GetChangeFeedIterator(new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

while (iteratorForTheEntireContainer.HasMoreResults)
{
   FeedResponse<User> users = await iteratorForTheEntireContainer.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

## <a name="consuming-a-partition-keys-changes"></a>使用資料分割索引鍵的變更

在某些情況下，您可能只想要處理特定的分割區索引鍵的變更。 您可以取得特定分割區索引鍵的，並以與 `FeedIterator` 整個容器相同的方式來處理變更：

```csharp
FeedIterator<User> iteratorForThePartitionKey = container.GetChangeFeedIterator(new PartitionKey("myPartitionKeyValueToRead"), new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

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

在[變更摘要處理器](change-feed-processor.md)中，工作會自動分散到多個取用者。 在變更摘要提取模型中，您可以使用 `FeedRange` 來平行處理變更摘要的處理。 `FeedRange`代表資料分割索引鍵值的範圍。

以下範例顯示如何取得容器的範圍清單：

```csharp
IReadOnlyList<FeedRange> ranges = await container.GetFeedRangesAsync();
```

當您取得容器的 FeedRanges 清單時， `FeedRange` 每個[實體分割](partition-data.md#physical-partitions)區會取得一個。

`FeedRange`您可以使用，建立 `FeedIterator` 來平行處理多部電腦或執行緒上的變更摘要處理。 與先前示範如何取得整個容器單一的範例不同的是 `FeedIterator` ，您可以使用 `FeedRange` 來取得可平行處理變更摘要的多個 FeedIterators。

在您想要使用 FeedRanges 的情況下，您需要有可取得 FeedRanges 的協調器進程，並將它們散發到這些電腦。 此散發可能是：

* 使用 `FeedRange.ToJsonString` 和散發這個字串值。 取用者可以使用此值搭配`FeedRange.FromJsonString`
* 如果是同進程散發，則傳遞 `FeedRange` 物件參考。

以下範例示範如何使用兩個平行讀取的假設個別電腦，從容器的變更摘要的開頭讀取：

機器1：

```csharp
FeedIterator<User> iteratorA = container.GetChangeFeedIterator<Person>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
while (iteratorA.HasMoreResults)
{
   FeedResponse<User> users = await iteratorA.ReadNextAsync();

   foreach (User user in users)
    {
        Console.WriteLine($"Detected change for user with id {user.id}");
    }
}
```

機器2：

```csharp
FeedIterator<User> iteratorB = container.GetChangeFeedIterator<User>(ranges[1], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});
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

您可以藉 `FeedIterator` 由建立接續 token 來儲存的位置。 接續 token 是一個字串值，會持續追蹤您的 FeedIterator 上次處理的變更。 這可讓 `FeedIterator` 稍後在此時間點繼續。 下列程式碼會在建立容器之後，讀取變更摘要。 在沒有更多變更可用之後，它會保存接續權杖，讓變更摘要耗用量可以在稍後繼續。

```csharp
FeedIterator<User> iterator = container.GetChangeFeedIterator<User>(ranges[0], new ChangeFeedRequestOptions{StartTime = DateTime.MinValue});

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
FeedIterator<User> iteratorThatResumesFromLastPoint = container.GetChangeFeedIterator<User>(continuation);
```

## <a name="comparing-with-change-feed-processor"></a>與變更摘要處理器比較

許多案例都可以使用[變更摘要處理器](change-feed-processor.md)或提取模型來處理變更摘要。 提取模型的接續權杖和變更摘要處理器的租用容器，都是變更摘要中最後處理的專案（或專案批次）的「書簽」。
不過，您無法將接續權杖轉換成租用容器（反之亦然）。

在這些情況下，您應該考慮使用提取模型：

- 您想要在變更摘要中執行一次讀取現有的資料
- 您只想要讀取特定分割區索引鍵的變更
- 您不想要使用推送模型，而且想要依自己的步調取用變更摘要

以下是變更摘要處理器和提取模型之間的一些主要差異：

|  | 變更摘要處理器| 提取模型 |
| --- | --- | --- |
| 追蹤目前處理變更摘要的時間點 | 租用（儲存在 Azure Cosmos DB 容器中） | 接續 token （儲存在記憶體中或以手動方式保存） |
| 重新執行過去變更的能力 | 是，使用推播模型 | 是，使用提取模型|
| 輪詢未來的變更 | 根據使用者指定的自動檢查變更`WithPollInterval` | 手動 |
| 處理整個容器的變更 | 是，並自動平行處理來自相同容器的多個執行緒/機器| 是，並使用 FeedTokens 手動平行處理 |
| 只處理單一分割區索引鍵的變更 | 不支援 | 是|
| 支援層級 | 正式推出 | 預覽 |

## <a name="next-steps"></a>後續步驟

* [變更摘要的概觀](change-feed.md)
* [使用變更摘要處理器](change-feed-processor.md)
* [觸發 Azure Functions](change-feed-functions.md)