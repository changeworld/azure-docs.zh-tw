---
title: 搜索 JSON Blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜索 Blob 索引子對文本內容進行爬網 Azure JSON Blob。 索引子可為選取的資料來源 (例如 Azure Blob 儲存體) 將資料擷取自動化。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533961"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>如何在 Azure 認知搜索中使用 Blob 索引子對 JSON Blob 進行索引

本文介紹如何配置 Azure 認知搜索 Blob[索引子](search-indexer-overview.md)，以便從 Azure Blob 存儲中的 JSON 文檔中提取結構化內容，並使其在 Azure 認知搜索中可搜索。 此工作流創建 Azure 認知搜索索引，並將其載入從 JSON blob 中提取的現有文本。 

您可以使用[入口網站](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 為 JSON 內容編製索引。 所有方法的通用方法是 JSON 文檔位於 Azure 存儲帳戶中的 Blob 容器中。 有關從其他非 Azure 平臺推送 JSON 文檔的指導，請參閱[Azure 認知搜索中的資料導入](search-what-is-data-import.md)。

Azure Blob 存儲中的 JSON Blob 通常是單個 JSON 文檔（分析`json`模式為 ）或 JSON 實體的集合。 對於集合，blob 可以具有格式良好的 JSON 元素**陣列**（分析模式為`jsonArray`）。 Blob 也可以由多個由 newline 分隔的單個 JSON 實體組成（分析模式`jsonLines`為 ）。 請求上的**解析模式**參數確定輸出結構。

> [!NOTE]
> 有關從單個 Blob 編制多個搜索文檔索引的詳細資訊，請參閱[一對多索引](search-howto-index-one-to-many-blobs.md)。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

為 JSON 文件索引編製時，最簡單的方法是使用 [Azure 入口網站](https://portal.azure.com/)中的精靈。 藉由剖析 Azure Blob 容器中的中繼資料，[**匯入資料**](search-import-data-portal.md)精靈可以建立預設索引、將來源欄位對應至目標索引欄位，並在單一作業中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

我們建議對 Azure 認知搜索和 Azure 存儲使用相同的區域或位置，以降低延遲並避免頻寬費用。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

[登錄到 Azure 門戶](https://portal.azure.com/)並[創建 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以包含資料。 公共存取層級可以設置為其任何有效值。

在 **"導入資料**嚮導"中，您將需要存儲帳戶名稱、容器名稱和訪問金鑰來檢索資料。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

在搜索服務的"概述"頁中，可以從命令列[啟動嚮導](search-import-data-portal.md)。

   ![門戶中的導入資料命令](./media/search-import-data-portal/import-data-cmd2.png "啟動匯入資料精靈")

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在 [資料來源]**** 頁面中，來源必須是 **Azure Blob 儲存體**，並具有下列規格：

+ [要擷取的資料]**** 應該是 [內容和中繼資料]**。 選擇此選項可讓精靈推斷索引結構描述，並對應要匯入的欄位。
   
+ **解析模式**應設置為*JSON、JSON**陣列*或*JSON線*。 

  [JSON]** 會將每個 Blob 清楚表達為單一搜尋文件，而在搜尋結果中顯示為獨立項目。 

  *JSON 陣列*適用于包含格式良好的 JSON 資料的 Blob - 格式良好的 JSON 對應于物件陣列，或者具有一個屬性，該屬性是物件陣列，您希望每個元素都作為獨立的獨立搜索文檔進行表述。 如果 Blob 很複雜，而且您未選擇 [JSON 陣列]**，則整個 Blob 會擷取為單一文件。

  *JSON 行*適用于由由新行分隔的多個 JSON 實體組成的 Blob，您希望每個實體都作為獨立的獨立搜索文檔進行表述。 如果 Blob 很複雜，並且不選擇*JSON 行*解析模式，則整個 Blob 將作為單個文檔引入。
   
+ [儲存體容器]**** 必須指定儲存體帳戶和容器，或是會解析為容器的連接字串。 您可以在 Blob 服務的入口網站頁面上取得連接字串。

   ![Blob 資料來源定義](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - 跳過嚮導中的"豐富內容"頁面

添加認知技能（或豐富）不是進口要求。 除非需要向索引管道添加[AI 充實](cognitive-search-concept-intro.md)，否則應跳過此步驟。

要跳過此步驟，請按一下頁面底部的藍色按鈕，查看"下一步"和"跳過"。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引]**** 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 嚮導可以基於中繼資料和採樣來源資料生成欄位清單。 

您可以通過按一下屬性列頂部的核取方塊批量選擇屬性。 為應返回到用戶端應用並受全文檢索搜尋處理的每個欄位選擇 **"可檢索**"和 **"可搜索**"。 您會注意到整數不是全文或模糊可搜索（數位是逐字計算的，在篩選器中通常很有用）。

有關詳細資訊，請查看[索引屬性](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib)和[語言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)的說明。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   ![Blob 索引定義](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件在 Azure 認知搜索服務中作為命名資源保存。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子，*索引子*是 Azure 認知搜索中的資源，用於對外部資料源進行搜索。 **導入資料**嚮導的輸出是一個索引子，用於對 JSON 資料來源進行爬網、提取可搜索內容並將其導入 Azure 認知搜索上的索引。

   ![Blob 索引子定義](media/search-howto-index-json/import-wizard-json-indexer.png)

按一下 [確定]**** 來執行精靈並建立所有物件。 編製索引的程序會立即開始。

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果看不到所需的資料，則可能需要在更多欄位上設置更多屬性。 刪除剛剛創建的索引和索引子，然後再次遍進嚮導，修改步驟 5 中索引屬性的選擇。 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

您可以使用 REST API 在 Azure 認知搜索中所有索引子共有的三部分工作流之後，使用 REST API 對 JSON blob 進行索引：創建資料來源、創建索引子、創建索引子。 提交創建索引子請求時，將從 Blob 存儲中提取資料。 此請求完成後，您將具有可查詢的索引。 

您可以在本節末尾查看[REST 示例代碼](#rest-example)，該代碼演示如何創建所有三個物件。 本節還包含有關[JSON 解析模式](#parsing-modes)、[單個 blob、JSON](#parsing-single-blobs)[陣列](#parsing-arrays)和[嵌套陣列的詳細資訊](#nested-json-arrays)。

對於基於代碼的 JSON 索引，請使用[Postman](search-get-started-postman.md)和 REST API 創建以下物件：

+ [指數](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [資料來源](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [索引](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

操作順序要求您按此順序創建和調用物件。 及閘戶工作流不同，代碼方法需要可用的索引來接受通過**創建索引子**要求傳送的 JSON 文檔。

Azure Blob 存儲中的 JSON Blob 通常是單個 JSON 文檔或 JSON"陣列"。 Azure 認知搜索中的 blob 索引子可以分析任一構造，具體取決於您在請求上設置**分析模式**參數的方式。

| JSON 文件 | parsingMode | 描述 | 可用性 |
|--------------|-------------|--------------|--------------|
| 一個 blob 一個 | `json` | 將 JSON blob 當作單一文字區塊來剖析。 每個 JSON Blob 將成為單個 Azure 認知搜索文檔。 | 一般在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中提供。 |
| 一個 blob 多個 | `jsonArray` | 在 blob 中分析 JSON 陣列，其中陣列的每個元素將成為單獨的 Azure 認知搜索文檔。  | 一般在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中提供。 |
| 一個 blob 多個 | `jsonLines` | 分析包含多個 JSON 實體（"陣列"）的 blob，該實體由一條新線分隔，其中每個實體將成為單獨的 Azure 認知搜索文檔。 | 一般在[REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) API 和[.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK 中提供。 |

### <a name="1---assemble-inputs-for-the-request"></a>1 - 為請求組裝輸入

對於每個請求，必須提供 Azure 認知搜索的服務名稱和管理金鑰（在 POST 標頭中）以及 Blob 存儲的存儲帳戶名稱和金鑰。 您可以使用[Postman](search-get-started-postman.md)向 Azure 認知搜索發送 HTTP 要求。

將以下四個值複製到記事本中，以便將它們粘貼到請求中：

+ Azure 認知搜索服務名稱
+ Azure 認知搜索管理金鑰
+ Azure 儲存體帳戶名稱
+ Azure 存儲帳戶金鑰

您可以在門戶中找到以下值：

1. 在 Azure 認知搜索的門戶頁中，從"概述"頁複製搜索服務 URL。

2. 在左側功能窗格中，按一下 **"鍵"，** 然後複製主鍵或輔助鍵（它們等效）。

3. 切換到存儲帳戶的門戶頁面。 在左側功能窗格中，**在"設置"** 下，按一下 **"便捷鍵**"。 此頁同時提供帳戶名稱和金鑰。 複製存儲帳戶名稱和記事本的金鑰之一。

### <a name="2---create-a-data-source"></a>2 - 創建資料來源

此步驟提供索引子使用的資料來源連接資訊。 資料來源是 Azure 認知搜索中的命名物件，它保留連接資訊。 資料來源類型 ，`azureblob`確定索引子調用哪些資料提取行為。 

將有效值替換為服務名稱、管理金鑰、存儲帳戶和帳戶金鑰預留位置。

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - 創建目標搜索索引 

索引子要搭配索引結構描述。 如果您使用 API (而不是入口網站)，請事先準備好索引，以便在索引子作業中指定它。

索引在 Azure 認知搜索中存儲可搜索的內容。 若要建立索引，請提供結構描述來指定文件中的欄位、屬性和其他可形塑搜尋體驗的建構。 如果您建立的索引具有與來源相同的欄位名稱和資料類型，索引子便會比對來源和目的地的欄位，讓您不必明確地對應欄位。

下列範例說明[建立索引](https://docs.microsoft.com/rest/api/searchservice/create-index)要求。 索引會有可搜尋的 `content` 欄位，以供儲存從 Blob 擷取到的文字︰   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - 配置並運行索引子

與索引和資料來源一樣，索引子也是您在 Azure 認知搜索服務上創建和重用的命名物件。 創建索引子的完全指定請求如下所示：

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

索引子配置位於請求正文中。 它需要 Azure 認知搜索中已經存在的資料來源和空目標索引。 

計畫和參數是可選的。 如果省略它們，索引子將立即運行，用作`json`分析模式。

此特定索引子不包括欄位映射。 在索引子定義中，如果源 JSON 文檔的屬性與目標搜索索引的欄位匹配，則可以排除**欄位映射**。 


### <a name="rest-example"></a>REST 示例

本節是用於創建物件的所有請求的概述。 有關元件部件的討論，請參閱本文中的前幾節。

### <a name="data-source-request"></a>資料來源請求

所有索引子都需要一個資料來源物件，該物件提供與現有資料的連接資訊。 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>索引請求

所有索引子都需要接收資料的目標索引。 請求的正文定義索引架構（由欄位組成），用於支援可搜索索引中的所需行為。 運行索引子時，此索引應為空。 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>索引子請求

此請求顯示完全指定的索引子。 它包括欄位映射，在前面的示例中省略了這些映射。 回想一下，"計畫"、"參數"和"欄位映射"是可選的，只要有可用的預設值。 省略"計畫"會導致索引子立即運行。 省略"分析模式"會導致索引使用"json"預設值。

在 Azure 認知搜索上創建索引子會觸發資料導入。 它立即運行，然後按計劃運行（如果您提供了）。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>使用 .NET SDK

.NET SDK 與 REST API 具有完全同位。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>分析模式

JSON blob 可以假定多個表單。 JSON 索引子上的**解析模式**參數確定如何在 Azure 認知搜索索引中解析 JSON blob 內容並構造：

| parsingMode | 描述 |
|-------------|-------------|
| `json`  | 將每個 Blob 索引為單個文檔。 這是預設值。 |
| `jsonArray` | 如果 blob 由 JSON 陣列組成，並且需要陣列的每個元素在 Azure 認知搜索中成為單獨的文檔，請選擇此模式。 |
|`jsonLines` | 如果 blob 由多個 JSON 實體組成，由新行分隔，並且需要每個實體成為 Azure 認知搜索中的單獨文檔，請選擇此模式。 |

您可以將文件視為搜尋結果中的單一項目。 如果希望陣列中的每個元素作為獨立項顯示在搜尋結果中，請使用 或`jsonArray``jsonLines`選項。

在索引子定義中，您可以選擇性地使用[欄位對應](search-indexer-field-mappings.md)，以選擇用來填入目標搜尋索引的來源 JSON 文件屬性。 對於`jsonArray`分析模式，如果陣列作為較低級別的屬性存在，則可以設置文檔根，指示陣列在 Blob 中的放置位置。

> [!IMPORTANT]
> 使用 或`json``jsonArray``jsonLines`解析模式時，Azure 認知搜索假定資料來源中的所有 Blob 都包含 JSON。 如果您需要支援在相同的資料來源中混用 JSON 和非 JSON Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上讓我們知道。


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>解析單個 JSON blob

預設情況下[，Azure 認知搜索 blob 索引子](search-howto-indexing-azure-blob-storage.md)將 JSON blob 解析為單個文字區塊。 通常，您會想要保留 JSON 文件的結構。 例如，假設您在 Azure Blob 儲存體中有下列 JSON 文件：

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

blob 索引子將 JSON 文檔解析為單個 Azure 認知搜索文檔。 索引子會比對來源中的 "text"、"datePublished"、"tags"，找出與目標索引欄位具有相同名稱和類型的索引，然後載入索引。

如前所述，不一定要使用欄位對應。 指定索引的 "text"、"datePublished"、"tags" 欄位，blob 索引子可以推斷正確的對應，故要求中不需要欄位對應。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>解析 JSON 陣列

或者，您可以使用 JSON 陣列選項。 當 blob 包含*格式良好的 JSON 物件的陣列*，並且希望每個元素成為單獨的 Azure 認知搜索文檔時，此選項非常有用。 例如，給定以下 JSON blob，可以使用三個單獨的文檔填充 Azure 認知搜索索引，每個文檔都包含"id"和"文本"欄位。  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

若為 JSON 陣列，索引子定義看起來應該像下列範例。 請注意，parsingMode 參數會指定 `jsonArray` 剖析器。 指定正確的解析器和具有正確的資料輸入是索引 JSON blob 的唯兩個特定于陣列的要求。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

同樣地，請注意您可以省略欄位對應。 採用 "id" 和 "text" 欄位名稱相同的索引，Blob 索引子便可以推斷正確的對應，而不需要明確的欄位對應清單。

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>解析嵌套陣列
對於具有嵌套元素的 JSON 陣列，可以指定`documentRoot`指示多級結構。 例如，如果您的 Blob 看起來像這樣︰

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

使用這個設定來索引 `level2` 屬性中包含的陣列：

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>解析由新線分隔的 blob

如果 blob 包含多個由換行分隔的 JSON 實體，並且希望每個元素成為單獨的 Azure 認知搜索文檔，則可以選擇 JSON 行選項。 例如，給定以下 Blob（其中有三個不同的 JSON 實體），可以使用三個單獨的文檔填充 Azure 認知搜索索引，每個文檔都包含"id"和"文本"欄位。

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

對於 JSON 行，索引子定義應類似于以下示例。 請注意，parsingMode 參數會指定 `jsonLines` 剖析器。 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

同樣，請注意，可以省略欄位映射，類似于`jsonArray`分析模式。

## <a name="add-field-mappings"></a>添加欄位映射

當來源和目標的欄位不能完全對齊時，您可以在要求主體中定義欄位對應區段，明確指定欄位對欄位的關聯。

目前，Azure 認知搜索不能直接索引任意 JSON 文檔，因為它僅支援原始資料類型、字串陣列和 GeoJSON 點。 不過，您可以使用 **欄位對應** 挑選 JSON 文件的幾個部分，並將它們「上移」到搜尋文件的最上層欄位。 要瞭解欄位映射基礎知識，請參閱 Azure[認知搜索索引子中的欄位映射](search-indexer-field-mappings.md)。

回到我們的範例 JSON 文件︰

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

假設搜尋索引有下列欄位︰`Edm.String` 類型的 `text`、`Edm.DateTimeOffset` 類型的 `date`、`Collection(Edm.String)` 類型的 `tags`。 請注意來源中的 "datePublished" 與索引中的 `date` 欄位之間的差異。 若要將 JSON 對應到所需形狀，請使用下列欄位對應︰

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

對應中的來源欄位名稱是使用 [JSON 指標](https://tools.ietf.org/html/rfc6901) 標記法指定。 以正斜線開始參考 JSON 文件的根目錄，然後使用正斜線分隔的路徑挑選所需的屬性 (使用任意層級的巢狀結構)。

您也可以使用以零為起始的索引來參考個別陣列元素。 比方說，若要從上述範例挑選 "tags" 陣列的第一個元素，請如下所示使用欄位對應︰

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> 如果欄位對應路徑中的來源欄位名稱參考在 JSON 中不存在的屬性，則會略過該對應且不會產生錯誤。 這麼做是為了讓我們可支援具有不同結構描述 (這是常見的使用案例) 的文件。 因為沒有任何驗證，您必須小心避免在欄位對應規格中出現錯字。
>
>

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜尋中的索引子](search-indexer-overview.md)
+ [使用 Azure 認知搜索索引 Azure Blob 存儲](search-howto-index-json-blobs.md)
+ [使用 Azure 認知搜索 blob 索引子索引 CSV blob](search-howto-index-csv-blobs.md)
+ [教程：從 Azure Blob 存儲中搜索半結構化資料](search-semi-structured-data.md)
