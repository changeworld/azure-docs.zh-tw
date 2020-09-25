---
title: 搜尋 Azure SQL 資料
titleSuffix: Azure Cognitive Search
description: 使用索引子從 Azure SQL Database 或 SQL 受控執行個體匯入資料，以在 Azure 認知搜尋中進行全文檢索搜尋。 本文涵蓋連線、索引子設定以及資料擷取。
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 631f5afbac4337cd0852f46ac4a336107f042397
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331636"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>使用 Azure 認知搜尋索引子連接至 Azure SQL 內容並為其編制索引

在查詢 [Azure 認知搜尋索引](search-what-is-an-index.md)之前，您必須先填入您的資料。 如果資料位於 Azure SQL Database 或 SQL 受控執行個體中，則適用于 Azure SQL Database (的 **Azure 認知搜尋索引子** 或適用于簡短) 的 **Azure SQL 索引子** ，可將編制索引程式自動化，這表示撰寫較少的程式碼，以及要在意的基礎結構。

本文涵蓋使用 [索引子](search-indexer-overview.md)的機制，但也描述 AZURE SQL DATABASE 或 SQL 受控執行個體所提供的功能 (例如，整合式變更追蹤) 。 

除了 Azure SQL Database 和 SQL 受控執行個體之外，Azure 認知搜尋還提供 [Azure Cosmos DB](search-howto-index-cosmosdb.md)、 [azure Blob 儲存體](search-howto-indexing-azure-blob-storage.md)和 [azure 資料表儲存體](search-howto-indexing-azure-tables.md)的索引子。 若要要求支援其他資料來源，請在 [Azure 認知搜尋意見反應論壇](https://feedback.azure.com/forums/263029-azure-search/)上提供您的意見反應。

## <a name="indexers-and-data-sources"></a>索引子和資料來源

**資料來源**能指定要編製索引的資料、存取資料所需的認證，以及能有效識別資料變更 (新增、修改或刪除的資料列) 的原則。 資料來源會被定義為獨立的資源，因此可供多個索引子使用。

**索引子**是一種用來將單一資料來源連線至目標搜尋索引的資源。 索引子的使用方式如下：

* 執行資料的一次性複製以填入索引。
* 依照排程使用資料來源中的變更來更新索引。
* 視需要執行隨選作業更新索引。

單一索引子只能取用一個資料表或檢視，但如果您想要填入多個搜尋索引，則可建立多個索引子。 如需概念的詳細資訊，請參閱[索引子作業：一般工作流程](/rest/api/searchservice/Indexer-operations#typical-workflow)。

您可以使用下列方式安裝及設定 Azure SQL 索引子︰

* [Azure 入口網站](https://portal.azure.com)中的匯入資料精靈
* Azure 認知搜尋 [.NET SDK](/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Azure 認知搜尋 [REST API](/rest/api/searchservice/indexer-operations)

在本文中，我們將使用 REST API 來建立**索引子**與**資料來源**。

## <a name="when-to-use-azure-sql-indexer"></a>使用 Azure SQL 索引子的時機
使用 Azure SQL 索引子適當與否，取決於一些資料相關因素。 如果您的資料符合下列需求，就可以使用 Azure SQL 索引子。

| 準則 | 詳細資料 |
|----------|---------|
| 資料來自單一資料表或檢視 | 如果資料散布在多個資料表中，您可以建立資料的單一檢視。 但是，如果您使用檢視，就不能使用 SQL Server 整合變更偵測，使用累加的變更來重新整理索引。 如需詳細資訊，請參閱下列的[擷取變更及刪除的資料列](#CaptureChangedRows)。 |
| 資料類型是可相容的 | Azure 認知搜尋索引中支援大部分但並非所有的 SQL 類型。 如需清單，請參閱[對應資料類型](#TypeMapping)。 |
| 不需要即時同步處理資料 | 索引子最多可以每隔五分鐘重新編製資料表的索引。 若您經常變更資料，且變更需要在幾秒或幾分鐘內反映到索引中，我們建議您使用 [REST API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) 或 [.NET SDK](./search-get-started-dotnet.md)，直接推送更新的資料列。 |
| 累加式編製索引是可行的 | 如果您有大型資料集並計畫依排程執行索引子，Azure 認知搜尋必須能夠有效率地識別新的、已變更或已刪除的資料列。 如果您會視需要 (而非排程) 編製索引，或要編製索引的資料列少於 100,000 個，則僅允許進行非累加的編製索引。 如需詳細資訊，請參閱下列的[擷取變更及刪除的資料列](#CaptureChangedRows)。 |

> [!NOTE] 
> Azure 認知搜尋僅支援 SQL Server 驗證。 如果您需要 Azure Active Directory 密碼驗證的支援，請針對這個 [UserVoice 建議](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica)進行投票。

## <a name="create-an-azure-sql-indexer"></a>建立 Azure SQL 索引子

1. 建立資料來源：

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   連接字串可以遵循下列其中一種格式：
    1. 您可以從 [Azure 入口網站](https://portal.azure.com)取得連接字串；使用 `ADO.NET connection string` 選項。
    1. 未包含帳戶金鑰的受控識別連接字串，格式如下： `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` 。 若要使用此連接字串，請遵循 [使用受控識別來設定 Azure SQL Database 的索引子連接](search-howto-managed-identities-sql.md)的指示。

2. 建立目標 Azure 認知搜尋索引（如果您還沒有的話）。 您可以使用[入口網站](https://portal.azure.com)或[建立索引 API](/rest/api/searchservice/Create-Index) 來建立索引。 請確認目標索引的架構與來源資料表的架構相容，請參閱 [SQL 和 Azure 認知搜尋資料類型之間的對應](#TypeMapping)。

3. 利用命名及參考資料來源和目標索引來建立索引子：

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

以這種方式建立索引子不需依照排程。 索引子一旦建立好就會自動執行。 您可以在任何時候使用 **執行索引子** 要求再執行一次：

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

您可以自訂數個層面的索引子行為，例如，批次處理大小，以及在索引子執行失敗前可略過多少份文件。 如需詳細資訊，請參閱[建立索引子 API](/rest/api/searchservice/Create-Indexer)。

在您可能需要允許 Azure 服務以連線您的資料庫的時候。 如需瞭解如何執行連線的指示，請參閱 [從 Azure連線](../azure-sql/database/firewall-configure.md) 。

若要監視索引子狀態及執行歷程紀錄 (項目索引編製數量、失敗等)，請使用 **索引子狀態** 要求：

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

回應看起來應該如下所示：

```
    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }
```

執行歷程記錄包含多達 50 個最近完成的執行，以倒序的方式進行排序 (因此最新的執行會排在回應中的第一位)。
有關回應的的其他資訊可在 [取得索引子狀態](/rest/api/searchservice/get-indexer-status)

## <a name="run-indexers-on-a-schedule"></a>依照排程執行索引子
您也可以排列索引子，依照排程定期執行。 若要執行此工作，請在建立或更新索引子時新增**排程**屬性。 下方範例顯示以 PUT 要求更新索引子：

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

**間隔** 參數是必需的。 間隔指兩個連續索引子開始執行的時間。 允許的最小間隔為 5 分鐘；最長間隔為一天。 其必須格式化為 XSD "dayTimeDuration" 值 ( [ISO 8601 持續時間](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 值的受限子集)。 間隔的模式為： `P(nD)(T(nH)(nM))`。 範例：`PT15M` 代表每隔 15 分鐘，`PT2H` 代表每隔 2 個小時。

如需有關定義索引子排程的詳細資訊，請參閱[如何排程 Azure 認知搜尋的索引子](search-howto-schedule-indexers.md)。

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>擷取新增、變更和刪除的資料列

Azure 認知搜尋使用累加 **式索引編制** ，以避免每次索引子執行時都必須重新編制整個資料表或視圖的索引。 Azure 認知搜尋提供兩個變更偵測原則來支援累加式編制索引。 

### <a name="sql-integrated-change-tracking-policy"></a>SQL 整合變更追蹤原則
如果您的 SQL 資料庫支援 [變更追蹤](/sql/relational-databases/track-changes/about-change-tracking-sql-server)，我們建議使用 **SQL 整合式變更追蹤原則**。 這是最有效率的原則。 此外，它還可讓 Azure 認知搜尋識別已刪除的資料列，而不需要在資料表中新增明確的「虛刪除」資料行。

#### <a name="requirements"></a>需求 

+ 資料庫版本需求：
  * SQL Server 2012 SP3 和更新版本 (若您在 Azure VM 上使用 SQL Server)。
  * Azure SQL Database 或 SQL 受控執行個體。
+ 僅限資料表 (沒有檢視)。 
+ 在資料庫上，針對資料表[啟用變更追蹤](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)。 
+ 資料表上沒有複合主索引鍵 (主索引鍵包含一個以上的資料行)。  

#### <a name="usage"></a>使用量

若要使用此原則，請以下列方式建立或更新您的資料來源：

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

使用 SQL 整合式變更追蹤原則時，請不要指定個別的資料刪除偵測原則，因為本原則已內建支援刪除資料列的識別。 不過，針對要「自動」偵測的刪除，您搜尋索引中的文件索引鍵必須與 SQL 資料表中的主索引鍵一樣。 

> [!NOTE]  
> 使用 [TRUNCATE TABLE](/sql/t-sql/statements/truncate-table-transact-sql) 來移除 SQL 資料表中的大量資料列時，必須[重設](/rest/api/searchservice/reset-indexer)索引子，才能重設變更追蹤狀態，以挑選資料列刪除。

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>上限標準變更偵測原則

這個變更偵測原則依賴在上次更新資料列時擷取版本或時間的「上限標準」資料行。 若您使用檢視，就必須使用上限標準原則。 上限標準資料行必須符合下列需求。

#### <a name="requirements"></a>需求 

* 所有插入都有指定資料行的值。
* 所有項目更新變更資料行的值。
* 每次插入或更新都會增加此資料行的值。
* 具有下列 WHERE 和 ORDER BY 子句的查詢可以有效率地執行︰`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> 我們強烈建議針對上限標記資料行使用 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 資料類型。 如果使用其他任何資料類型，就無法保證變更追蹤會擷取與索引子查詢同時執行之交易中發生的所有變更。 在具備唯讀複本的設定中使用 **rowversion** 時，您必須指向主要複本上的索引子。 只有主要複本可用於資料同步處理案例。

#### <a name="usage"></a>使用量

若要使用高標原則，請以下列方式建立或更新您的資料來源：

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }
```

> [!WARNING]
> 如果來源資料表沒有最高水位線資料行的索引，SQL 索引子所使用的查詢可能會超時。特別是， `ORDER BY [High Water Mark Column]` 當資料表包含許多資料列時，子句需要索引才能有效率地執行。
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

如果您使用 [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) 資料類型作為高水位線資料行，請考慮使用 `convertHighWaterMarkToRowVersion` 索引子設定。 `convertHighWaterMarkToRowVersion` 會執行兩個作業︰

* 針對索引子 SQL 查詢中的上限標準資料行，使用 rowversion 資料類型。 使用正確的資料類型可改善索引子查詢效能。
* 在索引子查詢執行之前，從 rowversion 值減去1。 具有1到多個聯結的視圖可能會有重複 rowversion 值的資料列。 減去1可確保索引子查詢不會錯過這些資料列。

若要啟用這項功能，請使用下列設定來建立或更新索引子：

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

如果您遇到逾時錯誤，您可以使用 `queryTimeout` 索引子組態設定，將查詢逾時設定為高於預設逾時 (5 分鐘) 的值。 例如，若要將逾時設定為 10 分鐘，請使用下列組態建立或更新索引子︰

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

您也可以停用 `ORDER BY [High Water Mark Column]` 子句。 不過不建議您這麼做，因為如果索引子的執行因發生錯誤而中斷，索引子必須在稍後執行時重新處理所有資料列，即便索引子在中斷發生當時已幾乎處理好所有資料列，也是如此。 若要停用 `ORDER BY` 子句，請在索引子定義中使用 `disableOrderByHighWaterMarkColumn` 設定︰  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>虛刪除資料行刪除偵測原則
當從來源資料表中刪除資料列時，您應該也想刪除在搜尋索引內的那些資料列。 若您使用 SQL 整合變更追蹤原則，就能幫您處理這件工作。 但是，上限標準變更追蹤原則無法幫助您刪除資料列。 怎麼辦？

如果資料列已實際從資料表中移除，則 Azure 認知搜尋無法推斷已不存在的記錄是否存在。  不過，您可以使用「虛刪除」技術，以邏輯方式刪除資料列，而不需從資料表加以移除。 在資料表或檢視中新增資料行，並使用該資料行將資料列標記為已刪除。

當您使用虛刪除技術時，可以在建立或升級資料來源時，按照下列方式指定虛刪除原則：

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** 必須為一個字串 (使用代表實際值的字串)。 例如，如果您有整數資料行，且其中的已刪除資料列標記為值 1，請使用 `"1"`。 如果您有 BIT 資料行，且其中的已刪除資料列標記為布林值 true，則請使用字串常值 `True` 或 `true`，大小寫並不重要。

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>SQL 和 Azure 認知搜尋資料類型之間的對應
| SQL 資料類型 | 允許的目標索引欄位類型 | 注意 |
| --- | --- | --- |
| bit |Edm.Boolean、Edm.String | |
| int、smallint、tinyint |Edm.Int32、Edm.Int64、Edm.String | |
| BIGINT |Edm.Int64、Edm.String | |
| real、float |Edm.Double、Edm.String | |
| smallmoney、money 十進位數值 |Edm.String |Azure 認知搜尋不支援將十進位類型轉換成 Edm，因為這樣會遺失精確度 |
| char、nchar、varchar、nvarchar |Edm.String<br/>Collection(Edm.String) |如果 SQL 字串代表下列 JSON 字串陣列，該字串可用來填入 Collection(Edm.String) 欄位：`["red", "white", "blue"]` |
| smalldatetime、datetime、datetime2、date、datetimeoffset |Edm.DateTimeOffset、Edm.String | |
| uniqueidentifer |Edm.String | |
| geography |Edm.GeographyPoint |僅支援使用 SRID 4326 (預設) 之 POINT 類型的 geography 執行個體。 |
| rowversion |N/A |資料列版本的資料行無法儲存在搜尋索引中，但可用於追蹤變更。 |
| time、timespan、binary、varbinary、image、xml、geometry、CLR 類型 |N/A |不支援 |

## <a name="configuration-settings"></a>組態設定
SQL 索引子公開數個組態設定︰

| 設定 | 資料類型 | 目的 | 預設值 |
| --- | --- | --- | --- |
| queryTimeout |字串 |設定 SQL 查詢執行的逾時 |5 分鐘 ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |導致上限標準原則所使用的 SQL 查詢省略 ORDER BY 子句。 請參閱[上限標準原則](#HighWaterMarkPolicy) |false |

這些設定會用於索引子定義中的 `parameters.configuration` 物件。 例如，若要將查詢逾時設定為 10 分鐘，請使用下列組態建立或更新索引子︰

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>常見問題集

**問：我可以使用 Azure SQL 索引子搭配在 Azure 中 IaaS Vm 上執行的 SQL 資料庫嗎？**

可以。 不過，您需要允許搜尋服務連接到資料庫。 如需詳細資訊，請參閱 [在 AZURE VM 上設定從 Azure 認知搜尋索引子到 SQL Server 的連接](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)。

**問：我可以使用 Azure SQL 索引子搭配在內部部署執行的 SQL 資料庫嗎？**

無法直接進行。 我們不建議或不支援直接連線，因為這樣做需要您開啟資料庫以接收網際網路流量。 客戶需要使用像是 Azure Data Factory 的橋接器技術，才能成功執行此案例。 如需詳細資訊，請參閱 [使用 Azure Data Factory 將資料推送至 Azure 認知搜尋索引](../data-factory/v1/data-factory-azure-search-connector.md)。

**問：我可以搭配使用 Azure SQL 索引子與在 Azure IaaS 中執行 SQL Server 以外的資料庫嗎？**

否。 我們不支援這類案例，因為我們尚未使用 SQL Server 以外的資料庫來測試索引子。  

**問：我可以建立多個依排程執行的索引子嗎？**

可以。 但是一次只能在一個節點上執行一個索引子。 如果您需要多個同時執行的索引子，請考慮將搜尋服務調整大於一個搜尋單位。

**問：執行索引子是否會影響我的查詢工作負載？**

可以。 索引子會在您搜尋服務中的其中一個節點執行，且節點上的資源會在索引及服務查詢流量和其他 API 要求之間共用。 如果您執行大量的索引編制和查詢工作負載，而且遇到高比率的503錯誤或增加回應時間，請考慮 [相應增加您的搜尋服務](search-capacity-planning.md)。

**問：是否可以在[容錯移轉叢集](../azure-sql/database/auto-failover-group-overview.md)中使用次要複本作為資料來源？**

要看情況而定。 針對完整編製索引的資料表或檢視，您可以使用次要複本。 

針對累加式編制索引，Azure 認知搜尋支援兩個變更偵測原則： SQL 整合式變更追蹤和高水位標記。

在唯讀複本上，SQL Database 不支援整合式變更追蹤。 因此，您必須使用上限標準原則。 

我們的標準建議是，針對上限標記資料行使用 rowversion 資料類型。 不過，使用 rowversion 相依于 `MIN_ACTIVE_ROWVERSION` 唯讀複本上不支援的函數。 因此，如果您使用 rowversion，就必須將索引子指向主要複本。

如果您嘗試在唯讀複本上使用 rowversion，將會看到下列錯誤： 

在次要 (唯讀) 可用性複本上，不支援使用 rowversion 資料行進行變更追蹤。 請更新資料來源，並指定與主要可用性複本的連接。目前的資料庫 ' 可更新性 ' 屬性為 ' READ_ONLY '。

**問：我是否可以針對上限標準變更追蹤使用替代的非 rowversion 資料行？**

我們不建議這樣做。 僅允許使用 **rowversion** 進行可靠的資料同步處理。 不過，根據您的應用程式邏輯而定，如果符合下列情況，則它可能是安全：

+ 您可以確定在索引子執行時，資料表上沒有任何未處理的交易 (例如，所有資料表更新都是以批次的方式進行，而且會設定 Azure 認知搜尋索引子排程，以避免與資料表更新排程) 重迭。  

+ 您會定期進行完整的重新編製索引，以挑選出任何遺失的資料列。