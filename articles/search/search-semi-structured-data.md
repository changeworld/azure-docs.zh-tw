---
title: 教學課程：為 JSON Blob 中的半結構化資料編製索引
titleSuffix: Azure Cognitive Search
description: 了解如何使用 Azure 認知搜尋 REST API 和 Postman，為半結構化 Azure JSON Blob 編製索引及加以搜尋。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: 7c88aea6aff942cdcf5cbc022df8f07cfe0d4cce
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701274"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>教學課程：使用 REST 為 Azure 儲存體中的 JSON Blob 編製索引

Azure 認知搜尋可以在 Azure Blob 儲存體中，使用[索引子](search-indexer-overview.md)為 JSON 文件和陣列編製索引，以了解如何讀取半結構化資料。 半結構化資料會包含在資料內分隔內容的標籤或標記。 這些資料會將不同的內容區分為必須完整編製索引的未結構化資料，以及遵循資料模型 (例如關聯式資料庫結構描述) 且可依據欄位逐一編製索引的正式結構化資料。

本教學課程將使用 Postman 和[搜尋 REST API](/rest/api/searchservice/) 執行下列工作：

> [!div class="checklist"]
> * 設定 Azure Blob 容器的 Azure 認知搜尋資料來源
> * 建立 Azure 認知搜尋索引以包含可搜尋的內容
> * 設定並執行索引子以讀取容器，並從 Azure Blob 儲存體擷取可搜尋的內容
> * 搜尋剛剛建立的索引

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

+ [Azure 儲存體](../storage/common/storage-account-create.md)
+ [Postman 桌面應用程式](https://www.getpostman.com/)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用免費服務來進行本教學課程。 免費的搜尋服務會有限制，您只能使用三個索引、三個索引子和三個資料來源。 本教學課程會各建立一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="download-files"></a>下載檔案

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) 包含本教學課程中使用的資料。 下載此檔案並將其解壓縮到它自己的資料夾中。 資料源自 [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)，已針對本教學課程轉換為 JSON。

## <a name="1---create-services"></a>1 - 建立服務

本教學課程將使用 Azure 認知搜尋進行索引編製和查詢，並以 Azure Blob 儲存體提供資料。 

為具備鄰近性和管理方面的優勢，如果可能，請將這兩項服務建立在相同的區域和資源群組中。 實際上，您的 Azure 儲存體帳戶可以位在任何區域中。

### <a name="start-with-azure-storage"></a>開始使用 Azure 儲存體

1. [登入 Azure 入口網站](https://portal.azure.com/)，然後按一下 [+ 建立資源]  。

1. 搜尋「儲存體帳戶」  ，然後選取 Microsoft 的儲存體帳戶供應項目。

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="建立儲存體帳戶" border="false":::

1. 在 [基本] 索引標籤中，需要下列項目。 接受所有其他項目的預設值。

   + **資源群組**。 選取現有群組或建立一個新的群組，但必須對所有服務使用相同的群組，以便您一起管理這些服務。

   + **儲存體帳戶名稱**。 如果您認為您可能會有多個相同類型的資源，請透過名稱在類型和區域上做出區別，例如 blobstoragewestus  。 

   + **位置**。 可能的話，請選擇用於 Azure 認知搜尋和認知服務的相同位置。 單一位置可避免產生頻寬費用。

   + **帳戶種類**。 選擇預設值 [StorageV2 (一般用途 v2)]  。

1. 按一下 [檢閱 + 建立]  以建立服務。

1. 建立後，按一下 [移至資源]  以開啟 [概觀] 頁面。

1. 按一下 [Blob]  服務。

1. [建立 Blob 容器](../storage/blobs/storage-quickstart-blobs-portal.md)以容納範例資料。 您可以將公用存取層級設定為任何有效值。

1. 建立容器之後，請加以開啟，然後選取命令列的 [上傳]  。

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="使用命令列上傳" border="false":::

1. 瀏覽至包含範例檔案的資料夾。 全部選取，然後按一下 [上傳]  。

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="上傳檔案" border="false":::

上傳完成之後，檔案應該會出現在資料容器內部它們本身的子資料夾中。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

下一個資源是 Azure 認知搜尋，您可以[在入口網站中建立](search-create-service-portal.md)該資源。 您可以使用免費層來完成此逐步解說。 

如同 Azure Blob 儲存體，請花點時間來收集存取金鑰。 此外，當您開始結構化要求時，您必須提供用來驗證每個要求的端點和管理員 API 金鑰。

### <a name="get-a-key-and-url"></a>取得金鑰和 URL

REST 呼叫需要服務 URL 和每個要求的存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

:::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="取得 HTTP 端點和存取金鑰" border="false":::

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 擁有有效的金鑰就能為每個要求在傳送要求之應用程式與處理要求之服務間建立信任。

## <a name="2---set-up-postman"></a>2 - 設定 Postman

啟動 Postman 及設定 HTTP 要求。 如果您不熟悉此工具，請參閱[探索 Azure 認知搜尋 REST API](search-get-started-rest.md)。

本教學課程中用於各個呼叫的要求方法為 **POST** 和 **GET**。 您將對搜尋服務進行三個 API 呼叫，以建立資料來源、索引及索引子。 資料來源包括指向您儲存體帳戶和您 JSON 資料的指標。 您的搜尋服務會在載入資料時進行連線。

在標頭中，將 "Content-type" 設定為 `application/json`，並將 `api-key` 設定為您 Azure 認知搜尋服務的管理員 API 金鑰。 設定標頭之後，您就可以在此練習中的每個要求上加以使用。

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Postman 要求 URL 和標頭" border="false":::

URI 必須指定 api-version，且每個呼叫都應傳回 **201 已建立**。 用於使用 JSON 陣列的公開推出 api-version 為 `2020-06-30`。

## <a name="3---create-a-data-source"></a>3 - 建立資料來源

[建立資料來源 API](/rest/api/searchservice/create-data-source) 會建立一個 Azure 認知搜尋物件，以指定要編製索引的資料。

1. 將此呼叫的端點設定為 `https://[service name].search.windows.net/datasources?api-version=2020-06-30`。 使用您的搜尋服務名稱來取代 `[service name]`。 

1. 將下列 JSON 複製到要求本文中。

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. 將連接字串取代為您帳戶的有效字串。

1. 將 "[Blob 容器名稱]" 取代為您為範例資料建立的容器。 

1. 傳送要求。 回應看起來應如下所示：

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
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

## <a name="4---create-an-index"></a>4 - 建立索引
    
第二個呼叫為[建立索引 API](/rest/api/searchservice/create-index)，進而建立可儲存所有可搜尋資料的 Azure 認知搜尋索引。 索引會指定所有參數及其屬性。

1. 將此呼叫的端點設定為 `https://[service name].search.windows.net/indexes?api-version=2020-06-30`。 使用您的搜尋服務名稱來取代 `[service name]`。

1. 將下列 JSON 複製到要求本文中。

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
      ]
    }
   ```

1. 傳送要求。 回應看起來應如下所示：

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
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 - 建立及執行索引子

索引子會連線至資料來源、將資料匯入至目標搜尋索引，並選擇性地提供排程將資料重新整理自動化。 REST API 為[建立索引子](/rest/api/searchservice/create-indexer)。

1. 將此呼叫的 URI 設定為 `https://[service name].search.windows.net/indexers?api-version=2020-06-30`。 使用您的搜尋服務名稱來取代 `[service name]`。

1. 將下列 JSON 複製到要求本文中。

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. 傳送要求。 立即處理要求。 當回應傳回時，您將具備可進行全文檢索搜尋的索引。 回應看起來應如下所示：

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
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

## <a name="6---search-your-json-files"></a>6 - 搜尋您的 JSON 檔案

第一個文件載入後，您就可以開始查詢。

1. 將動詞變更為 **GET**。

1. 將此呼叫的 URI 設定為 `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true`。 使用您的搜尋服務名稱來取代 `[service name]`。

1. 傳送要求。 這是未指定的全文檢索搜尋查詢，會傳回所有在索引中標記為可擷取的欄位，以及文件計數。 回應看起來應如下所示：

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. 新增 `$select` 查詢參數，以將結果限定為較少的欄位：`https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true`。  此查詢有 100 份相符的文件，但根據預設，Azure 認知搜尋只會傳回結果中的 50 份。

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="參數化查詢" border="false":::

1. 有一個更複雜的查詢範例會包含 `$filter=MinimumAge ge 30 and MaximumAge lt 75`，這只會傳回參數 MinimumAge 大於或等於 30 且 MaximumAge 小於 75 的結果。 請將 `$select` 運算式取代為 `$filter` 運算式。

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="半結構化搜尋" border="false":::

您也可以使用邏輯運算子 (and、or、not) 和比較運算子 (eq、ne、gt、lt、ge、le)。 字串比較是區分大小寫的。 如需詳細資訊和範例，請參閱[建立簡易查詢](search-query-simple-examples.md)。

> [!NOTE]
> `$filter` 參數只能與在索引建立期間標記為可篩選的中繼資料一起使用。

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

您可以使用入口網站來刪除索引、索引子和資料來源。 或者，使用 **DELETE** 並提供每個物件的 URL。 下列命令會刪除索引子。

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

成功刪除時會傳回狀態碼 204。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

現在您已熟悉 Azure Blob 索引編製的基本概念，接下來我們將進一步了解 Azure 儲存體中 JSON Blob 的索引子設定。

> [!div class="nextstepaction"]
> [設定 JSON Blob 索引編製](search-howto-index-json-blobs.md)