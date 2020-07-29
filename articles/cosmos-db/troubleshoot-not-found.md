---
title: Azure Cosmos DB 找不到例外狀況的疑難排解
description: 如何診斷和修正找不到的例外狀況
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f391772672904a1e2bdfa0a741d9f85a6edeea8b
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294156"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found"></a>診斷和疑難排解找不到 Azure Cosmos DB
HTTP 狀態碼404表示資源不再存在。

## <a name="expected-behavior"></a>預期的行為
有很多應用程式預期會有404的有效案例，而且會正確處理案例。

## <a name="not-found-was-returned-for-an-item-that-should-exist-or-does-exist"></a>針對應該存在或存在的專案，傳回 [找不到]
以下是在專案應該或離開時，傳回狀態碼404的可能原因。

### <a name="1-race-condition"></a>1. 競爭條件
有多個 SDK 用戶端實例，在寫入之前就會發生讀取。

#### <a name="solution"></a>解決方案：
1. Cosmos DB 的預設帳戶一致性是會話一致性。 當建立或更新專案時，回應會傳回可在 SDK 實例之間傳遞的會話權杖，以保證讀取要求是從具有該變更的複本讀取。
2. 將[一致性層級](consistency-levels-choosing.md)變更為較[強的層級](consistency-levels-tradeoffs.md)

### <a name="2-invalid-partition-key-and-id-combination"></a>2. 不正確資料分割索引鍵和識別碼組合
分割區索引鍵和識別碼的組合無效。

#### <a name="solution"></a>解決方案：
修正導致不正確組合的應用程式邏輯。 

### <a name="3-invalid-character-in-item-id"></a>3. 專案識別碼中的字元無效
專案會插入 Cosmos DB，但專案識別碼中含有[不正確字元](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks)。

#### <a name="solution"></a>解決方案：
建議使用者將識別碼變更為不包含特殊字元的不同值。 如果變更 ID 不是選項，您可以 Base64 編碼此識別碼，以將特殊字元轉義。

已插入容器中的專案識別碼可以使用 RID 值取代，而不是以名稱為基礎的參考。
```c#
// Get a container reference that use RID values
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// List of invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // It recommend to chose a new ID that does not contain special characters, but
        // if that is not possible then it can be Base64 encoded to escape the special characters
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value using the rid based container reference
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validate the new ID can be read using the original name based contianer reference
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="4-time-to-live-ttl-purge"></a>4. 存留時間（TTL）清除
專案已設定[存留時間（TTL）](https://docs.microsoft.com/azure/cosmos-db/time-to-live)屬性。 專案已清除，因為存留時間已過期。

#### <a name="solution"></a>解決方案：
將 [存留時間] 變更為 [即時]，以防止專案被清除。

### <a name="5-lazy-indexing"></a>5. 延遲索引
[延遲索引](index-policy.md#indexing-mode)尚未趕上。

#### <a name="solution"></a>解決方案：
等候索引來趕上或變更索引編制原則

### <a name="6-parent-resource-deleted"></a>6. 已刪除父資源
已刪除專案所在的資料庫和/或容器。

#### <a name="solution"></a>解決方案：
1. 請[還原](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore#backup-retention-period)父資源，或重新建立資源。
2. 建立新的資源以取代已刪除的資源

## <a name="next-steps"></a>後續步驟
* 針對使用 Azure Cosmos DB .NET SDK 時[的問題進行診斷和疑難排解](troubleshoot-dot-net-sdk.md)
* 瞭解[.Net V3](performance-tips-dotnet-sdk-v3-sql.md)和[.net V2](performance-tips.md)的效能指導方針