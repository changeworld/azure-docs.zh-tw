---
title: 使用 REST 建立知識存放區
titleSuffix: Azure Cognitive Search
description: 使用 REST API 和 Postman 建立 Azure 認知搜尋知識存放區，以保存來自 AI 擴充管線的擴充。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/30/2020
ms.openlocfilehash: f496413cf1a5175cee65bce46a2f7f6260d5451d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/30/2020
ms.locfileid: "85565222"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>使用 REST 和 Postman 建立知識存放區

知識存放區包含來自 Azure 認知搜尋擴充管線的輸出，以供稍後進行分析或其他下游處理。 AI 擴充管線可接受影像檔案或非結構化文字檔、使用 Azure 認知搜尋編製其索引、從認知服務 (例如影像分析和自然語言處理) 套用 AI 擴充，然後將結果儲存至 Azure 儲存體中的知識存放區。 您可以在 Azure 入口網站中使用 Power BI 或儲存體總管之類的工具來探索知識存放區。

在此文章文中，您會使用 REST API 介面將 AI 擴充內嵌、編製索引及套用至一組旅館評論。 旅館評論會匯入到 Azure Blob 儲存體中。 結果會以知識存放區的形式儲存在 Azure 資料表儲存體中。

建立知識存放區之後，您可以了解如何使用 [儲存體總管](knowledge-store-view-storage-explorer.md) 或 [Power BI](knowledge-store-connect-power-bi.md) 來存取知識存放區。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!TIP]
> 建議您在本文中使用 [Postman 桌面應用程式](https://www.getpostman.com/)。 本文的[原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store)包含 Postman 集合，內含所有的要求。 

## <a name="create-services-and-load-data"></a>建立服務並載入資料

本快速入門會使用 Azure 認知搜尋、Azure Blob 儲存體和 [Azure 認知服務](https://azure.microsoft.com/services/cognitive-services/)進行 AI 處理。 

由於工作負載很小，因此認知服務會在幕後連線以提供免費處理，每天最多 20 筆交易。 由於資料集很小，因此您可略過建立或連結認知服務資源的步驟。

1. [下載 HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D)。 這項資料是儲存在 CSV 檔案中的飯店評論資料 (源自於 Kaggle.com)，其中包含 19 個關於單一飯店的客戶意見反應。 

1. [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)，或在您目前的訂用帳戶下方[尋找現有帳戶](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。 您會將 Azure 儲存體同時用於要匯入的原始內容，以及作為最終結果的知識存放區。

   選擇 **StorageV2 (一般用途 V2)** 帳戶類型。

1. 開啟 Blob 服務頁面，並建立名為 *hotel-reviews* 的容器。

1. 按一下 [上傳] 。

    ![上傳資料](media/knowledge-store-create-portal/upload-command-bar.png "上傳飯店評論")

1. 選取您在第一個步驟中下載的 **HotelReviews-Free.csv** 檔案。

    ![建立 Azure Blob 容器](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "建立 Azure Blob 容器")

1. 這項資源的作業即將完成，但在離開這些頁面之前，請使用左側導覽窗格上的連結開啟 [存取金鑰] 頁面。 取得連接字串以從 Blob 儲存體中擷取資料。 連接字串會如下列範例所示：`DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. 同樣在入口網站中，切換至 [Azure 認知搜尋]。 [建立新的服務](search-create-service-portal.md)或[尋找現有的服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 在此練習中，您可以使用免費服務。

## <a name="configure-postman"></a>設定 Postman

安裝並設定 Postman。

### <a name="download-and-install-postman"></a>下載並安裝 Postman

1. 下載 [Postman 集合原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)。
1. 選取 [檔案] > [匯入]，將原始程式碼匯入 Postman。
1. 選取 [集合] 索引標籤，然後選取 **...** (省略符號) 按鈕。
1. 選取 [編輯]。 
   
   ![顯示導覽的 Postman 應用程式](media/knowledge-store-create-rest/postman-edit-menu.png "移至 Postman 中的 [編輯] 功能表")
1. 在 [編輯] 對話方塊中，選取 [變數] 索引標籤。 

在 [變數] 索引標籤上，您可以在每次遇到雙括弧內的特定變數時，新增 Postman 交換的值。 例如，Postman 會將符號 `{{admin-key}}` 取代為您針對 `admin-key` 所設定的目前值。 Postman 會在 URL、標頭、要求本文等項目中進行這項替代。 

若要取得 `admin-key` 的值，請移至 Azure 認知搜尋服務，然後選取 [金鑰] 索引標籤。將 `search-service-name` 和 `storage-account-name` 變更為您在 [建立服務](#create-services-and-load-data) 中選擇的值。 使用儲存體帳戶的 [存取金鑰] 索引標籤上的值設定 `storage-connection-string`。您可以將其他值保留為預設值。

![Postman 應用程式變數索引標籤](media/knowledge-store-create-rest/postman-variables-window.png "Postman 的變數視窗")


| 變數    | 從哪裡取得 |
|-------------|-----------------|
| `admin-key` | 在 Azure 認知搜尋服務的 [金鑰] 頁面上。  |
| `api-version` | 保持為 **2020-06-30**。 |
| `datasource-name` | 保留為 **hotel-reviews-ds**。 | 
| `indexer-name` | 保留為 **hotel-reviews-ixr**。 | 
| `index-name` | 保留為 **hotel-reviews-ix**。 | 
| `search-service-name` | Azure 認知搜尋服務的名稱。 URL 為 `https://{{search-service-name}}.search.windows.net`。 | 
| `skillset-name` | 保留為 **hotel-reviews-ss**。 | 
| `storage-account-name` | 儲存體帳戶名稱。 | 
| `storage-connection-string` | 在儲存體帳戶的 [存取金鑰] 索引標籤上，選取 [key1] > [連接字串]。 | 
| `storage-container-name` | 保留為 **hotel-reviews**。 | 

### <a name="review-the-request-collection-in-postman"></a>檢閱 Postman 中的要求集合

當您建立知識存放區時，必須發出四個 HTTP 要求： 

- **建立索引的 PUT 要求**：此索引會保存 Azure 認知搜尋所使用並傳回的資料。
- **建立資料來源的 POST 要求**：此資料來源會將您的 Azure 認知搜尋行為連線至資料和知識存放區的儲存體帳戶。 
- **建立技能集的 PUT 要求**：技能集會指定套用至資料的擴充，以及知識存放區的結構。
- **建立索引子的 PUT 要求**：執行索引子會讀取資料、套用技能集，並儲存結果。 您必須最後再執行此要求。

[原始程式碼](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json)包含具有這四個要求的 Postman 集合。 若要發出要求，請在 Postman 中選取要求的索引標籤。 接著，新增 `api-key` 和 `Content-Type` 要求標頭。 將 `api-key` 的值設定為 `{{admin-key}}`。 將 `Content-type` 值設定為 `application/json`。 

![顯示 Postman 標頭介面的螢幕擷取畫面](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> 您必須在所有要求中設定 `api-key` 和 `Content-type` 標頭。 如果 Postman 辨識了一個變數，該變數就會以橘色文字顯示，如同前面螢幕擷取畫面中的 `{{admin-key}}`。 如果變數拼錯，則會以紅色文字顯示。
>

## <a name="create-an-azure-cognitive-search-index"></a>建立 Azure 認知搜尋索引

建立 Azure 認知搜尋索引，以代表您想要搜尋、篩選及套用增強功能的資料。 對 `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` 發出 PUT 要求以建立索引。 Postman 會將以雙括弧括住的符號 (例如 `{{search-service-name}}`、`{{index-name}}` 和 `{{api-version}}`) 取代為您在 [設定 Postman](#configure-postman) 中設定的值。 如果您使用不同的工具來發出 REST 命令，則必須自行替換這些變數。

在要求本文中設定 Azure 認知搜尋索引的結構。 在 Postman 中，設定 `api-key` 和 `Content-type` 標頭之後，請移至要求的 [本文] 窗格。 您應該會看見下列 JSON。 如果沒有，請選取 [未經處理] > [JSON (application/json)]，然後貼上下列程式碼作為本文：

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

此索引定義是由您想要呈現給使用者的資料 (旅館的名稱、評論內容、日期)、搜尋中繼資料，以及 AI 增強資料 (情感、關鍵片語和語言) 所組合而成。

選取 [傳送] 以發出 PUT 要求。 您應該會看見狀態 `201 - Created`。 如果您看到不同的狀態，請在 [本文] 窗格中，尋找包含錯誤訊息的 JSON 回應。 

## <a name="create-the-datasource"></a>建立資料來源

接下來，將 Azure 認知搜尋連線至您在 Blob 儲存體中儲存的旅館資料。 若要建立資料來源，請將 POST 要求傳送至 `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`。 如先前所述，您必須設定 `api-key` 和 `Content-Type` 標頭。 

在 Postman 中，移至 [建立資料來源] 要求，然後前往 [本文] 窗格。 您應該會看見下列程式碼：

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

選取 [傳送] 以發出 POST 要求。 

## <a name="create-the-skillset"></a>建立技能集 

下一步是指定技能集，也就是指定要套用的增強功能，以及要用來儲存結果的知識存放區。 在 Postman 中，選取 [建立技能集] 索引標籤。此要求會將 PUT 傳送至 `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`。 如同您稍早所做的動作，設定 `api-key` 和 `Content-type` 標頭。 

有兩個大型的最上層物件：`skills` 和 `knowledgeStore`。 `skills` 物件中的每個物件都是擴充服務。 每個擴充服務都有 `inputs` 和 `outputs`。 `LanguageDetectionSkill` 具備 `Language` 的輸出 `targetName`。 大部分的其他技能都會使用此節點的值作為輸入。 原始檔為 `document/Language`。 這種將一個節點的輸出當作另一個節點的輸入使用的功能，在 `ShaperSkill` 中更為明顯，這會指定資料將如何流入知識存放區的資料表中。

`knowledge_store` 物件會透過 `{{storage-connection-string}}` Postman 變數連線至儲存體帳戶。 `knowledge_store` 在知識存放區中，包含增強式文件與資料表和資料行之間的一組對應。 

若要產生技能集，請選取 Postman 中的 [傳送] 按鈕來推送要求：

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>建立索引子

最後一個步驟是建立索引子。 索引子會讀取資料並啟用技能集。 在 Postman 中，選取 [建立索引子] 要求，然後檢閱本文。 索引子的定義是指您已建立的其他數個資源，也就是資料來源、索引和技能集。 

`parameters/configuration` 物件可控制索引子內嵌資料的方式。 在此情況下，輸入資料位於具有標頭行和逗號分隔值的單一文件中。 文件索引鍵是文件的唯一識別碼。 在編碼之前，文件索引鍵是來源文件的 URL。 最後，技能集輸出值 (例如語言代碼、情感和關鍵片語) 會對應至其在文件中的位置。 雖然 `Language` 具有單一值，但 `Sentiment` 會套用至 `pages` 之陣列中的每個元素。 `Keyphrases` 是一個陣列，而且也會套用至 `pages` 陣列中的每個元素。

在您設定 `api-key` 和 `Content-type` 標頭，並確認要求的本文類似於下列原始程式碼之後，請在 Postman 中選取 [傳送]。 Postman 會將 PUT 要求傳送至 `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`。 Azure 認知搜尋會建立並執行索引子。 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>執行索引子 

在 Azure 入口網站中，移至 Azure 認知搜尋服務的 [概觀] 頁面。 選取 [索引子] 索引標籤，然後選取 **hotels-reviews-ixr**。 如果索引子尚未執行，請選取 [執行]。 索引工作可能會引發一些與語言辨識相關的警告。 此資料包含以認知技能尚未支援的語言撰寫的一些評論。 

## <a name="next-steps"></a>後續步驟

既然您已經使用認知服務擴充資料，並將結果投射到知識存放區，接下來即可使用儲存體總管或 Power BI 來探索擴充的資料集。

若要了解如何使用儲存體總管來探索此知識存放區，請參閱下列逐步解說：

> [!div class="nextstepaction"]
> [使用儲存體總管檢視](knowledge-store-view-storage-explorer.md)

若要了解如何將此知識存放區連線到 Power BI，請參閱下列逐步解說：

> [!div class="nextstepaction"]
> [與 Power BI 連線](knowledge-store-connect-power-bi.md)

如果您想要重複此練習，或嘗試不同的 AI 擴充逐步解說，請刪除 hotel-reviews-idxr 索引子。 刪除索引子會將免費的每日交易計數器重設為零。
