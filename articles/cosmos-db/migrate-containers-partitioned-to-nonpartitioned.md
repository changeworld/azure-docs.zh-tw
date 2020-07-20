---
title: 將非資料分割的 Azure Cosmos 容器遷移至已分割的容器
description: 瞭解如何將所有現有的非資料分割容器遷移至已分割的容器。
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/25/2019
ms.author: mjbrown
ms.openlocfilehash: 619ec7e5510f9d3a5a17dcd5961fbd2182674df4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85263478"
---
# <a name="migrate-non-partitioned-containers-to-partitioned-containers"></a>將非資料分割的容器遷移至分割的容器

Azure Cosmos DB 支援建立不含分割區索引鍵的容器。 目前，您可以使用版本小於或等於2.x 的 Azure CLI 和 Azure Cosmos DB Sdk （.Net、JAVA、NodeJs）來建立非資料分割的容器。 您無法使用 Azure 入口網站建立非資料分割的容器。 不過，這類非資料分割的容器不具彈性，而且具有 20 GB 的固定儲存體容量和每秒 10K RU 的輸送量限制。

非資料分割的容器是舊版的，您應該將現有的非資料分割容器遷移至分割的容器，以調整儲存體和輸送量。 Azure Cosmos DB 提供系統定義的機制，將非資料分割的容器遷移至已分割的容器。 本檔說明如何將所有現有的非資料分割容器自動遷移至已分割的容器。 只有當您在所有語言中使用 V3 版本的 Sdk 時，才可以利用自動遷移功能。

> [!NOTE]
> 目前，您無法使用本檔中所述的步驟來遷移 Azure Cosmos DB MongoDB 和 Gremlin API 帳戶。

## <a name="migrate-container-using-the-system-defined-partition-key"></a>使用系統定義的分割區索引鍵來遷移容器

為了支援遷移，Azure Cosmos DB `/_partitionkey` 在所有沒有分割區索引鍵的容器上，提供名為的系統定義資料分割索引鍵。 遷移容器之後，您就無法變更分割區索引鍵定義。 例如，遷移至資料分割容器的容器定義如下所示：

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

遷移容器之後，您可以藉由擴展 `_partitionKey` 屬性和檔的其他屬性來建立檔。 `_partitionKey`屬性代表檔的資料分割索引鍵。

選擇正確的分割區索引鍵，對於使用已布建的輸送量而言非常重要。 如需詳細資訊，請參閱[如何選擇分割區索引鍵一](partitioning-overview.md)文。

> [!NOTE]
> 只有當您在所有語言中使用最新/V3 版本的 Sdk 時，才可以利用系統定義的分割區索引鍵。

下列範例顯示使用系統定義的分割區索引鍵來建立檔的範例程式碼，並讀取該檔：

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

如需完整範例，請參閱[.net 範例][1]GitHub 存放庫。
                      
## <a name="migrate-the-documents"></a>遷移檔

使用資料分割索引鍵屬性增強容器定義時，不會自動遷移容器內的檔。 這表示系統分割區索引鍵屬性 `/_partitionKey` 路徑不會自動新增至現有的檔。 若要重新分割現有的檔，您必須閱讀不含分割區索引鍵的檔，並以檔中的屬性重寫它們 `_partitionKey` 。

## <a name="access-documents-that-dont-have-a-partition-key"></a>存取沒有分割區索引鍵的檔

應用程式可以使用名為 "PartitionKey" 的特殊系統屬性，存取沒有分割區索引鍵的現有檔，這是未遷移檔的值。 您可以在所有 CRUD 和查詢作業中使用這個屬性。 下列範例顯示從 NonePartitionKey 讀取單一檔的範例。 

```csharp
CosmosItemResponse<DeviceInformationItem> readResponse = 
await migratedContainer.Items.ReadItemAsync<DeviceInformationItem>( 
  partitionKey: PartitionKey.None, 
  id: device.Id
); 

```

如需如何重新分割檔的完整範例，請參閱[.net 範例][1]GitHub 存放庫。 

## <a name="compatibility-with-sdks"></a>與 Sdk 的相容性

舊版的 Azure Cosmos DB Sdk （例如 V2. x. x. x. x）不支援系統定義的分割區索引鍵屬性。 因此，當您從較舊的 SDK 讀取容器定義時，它不會包含任何資料分割索引鍵定義，而這些容器的行為會與之前完全相同。 以舊版 Sdk 建立的應用程式會繼續使用非資料分割，但不會有任何變更。 

如果遷移的容器是由 SDK 的最新/V3 版本所取用，而且您開始在新檔中填入系統定義的分割區索引鍵，您就無法再從較舊的 Sdk 存取（讀取、更新、刪除、查詢）這類檔。

## <a name="known-issues"></a>已知問題

**使用 V3 SDK 查詢不含分割區索引鍵的專案計數，可能需要較高的輸送量耗用量**

如果您從 V3 SDK 針對使用 V2 SDK 所插入的專案進行查詢，或使用 V3 SDK 搭配參數插入的專案 `PartitionKey.None` ，則如果 `PartitionKey.None` FeedOptions 中提供參數，則 count 查詢可能會耗用更多 RU/秒。 `PartitionKey.None`如果沒有使用分割區索引鍵來插入其他專案，我們建議您不要提供參數。

如果使用不同的分割區索引鍵值來插入新的專案，在中傳遞適當的金鑰來查詢這類專案計數 `FeedOptions` 將不會有任何問題。 使用分割區索引鍵插入新檔之後，如果您只需要查詢檔計數而沒有分割區索引鍵值，則該查詢可能會再次產生與一般資料分割集合類似的 RU/秒。

## <a name="next-steps"></a>後續步驟

* [Azure Cosmos DB 中的資料分割](partitioning-overview.md)
* [Azure Cosmos DB 中的要求單位](request-units.md)
* [在容器和資料庫中佈建輸送量](set-throughput.md)
* [使用 Azure Cosmos 帳戶](account-overview.md)

[1]: https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/NonPartitionContainerMigration
