---
title: 教學課程：使用 REST API 在 Python 中建立技能集
titleSuffix: Azure Cognitive Search
description: 逐步說明使用 Jupyter Python 筆記本在 Azure 認知搜尋中進行資料擷取、自然語言和影像 AI 處理的範例。 擷取的資料會編製索引，而且可透過查詢輕鬆存取。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d9ae7f4b7dd8b0f45ae02bd2a90aca78127fd3d3
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472395"
---
# <a name="tutorial-create-an-ai-enrichment-pipeline-using-rest-and-python"></a>教學課程：使用 REST 和 Python 建立 AI 擴充管線

在本教學課程中，您將了解在 Azure 認知搜尋中使用*認知技能*進行資料擴充程式設計的機制。 技能會受到自然語言處理 (NLP) 與認知服務中的映像分析功能所支援。 透過技能組合和設定，您可以擷取文字，以及映像或所掃描文件檔案的文字表示法。 您也可以偵測語言、實體、關鍵片語等。 其結果是，會以索引編製管線中的 AI 擴充資料在搜尋索引中建立豐富的額外內容。 

在此教學課程中，您將使用 Python 執行下列工作：

> [!div class="checklist"]
> * 建立對索引路由中的範例資料進行擴充的索引管線
> * 套用內建的技能：實體辨識、語言偵測、文字操作和關鍵片語擷取
> * 了解如何藉由將技能集的輸入對應至輸出，將多項技術串聯在一起
> * 執行要求並檢閱結果
> * 重設索引和索引子以進行進一步開發

輸出是 Azure 認知搜尋可全文檢索搜尋的索引。 您可以使用其他標準功能來強化索引，例如[同義字](search-synonyms.md)、[評分設定檔](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)、[分析器](search-analyzers.md)和[篩選](search-filters.md)。 

本教學課程雖然在免費服務上執行，但可用的交易數目限制為每日 20 份文件。 如果您想要在同一天內多次執行本教學課程，請刪除索引子以重設計數器。

> [!NOTE]
> 當您透過增加處理頻率、新增更多文件或新增更多 AI 演算法來擴展範圍時，您必須[連結可計費的認知服務資源](cognitive-search-attach-cognitive-services.md)。 在認知服務中呼叫 API，以及在 Azure 認知搜尋的文件萃取階段中擷取影像時，都會產生費用。 從文件中擷取文字不會產生費用。
>
> 內建技能的執行會依現有的[認知服務預付型方案價格](https://azure.microsoft.com/pricing/details/cognitive-services/)收費。 影像擷取定價的說明請見 [Azure 認知搜尋定價頁面](https://go.microsoft.com/fwlink/?linkid=2042400)。

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>Prerequisites

本教學課程會使用下列服務、工具和資料。 

+ [建立 Azure 儲存體帳戶](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)，以儲存範例資料。 確定儲存體帳戶位於與 Azure 認知搜尋相同的區域中。

+ [Anaconda 3.x](https://www.anaconda.com/distribution/#download-section)，提供 Python 3.x 和 Jupyter Notebook。

+ 下載由不同類型小型檔案集組成的[範例資料](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4)。 

+ [建立 Azure 認知搜尋服務](search-create-service-portal.md)，或在您目前的訂用帳戶下方[尋找現有服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 您可以使用本教學課程的免費服務。

## <a name="get-a-key-and-url"></a>取得金鑰和 URL

若要與 Azure 認知搜尋服務互動，您需要服務 URL 和存取金鑰。 建立搜尋服務時需要這兩項資料，因此如果您將 Azure 認知搜尋新增至您的訂用帳戶，請依照下列步驟來取得必要的資訊：

1. [登入 Azure 入口網站](https://portal.azure.com/)，並在搜尋服務的 [概觀]  頁面上取得 URL。 範例端點看起來會像是 `https://mydemo.search.windows.net`。

1. 在 [設定]   >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

![取得 HTTP 端點和存取金鑰](media/search-get-started-postman/get-url-key.png "取得 HTTP 端點和存取金鑰")

所有要求均都需要在傳送至您服務上的每個要求上使用 API 金鑰。 有效的金鑰能為每個要求在傳送要求之應用程式與處理要求的服務間建立信任。

## <a name="prepare-sample-data"></a>準備範例資料

擴充管線會從 Azure 資料來源中提取資料。 來源資料必須來自 [Azure 認知搜尋索引子](search-indexer-overview.md)支援的資料來源類型。 針對此練習，我們會使用 Blob 儲存體來展現多個內容類型。

1. [登入 Azure 入口網站](https://portal.azure.com)瀏覽至您的 Azure 儲存體帳戶、按一下 [Blob]  ，然後按一下 [+ 容器]  。

1. [建立 Blob 容器](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)以容納範例資料。 您可以將公用存取層級設定為任何有效值。

1. 建立容器之後，將其開啟，然後在命令列上選取 [上傳]  ，將您在上一個步驟中下載的範例檔案上傳。

   ![Azure Blob 儲存體中的來源檔案](./media/cognitive-search-quickstart-blob/sample-data.png)

1. 範例檔案載入之後，請取得 Blob 儲存體的容器名稱和連接字串。 您可以瀏覽至 Azure 入口網站中的儲存體帳戶，以執行此動作。 按一下 [存取金鑰]  ，然後複製 [連接字串]  欄位。

連接字串的格式如下：`DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT-NAME>;AccountKey=<YOUR-STORAGE-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

將連接字串保存在隨手可及的位置。 您在之後的步驟中將會用到它。

此外也有其他方式可指定連接字串，例如提供共用存取簽章。 若要深入了解資料來源認證，請參閱[編製 Azure Blob 儲存體的索引](search-howto-indexing-azure-blob-storage.md#Credentials)。

## <a name="create-a-jupyter-notebook"></a>建立 Jupyter Notebook

> [!Note]
> 此文章會示範如何使用一系列的 Python 指令碼建置資料來源、索引、索引子和技能集。 若要下載完整的筆記本範例，請前往 [Azure-Search-python-samples 存放庫](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment) \(英文\)。

使用 Anaconda Navigator 啟動 Jupyter Notebook，然後建立一個新的 Python 3 筆記本。

## <a name="connect-to-azure-cognitive-search"></a>連線至 Azue 認知搜尋

在筆記本中，執行此指令碼來載入處理 JSON 和制訂 HTTP 要求所使用的程式庫。

```python
import json
import requests
from pprint import pprint
```

接下來，定義資料來源、索引、索引子和技能集的名稱。 執行此指令碼來設定此教學課程的名稱。

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

> [!Tip]
> 使用免費服務時，您受限於三個索引、索引子和資料來源。 本教學課程會各建立一個。 請先確定您有空間可以建立新的物件，然後再繼續。

在下列指令碼中，取代搜尋服務 (YOUR-SEARCH-SERVICE-NAME) 和管理員 API 金鑰 (YOUR-ADMIN-API-KEY) 的預留位置，然後執行該指令碼，以設定搜尋服務端點。

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="create-a-data-source"></a>建立資料來源

現在，您的服務和來源檔案皆已備妥，您可以開始組合索引管線的元件。 首先請從資料來源物件開始；此物件會向 Azure 認知搜尋指出如何擷取外部來源資料。

在下列指令碼中，將 YOUR-BLOB-RESOURCE-CONNECTION-STRING 預留位置取代為您在上一個步驟中建立之 Blob 的連接字串。 接著，執行該指令碼以建立名為 `cogsrch-py-datasource` 的資料來源。

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "basic-demo-data-pr"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

此要求應該會傳回確認成功的狀態碼 201。

在 Azure 入口網站的搜尋服務儀表板頁面上，確認 cogsrch-py-datasource 出現在 [資料來源]  清單中。 按一下 [重新整理]  來更新頁面。

![入口網站中的資料來源圖格](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "入口網站中的資料來源圖格")

## <a name="create-a-skillset"></a>建立技能集

在此步驟中，您將會定義一組要套用至資料的擴充步驟。 我們將每個擴充步驟稱為*技能*，一組擴充步驟則稱之為*技能集*。 本教學課程會使用為技能集[內建的認知技能](cognitive-search-predefined-skills.md)：

+ [語言偵測](cognitive-search-skill-language-detection.md)，用以識別內容的語言。

+ [文字分割](cognitive-search-skill-textsplit.md)，用以在呼叫關鍵片語擷取技能之前，將大型內容分成較小的區塊。 關鍵片語擷取可接受不超過 50,000 個字元的輸入。 有些範例檔案需要進行分割，以符合這項限制。

+ [實體辨識](cognitive-search-skill-entity-recognition.md)，用以從 Blob 容器中的內容擷取組織名稱。

+ [關鍵片語擷取](cognitive-search-skill-keyphrases.md)，用以提取最高排名的關鍵片語。 

### <a name="python-script"></a>Python 指令碼
執行下列指令碼以建立稱為 `cogsrch-py-skillset` 的技能集。

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

此要求應該會傳回確認成功的狀態碼 201。

每個頁面都會套用關鍵片語擷取技能。 藉由將內容設定為 `"document/pages/*"`，將可擴充每個文件/頁面陣列成員 (文件中的每個頁面) 的這項執行。

每項技術會分別對文件的內容執行。 在處理期間，Azure 認知搜尋會萃取每份文件，以讀取不同檔案格式的內容。 在來源檔案中找到的文字會放入 `content` 欄位中，每份文件一個欄位。 因此，請將輸入設定為 `"/document/content"`。

以下顯示技能集的圖形化表示法。

![了解技能集](media/cognitive-search-tutorial-blob/skillset.png "了解技能集")

輸出可以對應至索引、作為下游技能的輸入，或在使用語言代碼時同時作為對應和輸入。 在索引中，語言代碼可用於篩選。 作為輸入時，文字分析技能會使用語言代碼指出斷字方面的語言規則。

如需技能集基本概念的詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

## <a name="create-an-index"></a>建立索引

在此節中，您會藉由指定要在可搜尋索引中包含的欄位以及設定每個欄位的搜尋屬性，來定義索引結構描述。 欄位具有類型，並且可取用決定如何使用欄位 (可搜尋、可排序等等) 的屬性。 索引中的欄位名稱不需要完全符合來源中的欄位名稱。 在後續步驟中，您可以在索引子中新增欄位對應以連接來源-目的地欄位。 針對此步驟，請使用與搜尋應用程式相關的欄位命名慣例來定義索引。

此練習會使用下列欄位和欄位類型：

| 欄位名稱： | id         | content   | languageCode | keyPhrases         | 組織     |
|--------------|----------|-------|----------|--------------------|-------------------|
| 欄位類型： | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

執行此指令碼來建立名為 `cogsrch-py-index` 的索引。

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

此要求應該會傳回確認成功的狀態碼 201。

若要深入了解如何定義索引，請參閱[建立索引 (Azure 認知搜尋 REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)。

## <a name="create-an-indexer-map-fields-and-execute-transformations"></a>建立索引子、對應欄位，並執行轉換

至此，您已建立資料來源、技能集和索引。 這三項元件構成了[索引子](search-indexer-overview.md)的一部分，可將各項資料一併提取到多階段的單一作業中。 若要在索引子中結合這些物件，您必須定義欄位對應。

+ fieldMappings 會在技能集之前進行處理，用來將資料來源中的來源欄位對應到索引中的目標欄位。 如果兩端上的欄位名稱和類型都相同，則不需要任何對應。

+ outputFieldMappings 會在技能集之後進行處理，用來參考文件萃取或擴充後才建立的 sourceFieldNames。 targetFieldName 是索引中的欄位。

除了將輸入連結至輸出外，您也可以使用欄位對應來壓平合併資料結構。 如需詳細資訊，請參閱[如何將擴充的欄位對應至可搜尋的索引](cognitive-search-output-field-mapping.md)。

執行此指令碼來建立名為 `cogsrch-py-indexer` 的索引子。

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

此要求應該很快會傳回狀態碼 201，不過，處理可能需要幾分鐘才能完成。 雖然資料集很小，但是分析技能 (例如影像分析) 會耗用大量運算資源，而且需要時間。

使用下一節中的[檢查索引子狀態](#check-indexer-status)指令碼，以判斷索引子處理何時完成。

> [!TIP]
> 建立索引子時會叫用管線。 資料的存取、輸入和輸出的對應或作業順序若有問題，都會在這個階段中出現。 若要透過程式碼或指令碼的變更重新執行管線，您可能需要先刪除物件。 如需詳細資訊，請參閱[重設並重新執行](#reset)。

#### <a name="explore-the-request-body"></a>探索要求本文

指令碼會將 `"maxFailedItems"` 設定為 -1，這會指示索引引擎在資料匯入期間忽略錯誤。 此設定有其用處，因為示範資料來源中只有少量文件。 若要有較大的資料來源，您應將值設定為大於 0。

同時也請注意組態參數中的 `"dataToExtract":"contentAndMetadata"` 陳述式。 此陳述式會指示索引子擷取不同檔案格式的內容，以及每個檔案的相關中繼資料。

在擷取內容時，您可以設定 `imageAction`，以從在資料來源中找到的影像擷取文字。 `"imageAction":"generateNormalizedImages"` 組態可與 OCR 技術和文字合併技術結合，指示索引子從影像中擷取文字 (例如，從「停」交通號誌中擷取「停」這個字)，並將其內嵌為內容欄位的一部分。 此行為適用於內嵌在文件中的影像 (例如 PDF 內的影像)，以及在資料來源中找到的影像 (例如 JPG 檔案)。

<a name="check-indexer-status"></a>

## <a name="check-indexer-status"></a>檢查索引子狀態

索引子經定義後，將會在您提交要求時自動執行。 根據您所定義的認知技能，索引編製所需的時間可能會超出您的預期。 若要了解索引子處理是否完成，請執行下列指令碼。

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

在回應中，監視 "lastResult" 中是否有 "status" 和 "endTime" 的值。 定期執行指令碼以檢查狀態。 完成索引子之後，狀態將會設為 "success"、將會指定 "endTime"，而且回應將會包含擴充期間發生的所有錯誤和警告。

![建立索引子](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "建立索引子")

某些來源檔案和技能的組合常會出現警告，這並不一定表示有問題。 在此教學課程中，警告是無害的。 例如，沒有文字的其中一個 JPEG 檔案將會在此螢幕擷取畫面中顯示警告。

![範例索引子警告](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "範例索引子警告")

## <a name="query-your-index"></a>查詢您的索引

索引編製完成後，請執行會傳回個別欄位內容的查詢。 根據預設，Azure 認知搜尋會傳回前 50 項結果。 範例資料很小，因此預設值即足堪使用。 不過，在使用較大的資料集時，您可能需要在查詢字串中加上參數，以傳回較多結果。 如需相關指示，請參閱[如何在 Azure 認知搜尋中對結果分頁](search-pagination-page-layout.md)。

在驗證步驟中，您會查詢所有欄位的索引。

```python
# Query the index for all fields
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果看起來應該會類似以下的範例。 螢幕擷取畫面只會顯示回應的一部分。

![查詢所有欄位的索引](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "查詢所有欄位的索引")

輸出將是索引結構描述，附有每個欄位的名稱、類型和屬性。

提交第二個 `"*"` 查詢，以傳回單一欄位的所有內容，例如 `organizations`。

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果看起來應該會類似以下的範例。 螢幕擷取畫面只會顯示回應的一部分。

![查詢組織內容的索引](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "查詢索引以傳回組織的內容")

對其他欄位重複前述步驟：此練習中的內容、語言程式碼、關鍵片語和組織。 您可以透過使用逗號分隔清單的 `$select` 傳回多個欄位。

您可以使用 GET 或 POST，視查詢字串的複雜度和長度而定。 如需詳細資訊，請參閱[使用 REST API 進行查詢](https://docs.microsoft.com/rest/api/searchservice/search-documents)。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重設並重新執行

在管線開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

若要使用新的定義為您的文件重新編製索引：

1. 刪除索引以移除保存的資料。 刪除索引子以在服務上重新加以建立。
2. 修改技能集和索引定義。
3. 在服務上重新建立索引和索引子以執行管線。

您可以使用入口網站來刪除索引、索引子和技能集。 當您刪除索引子時，您可以選擇性地同時刪除索引、技能集和資料來源。

![刪除搜尋物件](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "刪除入口網站中的搜尋物件")

您也可以使用指令碼刪除它們。 下列指令碼將會刪除我們建立的技能集。 您可以輕鬆地修改要求，以刪除索引、索引子和資料來源。

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

當您的程式碼成熟時，您可以精簡重建策略。 如需詳細資訊，請參閱[如何重建索引](search-howto-reindex.md)。

## <a name="takeaways"></a>重要心得

本教學課程示範了藉由建立下列元件組件來建置擴充索引管線的基本步驟：資料來源、技能集、索引和索引子。

[內建技能](cognitive-search-predefined-skills.md)已透過輸入和輸出連同技能集定義和鏈結技能的方式一起導入。 您也已了解在將管線中的擴充值路由至 Azure 認知搜尋服務上的可搜尋索引時，索引子定義中必須要有 `outputFieldMappings`。

最後，您了解如何測試結果並重設系統，以進行進一步的反覆運算。 您已了解對索引發出查詢，會傳回由擴充的索引管線建立的輸出。 此版本提供了檢視內部建構 (由系統建立的擴充文件) 的機制。 您也已了解如何檢查索引子狀態，以及在重新執行管線之前必須刪除的物件。

## <a name="clean-up-resources"></a>清除資源

在完成教學課程後，最快速的清除方式是刪除包含 Azure 認知搜尋服務和 Azure Blob 服務的資源群組。 假設您將這兩個服務放在相同群組中，刪除資源群組可永久刪除其中的所有內容，包括服務與您為此教學課程建立的任何已儲存內容。 在入口網站中，資源群組名稱位在每個服務的 [概觀] 頁面上。

## <a name="next-steps"></a>後續步驟

以自訂技能自訂或擴充管線。 建立自訂技能並將其新增至技能集，以便讓您自行撰寫的文字或影像分析上線。

> [!div class="nextstepaction"]
> [範例：建立 AI 擴充的自訂技能](cognitive-search-create-custom-skill-example.md)
