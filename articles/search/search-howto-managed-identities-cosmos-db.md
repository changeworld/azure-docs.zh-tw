---
title: 使用受控識別來設定與 Cosmos DB 帳戶的連線 (預覽)
titleSuffix: Azure Cognitive Search
description: 了解如何使用受控識別來設定與 Cosmos DB 帳戶的索引子連線 (預覽)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 107cd113645a2cbd4b452f9350fa67d734ee6df8
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143651"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>使用受控識別來設定與 Cosmos DB 資料庫的索引子連線 (預覽)

> [!IMPORTANT] 
> 使用受控識別來設定資料來源連線的支援目前處於受管制公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。
> 您可以填寫[此表單](https://aka.ms/azure-cognitive-search/mi-preview-request)來要求預覽的存取權。

此頁面描述如何使用受控識別來設定 Azure Cosmos DB 資料庫的索引子連線，而不是在資料來源物件連接字串中提供認證。

在深入了解此功能之前，建議您先了解索引子是什麼，以及如何設定資料來源的索引子。 您可以以在下列連結找到更多資訊：
* [索引子概觀](search-indexer-overview.md)
* [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>使用受控識別設定連線

### <a name="1---turn-on-system-assigned-managed-identity"></a>1 - 開啟系統指派的受控識別

當系統指派的受控識別啟用時，Azure 會為您的搜尋服務建立身分識別，以便用來向相同租用戶與訂用帳戶內的其他 Azure 服務進行驗證。 接著，您可以在角色型存取控制 (RBAC) 指派中使用此身分識別，以允許在編製索引期間存取資料。

![開啟系統指派的受控識別](./media/search-managed-identities/turn-on-system-assigned-identity.png "開啟系統指派的受控識別")

選取 [儲存] 之後，您會看到已指派給搜尋服務的物件識別碼。

![物件識別碼](./media/search-managed-identities/system-assigned-identity-object-id.png "物件識別碼")
 
### <a name="2---add-a-role-assignment"></a>2 - 新增角色指派

在此步驟中，您將提供您 Azure 認知搜尋服務的權限，以從您的 Cosmos DB 資料庫讀取資料。

1. 在 Azure 入口網站中，瀏覽至包含您想要編製索引之資料的 Cosmos DB 帳戶。
2. 選取 [存取控制 (IAM)]
3. 選取 [新增]，然後選取 [新增角色指派]

    ![新增角色指派](./media/search-managed-identities/add-role-assignment-cosmos-db.png "新增角色指派")

4. 選取 [Cosmos DB 帳戶讀者角色]
5. 將 [存取權指派對象為] 保留為 [Azure AD 使用者、群組或服務主體]
6. 搜尋並選取您的搜尋服務，然後選取 [儲存]

    ![新增讀者及資料存取角色指派](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "新增讀者及資料存取角色指派")

### <a name="3---create-the-data-source"></a>3 - 建立資料來源

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

使用受控識別來驗證資料來源時，**認證**將不包含帳戶金鑰。

如何使用 [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) \(部分機器翻譯\) 建立 Cosmos DB 資料來源物件的範例：

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

要求的主體包含資料來源定義，其中應包含下列欄位：

| 欄位   | 描述 |
|---------|-------------|
| **name** | 必要。 選擇任何名稱，以代表您的資料來源物件。 |
|**type**| 必要。 必須是 `cosmosdb`。 |
|**credentials** | 必要。 <br/><br/>使用受控識別進行連線時，**credentials** 格式應該是：*Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId 格式：*ResourceId=/subscriptions/**您的訂用帳戶識別碼**/resourceGroups/**您的資源群組名稱**/providers/Microsoft.DocumentDB/databaseAccounts/**您的 cosmos db 帳戶名稱**/;*<br/><br/>針對 SQL 集合，連接字串不需要 ApiKind。<br/><br/>針對 MongoDB 集合，請將 **ApiKind=MongoDb** 新增至連接字串。 <br/><br/>如需 Gremlin 圖形與 Cassandra 資料表，請註冊[受管制索引子預覽](https://aka.ms/azure-cognitive-search/indexer-preview)，以取得預覽的存取權，以及如何設定認證格式的相關資訊。<br/>|
| **container** | 包含下列元素： <br/>**名稱**：必要。 指定要編製索引的資料庫集合識別碼。<br/>**查詢**：選擇性。 您可以指定查詢將任意 JSON 文件壓平合併成 Azure 認知搜尋可以編製索引的一般結構描述。<br/>針對 MongoDB API、Gremlin API 與 Cassandra API，則不支援查詢。 |
| **dataChangeDetectionPolicy** | 建議 |
|**dataDeletionDetectionPolicy** | 選用 |

Azure 入口網站和 [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) \(英文\) 也支援受控識別連接字串。 Azure 入口網站需要一個功能旗標，當您使用此頁面頂端的連結來註冊預覽時，就會提供給您。 

### <a name="4---create-the-index"></a>4 - 建立索引

索引會指定文件、屬性和其他建構中可形塑搜尋體驗的欄位。

以下說明如何使用可搜尋的 `booktitle` 欄位建立索引︰

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

如需建立索引的詳細資訊，請參閱[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="5---create-the-indexer"></a>5 - 建立索引子

索引子會以目標搜尋索引連接資料來源，並提供排程來將資料重新整理自動化。

建立索引與資料來源之後，您就可以開始建立索引子。

範例索引子定義：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

如需有關定義索引子排程的詳細資訊，請參閱[如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

## <a name="see-also"></a>另請參閱

深入了解 Cosmos DB 索引子：
* [Azure Cosmos DB 索引子](search-howto-index-cosmosdb.md)
