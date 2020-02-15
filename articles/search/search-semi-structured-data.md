---
title: 教學課程：索引 JSON blob 中的半 strutured 資料
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 認知搜尋 REST API 和 Postman，為半結構化 Azure JSON Blob 編製索引及加以搜尋。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 0603ad1fbecf33e5880fd7f18d35af51795f8e39
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251986"
---
# <a name="rest-tutorial-index-and-search-semi-structured-data-json-blobs-in-azure-cognitive-search"></a>REST 教學課程：在 Azure 認知搜尋中編制索引和搜尋半結構化資料（JSON blob）

Azure 認知搜尋可以在 Azure Blob 儲存體中，使用[索引子](search-indexer-overview.md)為 JSON 文件和陣列編製索引，以了解如何讀取半結構化資料。 半結構化資料會包含在資料內分隔內容的標籤或標記。 這些資料會將不同的內容區分為必須完整編製索引的未結構化資料，以及遵循資料模型 (例如關聯式資料庫結構描述) 且可依據欄位逐一編製索引的正式結構化資料。

在本教學課程中，請使用 [Azure 認知搜尋 REST API](https://docs.microsoft.com/rest/api/searchservice/) 和 REST 用戶端執行下列工作：

> [!div class="checklist"]
> * 設定 Azure Blob 容器的 Azure 認知搜尋資料來源
> * 建立 Azure 認知搜尋索引以包含可搜尋的內容
> * 設定並執行索引子以讀取容器，並從 Azure Blob 儲存體擷取可搜尋的內容
> * 搜尋剛剛建立的索引

## <a name="prerequisites"></a>Prerequisites

本快速入門會使用下列服務、工具和資料。 

[建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。 

[建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，以儲存範例資料。

用來將要求傳送至 Azure 認知搜尋的 [Postman 傳統型應用程式](https://www.getpostman.com/)。

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) 包含本教學課程中使用的資料。 下載此檔案並將其解壓縮到它自己的資料夾中。 資料源自 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)，已針對本教學課程轉換為 JSON。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀] 頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定] >  [金鑰] 中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="prepare-sample-data"></a>準備範例資料

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]，然後按一下 [+ 容器]。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料。 您可以將公用存取層級設定為任何有效值。

1. 建立容器之後，請加以開啟，然後選取命令列的 [上傳]。

   ![使用命令列上傳](media/search-semi-structured-data/upload-command-bar.png "使用命令列上傳")

1. 瀏覽至包含範例檔案的資料夾。 全部選取，然後按一下 [上傳]。

   ![上傳檔案](media/search-semi-structured-data/clinicalupload.png "上傳檔案")

上傳完成之後，檔案應該會出現在資料容器內部它們本身的子資料夾中。

## <a name="set-up-postman"></a>設定 Postman

啟動 Postman 及設定 HTTP 要求。 如果您不熟悉此工具，請參閱[使用 Postman 探索 Azure 認知搜尋 REST API](search-get-started-postman.md)。

本教學課程中每個呼叫的要求方法都是 **POST**。 標頭金鑰為 "Content-type" 和 "api-key"。 標頭金鑰的值分別為 "application/json" 和您的「管理金鑰」(管理金鑰是您搜尋主索引鍵的預留位置)。 主體是您放置呼叫實際內容的地方。 根據使用的用戶端而定，您用以建構查詢的方式可能會有一些變化，但那些都是基本的。

  ![半結構化搜尋](media/search-semi-structured-data/postmanoverview.png)

我們使用 Postman 來對您的搜尋服務進行三個 API 呼叫，以建立資料來源、索引及索引子。 資料來源包括指向您儲存體帳戶和您 JSON 資料的指標。 您的搜尋服務會在載入資料時進行連線。

查詢字串必須指定 api-version，且每個呼叫都應傳回 **201 Created**。 用於使用 JSON 陣列的公開推出 api-version 為 `2019-05-06`。

從您的 REST 用戶端執行下列三個 API 呼叫。

## <a name="create-a-data-source"></a>建立資料來源

[建立資料來源 API](https://docs.microsoft.com/rest/api/searchservice/create-data-source)會建立 Azure 認知搜尋物件，以指定要編制索引的資料。

此呼叫的端點為 `https://[service name].search.windows.net/datasources?api-version=2019-05-06`。 使用您的搜尋服務名稱來取代 `[service name]`。 

對於此呼叫，要求本文必須包含您的儲存體帳戶名稱、儲存體帳戶金鑰和 Blob 容器名稱。 儲存體帳戶金鑰可以在 Azure入口網站中，於儲存體帳戶的**存取金鑰**內部找到。 下面的影像顯示該位置：

  ![半結構化搜尋](media/search-semi-structured-data/storagekeys.png)

執行呼叫之前，請務必取代呼叫主體中的 `[storage account name]`、`[storage account key]` 和 `[blob container name]`。

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

回應看起來應如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>建立索引
    
第二個呼叫為[建立索引 API](https://docs.microsoft.com/rest/api/searchservice/create-index)，進而建立可儲存所有可搜尋資料的 Azure 認知搜尋索引。 索引會指定所有參數及其屬性。

此呼叫的 URL 是 `https://[service name].search.windows.net/indexes?api-version=2019-05-06`。 使用您的搜尋服務名稱來取代 `[service name]`。

首先，取代 URL。 接著複製下列程式碼並貼至您的主體，然後執行查詢。

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

回應看起來應如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>建立及執行索引子

索引子會與資料來源連線、將資料匯入至目標搜尋索引，並選擇性地提供排程來將資料重新整理自動化。 REST API 為[建立索引子](https://docs.microsoft.com/rest/api/searchservice/create-indexer)。

此呼叫的 URL 是 `https://[service name].search.windows.net/indexers?api-version=2019-05-06`。 使用您的搜尋服務名稱來取代 `[service name]`。

首先，取代 URL。 接著，複製下列程式碼並貼至您的本文，然後傳送要求。 立即處理要求。 當回應傳回時，您將具備可進行全文檢索搜尋的索引。

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

回應看起來應如下所示：

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>搜尋您的 JSON 檔案

第一個文件載入後，您就可以開始查詢。 針對此工作，在入口網站中使用[**搜尋總管**](search-explorer.md)。

在 Azure 入口網站中，開啟搜尋服務 [概觀] 頁面，尋找您在 [索引] 清單中建立的索引。

請務必選擇您剛才建立的索引。 

  ![非結構化搜尋](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>使用者定義的中繼資料搜尋

如前所述，您可以使用數種方式來查詢資料：全文檢索搜尋、系統內容或使用者定義的中繼資料。 如果已在建立目標索引期間將系統內容和使用者定義的中繼資料標記為 `$select`retrievable **，就只能使用**  參數來搜尋它們。 索引中的參數一旦建立之後，就無法改變。 不過，可以新增其他參數。

有一個基本查詢範例是 `$select=Gender,metadata_storage_size`，它會將傳回限制為這兩個參數。

  ![半結構化搜尋](media/search-semi-structured-data/lastquery.png)

有一個更複雜的查詢範例是 `$filter=MinimumAge ge 30 and MaximumAge lt 75`，它只會傳回參數 MinimumAge 大於或等於 30 且 MaximumAge 小於 75 的結果。

  ![半結構化搜尋](media/search-semi-structured-data/metadatashort.png)

如果您想要自行實驗並嘗試其他更多查詢，請隨意嘗試。 了解您可以使用邏輯運算子 (and、or、not) 和比較運算子 (eq、ne、gt、lt、ge、le)。 字串比較是區分大小寫的。

`$filter` 參數只能與在索引建立期間標記為可篩選的中繼資料一起使用。

## <a name="clean-up-resources"></a>清除資源

在完成教學課程後，最快速的清除方式是刪除包含 Azure 認知搜尋服務的資源群組。 您現在可以刪除資源群組，以永久刪除當中所包含的所有項目。 在入口網站中，資源群組名稱位在 Azure 認知搜尋服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

有數種方法和多個選項可用於編製 JSON Blob 的索引。 下一個步驟中，檢閱及測試各種不同的選項，以查看最適合您的案例。

> [!div class="nextstepaction"]
> [如何使用 Azure 認知搜尋 Blob 索引子編製 JSON Blob 的索引](search-howto-index-json-blobs.md)
