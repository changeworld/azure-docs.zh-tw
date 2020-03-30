---
title: 搜索 Azure 宇宙資料庫資料
titleSuffix: Azure Cognitive Search
description: 將資料從 Azure 宇宙資料庫導入到 Azure 認知搜索中的可搜索索引中。 索引子可為選取的資料來源 (例如 Azure Cosmos DB) 將資料擷取自動化。
author: mgottein
manager: nitinme
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/02/2020
ms.openlocfilehash: d1723b6c5d56554fbff576f6a07e37455845bda4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283000"
---
# <a name="how-to-index-cosmos-db-data-using-an-indexer-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用索引子為 Cosmos DB 資料編製索引 

> [!IMPORTANT] 
> SQL API 通常可用。
> 蒙戈DB API、格雷姆林 API 和卡珊多拉 API 支援當前處於公共預覽版中。 預覽功能是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 您可以通過填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)來請求訪問預覽。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供預覽功能。 目前的入口網站支援有限，而且沒有 .NET SDK 支援。

> [!WARNING]
> Azure 認知搜索僅支援將[索引策略](https://docs.microsoft.com/azure/cosmos-db/index-policy)設置為["一致](https://docs.microsoft.com/azure/cosmos-db/index-policy#indexing-mode)"的 Cosmos DB 集合。 不建議使用延遲索引策略對集合進行索引，並可能導致資料丟失。 不支援禁用索引的集合。

本文介紹如何配置 Azure Cosmos DB[索引子](search-indexer-overview.md)以提取內容並使之在 Azure 認知搜索中可搜索。 此工作流創建 Azure 認知搜索索引，並將其載入從 Azure Cosmos DB 提取的現有文本。 

由於術語可能令人困惑，因此值得注意的是[，Azure Cosmos DB 索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)和 Azure[認知搜索索引](search-what-is-an-index.md)是不同的操作，每個服務都獨有。 在開始 Azure 認知搜索索引之前，Azure Cosmos 資料庫必須已經存在並包含資料。

Azure 認知搜索中的 Cosmos DB 索引子可以爬網通過不同協定訪問的[Azure Cosmos DB 項](https://docs.microsoft.com/azure/cosmos-db/databases-containers-items#azure-cosmos-items)。 

+ 對於[通常](https://docs.microsoft.com/azure/cosmos-db/sql-api-query-reference)可用的 SQL API ，可以使用[門戶](#cosmos-indexer-portal)REST [API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)或[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)來創建資料來源和索引子。

+ 對於[MongoDB API（預覽），](https://docs.microsoft.com/azure/cosmos-db/mongodb-introduction)您可以使用[門戶](#cosmos-indexer-portal)或 REST API[版本 2019-05-06-預覽](search-api-preview.md)來創建資料來源和索引子。

+ 對於[卡珊多拉 API（預覽）](https://docs.microsoft.com/azure/cosmos-db/cassandra-introduction)和[格雷姆林 API（預覽），](https://docs.microsoft.com/azure/cosmos-db/graph-introduction)您只能使用[REST API 版本 2019-05-06-預覽](search-api-preview.md)來創建資料來源和索引子。


> [!Note]
> 如果您希望在 Azure 認知搜索中支援[表 API，](https://feedback.azure.com/forums/263029-azure-search/suggestions/32759746-azure-search-should-be-able-to-index-cosmos-db-tab)則可以對"使用者語音"投一票。
>

<a name="cosmos-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

> [!Note]
> 門戶當前支援 SQL API 和 MongoDB API（預覽）。

索引 Azure Cosmos DB 項的最簡單方法是在[Azure 門戶](https://portal.azure.com/)中使用嚮導。 通過採樣資料並在容器上讀取中繼資料，Azure 認知搜索中的[**導入資料**](search-import-data-portal.md)嚮導可以創建預設索引、將源欄位映射到目標索引欄位，並在單個操作中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

我們建議對 Azure 認知搜索和 Azure Cosmos DB 使用相同的區域或位置來降低延遲並避免頻寬費用。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

您應該具有 Cosmos DB 帳戶、映射到 SQL API 的 Azure Cosmos 資料庫、蒙戈DB API（預覽）或 Gremlin API（預覽）以及資料庫中的內容。

確保您的 Cosmos 資料庫資料庫包含資料。 [導入資料嚮導](search-import-data-portal.md)讀取中繼資料並執行資料採樣以推斷索引架構，但它也載入來自 Cosmos DB 的資料。 如果缺少資料，嚮導將停止此錯誤"從資料來源檢測索引架構錯誤：無法生成原型索引，因為資料來源"emptycollection"未返回任何資料。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

可以從 Azure 認知搜索服務頁中的命令列[啟動嚮導](search-import-data-portal.md)，或者如果您要連接到 Cosmos DB SQL API，則可以在 Cosmos DB 帳戶左側功能窗格的 **"設置"** 部分按一下"**添加 Azure 認知搜索**"。

   ![門戶中的導入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在**資料來源**頁中，源必須是**Cosmos DB，** 具有以下規範：

+ **名稱**是資料來源物件的名稱。 創建後，您可以選擇它用於其他工作負荷。

+ **Cosmos DB 帳戶**應該是 Cosmos DB 的主連接字串，具有`AccountEndpoint`和`AccountKey`。 對於 MongoDB 集合，將**ApiKind_MongoDb**添加到連接字串的末尾，並將其與具有分號的連接字串分開。 對於 Gremlin API 和卡珊多拉 API，請使用[REST API](#cosmosdb-indexer-rest)的說明。

+ **資料庫**是帳戶中的現有資料庫。 

+ **集合**是文檔的容器。 單據必須存在才能成功導入。 

+ 如果需要所有文檔，**查詢**可以為空，否則可以輸入選擇文檔子集的查詢。 **查詢**僅適用于 SQL API。

   ![宇宙資料庫資料來源定義](media/search-howto-index-cosmosdb/cosmosdb-datasource.png "宇宙資料庫資料來源定義")

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - 跳過嚮導中的"豐富內容"頁面

添加認知技能（或豐富）不是進口要求。 除非需要向索引管道添加[AI 充實](cognitive-search-concept-intro.md)，否則應跳過此步驟。

要跳過此步驟，請按一下頁面底部的藍色按鈕，查看"下一步"和"跳過"。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引]**** 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 嚮導可以基於中繼資料和採樣來源資料生成欄位清單。 

您可以通過按一下屬性列頂部的核取方塊批量選擇屬性。 為應返回到用戶端應用並受全文檢索搜尋處理的每個欄位選擇 **"可檢索**"和 **"可搜索**"。 您會注意到整數不是全文或模糊可搜索（數位是逐字計算的，在篩選器中通常很有用）。

有關詳細資訊，請查看[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的說明。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![宇宙資料庫索引定義](media/search-howto-index-cosmosdb/cosmosdb-index-schema.png "宇宙資料庫索引定義")

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件在 Azure 認知搜索服務中作為命名資源保存。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子，*索引子*是 Azure 認知搜索中的資源，用於對外部資料源進行搜索。 **導入資料**嚮導的輸出是一個索引子，用於對 Cosmos DB 資料來源進行爬網、提取可搜索內容並將其導入 Azure 認知搜索上的索引。

以下螢幕截圖顯示了預設索引子配置。 如果要運行索引子一次，可以切換到 **"一**次"。 按一下"**提交**"以運行嚮導並創建所有物件。 編製索引的程序會立即開始。

   ![宇宙資料庫索引子定義](media/search-howto-index-cosmosdb/cosmosdb-indexer.png "宇宙資料庫索引子定義")

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果看不到所需的資料，則可能需要在更多欄位上設置更多屬性。 刪除剛剛創建的索引和索引子，然後再次遍進嚮導，修改步驟 5 中索引屬性的選擇。 

<a name="cosmosdb-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

可以使用 REST API 根據 Azure 認知搜索中所有索引者共有的三部分工作流來索引 Azure Cosmos 資料庫資料：創建資料來源、創建索引子、創建索引子。 提交創建索引子請求時，將從 Cosmos DB 提取資料。 此請求完成後，您將具有可查詢的索引。 

> [!NOTE]
> 要索引來自 Cosmos DB Gremlin API 或 Cosmos DB Cassandra API 的資料，您必須首先通過填寫[此表單](https://aka.ms/azure-cognitive-search/indexer-preview)請求訪問門控預覽。 處理請求後，您將收到有關如何使用[REST API 版本 2019-05-06-預覽](search-api-preview.md)創建資料來源的說明。

本文前面提到[Azure Cosmos DB 索引](https://docs.microsoft.com/azure/cosmos-db/index-overview)和 Azure[認知搜索索引](search-what-is-an-index.md)索引是不同的操作。 對於 Cosmos DB 索引，預設情況下，除使用 Cassandra API 外，所有文檔都將自動編制索引。 如果關閉自動索引，則只能通過文檔的自連結或使用文件識別碼 查詢訪問文檔。 Azure 認知搜索索引要求在集合中打開 Cosmos DB 自動索引，該集合將由 Azure 認知搜索編制索引。 註冊 Cosmos DB Cassandra API 索引子預覽時，您將獲得有關如何設置 Cosmos DB 索引的說明。

> [!WARNING]
> Azure Cosmos DB 是新一代的 DocumentDB。 以前使用 API 版本**2017-11-11，** 您可以使用語法`documentdb`。 這意味著您可以將資料來源類型指定為`cosmosdb`或`documentdb`。 從 API 版本**2019-05-06**開始，Azure 認知搜索 API`cosmosdb`和門戶僅支援本文中指示的語法。 這意味著，如果要連接到 Cosmos `cosmosdb` DB 終結點，則必須使用資料來源類型。

### <a name="1---assemble-inputs-for-the-request"></a>1 - 為請求組裝輸入

對於每個請求，必須提供 Azure 認知搜索的服務名稱和管理金鑰（在 POST 標頭中）以及 Blob 存儲的存儲帳戶名稱和金鑰。 您可以使用[Postman](search-get-started-postman.md)向 Azure 認知搜索發送 HTTP 要求。

將以下四個值複製到記事本中，以便將它們粘貼到請求中：

+ Azure 認知搜索服務名稱
+ Azure 認知搜索管理金鑰
+ 宇宙 DB 連接字串

您可以在門戶中找到以下值：

1. 在 Azure 認知搜索的門戶頁中，從"概述"頁複製搜索服務 URL。

2. 在左側功能窗格中，按一下 **"鍵"，** 然後複製主鍵或輔助鍵（它們等效）。

3. 切換到 Cosmos 存儲帳戶的門戶頁面。 在左側功能窗格中，**在"設置"** 下，按一下 **"鍵**"。 此頁提供 URI、兩組連接字串和兩組鍵。 將其中一個連接字串複製到記事本。

### <a name="2---create-a-data-source"></a>2 - 創建資料來源

**資料來源**指定要編製索引的資料、認證，以及可識別資料是否變更 (例如修改或刪除集合內的文件) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

要創建資料來源，請制定 POST 請求：

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
| **名稱** | 必要。 選擇任何名稱來表示資料來源物件。 |
|**型別**| 必要。 必須是 `cosmosdb`。 |
|**憑據** | 必要。 必須是 Cosmos DB 連接字串。<br/>對於 SQL 集合，連接字串採用此格式：`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>`<br/><br/> 對於 MongoDB 集合，請將 **ApiKind=MongoDb** 新增至連接字串：<br/>`AccountEndpoint=<Cosmos DB endpoint url>;AccountKey=<Cosmos DB auth key>;Database=<Cosmos DB database id>;ApiKind=MongoDb`<br/><br/>對於 Gremlin 圖形和 Cassandra 表，請註冊[門控索引子預覽](https://aka.ms/azure-cognitive-search/indexer-preview)以訪問預覽以及如何格式化憑據的資訊。<br/><br/>請避免在端點 URL 中使用連接埠號碼。 如果包含埠號，Azure 認知搜索將無法索引 Azure Cosmos 資料庫。|
| **容器** | 包含下列元素： <br/>**名稱**： 必需。 指定要編制索引的資料庫集合的 ID。<br/>**查詢**：選擇性。 您可以指定查詢，將任意 JSON 文檔拼平到 Azure 認知搜索可以索引的平面架構中。<br/>對於蒙戈DB API、格雷姆林 API 和卡珊多拉 API，不支援查詢。 |
| **資料更改檢測策略** | 建議使用。 請參閱[索引變更的文件](#DataChangeDetectionPolicy)小節。|
|**資料刪除檢測策略** | 選擇性。 請參閱[索引刪除的文件](#DataDeletionDetectionPolicy)小節。|

### <a name="using-queries-to-shape-indexed-data"></a>使用查詢來形塑索引的資料
您可以指定 SQL 查詢來壓平合併巢狀屬性或陣列、投影 JSON 屬性，以及篩選要編製索引的資料。 

> [!WARNING]
> **MongoDB API、****格雷姆林 API**和**Cassandra API**不支援自訂查詢`container.query`：參數必須設置為 null 或省略。 如果您需要使用自訂查詢，請在 [User Voice](https://feedback.azure.com/forums/263029-azure-search) 告訴我們。

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


### <a name="3---create-a-target-search-index"></a>3 - 創建目標搜索索引 

如果尚未創建[目標 Azure 認知搜索索引，請創建目標 Azure 認知搜索索引](/rest/api/searchservice/create-index)。 以下示例創建具有 ID 和說明欄位的索引：

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
> 對於分區集合，預設文件鍵是 Azure Cosmos DB`_rid`的屬性，Azure 認知搜索會自動重命名為`rid`該屬性，因為欄位名稱不能以底線開頭。 此外，Azure Cosmos `_rid` DB 值包含在 Azure 認知搜索鍵中不正確字元。 因此，`_rid` 值採用 Base64 編碼。
> 
> 對於 MongoDB 集合，Azure 認知搜索會自動`_id`將屬性`id`重命名為 。  

### <a name="mapping-between-json-data-types-and-azure-cognitive-search-data-types"></a>JSON 資料類型和 Azure 認知搜索資料類型之間的映射
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

### <a name="4---configure-and-run-the-indexer"></a>4 - 配置並運行索引子

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

有關定義索引子計畫的詳細資訊，請參閱[如何為 Azure 認知搜索計畫索引子](search-howto-schedule-indexers.md)。

## <a name="use-net"></a>使用 .NET

一般可用的 .NET SDK 具有與一般可用的 REST API 完全同位。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="DataChangeDetectionPolicy"></a>

## <a name="indexing-changed-documents"></a>索引已變更的文件

資料變更偵測原則是用來有效識別已變更的資料項目。 目前，唯一受支援的策略是[`HighWaterMarkChangeDetectionPolicy`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.highwatermarkchangedetectionpolicy)使用`_ts`Azure Cosmos DB 提供的（時間戳記）屬性，該屬性指定如下：

    {
        "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName" : "_ts"
    }

為確保索引子效能良好，強烈建議使用此原則。 

如果您使用自訂查詢，請確定查詢有投射 `_ts` 屬性。

<a name="IncrementalProgress"></a>

### <a name="incremental-progress-and-custom-queries"></a>累加進度與自訂查詢

建立索引期間，採累加進度能確保當索引子執行因暫時性失敗或執行時間限制而中斷時，索引子仍能夠在下次執行時從中斷處繼續建立索引，而不需要重新建立整個集合的索引。 這在建立大型集合的索引時尤其重要。 

若要在使用自訂查詢時啟用累加進度，請確保您的查詢是依 `_ts` 欄排序查詢結果。 這允許 Azure 認知搜索用於在發生故障時提供增量進度的定期檢查。   

在某些情況下，即使您的查詢包含`ORDER BY [collection alias]._ts`子句，Azure 認知搜索也可能不會推斷查詢是由 排序的。 `_ts` 您可以告訴 Azure 認知搜索使用`assumeOrderByHighWaterMarkColumn`配置屬性排序結果。 若要指定這項提示，請依下列指示更新您的索引子： 

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

## <a name="next-steps"></a><a name="NextSteps"></a>後續步驟

恭喜！ 您已經瞭解如何使用索引子將 Azure 宇宙 DB 與 Azure 認知搜索集成。

* 若要深入了解 Azure Cosmos DB，請參閱 [Azure Cosmos DB 服務頁面](https://azure.microsoft.com/services/cosmos-db/)。
* 要瞭解有關 Azure 認知搜索的更多資訊，請參閱[搜索服務頁面](https://azure.microsoft.com/services/search/)。
