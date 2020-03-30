---
title: Azure Blob 存儲（預覽）中的處理更改源 |微軟文檔
description: 瞭解如何處理 .NET 用戶端應用程式中的更改源日誌
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74111863"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob 存儲中的處理更改源（預覽）

更改源提供存儲帳戶中 Blob 和 blob 中繼資料發生的所有更改的事務日誌。 本文介紹如何使用 Blob 更改饋送處理器庫讀取更改源記錄。

要瞭解有關更改源的更多資訊，請參閱[Azure Blob 存儲中的更改源（預覽）。](storage-blob-change-feed.md)

> [!NOTE]
> 更改源處於公共預覽版中，可在**西中部**和**西部 2**區域提供。 要瞭解有關此功能以及已知問題和限制的更多資訊，請參閱 Azure [Blob 存儲 中的更改源支援](storage-blob-change-feed.md)。 更改源處理器庫在現在和此庫普遍可用之間可能會發生變化。

## <a name="get-the-blob-change-feed-processor-library"></a>獲取 Blob 更改饋送處理器庫

1. 在 Visual Studio 中`https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json`，將 URL 添加到 NuGet 包源。 

   要瞭解如何使用，請參閱[包源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 包管理器中，查找**Microsoft.Azure.Storage.更改源**包，並將其安裝到專案中。 

   要瞭解如何使用，請參閱[查找並安裝包](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

## <a name="connect-to-the-storage-account"></a>連接到儲存體帳戶

通過調用[雲存儲帳戶.TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse)方法解析連接字串。 

然後，通過調用[雲存儲帳戶.CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)方法，創建一個表示存儲帳戶中的 Blob 存儲的物件。

```cs
public bool GetBlobClient(ref CloudBlobClient cloudBlobClient, string storageConnectionString)
{
    if (CloudStorageAccount.TryParse
        (storageConnectionString, out CloudStorageAccount storageAccount))
        {
            cloudBlobClient = storageAccount.CreateCloudBlobClient();

            return true;
        }
        else
        {
            return false;
        }
    }
}
```

## <a name="initialize-the-change-feed"></a>初始化更改源

將以下使用語句添加到代碼檔的頂部。 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

然後，通過調用**GetContainerReference**方法創建**ChangeFeed**類的實例。 傳遞更改源容器的名稱。

```csharp
public async Task<ChangeFeed> GetChangeFeed(CloudBlobClient cloudBlobClient)
{
    CloudBlobContainer changeFeedContainer =
        cloudBlobClient.GetContainerReference("$blobchangefeed");

    ChangeFeed changeFeed = new ChangeFeed(changeFeedContainer);
    await changeFeed.InitializeAsync();

    return changeFeed;
}
```

## <a name="reading-records"></a>讀取記錄

> [!NOTE]
> 更改源是存儲帳戶中的不可變和唯讀實體。 任意數量的應用程式都可以在方便的時候同時獨立地讀取和處理更改源。 當應用程式讀取記錄時，不會從更改源中刪除記錄。 每個使用讀取器的讀取或反覆運算狀態僅由應用程式保持獨立。

讀取記錄的最簡單方法是創建**更改源閱讀器**類的實例。 

此示例遍歷更改源中的所有記錄，然後從每個記錄列印到主控台幾個值。 
 
```csharp
public async Task ProcessRecords(ChangeFeed changeFeed)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="resuming-reading-records-from-a-saved-position"></a>從保存的位置恢復讀取記錄

您可以選擇在更改源中保存讀取位置，並在將來繼續反覆運算記錄。 您可以隨時使用**ChangeFeedReader.序列化狀態（）** 方法保存更改源的反覆運算狀態。 狀態是**字串**，應用程式可以基於應用程式的設計保存該狀態（例如：到資料庫或檔）。

```csharp
    string currentReadState = processor.SerializeState();
```

您可以使用**創建更改源閱讀器從PointerAsync**方法創建**更改源閱讀器**，從而繼續從上次狀態中反覆運算記錄。

```csharp
public async Task ProcessRecordsFromLastPosition(ChangeFeed changeFeed, string lastReadState)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderFromPointerAsync(lastReadState);

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}

```

## <a name="stream-processing-of-records"></a>記錄的流處理

您可以選擇在更改源記錄到達時處理這些更改。 請參閱[規格](storage-blob-change-feed.md#specifications)。

```csharp
public async Task ProcessRecordsStream(ChangeFeed changeFeed, int waitTimeMs)
{
    ChangeFeedReader processor = await changeFeed.CreateChangeFeedReaderAsync();

    ChangeFeedRecord currentRecord = null;
    while (true)
    {
        do
        {
            currentRecord = await processor.GetNextItemAsync();

            if (currentRecord != null)
            {
                string subject = currentRecord.record["subject"].ToString();
                string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
                string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

                Console.WriteLine("Subject: " + subject + "\n" +
                    "Event Type: " + eventType + "\n" +
                    "Api: " + api);
            }

        } while (currentRecord != null);

        await Task.Delay(waitTimeMs);
    }
}
```

## <a name="reading-records-within-a-time-range"></a>在時間範圍內讀取記錄

更改源根據變更事件時間組織成每小時段。 請參閱[規格](storage-blob-change-feed.md#specifications)。 可以從屬於特定時間範圍的更改源段讀取記錄。

此示例獲取所有段的開始時間。 然後，它會遍載該清單，直到開始時間超過最後一個消耗品段的時間或超出所需範圍的結束時間。 

### <a name="selecting-segments-for-a-time-range"></a>選擇時間範圍的段

```csharp
public async Task<List<DateTimeOffset>> GetChangeFeedSegmentRefsForTimeRange
    (ChangeFeed changeFeed, DateTimeOffset startTime, DateTimeOffset endTime)
{
    List<DateTimeOffset> result = new List<DateTimeOffset>();

    DateTimeOffset stAdj = startTime.AddMinutes(-15);
    DateTimeOffset enAdj = endTime.AddMinutes(15);

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;

    List<DateTimeOffset> segments = 
        (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();

    foreach (var segmentStart in segments)
    {
        if (lastConsumable.CompareTo(segmentStart) < 0)
        {
            break;
        }

        if (enAdj.CompareTo(segmentStart) < 0)
        {
            break;
        }

        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);

        bool overlaps = stAdj.CompareTo(segmentEnd) < 0 && 
            segmentStart.CompareTo(enAdj) < 0;

        if (overlaps)
        {
            result.Add(segmentStart);
        }
    }

    return result;
}
```

### <a name="reading-records-in-a-segment"></a>讀取段中的記錄

可以從單個段或段範圍讀取記錄。

```csharp
public async Task ProcessRecordsInSegment(ChangeFeed changeFeed, DateTimeOffset segmentOffset)
{
    ChangeFeedSegment segment = new ChangeFeedSegment(segmentOffset, changeFeed);
    await segment.InitializeAsync();

    ChangeFeedSegmentReader processor = await segment.CreateChangeFeedSegmentReaderAsync();

    ChangeFeedRecord currentRecord = null;
    do
    {
        currentRecord = await processor.GetNextItemAsync();

        if (currentRecord != null)
        {
            string subject = currentRecord.record["subject"].ToString();
            string eventType = ((GenericEnum)currentRecord.record["eventType"]).Value;
            string api = ((GenericEnum)((GenericRecord)currentRecord.record["data"])["api"]).Value;

            Console.WriteLine("Subject: " + subject + "\n" +
                "Event Type: " + eventType + "\n" +
                "Api: " + api);
        }

    } while (currentRecord != null);
}
```

## <a name="read-records-starting-from-a-time"></a>從時間開始讀取記錄

可以從起始段讀取更改源的記錄，直到結束。 與在時間範圍內讀取記錄類似，您可以列出段並選擇要開始反覆運算的段。

此示例獲取要處理的第一個段的[DateTime 偏移](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8)。

```csharp
public async Task<DateTimeOffset> GetChangeFeedSegmentRefAfterTime
    (ChangeFeed changeFeed, DateTimeOffset timestamp)
{
    DateTimeOffset result = new DateTimeOffset();

    DateTimeOffset lastConsumable = (DateTimeOffset)changeFeed.LastConsumable;
    DateTimeOffset lastConsumableEnd = lastConsumable.AddMinutes(60);

    DateTimeOffset timestampAdj = timestamp.AddMinutes(-15);

    if (lastConsumableEnd.CompareTo(timestampAdj) < 0)
    {
        return result;
    }

    List<DateTimeOffset> segments = (await changeFeed.ListAvailableSegmentTimesAsync()).ToList();
    foreach (var segmentStart in segments)
    {
        DateTimeOffset segmentEnd = segmentStart.AddMinutes(60);
        if (timestampAdj.CompareTo(segmentEnd) <= 0)
        {
            result = segmentStart;
            break;
        }
    }

    return result;
}
```

此示例處理從起始段[的 DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8)開始的更改源記錄。

```csharp
public async Task ProcessRecordsStartingFromSegment(ChangeFeed changeFeed, DateTimeOffset segmentStart)
{
    TimeSpan waitTime = new TimeSpan(60 * 1000);

    ChangeFeedSegment segment = new ChangeFeedSegment(segmentStart, changeFeed);

    await segment.InitializeAsync();

    while (true)
    {
        while (!await IsSegmentConsumableAsync(changeFeed, segment))
        {
            await Task.Delay(waitTime);
        }

        ChangeFeedSegmentReader reader = await segment.CreateChangeFeedSegmentReaderAsync();

        do
        {
            await reader.CheckForFinalizationAsync();

            ChangeFeedRecord currentItem = null;
            do
            {
                currentItem = await reader.GetNextItemAsync();
                if (currentItem != null)
                {
                    string subject = currentItem.record["subject"].ToString();
                    string eventType = ((GenericEnum)currentItem.record["eventType"]).Value;
                    string api = ((GenericEnum)((GenericRecord)currentItem.record["data"])["api"]).Value;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            } while (currentItem != null);

            if (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized)
            {
                await Task.Delay(waitTime);
            }
        } while (segment.timeWindowStatus != ChangefeedSegmentStatus.Finalized);

        segment = await segment.GetNextSegmentAsync(); // TODO: What if next window doesn't yet exist?
        await segment.InitializeAsync(); // Should update status, shard list.
    }
}

private async Task<bool> IsSegmentConsumableAsync(ChangeFeed changeFeed, ChangeFeedSegment segment)
{
    if (changeFeed.LastConsumable >= segment.startTime)
    {
        return true;
    }
    await changeFeed.InitializeAsync();
    return changeFeed.LastConsumable >= segment.startTime;
}
```

>[!TIP]
> 的一段可以在一個或多個*塊 FilePath*中具有更改源日誌。 在多個*塊FilePath*的情況下，系統已在內部將記錄分區為多個分片，以管理發佈輸送量。 保證段的每個分區將包含互斥 blob 的更改，並且可以獨立處理，而不會違反排序。 如果對方案最有效，則可以使用**ChangeFeed段ShardReader**類在分片級別反覆運算記錄。

## <a name="next-steps"></a>後續步驟

瞭解有關更改源日誌的更多資訊。 請參閱[Azure Blob 存儲中的更改源（預覽）](storage-blob-change-feed.md)
