---
title: 針對找不到 Azure Cosmos DB 的例外狀況進行疑難排解
description: 瞭解如何診斷及修正找不到的例外狀況。
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f0fa8e5923639ea74a83a9a775bd5d580234b7ed
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93340119"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-not-found-exceptions"></a>針對找不到 Azure Cosmos DB 找不到例外狀況進行診斷和疑難排解
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 狀態碼 404 表示資源已不存在。

## <a name="expected-behavior"></a>預期的行為
在許多有效的案例中，應用程式會預期程式碼404並正確處理此案例。

## <a name="a-not-found-exception-was-returned-for-an-item-that-should-exist-or-does-exist"></a>針對應該存在或存在的專案，傳回了找不到的例外狀況
以下是如果專案應該存在或存在，可能會傳回狀態碼404的原因。

### <a name="race-condition"></a>競爭條件
有多個 SDK 用戶端實例，並在寫入之前發生讀取。

#### <a name="solution"></a>解決方案：
1. Azure Cosmos DB 的預設帳戶一致性是會話一致性。 建立或更新專案時，回應會傳回可在 SDK 實例之間傳遞的會話權杖，以保證讀取要求會從具有該變更的複本讀取。
1. 將 [一致性層級](./consistency-levels.md) 變更為較 [強的層級](./consistency-levels.md)。

### <a name="invalid-partition-key-and-id-combination"></a>不正確資料分割索引鍵和識別碼組合
分割區索引鍵和識別碼組合無效。

#### <a name="solution"></a>解決方案：
修正導致不正確組合的應用程式邏輯。 

### <a name="invalid-character-in-an-item-id"></a>專案識別碼中有不正確字元
專案會插入 Azure Cosmos DB 中，並在專案識別碼中包含 [不正確字元](/dotnet/api/microsoft.azure.documents.resource.id?preserve-view=true&view=azure-dotnet#remarks) 。

#### <a name="solution"></a>解決方案：
將識別碼變更為不包含特殊字元的其他值。 如果無法變更識別碼，您可以使用 Base64 編碼此識別碼以將特殊字元換用。

您可以使用 RID 值（而不是以名稱為基礎的參考）來取代已在識別碼容器中插入的專案。
```c#
// Get a container reference that uses RID values.
ContainerProperties containerProperties = await this.Container.ReadContainerAsync();
string[] selfLinkSegments = containerProperties.SelfLink.Split('/');
string databaseRid = selfLinkSegments[1];
string containerRid = selfLinkSegments[3];
Container containerByRid = this.cosmosClient.GetContainer(databaseRid, containerRid);

// Invalid characters are listed here.
//https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.resource.id?view=azure-dotnet&preserve-view=true#remarks
FeedIterator<JObject> invalidItemsIterator = this.Container.GetItemQueryIterator<JObject>(
    @"select * from t where CONTAINS(t.id, ""/"") or CONTAINS(t.id, ""#"") or CONTAINS(t.id, ""?"") or CONTAINS(t.id, ""\\"") ");
while (invalidItemsIterator.HasMoreResults)
{
    foreach (JObject itemWithInvalidId in await invalidItemsIterator.ReadNextAsync())
    {
        // Choose a new ID that doesn't contain special characters.
        // If that isn't possible, then Base64 encode the ID to escape the special characters.
        byte[] plainTextBytes = Encoding.UTF8.GetBytes(itemWithInvalidId["id"].ToString());
        itemWithInvalidId["id"] = Convert.ToBase64String(plainTextBytes);

        // Update the item with the new ID value by using the RID-based container reference.
        JObject item = await containerByRid.ReplaceItemAsync<JObject>(
            item: itemWithInvalidId,
            ID: itemWithInvalidId["_rid"].ToString(),
            partitionKey: new Cosmos.PartitionKey(itemWithInvalidId["status"].ToString()));

        // Validating the new ID can be read by using the original name-based container reference.
        await this.Container.ReadItemAsync<ToDoActivity>(
            item["id"].ToString(),
            new Cosmos.PartitionKey(item["status"].ToString())); ;
    }
}
```

### <a name="time-to-live-purge"></a>存留時間清除
專案具有 [存留時間 (TTL) ](./time-to-live.md) 屬性集。 已清除此專案，因為 TTL 屬性已過期。

#### <a name="solution"></a>解決方案：
變更 TTL 屬性以防止專案被清除。

### <a name="lazy-indexing"></a>延遲編制索引
未趕上 [延遲索引](index-policy.md#indexing-mode) 。

#### <a name="solution"></a>解決方案：
等候編制索引以趕上或變更索引編制原則。

### <a name="parent-resource-deleted"></a>父資源已刪除
專案所在的資料庫或容器已刪除。

#### <a name="solution"></a>解決方案：
1. [還原](./online-backup-and-restore.md#request-data-restore-from-a-backup) 父資源，或重新建立資源。
1. 建立新的資源以取代已刪除的資源。

### <a name="7-containercollection-names-are-case-sensitive"></a>7. 容器/集合名稱區分大小寫
容器/集合名稱在 Cosmos DB 中區分大小寫。

#### <a name="solution"></a>解決方案：
連接到 Cosmos DB 時，請務必使用確切的名稱。

## <a name="next-steps"></a>後續步驟
* 當您使用 Azure Cosmos DB .NET SDK 時[，診斷和疑難排解](troubleshoot-dot-net-sdk.md)問題。
* 瞭解 [.net v3](performance-tips-dotnet-sdk-v3-sql.md) 和 [.net v2](performance-tips.md)的效能指導方針。
