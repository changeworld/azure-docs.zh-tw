---
title: 將未分割的 Azure Cosmos 容器遷移至已分割的容器
description: 瞭解如何將所有現有的非資料分割容器遷移至已分割的容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 01e61abacac63d698456c3ab25826f9799d2837e
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482276"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>將未分割的容器遷移至分割的容器

Azure Cosmos DB 支援建立沒有分割區索引鍵的容器。 目前，您可以使用 Azure CLI 和 Azure Cosmos DB Sdk ( .Net、JAVA、NodeJs) 版本小於或等於2.x 來建立非資料分割的容器。 您無法使用 Azure 入口網站建立非資料分割的容器。 不過，這類非分割的容器不是彈性的，且具有 20 GB 的固定儲存體容量，且輸送量限制為 10K RU/秒。

非分割的容器是舊版，您應該將現有的非資料分割容器遷移至分割的容器，以調整儲存體和輸送量。 Azure Cosmos DB 提供系統定義的機制，可將未分割的容器遷移至已分割的容器。 本檔說明如何將所有現有的非資料分割容器自動遷移至分割的容器。 只有在您使用所有語言的 Sdk 版本時，才能利用自動遷移功能。

> [!NOTE]
> 目前，您無法使用本檔中所述的步驟來遷移 Azure Cosmos DB MongoDB 和 Gremlin API 帳戶。

## <a name="migrate-container-using-the-system-defined-partition-key"></a>使用系統定義的分割區索引鍵來遷移容器

為了支援遷移，Azure Cosmos DB 在 `/_partitionkey` 沒有分割區索引鍵的所有容器上提供名為的系統定義資料分割索引鍵。 遷移容器之後，您就無法變更資料分割索引鍵定義。 例如，遷移至資料分割容器的容器定義如下所示：

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

遷移容器之後，您可以藉由 `_partitionKey` 將屬性與檔的其他屬性一起填入來建立檔。 `_partitionKey`屬性代表檔的資料分割索引鍵。

選擇正確的資料分割索引鍵對於充分利用布建的輸送量很重要。 如需詳細資訊，請參閱 [如何選擇資料分割索引鍵](partitioning-overview.md) 文章。

> [!NOTE]
> 只有當您在所有語言中使用最新/V3 版本的 Sdk 時，才能利用系統定義的資料分割索引鍵。

下列範例顯示的範例程式碼會使用系統定義的分割區索引鍵來建立檔，並讀取該檔：

**檔的 JSON 標記法**

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

如需完整範例，請參閱 [.net 範例][1] GitHub 存放庫。
                      
## <a name="migrate-the-documents"></a>遷移檔

當使用資料分割索引鍵屬性增強容器定義時，不會自動遷移容器內的檔。 這表示系統分割區索引鍵屬性 `/_partitionKey` 路徑不會自動新增至現有的檔。 您必須在不使用分割區索引鍵的情況下讀取已建立的檔，然後使用檔中的屬性重新撰寫這些檔，以重新分割現有的檔 `_partitionKey` 。

## <a name="access-documents-that-dont-have-a-partition-key"></a>存取沒有分割區索引鍵的檔

應用程式可以使用名為 "PartitionKey" 的特殊系統屬性（稱為「」）來存取沒有分割區索引鍵的現有檔，這是未遷移檔的值。 您可以在所有 CRUD 和查詢作業中使用此屬性。 下列範例顯示從 NonePartitionKey 讀取單一檔的範例。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

如需如何重新分割檔的完整範例，請參閱 [.net 範例][1] GitHub 存放庫。 

## <a name="compatibility-with-sdks"></a>與 Sdk 的相容性

舊版的 Azure Cosmos DB Sdk （例如 V2. x. x 和 V1. x. x）不支援系統定義的資料分割索引鍵屬性。 因此，當您從較舊的 SDK 讀取容器定義時，它不會包含任何分割區索引鍵定義，而這些容器的行為會與之前完全相同。 使用舊版 Sdk 所建立的應用程式會繼續使用非資料分割，而不會有任何變更。 

如果已遷移的容器是由 SDK 的最新/V3 版本所取用，而您開始在新檔中填入系統定義的分割區索引鍵，則您無法從舊版 Sdk 存取 (讀取、更新、刪除、查詢) 這類檔。

## <a name="known-issues"></a>已知問題

**使用 V3 SDK 查詢未使用分割區索引鍵插入的專案計數，可能需要較高的輸送量耗用量**

如果您從 V3 SDK 針對使用 V2 SDK 所插入的專案進行查詢，或是使用 V3 SDK 搭配參數來插入的專案 `PartitionKey.None` ，計數查詢可能會耗用更多 RU/秒（如果 `PartitionKey.None` FeedOptions 中提供參數的話）。 `PartitionKey.None`如果未使用分割區索引鍵插入其他專案，建議您不要提供參數。

如果針對分割區索引鍵使用不同的值來插入新專案，在中傳遞適當的金鑰來查詢這類專案計數 `FeedOptions` 將不會有任何問題。 使用分割區索引鍵插入新檔之後，如果您只需要查詢檔計數而沒有分割區索引鍵值，則該查詢可能會再次產生更高的 RU/s，類似于一般分割的集合。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](./account-databases-containers-items.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration