---
title: 搜尋 JSON blob
titleSuffix: Azure Cognitive Search
description: 使用 Azure 認知搜尋 Blob 索引子，對文字內容的 Azure JSON blob 進行編目。 索引子可為選取的資料來源 (例如 Azure Blob 儲存體) 將資料擷取自動化。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 1fc6c7086917f2bcd6e4991d2dac37ea24cbfa83
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116376"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中使用 Blob 索引子編制 JSON blob 的索引

本文說明如何設定 Azure 認知搜尋 blob [索引子](search-indexer-overview.md) ，以從 Azure blob 儲存體中的 JSON 檔解壓縮結構化內容，並讓它可在 Azure 認知搜尋中進行搜尋。 此工作流程會建立 Azure 認知搜尋的索引，並使用從 JSON blob 解壓縮的現有文字載入它。 

您可以使用[入口網站](#json-indexer-portal)、[REST API](#json-indexer-rest) 或 [.NET SDK](#json-indexer-dotnet) 為 JSON 內容編製索引。 所有方法共通的是，JSON 檔位於 Azure 儲存體帳戶的 blob 容器中。 如需從其他非 Azure 平臺推送 JSON 檔的指引，請參閱 [Azure 認知搜尋中的資料匯入](search-what-is-data-import.md)。

Azure Blob 儲存體中的 JSON blob 通常是單一 JSON 檔， (剖析模式 `json`) 或 json 實體集合。 針對集合，blob 可以有格式正確 **的 JSON 元素， (** 剖析模式是 `jsonArray`) 。 Blob 也可以由多個以分行符號分隔的個別 JSON 實體所組成， (剖析模式是 `jsonLines`) 。 要求上的 **parsingMode** 參數會決定輸出結構。

> [!NOTE]
> 如需有關從單一 blob 編制多個搜尋檔索引的詳細資訊，請參閱 [一對多索引](search-howto-index-one-to-many-blobs.md)。

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>使用入口網站

為 JSON 文件索引編製時，最簡單的方法是使用 [Azure 入口網站](https://portal.azure.com/)中的精靈。 藉由剖析 Azure Blob 容器中的中繼資料，[**匯入資料**](search-import-data-portal.md)精靈可以建立預設索引、將來源欄位對應至目標索引欄位，並在單一作業中載入索引。 根據來源資料的大小和複雜度，只需要幾分鐘的時間，您就可以擁有正常運作的全文檢索搜尋索引。

針對 Azure 認知搜尋和 Azure 儲存體，建議使用相同的區域或位置，以降低延遲，並避免頻寬費用。

### <a name="1---prepare-source-data"></a>1 - 準備來源資料

登[入 Azure 入口網站](https://portal.azure.com/)並[建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)以包含您的資料。 公用存取層級可以設定為任何有效的值。

您將需要儲存體帳戶名稱、容器名稱和存取金鑰，才能在 [匯 **入資料** ] 嚮導中取出您的資料。

### <a name="2---start-import-data-wizard"></a>2 - 啟動匯入資料精靈

在搜尋服務的 [總覽] 頁面中，您可以從命令列 [啟動精靈](search-import-data-portal.md) 。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd2.png" alt-text="入口網站中的匯入資料命令" border="false":::

### <a name="3---set-the-data-source"></a>3 - 設定資料來源

在 [資料來源] 頁面中，來源必須是 **Azure Blob 儲存體**，並具有下列規格：

+ [要擷取的資料] 應該是 [內容和中繼資料]。 選擇此選項可讓精靈推斷索引結構描述，並對應要匯入的欄位。
   
+ **剖析模式** 應設定為 *json*、 *json 陣列* 或 *json 行*。 

  [JSON] 會將每個 Blob 清楚表達為單一搜尋文件，而在搜尋結果中顯示為獨立項目。 

  *Json 陣列* 適用于包含格式正確的 json 資料的 blob-格式正確的 json 會對應至物件的陣列，或具有屬性（property），而您想要將每個專案明確地表示為獨立的獨立搜尋檔。 如果 Blob 很複雜，而且您未選擇 [JSON 陣列]，則整個 Blob 會擷取為單一文件。

  *Json 行* 適用于由多個 JSON 實體（以新行分隔）所組成的 blob，您可以在其中將每個實體都表達為獨立的獨立搜尋檔。 如果 blob 很複雜，而且您沒有選擇 *JSON 程式碼* 剖析模式，則會將整個 blob 內嵌為單一檔。
   
+ [儲存體容器] 必須指定儲存體帳戶和容器，或是會解析為容器的連接字串。 您可以在 Blob 服務的入口網站頁面上取得連接字串。

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-data-source.png" alt-text="Blob 資料來源定義" border="false":::

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4-略過 wizard 中的 [豐富內容] 頁面

新增認知技能 (或擴充) 不是匯入需求。 除非您有特定需求 [將 AI 擴充新增](cognitive-search-concept-intro.md) 至索引管線，否則您應該略過此步驟。

若要略過此步驟，請按一下頁面底部的藍色按鈕，以尋找「下一步」和「略過」。

### <a name="5---set-index-attributes"></a>5 - 設定索引屬性

在 [索引] 頁面上，您應該會看到欄位清單，其中有資料類型以及一系列用於設定索引屬性的核取方塊。 Wizard 可以根據中繼資料和取樣來源資料來產生欄位清單。 

您可以按一下屬性資料行頂端的核取方塊，以大量選取屬性。 針對每個應該傳回給用戶端應用 **程式的欄位**，選擇 [可取得] 和 [可搜尋 **]，並** 受限於全文檢索搜尋處理。 您將會注意到，整數不是全文檢索或模糊可搜尋 (數位會逐字進行評估，而且在篩選) 中通常很有用。

如需詳細資訊，請參閱 [索引屬性](/rest/api/searchservice/create-index#bkmk_indexAttrib) 和 [語言分析器](/rest/api/searchservice/language-support) 的描述。 

請花一點時間檢閱您的選擇。 一旦執行精靈，就會建立實體的資料結構，而且除非您捨棄並重新建立所有物件，否則將無法編輯這些欄位。

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-index.png" alt-text="Blob 索引定義" border="false":::

### <a name="6---create-indexer"></a>6 - 建立索引子

若完整指定，精靈會在搜尋服務中建立三個不同的物件。 資料來源物件和索引物件會儲存為 Azure 認知搜尋服務中的命名資源。 最後一個步驟則會建立索引子物件。 為索引子命名可讓索引子以獨立資源的形式存在，索引子可以獨立地排程及管理，而不會受制於索引和資料來源物件 (這兩個物件會在相同的精靈序列中建立)。

如果您不熟悉索引子， *索引子* 是 Azure 認知搜尋中的資源，可將外部資料源編目以取得可搜尋的內容。 匯 **入資料** wizard 的輸出是一個索引子，可編目您的 JSON 資料來源、解壓縮可搜尋的內容，並將它匯入 Azure 認知搜尋的索引中。

   :::image type="content" source="media/search-howto-index-json/import-wizard-json-indexer.png" alt-text="Blob 索引子定義" border="false":::

按一下 [確定] 來執行精靈並建立所有物件。 編製索引的程序會立即開始。

您可以在入口網站的頁面中監視資料匯入過程。 進度通知會指出編製索引的狀態，以及所上傳的文件數量。 

編製索引的程序完成後，您可以使用[搜尋總管](search-explorer.md)來查詢索引。

> [!NOTE]
> 如果您沒有看到預期的資料，您可能需要在更多欄位上設定更多屬性。 刪除您剛才建立的索引和索引子，然後再次逐步執行嚮導，以修改您在步驟5中選取的索引屬性。 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>使用 REST API

您可以使用 REST API 來編制 JSON blob 的索引，並遵循 Azure 認知搜尋中所有索引子通用的三部分工作流程：建立資料來源、建立索引、建立索引子。 當您提交建立索引子要求時，就會從 blob 儲存體提取資料。 完成此要求之後，您將會有可查詢的索引。 

您可以在本節結尾處查看 [REST 範例程式碼](#rest-example) ，說明如何建立這三個物件。 本節也包含 [json 剖析模式](#parsing-modes)、 [單一 blob](#parsing-single-blobs)、 [JSON 陣列](#parsing-arrays)和 [嵌套陣列](#nested-json-arrays)的詳細資料。

若要進行以程式碼為基礎的 JSON 索引，請使用 [Postman](search-get-started-rest.md) 或 [Visual Studio Code](search-get-started-vs-code.md) ，並使用 REST API 來建立這些物件：

+ [index](/rest/api/searchservice/create-index)
+ [資料來源](/rest/api/searchservice/create-data-source)
+ [索引](/rest/api/searchservice/create-indexer)

作業的順序需要您依此順序建立和呼叫物件。 相較于入口網站工作流程，程式碼方法需要可用的索引，以接受透過 **建立索引子** 要求所傳送的 JSON 檔。

Azure Blob 儲存體中的 JSON blob 通常是單一 JSON 檔或 JSON 「陣列」。 Azure 認知搜尋中的 blob 索引子可以剖析其中一個結構，視您在要求上設定 **parsingMode** 參數的方式而定。

| JSON 文件 | parsingMode | 描述 | 可用性 |
|--------------|-------------|--------------|--------------|
| 一個 blob 一個 | `json` | 將 JSON blob 當作單一文字區塊來剖析。 每個 JSON blob 都會變成單一 Azure 認知搜尋檔。 | [REST](/rest/api/searchservice/indexer-operations) API 和[.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK 均已正式推出。 |
| 一個 blob 多個 | `jsonArray` | 剖析 blob 中的 JSON 陣列，其中陣列的每個元素都會變成個別 Azure 認知搜尋檔。  | [REST](/rest/api/searchservice/indexer-operations) API 和[.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK 均已正式推出。 |
| 一個 blob 多個 | `jsonLines` | 剖析 blob，其中包含多個 JSON 實體 (「陣列」 ) 以分行符號分隔，其中每個實體都會變成個別的 Azure 認知搜尋檔。 | [REST](/rest/api/searchservice/indexer-operations) API 和[.net](/dotnet/api/azure.search.documents.indexes.models.searchindexer) SDK 均已正式推出。 |

### <a name="1---assemble-inputs-for-the-request"></a>1-組合要求的輸入

針對每個要求，您必須為 POST 標頭) 中的 Azure 認知搜尋 (提供服務名稱和管理金鑰，以及 blob 儲存體的儲存體帳戶名稱和金鑰。 您可以使用 [WEB API 測試控管](search-get-started-rest.md) 將 HTTP 要求傳送至 Azure 認知搜尋。

將下列四個值複製到 [記事本]，讓您可以將它們貼到要求中：

+ Azure 認知搜尋服務名稱
+ Azure 認知搜尋管理金鑰
+ Azure 儲存體帳戶名稱
+ Azure 儲存體帳戶金鑰

您可以在入口網站中找到這些值：

1. 在 Azure 認知搜尋的入口網站頁面中，複製 [總覽] 頁面中的 [搜尋服務 URL]。

2. 在左側流覽窗格中，按一下 [機 **碼** ]，然後複製主要或次要金鑰， (它們相等) 。

3. 切換至儲存體帳戶的入口網站頁面。 在左側流覽窗格的 [ **設定**] 底下，按一下 [ **存取金鑰**]。 此頁面提供帳戶名稱和金鑰。 將儲存體帳戶名稱和其中一個金鑰複製到 [記事本]。

### <a name="2---create-a-data-source"></a>2-建立資料來源

此步驟會提供索引子所使用的資料來源連接資訊。 資料來源是 Azure 認知搜尋中的命名物件，可保存連接資訊。 資料來源類型 `azureblob` 會決定索引子會叫用哪些資料解壓縮行為。 

請將服務名稱、系統管理金鑰、儲存體帳戶和帳戶金鑰預留位置的有效值替換為有效值。

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   
```

### <a name="3---create-a-target-search-index"></a>3-建立目標搜尋索引 

索引子要搭配索引結構描述。 如果您使用 API (而不是入口網站)，請事先準備好索引，以便在索引子作業中指定它。

索引會將可搜尋的內容儲存在 Azure 認知搜尋中。 若要建立索引，請提供結構描述來指定文件中的欄位、屬性和其他可形塑搜尋體驗的建構。 如果您建立的索引具有與來源相同的欄位名稱和資料類型，索引子便會比對來源和目的地的欄位，讓您不必明確地對應欄位。

下列範例說明[建立索引](/rest/api/searchservice/create-index)要求。 索引會有可搜尋的 `content` 欄位，以供儲存從 Blob 擷取到的文字︰   

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```


### <a name="4---configure-and-run-the-indexer"></a>4-設定和執行索引子

如同索引和資料來源，索引子也是您在 Azure 認知搜尋服務上建立和重複使用的命名物件。 建立索引子的完整指定要求看起來可能如下所示：

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }
```

索引子設定在要求的主體中。 它需要資料來源和已存在於 Azure 認知搜尋中的空白目標索引。 

排程和參數是選擇性的。 如果您省略它們，索引子會立即執行， `json` 並使用做為剖析模式。

這個特定的索引子不包含欄位對應。 在索引子定義中，如果來源 JSON 檔的屬性符合目標搜尋索引的欄位，您就可以離開 **欄位** 對應。 


### <a name="rest-example"></a>REST 範例

本節將概述用來建立物件的所有要求。 如需元件元件的討論，請參閱這篇文章中的上一節。

### <a name="data-source-request"></a>資料來源要求

所有索引子都需要資料來源物件，以提供現有資料的連接資訊。 

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  
```

### <a name="index-request"></a>索引要求

所有索引子都需要接收資料的目標索引。 要求的主體會定義索引架構（由欄位組成），以支援可搜尋索引中的所需行為。 當您執行索引子時，此索引應該是空的。 

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }
```

### <a name="indexer-request"></a>索引子要求

此要求會顯示完整指定的索引子。 它包含先前範例中省略的欄位對應。 回想一下，只要有可用的預設值，就可以選擇「排程」、「參數」和「fieldMappings」。 省略 "schedule" 會導致立即執行索引子。 省略 "parsingMode" 會導致索引使用 "json" 預設值。

在 Azure 認知搜尋上建立索引子會觸發資料匯入。 它會立即執行，而如果您已提供，則會按照排程執行。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
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
```

<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>使用 .NET SDK

.NET SDK 與 REST API 有完整的同位檢查。 建議您檢閱先前的 REST API 章節，以了解其概念、工作流程和需求。 然後，您可以參閱下列 .NET API 參考文件，以在受控程式碼中實作 JSON 索引子。

+ [azure.search.documents。 searchindexerdatasourceconnection](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourceconnection)
+ [azure.search.documents。 searchindexerdatasourcetype](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype) 
+ [azure.search.documents。 searchindex](/dotnet/api/azure.search.documents.indexes.models.searchindex) 
+ [azure.search.documents。 searchindexer](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>剖析模式

JSON blob 可採用多種形式。 JSON 索引子上的 **parsingMode** 參數會決定 json blob 內容在 Azure 認知搜尋索引中的剖析和結構：

| parsingMode | 描述 |
|-------------|-------------|
| `json`  | 將每個 blob 的索引編制為單一檔。 此為預設值。 |
| `jsonArray` | 如果您的 blob 包含 JSON 陣列，且您需要陣列的每個元素成為 Azure 認知搜尋中的個別檔，請選擇此模式。 |
|`jsonLines` | 如果您的 blob 包含以新行分隔的多個 JSON 實體，而且您需要每個實體成為 Azure 認知搜尋中的個別檔，請選擇此模式。 |

您可以將文件視為搜尋結果中的單一項目。 如果您希望陣列中的每個專案都以獨立專案的形式顯示在搜尋結果中，則 `jsonArray` 請 `jsonLines` 適當地使用或選項。

在索引子定義中，您可以選擇性地使用[欄位對應](search-indexer-field-mappings.md)，以選擇用來填入目標搜尋索引的來源 JSON 文件屬性。 若是 `jsonArray` 剖析模式，如果陣列以較低層級的屬性存在，您可以設定檔根目錄，指出陣列放在 blob 中的位置。

> [!IMPORTANT]
> 當您使用 `json` `jsonArray` 或 `jsonLines` 剖析模式時，Azure 認知搜尋會假設資料來源中的所有 BLOB 都包含 JSON。 如果您需要支援在相同的資料來源中混用 JSON 和非 JSON Blob，請在 [UserVoice 網站](https://feedback.azure.com/forums/263029-azure-search)上讓我們知道。


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>剖析單一 JSON blob

依預設， [Azure 認知搜尋 blob 索引子](search-howto-indexing-azure-blob-storage.md) 會將 JSON blob 剖析為單一的文字區塊。 通常，您會想要保留 JSON 文件的結構。 例如，假設您在 Azure Blob 儲存體中有下列 JSON 文件：

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

Blob 索引子會將 JSON 檔剖析成單一 Azure 認知搜尋檔。 索引子會比對來源中的 "text"、"datePublished"、"tags"，找出與目標索引欄位具有相同名稱和類型的索引，然後載入索引。

如前所述，不一定要使用欄位對應。 指定索引的 "text"、"datePublished"、"tags" 欄位，blob 索引子可以推斷正確的對應，故要求中不需要欄位對應。

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>剖析 JSON 陣列

或者，您可以使用 JSON 陣列選項。 當 blob 包含格式正確的 *JSON 物件陣列*，而您想要每個元素變成個別 Azure 認知搜尋檔時，此選項非常有用。 例如，假設有下列的 JSON blob，您可以在 Azure 認知搜尋索引中填入三個不同的檔，每個都有 "id" 和 "text" 欄位。  

```text
    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]
```

若為 JSON 陣列，索引子定義看起來應該像下列範例。 請注意，parsingMode 參數會指定 `jsonArray` 剖析器。 指定正確的剖析器並擁有正確的資料輸入，就是編制 JSON blob 索引的兩個數組特定需求。

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
```

同樣地，請注意您可以省略欄位對應。 採用 "id" 和 "text" 欄位名稱相同的索引，Blob 索引子便可以推斷正確的對應，而不需要明確的欄位對應清單。

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>剖析嵌套陣列
若為具有嵌套專案的 JSON 陣列，您可以指定 `documentRoot` 來指出多層結構。 例如，如果您的 Blob 看起來像這樣︰

```http
    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }
```

使用這個設定來索引 `level2` 屬性中包含的陣列：

```http
    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }
```

## <a name="parse-blobs-separated-by-newlines"></a>剖析以分行符號分隔的 blob

如果您的 blob 包含以分行符號分隔的多個 JSON 實體，而且您想要讓每個專案成為不同的 Azure 認知搜尋檔，您可以選擇 [JSON 行] 選項。 例如，假設下列 blob (有三個不同的 JSON 實體) ，您可以使用三個不同的檔填入 Azure 認知搜尋索引，每個檔都有「識別碼」和「文字」欄位。

```text
{ "id" : "1", "text" : "example 1" }
{ "id" : "2", "text" : "example 2" }
{ "id" : "3", "text" : "example 3" }
```

針對 JSON 行，索引子定義看起來應該類似下列範例。 請注意，parsingMode 參數會指定 `jsonLines` 剖析器。 

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }
```

同樣地，請注意，欄位對應可以省略，類似于 `jsonArray` 剖析模式。

## <a name="add-field-mappings"></a>新增欄位對應

當來源和目標的欄位不能完全對齊時，您可以在要求主體中定義欄位對應區段，明確指定欄位對欄位的關聯。

Azure 認知搜尋目前無法直接編制任意 JSON 檔的索引，因為它只支援基本資料類型、字串陣列和 GeoJSON 點。 不過，您可以使用 **欄位對應** 挑選 JSON 文件的幾個部分，並將它們「上移」到搜尋文件的最上層欄位。 若要瞭解欄位對應的基本概念，請參閱 [Azure 認知搜尋索引子中的欄位](search-indexer-field-mappings.md)對應。

回到我們的範例 JSON 文件︰

```http
    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }
```

假設搜尋索引有下列欄位︰`Edm.String` 類型的 `text`、`Edm.DateTimeOffset` 類型的 `date`、`Collection(Edm.String)` 類型的 `tags`。 請注意來源中的 "datePublished" 與索引中的 `date` 欄位之間的差異。 若要將 JSON 對應到所需形狀，請使用下列欄位對應︰

```http
    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]
```

對應中的來源欄位名稱是使用 [JSON 指標](https://tools.ietf.org/html/rfc6901) 標記法指定。 以正斜線開始參考 JSON 文件的根目錄，然後使用正斜線分隔的路徑挑選所需的屬性 (使用任意層級的巢狀結構)。

您也可以使用以零為起始的索引來參考個別陣列元素。 比方說，若要從上述範例挑選 "tags" 陣列的第一個元素，請如下所示使用欄位對應︰

```http
    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }
```

> [!NOTE]
> 如果欄位對應路徑中的來源欄位名稱參考在 JSON 中不存在的屬性，則會略過該對應且不會產生錯誤。 這麼做是為了讓我們可支援具有不同結構描述 (這是常見的使用案例) 的文件。 因為沒有任何驗證，您必須小心避免在欄位對應規格中出現錯字。
>

## <a name="help-us-make-azure-cognitive-search-better"></a>協助我們改善 Azure 認知搜尋
如果您有功能要求或改進的想法，請在 [UserVoice](https://feedback.azure.com/forums/263029-azure-search/) 提供。 如果您需要使用現有功能的協助，請將您的問題張貼在 [Stack Overflow](https://stackoverflow.microsoft.com/questions/tagged/18870)上。

## <a name="see-also"></a>另請參閱

+ [Azure 認知搜尋中的索引子](search-indexer-overview.md)
+ [使用 Azure 認知搜尋編制索引 Azure Blob 儲存體](search-howto-index-json-blobs.md)
+ [使用 Azure 認知搜尋 blob 索引子編制 CSV blob 的索引](search-howto-index-csv-blobs.md)
+ [教學課程：從 Azure Blob 儲存體搜尋半結構化資料](search-semi-structured-data.md)