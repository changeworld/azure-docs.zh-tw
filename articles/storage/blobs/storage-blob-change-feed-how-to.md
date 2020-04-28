---
title: Azure Blob 儲存體中的處理變更摘要（預覽） |Microsoft Docs
description: 瞭解如何在 .NET 用戶端應用程式中處理變更摘要記錄
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 75995eeb3f8255cb4c60d5be267f9c343edfea89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/27/2020
ms.locfileid: "74111863"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob 儲存體中的處理變更摘要（預覽）

變更摘要提供 blob 和儲存體帳戶中 blob 中繼資料所發生之所有變更的交易記錄。 本文說明如何使用 blob 變更摘要處理器程式庫來讀取變更摘要記錄。

若要深入瞭解變更摘要，請參閱[Azure Blob 儲存體（預覽）中的變更](storage-blob-change-feed.md)摘要。

> [!NOTE]
> 變更摘要處於公開預覽狀態，並可在**westcentralus**和**westus2**區域中使用。 若要深入瞭解這項功能以及已知的問題和限制，請參閱[Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)。 變更摘要處理器程式庫在現在和此程式庫正式推出時可能會有所變更。

## <a name="get-the-blob-change-feed-processor-library"></a>取得 blob 變更摘要處理器程式庫

1. 在 Visual Studio 中，將 URL `https://azuresdkartifacts.blob.core.windows.net/azuresdkpartnerdrops/index.json`新增至您的 NuGet 套件來源。 

   若要瞭解作法，請參閱[套件來源](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#package-sources)。

2. 在 NuGet 套件管理員中，尋找**changefeed program.cs**套件，並將它安裝到您的專案。 

   若要瞭解作法，請參閱[尋找及安裝套件](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)。

## <a name="connect-to-the-storage-account"></a>連接到儲存體帳戶

藉由呼叫[CloudStorageAccount. TryParse](/dotnet/api/microsoft.azure.storage.cloudstorageaccount.tryparse)方法來剖析連接字串。 

然後，藉由呼叫[CloudStorageAccount. CreateCloudBlobClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.blobaccountextensions.createcloudblobclient)方法，建立代表儲存體帳戶中 Blob 儲存體的物件。

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

## <a name="initialize-the-change-feed"></a>初始化變更摘要

將下列 using 語句新增至程式碼檔案的頂端。 

```csharp
using Avro.Generic;
using ChangeFeedClient;
```

然後，藉由呼叫**GetContainerReference**方法來建立**changefeed program.cs**類別的實例。 傳入變更摘要容器的名稱。

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
> 變更摘要是您儲存體帳戶中不可變且唯讀的實體。 任何數目的應用程式都能以自己的便利性，同時讀取和處理變更摘要。 當應用程式讀取記錄時，不會將它們從變更摘要中移除。 每個取用讀取器的讀取或反復專案狀態都是獨立的，而且只由您的應用程式維護。

讀取記錄最簡單的方式，就是建立**ChangeFeedReader**類別的實例。 

這個範例會逐一查看變更摘要中的所有記錄，然後將每筆記錄的幾個值列印到主控台。 
 
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

## <a name="resuming-reading-records-from-a-saved-position"></a>從儲存的位置繼續讀取記錄

您可以選擇將您的讀取位置儲存在變更摘要中，並繼續在未來的時間逐一查看記錄。 您可以使用**ChangeFeedReader. SerializeState （）** 方法，隨時儲存變更摘要的反復專案狀態。 狀態是**字串**，您的應用程式可以根據應用程式的設計來儲存該狀態（例如：到資料庫或檔案）。

```csharp
    string currentReadState = processor.SerializeState();
```

您可以使用**CreateChangeFeedReaderFromPointerAsync**方法建立**ChangeFeedReader** ，以繼續逐一查看最後狀態的記錄。

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

## <a name="stream-processing-of-records"></a>記錄的串流處理

您可以選擇在變更摘要記錄抵達時進行處理。 請參閱[規格](storage-blob-change-feed.md#specifications)。

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

## <a name="reading-records-within-a-time-range"></a>讀取時間範圍內的記錄

變更摘要會根據變更事件時間組織成每小時區段。 請參閱[規格](storage-blob-change-feed.md#specifications)。 您可以從屬於特定時間範圍內的變更摘要區段讀取記錄。

這個範例會取得所有區段的開始時間。 然後，它會逐一查看該清單，直到開始時間超過最後一個可取用區段的時間，或超出所需範圍的結束時間。 

### <a name="selecting-segments-for-a-time-range"></a>選取時間範圍的區段

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

### <a name="reading-records-in-a-segment"></a>讀取區段中的記錄

您可以從個別區段或區段範圍讀取記錄。

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

## <a name="read-records-starting-from-a-time"></a>從一段時間開始讀取記錄

您可以從起始區段讀取變更摘要的記錄，直到結束為止。 類似于讀取時間範圍內的記錄，您可以列出區段並選擇區段以開始逐一查看。

這個範例會取得第一個要處理之區段的[DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8) 。

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

這個範例會處理從起始區段的[DateTimeOffset](https://docs.microsoft.com/dotnet/api/system.datetimeoffset?view=netframework-4.8)開始的變更摘要記錄。

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
> 的區段可以有一或多個*chunkFilePath*中的變更摘要記錄。 如果有多個*chunkFilePath* ，系統就會在內部將記錄分割成多個分區，以管理發佈輸送量。 保證區段的每個分割區都包含對互斥 blob 的變更，而且可以獨立處理，而不會違反順序。 如果您的案例最有效率，您可以使用**ChangeFeedSegmentShardReader**類別來逐一查看分區層級的記錄。

## <a name="next-steps"></a>後續步驟

深入瞭解變更摘要記錄。 請參閱[Azure Blob 儲存體中的變更摘要（預覽）](storage-blob-change-feed.md)
