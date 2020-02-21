---
title: 搜尋 Azure Cosmos DB 的資料
titleSuffix: Azure Cognitive Search
description: 從 Azure Cosmos DB 將資料匯入 Azure 認知搜尋中的可搜尋索引。 索引子可為選取的資料來源 (例如 Azure Cosmos DB) 將資料擷取自動化。
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/20/2020
ms.locfileid: "77498636"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>如何使用 Azure 認知搜尋中的索引子為 Cosmos DB 資料編制索引 

> [!IMPORTANT] 
> SQL API 已正式推出。
> MongoDB API、Gremlin API 和 Cassandra API 支援目前處於公開預覽狀態。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來要求預覽的存取權。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

> [!WARNING]
> Azure 認知搜尋僅支援將[索引編制原則](https://docs.microsoft.com/azure/cosmos-db/index-policy)設定為[一致](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)的 Cosmos DB 集合。 不建議將具有延遲索引編制原則的集合編制索引，而且可能會導致資料遺失。 不支援已停用索引的集合。

本文說明如何設定 Azure Cosmos DB[索引子](search-indexer-overview.md)來解壓縮內容，並讓它在 Azure 認知搜尋中可供搜尋。 此工作流程會建立 Azure 認知搜尋索引，並使用從 Azure Cosmos DB 解壓縮的現有文字來載入它。 

由於術語可能會造成混淆，值得注意的是， [Azure Cosmos DB 索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)和[Azure 認知搜尋索引](search-what-is-an-index.md)是不同的作業，每個服務都是唯一的。 開始進行 Azure 認知搜尋索引之前，您的 Azure Cosmos DB 資料庫必須已經存在並包含資料。

Azure 認知搜尋中的 Cosmos DB 索引子可以編目透過不同通訊協定存取的[Azure Cosmos DB 專案](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)。 

+ 針對正式推出的[SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)，您可以使用[入口網站](#cosmos-indexer-portal)、 [REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)或[.net SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)來建立資料來源和索引子。

+ 針對[MONGODB API （預覽）](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)，您可以使用[入口網站](#cosmos-indexer-portal)或[REST API 版本 2019-05-06-preview](search-api-preview.md)來建立資料來源和索引子。

+ 針對[Cassandra API （預覽）](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)和[Gremlin API （預覽）](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)，您只能使用[REST API 版本 2019-05-06-preview](search-api-preview.md)來建立資料來源和索引子。


> [!Note]
> 如果您想要在「Azure 認知搜尋」中看到支援，您可以針對[資料表 API](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab) ，將投票轉型為「使用者語音」。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

> [!Note]
> 入口網站目前支援 SQL API 和 MongoDB API （預覽）。

索引 Azure Cosmos DB 專案最簡單的方法是使用[Azure 入口網站](https://portal.azure.com/)中的 wizard。 藉由取樣資料並讀取容器的中繼資料，Azure 認知搜尋中的「匯[**入資料**](search-import-data-portal.md)」 wizard 可以建立預設索引、將來源欄位對應至目標索引欄位，以及在單一作業中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

我們建議您針對 Azure 認知搜尋和 Azure Cosmos DB 使用相同的區域或位置，以取得較低的延遲，並避免頻寬費用。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

您應該會有 Cosmos DB 帳戶、對應至 SQL API、MongoDB API （預覽）的 Azure Cosmos DB 資料庫，或 Gremlin API （預覽），以及資料庫中的內容。

請確定您的 Cosmos DB 資料庫包含資料。 「匯[入資料」 wizard](search-import-data-portal.md)會讀取中繼資料，並執行資料取樣來推斷索引架構，但它也會從 Cosmos DB 載入資料。 如果資料遺失，嚮導會停止並出現下列錯誤：「從資料來源偵測索引架構時發生錯誤：無法建立原型索引，因為資料來源 ' emptycollection ' 未傳回任何資料」。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

您可以從 [Azure 認知搜尋服務] 頁面的命令列[啟動精靈](search-import-data-portal.md)，或者如果您要連線至 COSMOS DB SQL API，您可以在 Cosmos DB 帳戶左側流覽窗格的 [**設定**] 區段中，按一下 [**新增 Azure 認知搜尋**]。

   ![入口網站中的匯入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在 [**資料來源**] 頁面中，來源必須是 [ **Cosmos DB**]，並具有下列規格：

+ **Name**是資料來源物件的名稱。 建立之後，您可以為其他工作負載選擇它。

+ **Cosmos DB 帳戶**應該是 Cosmos DB 的主要或次要連接字串，以及 `AccountEndpoint` 和 `AccountKey`。 針對 MongoDB 集合，請將**ApiKind = MongoDB**新增至連接字串的結尾，並以分號分隔它與連接字串。 針對 Gremlin API 和 Cassandra API，請使用[REST API](#cosmosdb-indexer-rest)的指示。

+ **資料庫**是帳戶中的現有資料庫。 

+ **集合**是檔的容器。 檔必須存在，才能成功匯入。 

+ 如果您想要所有檔，**查詢**可以是空白，否則可以輸入可選取檔子集的查詢。 **查詢**只適用于 SQL API。

   ![Cosmos DB 資料來源定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "Cosmos DB 資料來源定義")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-略過 wizard 中的 [擴充內容] 頁面

新增認知技能（或擴充）不是匯入需求。 除非您有特定的需求，才能[將 AI 擴充加入](cognitive-search-concept-intro.md)至索引管線，所以您應該略過此步驟。

若要略過此步驟，請按一下頁面底部的藍色按鈕，以尋找 [下一步] 和 [略過]。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引] 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 此 wizard 可以根據中繼資料和取樣來源資料來產生欄位清單。 

您可以按一下屬性資料行頂端的核取方塊，以大量選取屬性。 針對應傳回給用戶端應用程式並受全文檢索搜尋**處理的每**個欄位 **，選擇 [** 可取得] 和 [可搜尋]。 您會發現整數不是全文檢索或模糊搜尋（數位會逐字評估，而且通常適用于篩選）。

如需詳細資訊，請參閱[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的描述。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![Cosmos DB 索引定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "Cosmos DB 索引定義")

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 在您的 Azure 認知搜尋服務中，資料來源物件和索引物件會儲存為已命名的資源。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子，*索引子*是 Azure 認知搜尋中的一項資源，可將外部資料源編目以尋找可搜尋的內容。 「匯**入資料**」 wizard 的輸出是一個索引子，可將您的 Cosmos DB 資料來源編目、將可搜尋的內容解壓縮，並將其匯入 Azure 認知搜尋的索引中。

下列螢幕擷取畫面顯示預設的索引子設定。 如果您想要一次執行索引子，您可以切換為 [**一次**]。 按一下 [**提交**] 以執行嚮導並建立所有物件。 編製索引的程序會立即開始。

   ![Cosmos DB 索引子定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "Cosmos DB 索引子定義")

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果您看不到預期的資料，可能需要在更多欄位上設定更多屬性。 刪除您剛建立的索引和索引子，然後再次逐步執行嚮導，修改您在步驟5中索引屬性的選取專案。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

您可以使用 REST API 來編制 Azure Cosmos DB 資料的索引，遵循 Azure 認知搜尋中所有索引子通用的三部分工作流程：建立資料來源、建立索引、建立索引子。 當您提交建立索引子要求時，會從 Cosmos DB 進行資料解壓縮。 完成此要求之後，您將會有可查詢的索引。 

> [!NOTE]
> 若要從 Cosmos DB Gremlin API 或 Cosmos DB 編制索引資料 Cassandra API 您必須先填寫[這份表單](https://aka.ms/azure-cognitive-search/indexer-preview)，以要求存取閘道預覽。 處理您的要求之後，您會收到如何使用[REST API 版本 2019-05-06-Preview](search-api-preview.md)來建立資料來源的指示。

本文稍早提到， [Azure Cosmos DB 編制索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)和[Azure 認知搜尋索引](search-what-is-an-index.md)編制索引是不同的作業。 針對 Cosmos DB 編制索引，預設會自動為所有檔編制索引，但不含 Cassandra API。 如果您關閉自動編制索引，則只能透過其自我連結或使用檔識別碼的查詢來存取檔。 Azure 認知搜尋索引需要在將由 Azure 認知搜尋編制索引的集合中開啟 Cosmos DB 自動編制索引。 註冊 Cosmos DB Cassandra API 索引子預覽時，將會提供如何設定 Cosmos DB 索引的指示。

> [!WARNING]
> Azure Cosmos DB 是新一代的 DocumentDB。 先前使用 API 版本**2017-11-11** ，您可以使用 `documentdb` 語法。 這表示您可以將資料來源類型指定為 `cosmosdb` 或 `documentdb`。 從 API 版本**2019-05-06**開始，Azure 認知搜尋 Api 和入口網站僅支援本文中指示的 `cosmosdb` 語法。 這表示，如果您想要連接到 Cosmos DB 端點，則資料來源類型必須 `cosmosdb`。

### <a name="1---assemble-inputs-for-the-request"></a>1-組合要求的輸入

針對每個要求，您必須提供 Azure 認知搜尋的服務名稱和管理金鑰（在 POST 標頭中），以及 blob 儲存體的儲存體帳戶名稱和金鑰。 您可以使用[Postman](search-get-started-postman.md)將 HTTP 要求傳送至 Azure 認知搜尋。

將下列四個值複製到 [記事本]，讓您可以將其貼入要求中：

+ Azure 認知搜尋服務名稱
+ Azure 認知搜尋管理金鑰
+ Cosmos DB 連接字串

您可以在入口網站中找到這些值：

1. 在 Azure 認知搜尋的入口網站頁面中，從 [總覽] 頁面複製 [搜尋服務 URL]。

2. 在左側導覽窗格中，按一下 [**金鑰**]，然後複製主要或次要金鑰（它們是相等的）。

3. 切換至 Cosmos 儲存體帳戶的入口網站頁面。 在左側流覽窗格的 [**設定**] 底下，按一下 [**金鑰**]。 此頁面提供 URI、兩組連接字串，以及兩組索引鍵。 將其中一個連接字串複製到 [記事本]。

### <a name="2---create-a-data-source"></a>2-建立資料來源

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

若要建立資料來源，請制訂 POST 要求：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

要求的主體包含資料來源定義，其中應包含下列欄位：

| 欄位   | 描述 |
|---------|-------------|
| **name** | 必要。 選擇任何名稱來表示您的資料來源物件。 |
|**type**| 必要。 必須是 `cosmosdb`。 |
|**credentials** | 必要。 必須是 Cosmos DB 連接字串。<br/>若是 SQL 集合，連接字串的格式如下： `AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/>針對 MongoDB 集合，請將**ApiKind = MongoDB**新增至連接字串：<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>針對 Gremlin 圖形和 Cassandra 資料表，請註冊[閘道索引子預覽](https://aka.ms/azure-cognitive-search/indexer-preview)以取得預覽的存取權，以及如何格式化認證的相關資訊。<br/><br/>請避免在端點 URL 中使用連接埠號碼。 如果您包含埠號碼，Azure 認知搜尋將無法為您的 Azure Cosmos DB 資料庫編制索引。|
| **container** | 包含下列元素： <br/>**名稱**：必要。 指定要編制索引之資料庫集合的識別碼。<br/>**查詢**：選擇性。 您可以指定查詢，將任意 JSON 檔壓平合併為 Azure 認知搜尋可以編制索引的一般架構。<br/>針對 MongoDB API、Gremlin API 和 Cassandra API，不支援查詢。 |
| **dataChangeDetectionPolicy** | 建議使用。 請參閱[索引變更的文件](#DataChangeDetectionPolicy)小節。|
|**dataDeletionDetectionPolicy** | 選擇性。 請參閱[索引刪除的文件](#DataDeletionDetectionPolicy)小節。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查詢來形塑索引的資料
您可以指定 SQL 查詢來壓平合併巢狀屬性或陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 

> [!WARNING]
> **MONGODB api**、 **Gremlin API**和**Cassandra API**不支援自訂查詢： `container.query` 參數必須設定為 null 或省略。 如果您需要使用自訂查詢，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search) 告訴我們。

範例文件︰

    {
        "userId": 10001,
        "contact": {
            "firstName": "andy",
            "lastName": "hoh"
        },
        "company": "microsoft",
        "tags": ["azure", "cosmosdb", "search"]
    }

篩選查詢：

    SELECT * FROM c WHERE c.company = "microsoft" and c._ts >= @HighWaterMark ORDER BY c._ts

壓平合併查詢︰

    SELECT c.id, c.userId, c.contact.firstName, c.contact.lastName, c.company, c._ts FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts
    
    
投影查詢：

    SELECT VALUE { "id":c.id, "Name":c.contact.firstName, "Company":c.company, "_ts":c._ts } FROM c WHERE c._ts >= @HighWaterMark ORDER BY c._ts


陣列壓平合併查詢︰

    SELECT c.id, c.userId, tag, c._ts FROM c JOIN tag IN c.tags WHERE c._ts >= @HighWaterMark ORDER BY c._ts


### <a name="3---create-a-target-search-index"></a>3-建立目標搜尋索引 

[建立目標 Azure 認知搜尋索引](/rest/api/searchservice/create-index)（如果您還沒有的話）。 下列範例會建立具有識別碼和描述欄位的索引：

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
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

請確定目標索引的結構描述會與來源 JSON 文件的結構描述或自訂查詢投射的輸出相容。

> [!NOTE]
> 針對已分割的集合，預設檔索引鍵是 Azure Cosmos DB 的 `_rid` 屬性，而 Azure 認知搜尋會自動將其重新命名為 `rid`，因為功能變數名稱的開頭不能是底線字元。 此外，Azure Cosmos DB `_rid` 值包含 Azure 認知搜尋金鑰中的無效字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 針對 MongoDB 集合，Azure 認知搜尋會自動將 `_id` 屬性重新命名為 `id`。  

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

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "mycosmosdbindexer",
      "dataSourceName" : "mycosmosdbdatasource",
      "targetIndexName" : "mysearchindex",
      "schedule" : { "interval" : "PT2H" }
    }

這個索引子每隔兩小時就會執行一次 (已將排程間隔設為 "PT2H")。 若每隔 30 分鐘就要執行索引子，可將間隔設為 "PT30M"。 支援的最短間隔為 5 分鐘。 排程為選擇性 - 如果省略，索引子只會在建立時執行一次。 不過，您隨時都可依需求執行索引子。   

如需建立索引子 API 的詳細資訊，請參閱 [建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

如需定義索引子排程的詳細資訊，請參閱[如何針對 Azure 認知搜尋排定索引子](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

正式推出的 .NET SDK 與正式運作的 REST API 完全相同。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>索引已變更的文件

資料變更偵測原則是用來有效識別已變更的資料項目。 目前，唯一支援的原則是使用 Azure Cosmos DB 所提供的 [`_ts` （時間戳記）] 屬性的[`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy) ，其指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

為確保索引子效能良好，強烈建議使用此原則。 

如果您使用自訂查詢，請確定查詢有投射 `_ts` 屬性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>累加進度與自訂查詢

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷時，索引子仍能夠在下次執行時從中斷處繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。 

若要在使用自訂查詢時啟用累加進度，請確保您的查詢是依 `_ts` 欄排序查詢結果。 這會啟用週期性檢查指標，Azure 認知搜尋會使用這些功能來提供發生失敗的累加進度。   

在某些情況下，即使您的查詢包含 `ORDER BY [collection alias]._ts` 子句，Azure 認知搜尋也可能無法推斷查詢是由 `_ts`排序。 您可以使用 [`assumeOrderByHighWaterMarkColumn` 設定] 屬性，告訴 Azure 認知搜尋結果是否已排序。 若要指定這項提示，請依下列指示更新您的索引子： 

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "assumeOrderByHighWaterMarkColumn" : true } }
    } 

<a name="DataDeletionDetectionPolicy"></a>

## <a name="indexing-deleted-documents"></a>索引已刪除的文件

從集合中刪除資料列時，通常也會想刪除搜尋索引內的那些資料列。 資料刪除偵測原則可用來有效識別刪除的資料項目。 目前，唯一支援的原則是「 `Soft Delete` 」原則 (刪除會標示為某種形式的旗標)，指定方式如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
        "softDeleteColumnName" : "the property that specifies whether a document was deleted",
        "softDeleteMarkerValue" : "the value that identifies a document as deleted"
    }

如果您使用自訂查詢，請確定查詢有投射到 `softDeleteColumnName` 參考的屬性。

下列範例會建立包含虛刪除原則的資料來源：

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
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

## <a name="NextSteps"></a>後續步驟

恭喜！ 您已瞭解如何使用索引子來整合 Azure Cosmos DB 與 Azure 認知搜尋。

* 若要深入了解 Azure Cosmos DB，請參閱 [Azure Cosmos DB 服務頁面](https://azure.microsoft.com/services/cosmos-db/)。
* 若要深入瞭解 Azure 認知搜尋，請參閱[搜尋服務頁面](https://azure.microsoft.com/services/search/)。
