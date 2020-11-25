---
title: 搜尋 Azure Cosmos DB 資料
titleSuffix: Azure Cognitive Search
description: 將 Azure Cosmos DB 中的資料匯入 Azure 認知搜尋的可搜尋索引中。 索引子可為選取的資料來源 (例如 Azure Cosmos DB) 將資料擷取自動化。
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: aed1aa03527481014a63c636181725b91b17a1e8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003883"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用索引子為 Cosmos DB 資料編製索引 

> [!IMPORTANT] 
> SQL API 已正式推出。
> MongoDB API、Gremlin API 和 Cassandra API 支援目前處於公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以填寫 [此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來要求存取預覽版。 
> [REST API 預覽版本](search-api-preview.md) 可提供這些功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

> [!WARNING]
> Azure 認知搜尋只支援將 [索引編制原則](/azure/cosmos-db/index-policy) 設定為 [一致](/azure/cosmos-db/index-policy#indexing-mode) Cosmos DB 集合。 不建議使用延遲編制索引原則來編制索引集合的索引，而且可能會導致資料遺失。 不支援已停用索引的集合。

本文說明如何設定 Azure Cosmos DB [索引子](search-indexer-overview.md) 來將內容解壓縮，並讓它可在 Azure 認知搜尋中搜尋。 此工作流程會建立 Azure 認知搜尋的索引，並使用從 Azure Cosmos DB 解壓縮的現有文字載入它。 

由於術語可能會造成混淆，值得注意的是， [Azure Cosmos DB 索引編制](/azure/cosmos-db/index-overview) 和 [Azure 認知搜尋索引](search-what-is-an-index.md) 是不同的作業，每個服務都是唯一的。 開始 Azure 認知搜尋索引之前，您的 Azure Cosmos DB 資料庫必須已經存在且包含資料。

Azure 認知搜尋中的 Cosmos DB 索引子可以編目透過不同通訊協定存取的 [Azure Cosmos DB 專案](../cosmos-db/databases-containers-items.md#azure-cosmos-items) 。 

+ 針對正式推出的 [SQL API](../cosmos-db/sql-query-getting-started.md)，您可以使用 [入口網站](#cosmos-indexer-portal)、 [REST API](/rest/api/searchservice/indexer-operations)或 [.net SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer) 來建立資料來源和索引子。

+ 針對 [MONGODB API (預覽版) ](../cosmos-db/mongodb-introduction.md)，您可以使用 [入口網站](#cosmos-indexer-portal) 或 [REST API 版本 2020-06-30-preview](search-api-preview.md) 來建立資料來源和索引子。

+ 針對 [Cassandra API (preview) ](../cosmos-db/cassandra-introduction.md) 和 [Gremlin API (preview) ](../cosmos-db/graph-introduction.md)，您只能使用 [REST API 2020-06-30-preview 版](search-api-preview.md) 來建立資料來源和索引子。


> [!Note]
> 如果您想要在 Azure 認知搜尋中看到支援，您可以對 [資料表 API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) 的使用者心聲進行投票。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

> [!Note]
> 入口網站目前支援 SQL API 和 MongoDB API (preview) 。

Azure Cosmos DB 專案編制索引最簡單的方法是使用 [Azure 入口網站](https://portal.azure.com/)中的 wizard。 藉由取樣資料和讀取容器的中繼資料，Azure 認知搜尋中的 [匯 [**入資料**](search-import-data-portal.md) ] 嚮導可以建立預設索引、將來源欄位對應至目標索引欄位，以及在單一作業中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

針對 Azure 認知搜尋和 Azure Cosmos DB，建議使用相同的區域或位置，以降低延遲，並避免頻寬費用。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

您應該會有 Cosmos DB 帳戶、對應至 SQL API 的 Azure Cosmos DB 資料庫、MongoDB API (preview) 或 Gremlin API (preview) ，以及資料庫中的內容。

請確定您的 Cosmos DB 資料庫包含資料。 [匯 [入資料] 嚮導](search-import-data-portal.md) 會讀取中繼資料並執行資料取樣，以推斷索引架構，但它也會從 Cosmos DB 載入資料。 如果遺失資料，則嚮導會停止，並出現下列錯誤：「從資料來源偵測索引架構時發生錯誤：無法建立原型索引，因為資料來源 ' emptycollection ' 未傳回任何資料」。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

您可以從 [Azure 認知搜尋服務] 頁面的命令列 [啟動精靈](search-import-data-portal.md)，或者，如果您要連線到 COSMOS DB SQL API，您可以按一下 Cosmos DB 帳戶左側導覽窗格的 [**設定**] 區段中的 [**新增] Azure 認知搜尋**。

   ![入口網站中的匯入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在 [ **資料來源** ] 頁面中，來源必須是 **Cosmos DB**，並具有下列規格：

+ **Name** 是資料來源物件的名稱。 建立之後，您可以針對其他工作負載進行選擇。

+ **Cosmos DB 帳戶** 的格式應為下列其中一種：
    1. Cosmos DB 的主要或次要連接字串，格式如下： `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;` 。
        + 針對3.2 版和3.6 版的 **MongoDB 集合** ，Azure 入口網站中的 Cosmos DB 帳戶使用下列格式： `AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;ApiKind=MongoDb`
        + 若為 **Gremlin 的圖形和 Cassandra 資料表**，請註冊 [閘道索引子預覽](https://aka.ms/azure-cognitive-search/indexer-preview) 以取得預覽版的存取權，以及如何格式化認證的相關資訊。
    1.  具有下列格式的受控識別連接字串不包含帳戶金鑰： `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;(ApiKind=[api-kind];)` 。 若要使用此連接字串格式，請遵循 [使用受控識別來設定 Cosmos DB 資料庫的索引子連接](search-howto-managed-identities-cosmos-db.md)的指示。

+ **資料庫** 是帳戶中的現有資料庫。 

+ **集合** 是檔的容器。 必須要有檔才能成功匯入。 

+ 如果您想要所有檔，**查詢** 可以是空白的，否則您可以輸入可選取檔子集的查詢。 **查詢** 僅適用于 SQL API。

   ![Cosmos DB 資料來源定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 資料來源定義")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-略過 wizard 中的 [豐富內容] 頁面

新增認知技能 (或擴充) 不是匯入需求。 除非您有特定需求 [將 AI 擴充新增](cognitive-search-concept-intro.md) 至索引管線，否則您應該略過此步驟。

若要略過此步驟，請按一下頁面底部的藍色按鈕，以尋找「下一步」和「略過」。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引] 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 Wizard 可以根據中繼資料和取樣來源資料來產生欄位清單。 

您可以按一下屬性資料行頂端的核取方塊，以大量選取屬性。 針對每個應該傳回給用戶端應用 **程式的欄位**，選擇 [可取得] 和 [可搜尋 **]，並** 受限於全文檢索搜尋處理。 您將會注意到，整數不是全文檢索或模糊可搜尋 (數位會逐字進行評估，而且在篩選) 中通常很有用。

如需詳細資訊，請參閱 [索引屬性](/rest/api/searchservice/create-index#bkmk_indexAttrib) 和 [語言分析器](/rest/api/searchservice/language-support) 的描述。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![Cosmos DB 索引定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 索引定義")

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件會儲存為 Azure 認知搜尋服務中的命名資源。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子， *索引子* 是 Azure 認知搜尋中的資源，可將外部資料源編目以取得可搜尋的內容。 匯 **入資料** wizard 的輸出是一個索引子，可編目 Cosmos DB 資料來源、將可搜尋的內容解壓縮，並將其匯入 Azure 認知搜尋的索引中。

下列螢幕擷取畫面顯示預設的索引子設定。 如果您想要一次執行索引子，您可以切換為 **一次** 。 按一下 [ **提交** ]，執行嚮導並建立所有物件。 編製索引的程序會立即開始。

   ![Cosmos DB 索引子定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 索引子定義")

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果您沒有看到預期的資料，您可能需要在更多欄位上設定更多屬性。 刪除您剛才建立的索引和索引子，然後再次逐步執行嚮導，以修改您在步驟5中選取的索引屬性。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

您可以使用 REST API 來編制 Azure Cosmos DB 資料的索引，並遵循 Azure 認知搜尋中所有索引子通用的三部分工作流程：建立資料來源、建立索引、建立索引子。 當您提交建立索引子要求時，就會從 Cosmos DB 中進行資料解壓縮。 完成此要求之後，您將會有可查詢的索引。 

> [!NOTE]
> 針對 Cosmos DB Gremlin API 或 Cosmos DB 的資料編制索引 Cassandra API 您必須先填寫 [此表單](https://aka.ms/azure-cognitive-search/indexer-preview)，要求存取閘道預覽。 處理您的要求之後，您將會收到如何使用 [REST API 2020-06-30-Preview 版](search-api-preview.md) 來建立資料來源的指示。

稍早在本文中，我們提到 [Azure Cosmos DB 索引](/azure/cosmos-db/index-overview) 編制索引和 [Azure 認知搜尋索引](search-what-is-an-index.md) 編制索引是不同的作業。 針對 Cosmos DB 索引編制，預設會自動編制所有檔的索引，但 Cassandra API 除外。 如果您關閉自動編制索引，只能透過其自我連結或使用檔識別碼的查詢來存取檔。 Azure 認知搜尋索引需要在將依 Azure 認知搜尋編制索引的集合中開啟 Cosmos DB 自動編制索引。 註冊 Cosmos DB Cassandra API 索引子預覽時，系統會提供您如何設定 Cosmos DB 索引的指示。

> [!WARNING]
> Azure Cosmos DB 是新一代的 DocumentDB。 先前使用 API 版本 **2017-11-11** ，您可以使用 `documentdb` 語法。 這表示您可以將您的資料來源類型指定為 `cosmosdb` 或 `documentdb` 。 從 API 版本 **2019-05-06** 開始，Azure 認知搜尋 Api 和入口網站都只支援本文 `cosmosdb` 中指示的語法。 這表示， `cosmosdb` 如果您想要連接到 Cosmos DB 端點，則必須要有此資料來源類型。

### <a name="1---assemble-inputs-for-the-request"></a>1-組合要求的輸入

針對每個要求，您必須為 POST 標頭) 中的 Azure 認知搜尋 (提供服務名稱和管理金鑰，以及 blob 儲存體的儲存體帳戶名稱和金鑰。 您可以使用 [Postman 或 Visual Studio Code](search-get-started-rest.md) ，將 HTTP 要求傳送至 Azure 認知搜尋。

將下列四個值複製到 [記事本]，讓您可以將它們貼到要求中：

+ Azure 認知搜尋服務名稱
+ Azure 認知搜尋管理金鑰
+ Cosmos DB 連接字串

您可以在入口網站中找到這些值：

1. 在 Azure 認知搜尋的入口網站頁面中，複製 [總覽] 頁面中的 [搜尋服務 URL]。

2. 在左側流覽窗格中，按一下 [機 **碼** ]，然後複製主要或次要金鑰， (它們相等) 。

3. 切換至 Cosmos 儲存體帳戶的入口網站頁面。 在左側流覽窗格的 [ **設定**] 底下，按一下 [ **金鑰**]。 此頁面提供 URI、兩組連接字串，以及兩組索引鍵。 將其中一個連接字串複製到 [記事本]。

### <a name="2---create-a-data-source"></a>2-建立資料來源

**資料來源** 指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

若要建立資料來源，請制訂 POST 要求：

```http

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
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
|**credentials** | 必要。 必須遵循 Cosmos DB 連接字串格式或受控識別連接字串格式。<br/><br/>針對 **SQL 集合**，連接字串可以遵循下列其中一種格式： <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<li>具有下列格式的受控識別連接字串不包含帳戶金鑰： `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;` 。 若要使用此連接字串格式，請遵循 [使用受控識別來設定 Cosmos DB 資料庫的索引子連接](search-howto-managed-identities-cosmos-db.md)的指示。<br/><br/>針對3.2 版和3.6 版的 **MongoDB 集合** ，請使用下列其中一種連接字串格式： <li>`AccountEndpoint=https://<Cosmos DB account name>.documents.azure.com;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<li>具有下列格式的受控識別連接字串不包含帳戶金鑰： `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.DocumentDB/databaseAccounts/<your cosmos db account name>/;ApiKind=MongoDb;` 。 若要使用此連接字串格式，請遵循 [使用受控識別來設定 Cosmos DB 資料庫的索引子連接](search-howto-managed-identities-cosmos-db.md)的指示。<br/><br/>若為 **Gremlin 的圖形和 Cassandra 資料表**，請註冊 [閘道索引子預覽](https://aka.ms/azure-cognitive-search/indexer-preview) 以取得預覽版的存取權，以及如何格式化認證的相關資訊。<br/><br/>請避免在端點 URL 中使用連接埠號碼。 如果您包含埠號碼，Azure 認知搜尋將無法為您的 Azure Cosmos DB 資料庫編制索引。|
| **container** | 包含下列元素： <br/>**名稱**：必要。 指定要編製索引的資料庫集合識別碼。<br/>**查詢**：選擇性。 您可以指定查詢將任意 JSON 文件壓平合併成 Azure 認知搜尋可以編製索引的一般結構描述。<br/>針對 MongoDB API、Gremlin API 與 Cassandra API，則不支援查詢。 |
| **dataChangeDetectionPolicy** | 建議使用。 請參閱[索引變更的文件](#DataChangeDetectionPolicy)小節。|
|**dataDeletionDetectionPolicy** | 選擇性。 請參閱[索引刪除的文件](#DataDeletionDetectionPolicy)小節。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查詢來形塑索引的資料
您可以指定 SQL 查詢來壓平合併巢狀屬性或陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 

> [!WARNING]
> **MONGODB API**、 **Gremlin api** 和 **Cassandra API** 的自訂查詢不支援： `container.query` 參數必須設定為 null 或省略。 如果您需要使用自訂查詢，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search) 告訴我們。

範例文件︰

```http
    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }
```

篩選查詢：

```sql
SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts
```

壓平合併查詢︰

```sql
SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

投影查詢：

```sql
SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

陣列壓平合併查詢︰

```sql
SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts
```

### <a name="3---create-a-target-search-index"></a>3-建立目標搜尋索引 

如果您還沒有[目標 Azure 認知搜尋索引](/rest/api/searchservice/create-index)，請先建立一個。 下列範例會建立具有識別碼和描述欄位的索引：

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
       "name": "mysearchindex",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "sortable": false,
         "facetable": false,
         "suggestions": true
       }]
     }
```

請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

> [!NOTE]
> 針對已分割的集合，預設檔索引鍵是 Azure Cosmos DB 的 `_rid` 屬性， `rid` 因為功能變數名稱不能以底線字元開頭，所以 Azure 認知搜尋會自動重新命名為。 此外，Azure Cosmos DB `_rid` 值包含 Azure 認知搜尋索引鍵中不正確字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 針對 MongoDB 集合，Azure 認知搜尋會自動將 `_id` 屬性重新命名為 `id` 。  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 資料類型與 Azure 認知搜尋資料類型之間的對應
| JSON 資料類型 | 相容的目標索引欄位類型 |
| --- | --- |
| Bool |Edm.Boolean、Edm.String |
| 看起來像是整數的數字 |Edm.Int32、Edm.Int64、Edm.String |
| 看起來像是浮點的數字 |Edm.Double、Edm.String |
| String |Edm.String |
| 基本類型的陣列，例如 ["a", "b", "c"] |Collection(Edm.String) |
| 看起來像是日期的字串 |Edm.DateTimeOffset、Edm.String |
| GeoJSON 物件，例如 { "type": "Point", "coordinates": [long, lat] } |Edm.GeographyPoint |
| 其他 JSON 物件 |N/A |

### <a name="4---configure-and-run-the-indexer"></a>4-設定和執行索引子

建立索引和資料來源之後，您就可以開始建立索引子︰

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }
```

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](/rest/api/searchservice/create-indexer)。

如需定義索引子排程的詳細資訊，請參閱 [如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

正式推出的 .NET SDK 與正式運作的 REST API 有完全相同的同位。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [azure.search.documents。 searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents。 searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)
+ [azure.search.documents。 searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex)
+ [azure.search.documents。 searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>索引已變更的文件

資料變更偵測原則是用來有效識別已變更的資料項目。 目前唯一支援的原則是 [`HighWaterMarkChangeDetectionPolicy`](/dotnet/api/azure.search.documents.indexes.models.highwatermarkchangedetectionpolicy) 使用 `_ts` Azure Cosmos DB 所提供的 (timestamp) 屬性，其指定方式如下：

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }
```

為確保索引子效能良好，強烈建議使用此原則。 

如果您使用自訂查詢，請確定查詢有投射 `_ts` 屬性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>累加進度與自訂查詢

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷時，索引子仍能夠在下次執行時從中斷處繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。 

若要在使用自訂查詢時啟用累加進度，請確保您的查詢是依 `_ts` 欄排序查詢結果。 這可讓 Azure 認知搜尋用來在發生失敗時提供增量進度的定期檢查指標。   

在某些情況下，即使您的查詢包含 `ORDER BY [collection alias]._ts` 子句，Azure 認知搜尋可能不會推斷查詢是依排序 `_ts` 。 您可以使用 [設定] 屬性，告訴 Azure 認知搜尋結果已排序 `assumeOrderByHighWaterMarkColumn` 。 若要指定這項提示，請依下列指示更新您的索引子： 

```http
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 
```

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>索引已刪除的文件

從集合中刪除資料列時，通常也會想刪除搜尋索引內的那些資料列。 資料刪除偵測原則可用來有效識別刪除的資料項目。 目前，唯一支援的原則是「 `Soft Delete` 」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

```http
    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }
```

如果您使用自訂查詢，請確定查詢有投射到 `softDeleteColumnName` 參考的屬性。

下列範例會建立包含虛刪除原則的資料來源：

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [Search service admin key]

    {
        "name": "mycosmosdbdatasource",
        "type": "cosmosdb",
        "credentials": {
            "connectionString": "AccountEndpoint=https://myCosmosDbEndpoint.documents.azure.com;AccountKey=myCosmosDbAuthKey;Database=myCosmosDbDatabaseId"
        },
        "container": { "name": "myCosmosDbCollectionId" },
        "dataChangeDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "_ts"
        },
        "dataDeletionDetectionPolicy": {
            "@odata.type": "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName": "isDeleted",
            "softDeleteMarkerValue": "true"
        }
    }
```

## <a name="next-steps"></a><a name="NextSteps"></a>後續步驟

恭喜！ 您已瞭解如何使用索引子將 Azure Cosmos DB 與 Azure 認知搜尋整合。

* 若要深入了解 Azure Cosmos DB，請參閱 [Azure Cosmos DB 服務頁面](https://azure.microsoft.com/services/cosmos-db/)。
* 若要深入瞭解 Azure 認知搜尋，請參閱 [搜尋服務頁面](https://azure.microsoft.com/services/search/)。