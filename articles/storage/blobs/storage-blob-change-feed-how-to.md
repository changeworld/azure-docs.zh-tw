---
title: Azure Blob 儲存體 (Preview 中處理變更摘要) |Microsoft Docs
description: 瞭解如何在 .NET 用戶端應用程式中處理變更摘要記錄
author: normesta
ms.author: normesta
ms.date: 06/18/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: dedf1174e00f5bb75822fb720a592af86121ec2d
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88691423"
---
# <a name="process-change-feed-in-azure-blob-storage-preview"></a>Azure Blob 儲存體 (Preview 中處理變更摘要) 

變更摘要提供 blob 和儲存體帳戶中 blob 中繼資料所發生之所有變更的交易記錄。 本文說明如何使用 blob 變更摘要處理器程式庫來讀取變更摘要記錄。

若要深入瞭解變更摘要，請參閱 [Azure Blob 儲存體 (Preview) 的變更 ](storage-blob-change-feed.md)摘要。

> [!NOTE]
> 變更摘要處於公開預覽狀態，可在 **westcentralus** 和 **westus2** 區域中使用。 若要深入瞭解這項功能以及已知問題和限制，請參閱 [Azure Blob 儲存體中的變更摘要支援](storage-blob-change-feed.md)。 變更摘要處理器程式庫可能會在現在與此程式庫正式推出時進行變更。

## <a name="get-the-blob-change-feed-processor-library"></a>取得 blob 變更摘要處理器程式庫

1. 開啟命令視窗 (例如： Windows PowerShell) 。
2. 從您的專案目錄中，安裝 **Changefeed** NuGet 套件。

```console
dotnet add package Azure.Storage.Blobs.ChangeFeed --source https://azuresdkartifacts.blob.core.windows.net/azure-sdk-for-net/index.json --version 12.0.0-dev.20200604.2
```
## <a name="read-records"></a>讀取記錄

> [!NOTE]
> 變更摘要是您儲存體帳戶中不可變且唯讀的實體。 任何數量的應用程式都可以在其本身的便利性中，同時或獨立地讀取和處理變更摘要。 當應用程式讀取記錄時，不會從變更摘要中移除記錄。 每個取用讀取器的讀取或反復專案狀態都是獨立的，而且只由您的應用程式維護。

此範例會逐一查看變更摘要中的所有記錄，並將其新增至清單，然後將該清單傳回給呼叫者。
 
```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Get all the events in the change feed. 
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync())
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

此範例會從清單中的每一筆記錄，將幾個值列印到主控台。 

```csharp
public void showEventData(List<BlobChangeFeedEvent> changeFeedEvents)
{
    foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedEvents)
    {
        string subject = changeFeedEvent.Subject;
        string eventType = changeFeedEvent.EventType.ToString();
        string api = changeFeedEvent.EventData.Api;

        Console.WriteLine("Subject: " + subject + "\n" +
        "Event Type: " + eventType + "\n" +
        "Api: " + api);
    }
}
```

## <a name="resume-reading-records-from-a-saved-position"></a>繼續從儲存的位置讀取記錄

您可以選擇將讀取位置儲存在變更摘要中，然後繼續在未來的時間逐一查看記錄。 您可以藉由取得變更摘要資料指標來儲存讀取位置。 資料指標是一個 **字串** ，而您的應用程式可以將該字串以任何適合您應用程式設計的方式儲存 (例如：檔案或資料庫) 。

此範例會逐一查看變更摘要中的所有記錄，並將其新增至清單，並儲存資料指標。 清單和資料指標會傳回給呼叫端。 

```csharp
public async Task<(string, List<BlobChangeFeedEvent>)> ChangeFeedResumeWithCursorAsync
    (string connectionString,  string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor)
        .AsPages(pageSizeHint: 10)
        .GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
    {
    
        changeFeedEvents.Add(changeFeedEvent);             
    }
    
    // Update the change feed cursor.  The cursor is not required to get each page of events,
    // it is intended to be saved and used to resume iterating at a later date.
    cursor = enumerator.Current.ContinuationToken;
    return (cursor, changeFeedEvents);
}
```

## <a name="stream-processing-of-records"></a>記錄的串流處理

您可以選擇在資料抵達時處理變更摘要記錄。 請參閱 [規格](storage-blob-change-feed.md#specifications)。 建議您每小時輪詢一次變更。

此範例會定期輪詢變更。  如果有變更記錄存在，此程式碼會處理這些記錄，並儲存變更摘要資料指標。 如此一來，如果進程停止後再重新開機，則應用程式可以使用資料指標來繼續進行最後一次中斷的處理記錄。 此範例會將資料指標儲存至本機應用程式佈建檔，但您的應用程式可以將它儲存為最適合您案例的任何形式。 

```csharp
public async Task ChangeFeedStreamAsync
    (string connectionString, int waitTimeMs, string cursor)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();

    while (true)
    {
        IAsyncEnumerator<Page<BlobChangeFeedEvent>> enumerator = changeFeedClient
        .GetChangesAsync(continuation: cursor).AsPages().GetAsyncEnumerator();

        while (true) 
        {
            var result = await enumerator.MoveNextAsync();

            if (result)
            {
                foreach (BlobChangeFeedEvent changeFeedEvent in enumerator.Current.Values)
                {
                    string subject = changeFeedEvent.Subject;
                    string eventType = changeFeedEvent.EventType.ToString();
                    string api = changeFeedEvent.EventData.Api;

                    Console.WriteLine("Subject: " + subject + "\n" +
                        "Event Type: " + eventType + "\n" +
                        "Api: " + api);
                }
            
                // helper method to save cursor. 
                SaveCursor(enumerator.Current.ContinuationToken);
            }
            else
            {
                break;
            }

        }
        await Task.Delay(waitTimeMs);
    }

}

public void SaveCursor(string cursor)
{
    System.Configuration.Configuration config = 
        ConfigurationManager.OpenExeConfiguration
        (ConfigurationUserLevel.None);

    config.AppSettings.Settings.Clear();
    config.AppSettings.Settings.Add("Cursor", cursor);
    config.Save(ConfigurationSaveMode.Modified);
}
```

## <a name="reading-records-within-a-time-range"></a>讀取時間範圍內的記錄

您可以讀取落在特定時間範圍內的記錄。 此範例會逐一查看變更摘要中的所有記錄，這些記錄會在3:00 年3月的下午，和 2 2017 年 10 7 2019 月2:00，將其新增至清單，然後將該清單傳回給呼叫者。

### <a name="selecting-segments-for-a-time-range"></a>選取時間範圍的區段

```csharp
public async Task<List<BlobChangeFeedEvent>> ChangeFeedBetweenDatesAsync(string connectionString)
{
    // Get a new blob service client.
    BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

    // Get a new change feed client.
    BlobChangeFeedClient changeFeedClient = blobServiceClient.GetChangeFeedClient();
    List<BlobChangeFeedEvent> changeFeedEvents = new List<BlobChangeFeedEvent>();

    // Create the start and end time.  The change feed client will round start time down to
    // the nearest hour, and round endTime up to the next hour if you provide DateTimeOffsets
    // with minutes and seconds.
    DateTimeOffset startTime = new DateTimeOffset(2017, 3, 2, 15, 0, 0, TimeSpan.Zero);
    DateTimeOffset endTime = new DateTimeOffset(2020, 10, 7, 2, 0, 0, TimeSpan.Zero);

    // You can also provide just a start or end time.
    await foreach (BlobChangeFeedEvent changeFeedEvent in changeFeedClient.GetChangesAsync(
        start: startTime,
        end: endTime))
    {
        changeFeedEvents.Add(changeFeedEvent);
    }

    return changeFeedEvents;
}
```

您提供的開始時間會四捨五入到最接近的小時，而結束時間則會四捨五入到最接近的小時。 使用者可能會看到在開始時間之前和結束時間之後發生的事件。 在開始與結束時間之間發生的某些事件也可能不會出現。 這是因為事件可能會在開始時間之前的一小時內，或在結束時間之後的一小時內記錄。

## <a name="next-steps"></a>後續步驟

深入瞭解變更摘要記錄。 請參閱 [Azure Blob 儲存體 (預覽中的變更摘要) ](storage-blob-change-feed.md)
