---
title: 將非分區 Azure Cosmos 容器遷移到分區容器
description: 瞭解如何將所有現有的非分區容器遷移到分區容器中。
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 742ef62895f3ef64e8fa22ab21d2947bee57776b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623359"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>將非分區容器遷移到分區容器

Azure Cosmos DB 支援創建沒有分區鍵的容器。 目前，可以使用 Azure CLI 和 Azure Cosmos DB SDK（.Net、JAVA、NodeJs）創建版本小於或等於 2.x 的非分區容器。 不能使用 Azure 門戶創建非分區容器。 但是，此類非分區容器沒有彈性，固定存儲容量為 20 GB，輸送量限制為 10K RU/s。

非分區容器是舊容器，應將現有非分區容器遷移到分區容器以擴展存儲和輸送量。 Azure Cosmos DB 提供了一個系統定義的機制，用於將非分區容器遷移到分區容器。 本文檔說明如何將所有現有的非分區容器自動遷移到分區容器中。 僅當使用所有語言的 V3 版本的 SDK 時，才能利用自動遷移功能。

> [!NOTE]
> 目前，無法使用本文檔中描述的步驟遷移 Azure Cosmos DB MongoDB 和 Gremlin API 帳戶。

## <a name="migrate-container-using-the-system-defined-partition-key"></a>使用系統定義的分區鍵遷移容器

為了支援遷移，Azure Cosmos DB 提供了一個系統定義的`/_partitionkey`分區金鑰，該鍵在沒有分區鍵的所有容器上命名。 遷移容器後，無法更改分區金鑰定義。 例如，遷移到分區容器的容器的定義如下：

```json
{
    "Id": "CollId" 
  "partitionKey": {
    "paths": [
      "/_partitionKey"
    ],
    "kind": "Hash"
  },
}
```

遷移容器後，可以通過填充`_partitionKey`屬性以及文檔的其他屬性來創建文檔。 屬性`_partitionKey`表示文檔的分區鍵。

選擇正確的分區鍵對於優化利用預配輸送量非常重要。 有關詳細資訊，請參閱[如何選擇分區金鑰](partitioning-overview.md)一文。

> [!NOTE]
> 僅當使用所有語言的最新/V3 版本的 SDK 時，才能利用系統定義的分區金鑰。

下面的示例顯示了一個示例代碼，用於使用系統定義的分區鍵創建文檔並讀取該文檔：

**文檔的 JSON 表示形式**

```csharp
DeviceInformationItem = new DeviceInformationItem
{
   "id": "elevator/PugetSound/Building44/Floor1/1",
   "deviceId": "3cf4c52d-cc67-4bb8-b02f-f6185007a808",
   "_partitionKey": "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
} 

public class DeviceInformationItem
{
    [JsonProperty(PropertyName = "id")]
    public string Id { get; set; }

    [JsonProperty(PropertyName = "deviceId")]
    public string DeviceId { get; set; }

    [JsonProperty(PropertyName = "_partitionKey", NullValueHandling = NullValueHandling.Ignore)]
    public string PartitionKey {get {return this.DeviceId; set; }
}

CosmosContainer migratedContainer = database.Containers["testContainer"];

DeviceInformationItem deviceItem = new DeviceInformationItem() {
  Id = "1234",
  DeviceId = "3cf4c52d-cc67-4bb8-b02f-f6185007a808"
}

ItemResponse<DeviceInformationItem > response = 
  await migratedContainer.CreateItemAsync<DeviceInformationItem>(
    deviceItem.PartitionKey, 
    deviceItem
  );

// Read back the document providing the same partition key
ItemResponse<DeviceInformationItem> readResponse = 
  await migratedContainer.ReadItemAsync<DeviceInformationItem>( 
    partitionKey:deviceItem.PartitionKey, 
    id: device.Id
  );

```

有關完整示例，請參閱[.Net 示例][1]GitHub 存儲庫。
                      
## <a name="migrate-the-documents"></a>遷移文檔

雖然容器定義使用分區鍵屬性增強，但容器中的文檔不會自動遷移。 這意味著系統磁碟分割金鑰屬性`/_partitionKey`路徑不會自動添加到現有文檔中。 您需要通過讀取在沒有分區鍵的情況下創建的文檔重新分區，然後用`_partitionKey`文檔中的屬性重寫它們。

## <a name="access-documents-that-dont-have-a-partition-key"></a>訪問沒有分區鍵的文檔

應用程式可以使用名為"分區鍵.none"的特殊系統屬性訪問沒有分區金鑰的現有文檔，這是非遷移文檔的值。 您可以在所有 CRUD 和查詢操作中使用此屬性。 下面的示例顯示了從 None分區金鑰讀取單個文檔的示例。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

有關如何重新分區文檔的完整示例，請參閱[.Net 示例][1]GitHub 存儲庫。 

## <a name="compatibility-with-sdks"></a>與 SDK 的相容性

Azure Cosmos DB SDK 的舊版本（如 V2.x.x 和 V1.x.x）不支援系統定義的分區金鑰屬性。 因此，當您從較舊的 SDK 讀取容器定義時，它不包含任何分區金鑰定義，並且這些容器的行為將完全與以前一樣。 使用舊版本的 SDK 生成的應用程式將繼續使用非分區，無需進行任何更改。 

如果遷移的容器被最新/V3 版本的 SDK 使用，並且您開始在新文檔中填充系統定義的分區金鑰，則無法再從較舊的 SDK 訪問（讀取、更新、刪除、查詢）此類文檔。

## <a name="known-issues"></a>已知問題

**使用 V3 SDK 查詢在沒有分區金鑰的情況下插入的專案計數可能涉及更高的輸送量消耗**

如果在 V3 SDK 中查詢使用 V2 SDK 插入的專案，或者使用具有`PartitionKey.None`參數的 V3 SDK 插入的專案，則如果在 FeedOptions 中提供該參數`PartitionKey.None`，計數查詢可能會消耗更多的 RU/s。 如果沒有使用分區鍵插入其他項，`PartitionKey.None`我們建議您不提供該參數。

如果插入的新專案具有分區鍵的不同值，則通過傳入相應的鍵來查詢此類項計數`FeedOptions`將沒有任何問題。 使用分區鍵插入新文檔後，如果只需要查詢沒有分區鍵值的文檔計數，則該查詢可能再次產生與常規分區集合類似的更高的 RU/s。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [在 Azure 宇宙資料庫中請求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration