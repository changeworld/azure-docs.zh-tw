---
title: 教學課程：使用 Python 和 AI 處理 Azure Blob
titleSuffix: Azure Cognitive Search
description: 逐步解說使用 Jupyter Python Notebook 和 Azure 搜尋服務 REST API 對 Blob 儲存體中的內容進行文字擷取和自然語言處理的範例。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 09/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: ea1cc022cbea7dbf3d1fa12cb83cfe3084b28560
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92788076"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>教學課程：使用 Python 和 AI 從 Azure Blob 產生可搜尋的內容

如果您在 Azure Blob 儲存體中有非結構化的文字或影像，您可以利用 [AI 擴充管線](cognitive-search-concept-intro.md)來擷取資訊，並建立適用於全文檢索搜尋或知識採礦案例的新內容。 雖然管線可以處理影像，但此 Python 教學課程將著重於文字，套用語言偵測和自然語言處理，以建立可讓您在查詢、Facet 和篩選器中運用的新欄位。

本教學課程將使用 Python 和[搜尋 REST API](/rest/api/searchservice/) 執行下列工作：

> [!div class="checklist"]
> * 我們將從 Azure Blob 儲存體中的完整文件 (非結構化文字) 開始，例如 PDF、HTML、DOCX 和 PPTX。
> * 定義管線來擷取文字、偵測語言、辨識實體及偵測關鍵片語。
> * 定義用來儲存輸出的索引 (原始內容加上管線產生的名稱/值組)。
> * 執行管線以開始轉換和分析，以及建立和載入索引。
> * 使用全文檢索搜尋和豐富的查詢語法來探索結果。

如果您沒有 Azure 訂用帳戶，請在開始前開啟[免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>必要條件

+ [Azure 儲存體](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3.7](https://www.anaconda.com/distribution/#download-section)
+ [建立](search-create-service-portal.md)或[尋找現有的搜尋服務](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> 您可以使用免費服務來進行本教學課程。 免費的搜尋服務會有限制，您只能使用三個索引、三個索引子和三個資料來源。 本教學課程會各建立一個。 開始之前，請確定您的服務有空間可接受新的資源。

## <a name="download-files"></a>下載檔案

1. 開啟此  ，將檔案複製到您的電腦。 

1. 以滑鼠右鍵按一下 ZIP 檔案並選取 [全部解壓縮]  。 其中有 14 個不同類型的檔案。 在此練習中，您將使用 7 個檔案。

## <a name="1---create-services"></a>1 - 建立服務

本教學課程會使用 Azure 認知搜尋進行索引編製和查詢、在後端將認知服務用於 AI 擴充，以及使用 Azure Blob 儲存體來提供資料。 本教學課程在認知服務上使用的資源會保持在每個索引子每日 20 筆交易的免費配置以內，因此您需要建立的服務只有搜尋和儲存體。

為具備鄰近性和管理方面的優勢，如果可能，請將這兩項服務建立在相同的區域和資源群組中。 實際上，您的 Azure 儲存體帳戶可以位在任何區域中。

### <a name="start-with-azure-storage"></a>開始使用 Azure 儲存體

1.  。

1. 搜尋「儲存體帳戶」  ，然後選取 Microsoft 的儲存體帳戶供應項目。

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="建立儲存體帳戶" border="false":::

1. 在 [基本] 索引標籤中，需要下列項目。 接受所有其他項目的預設值。

   + **資源群組** 。 選取現有群組或建立一個新的群組，但必須對所有服務使用相同的群組，以便您一起管理這些服務。

   + **儲存體帳戶名稱** 。 如果您認為您可能會有多個相同類型的資源，請透過名稱在類型和區域上做出區別，例如 blobstoragewestus  。 

   + **位置** 。 可能的話，請選擇用於 Azure 認知搜尋和認知服務的相同位置。 單一位置可避免產生頻寬費用。

   + **帳戶種類** 。 選擇預設值 [StorageV2 (一般用途 v2)]  。

1. 按一下 [檢閱 + 建立]  以建立服務。

1. 建立後，按一下 [移至資源]  以開啟 [概觀] 頁面。

1. 按一下 [Blob]  服務。

1. 按一下 [+ 容器]  來建立容器，並將其命名為 cog-search-demo  。

1. 選取 [cog-search-demo]  ，然後按一下 [上傳]  以開啟其中儲存下載檔案的資料夾。 選取所有非影像檔案。 您應該會有 7 個檔案。 按一下 [確定]  來上傳。

   :::image type="content" source="media/cognitive-search-tutorial-blob/sample-files.png" alt-text="建立儲存體帳戶" border="false":::

1. 在您離開 Azure 儲存體之前，請取得連接字串，以便在 Azure 認知搜尋中制定連線。 

   1. 往回瀏覽到儲存體帳戶的 [概觀] 頁面 (我們使用 blobstragewestus  作為範例)。 
   
   1. 在左側導覽窗格中，選取 [存取金鑰]  並複製其中一個連接字串。 

   連接字串應為類似於下列範例的 URL：

      ```http
      DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. 將連接字串儲存到記事本。 您稍後設定資料來源連線時會用到該字串。

### <a name="cognitive-services"></a>認知服務

AI 擴充以認知服務為後盾，包括用於自然語言和影像處理的文字分析和電腦視覺。 如果您的目標是要完成實際的原型或專案，您應在此時佈建認知服務 (位於 Azure 認知搜尋所在的區域)，以便將其連結至索引作業。

由於本教學課程僅使用 7 筆交易，您可略過資源佈建，因為 Azure 認知搜尋可以連線到認知服務，並為每個索引子執行提供 20 筆免費交易。 免費配置就已足夠。 針對較大型的專案，請考慮以隨用隨付 S0 層來佈建認知服務。 如需詳細資訊，請參閱[連結認知服務](cognitive-search-attach-cognitive-services.md)。

### <a name="azure-cognitive-search"></a>Azue 認知搜尋

第三個元件是 Azure 認知搜尋，您可以[在入口網站中建立](search-create-service-portal.md)該服務。 您可以使用免費層來完成此逐步解說。 

如同 Azure Blob 儲存體，請花點時間來收集存取金鑰。 此外，當您開始結構化要求時，您必須提供用來驗證每個要求的端點和管理員 API 金鑰。

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>取得 Azure 認知搜尋的管理員 API 金鑰和 URL

1.  頁面中取得您的搜尋服務名稱。 您可藉由檢閱端點 URL 來確認您的服務名稱。 如果您的端點 URL 為 `https://mydemo.search.windows.net`，您的服務名稱會是 `mydemo`。

2. 在 [設定]  >  [金鑰]  中，取得服務上完整權限的管理金鑰。 可互換的管理金鑰有兩個，可在您需要變換金鑰時提供商務持續性。 您可以在新增、修改及刪除物件的要求上使用主要或次要金鑰。

   一併取得查詢金鑰。 最佳做法是發出具有唯讀存取權的查詢要求。

   :::image type="content" source="media/search-get-started-javascript/service-name-and-keys.png" alt-text="建立儲存體帳戶" border="false":::

在傳送至您服務的每個要求的標頭中都需要有 api-key。 有效的金鑰能為每個要求在傳送要求之應用程式與處理要求的服務間建立信任。

## <a name="2---start-a-notebook"></a>2 - 啟動 Notebook

依照下列指示建立 Notebook，或從 [Azure-Search-python-samples 存放庫](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)下載已完成的 Notebook。

使用 Anaconda Navigator 啟動 Jupyter Notebook，然後建立一個新的 Python 3 筆記本。

在筆記本中，執行此指令碼來載入處理 JSON 和制訂 HTTP 要求所使用的程式庫。

```python
import json
import requests
from pprint import pprint
```

在相同的 Notebook 中，定義資料來源、索引、索引子和技能集的名稱。 執行此指令碼來設定此教學課程的名稱。

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

在下列指令碼中，取代搜尋服務 (YOUR-SEARCH-SERVICE-NAME) 和管理員 API 金鑰 (YOUR-ADMIN-API-KEY) 的預留位置，然後執行該指令碼，以設定搜尋服務端點。

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2020-06-30'
}
```

## <a name="3---create-the-pipeline"></a>3 - 建立管線

在 Azure 認知搜尋中，AI 處理會在編製索引 (或資料擷取) 期間進行。 逐步解說的這個部分會建立四個物件：資料來源、索引定義、技能集、索引子。 

### <a name="step-1-create-a-data-source"></a>步驟 1:建立資料來源

[資料來源物件](/rest/api/searchservice/create-data-source)會對包含檔案的 Blob 容器提供連接字串。

在下列指令碼中，將 YOUR-BLOB-RESOURCE-CONNECTION-STRING 預留位置取代為您在上一個步驟中建立之 Blob 的連接字串。 取代容器的預留位置文字。 接著，執行該指令碼以建立名為 `cogsrch-py-datasource` 的資料來源。

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
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

此要求應該會傳回確認成功的狀態碼 201。

在 Azure 入口網站的搜尋服務儀表板頁面上，確認 cogsrch-py-datasource 出現在 [資料來源]  清單中。 按一下 [重新整理]  來更新頁面。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-data-source-tile.png" alt-text="建立儲存體帳戶" border="false":::

### <a name="step-2-create-a-skillset"></a>步驟 2:建立技能集

在此步驟中，您將會定義一組要套用至資料的擴充步驟。 我們將每個擴充步驟稱為 *技能* ，一組擴充步驟則稱之為 *技能集* 。 本教學課程會使用為技能集[內建的認知技能](cognitive-search-predefined-skills.md)：

+ [實體辨識](cognitive-search-skill-entity-recognition.md)，用以從 Blob 容器中的內容擷取組織名稱。

+ [語言偵測](cognitive-search-skill-language-detection.md)，用以識別內容的語言。

+ [文字分割](cognitive-search-skill-textsplit.md)，用以在呼叫關鍵片語擷取技能之前，將大型內容分成較小的區塊。 關鍵片語擷取可接受不超過 50,000 個字元的輸入。 有些範例檔案需要進行分割，以符合這項限制。

+ [關鍵片語擷取](cognitive-search-skill-keyphrases.md)，用以提取最高排名的關鍵片語。 

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
                    "name": "text", 
                    "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", 
                    "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", 
                    "source": "/document/content"
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
                    "name": "text", 
                    "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", 
                    "source": "/document/languageCode"
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

:::image type="content" source="media/cognitive-search-tutorial-blob/skillset.png" alt-text="建立儲存體帳戶" border="false":::

輸出可以對應至索引、作為下游技能的輸入，或在使用語言代碼時同時作為對應和輸入。 在索引中，語言代碼可用於篩選。 作為輸入時，文字分析技能會使用語言代碼指出斷字方面的語言規則。

如需技能集基本概念的詳細資訊，請參閱[如何定義技能集](cognitive-search-defining-skillset.md)。

### <a name="step-3-create-an-index"></a>步驟 3：建立索引

在此節中，您會藉由指定要在可搜尋索引中包含的欄位以及設定每個欄位的搜尋屬性，來定義索引結構描述。 欄位具有類型，並且可取用決定如何使用欄位 (可搜尋、可排序等等) 的屬性。 索引中的欄位名稱不需要完全符合來源中的欄位名稱。 在後續步驟中，您可以在索引子中新增欄位對應以連接來源-目的地欄位。 針對此步驟，請使用與搜尋應用程式相關的欄位命名慣例來定義索引。

此練習會使用下列欄位和欄位類型：

| 欄位名稱： | 識別碼         | content   | languageCode | keyPhrases         | 組織     |
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

若要深入了解如何定義索引，請參閱[建立索引 (Azure 認知搜尋 REST API)](/rest/api/searchservice/create-index)。

### <a name="step-4-create-and-run-an-indexer"></a>步驟 4：建立及執行索引子

[索引子](/rest/api/searchservice/create-indexer)會驅動管線。 到目前為止所建立的三個元件 (資料來源、技能集、索引) 都是索引子的輸入。 在 Azure 認知搜尋上建立索引子，是用以啟動整個管線的事件。 

若要在索引子中結合這些物件，您必須定義欄位對應。

+ `"fieldMappings"` 會在技能集之前進行處理，用來將資料來源中的來源欄位對應到索引中的目標欄位。 如果兩端上的欄位名稱和類型都相同，則不需要任何對應。

+ `"outputFieldMappings"` 會在技能集之後進行處理，用來參考文件萃取或擴充後才建立的 `"sourceFieldNames"`。 `"targetFieldName"` 是索引中的欄位。

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

此要求應會在短時間內傳回狀態碼 201，但處理可能需要幾分鐘才能完成。 雖然資料集很小，但是分析技能 (例如影像分析) 會耗用大量運算資源，而且需要時間。

您可以[監視索引子狀態](#check-indexer-status)，以判斷索引子於何時執行或完成。

> [!TIP]
> 建立索引子時會叫用管線。 資料的存取、輸入和輸出的對應或作業順序若有問題，都會在這個階段中出現。 若要透過程式碼或指令碼的變更重新執行管線，您可能需要先刪除物件。 如需詳細資訊，請參閱[重設並重新執行](#reset)。

#### <a name="about-the-request-body"></a>關於要求本文

指令碼會將 `"maxFailedItems"` 設定為 -1，這會指示索引引擎在資料匯入期間忽略錯誤。 此設定有其用處，因為示範資料來源中只有少量文件。 若要有較大的資料來源，您應將值設定為大於 0。

同時也請注意組態參數中的 `"dataToExtract":"contentAndMetadata"` 陳述式。 此陳述式會指示索引子擷取不同檔案格式的內容，以及每個檔案的相關中繼資料。

在擷取內容時，您可以設定 `imageAction`，以從在資料來源中找到的影像擷取文字。 `"imageAction":"generateNormalizedImages"` 組態可與 OCR 技術和文字合併技術結合，指示索引子從影像中擷取文字 (例如，從「停」交通號誌中擷取「停」這個字)，並將其內嵌為內容欄位的一部分。 此行為適用於內嵌在文件中的影像 (例如 PDF 內的影像)，以及在資料來源中找到的影像 (例如 JPG 檔案)。

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 - 監視編製索引

索引子經定義後，將會在您提交要求時自動執行。 根據您所定義的認知技能，索引編製所需的時間可能會超出您的預期。 若要了解索引子處理是否完成，請執行下列指令碼。

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

在回應中，監視 `"lastResult"` 的 `"status"` 和 `"endTime"` 值。 定期執行指令碼以檢查狀態。 完成索引子之後，狀態將會設為 "success"、將會指定 "endTime"，而且回應將會包含擴充期間發生的所有錯誤和警告。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png" alt-text="建立儲存體帳戶" border="false":::

某些來源檔案和技能的組合常會出現警告，這並不一定表示有問題。 許多警告都是良性的。 例如，如果您為不含文字的 JPEG 檔案編製索引，則會在此螢幕擷取畫面中看到警告。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png" alt-text="建立儲存體帳戶" border="false":::

## <a name="5---search"></a>5 - 搜尋

索引編製完成後，請執行會傳回個別欄位內容的查詢。 根據預設，Azure 認知搜尋會傳回前 50 項結果。 範例資料很小，因此預設值即足堪使用。 不過，在使用較大的資料集時，您可能需要在查詢字串中加上參數，以傳回較多結果。 如需相關指示，請參閱[如何在 Azure 認知搜尋中對結果分頁](search-pagination-page-layout.md)。

在驗證步驟中，取得會顯示所有欄位的索引定義。

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果看起來應該會類似以下的範例。 螢幕擷取畫面只會顯示回應的一部分。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png" alt-text="建立儲存體帳戶" border="false":::

輸出將是索引結構描述，附有每個欄位的名稱、類型和屬性。

提交第二個 `"*"` 查詢，以傳回單一欄位的所有內容，例如 `organizations`。

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

結果看起來應該會類似以下的範例。 螢幕擷取畫面只會顯示回應的一部分。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png" alt-text="建立儲存體帳戶" border="false":::

針對其他欄位重複執行：此練習中的 `content`、`languageCode`、`keyPhrases` 和 `organizations`。 您可以透過使用逗號分隔清單的 `$select` 傳回多個欄位。

您可以使用 GET 或 POST，視查詢字串的複雜度和長度而定。 如需詳細資訊，請參閱[使用 REST API 進行查詢](/rest/api/searchservice/search-documents)。

<a name="reset"></a>

## <a name="reset-and-rerun"></a>重設並重新執行

在開發的早期實驗階段中若要設計反覆項目，最實用的方法是從 Azure 認知搜尋中刪除物件，並讓您的程式碼加以重建。 資源名稱是唯一的。 刪除物件可讓您使用相同的名稱加以重新建立。

您可以使用入口網站來刪除索引、索引子、資料來源和技能集。 當您刪除索引子時，您可以選擇性地同時刪除索引、技能集和資料來源。

:::image type="content" source="media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png" alt-text="建立儲存體帳戶" border="false":::

您也可以使用指令碼刪除它們。 下列指令碼說明如何刪除技能集。 

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

成功刪除時會傳回狀態碼 204。

## <a name="takeaways"></a>重要心得

本教學課程示範了藉由建立下列元件組件來建置擴充索引管線的基本步驟：資料來源、技能集、索引和索引子。

[內建技能](cognitive-search-predefined-skills.md)已透過輸入和輸出連同技能集定義和鏈結技能的方式一起導入。 您也已了解在將管線中的擴充值路由至 Azure 認知搜尋服務上的可搜尋索引時，索引子定義中必須要有 `outputFieldMappings`。

最後，您了解如何測試結果並重設系統，以進行進一步的反覆運算。 您已了解對索引發出查詢，會傳回由擴充的索引管線建立的輸出。 此版本提供了檢視內部建構 (由系統建立的擴充文件) 的機制。 您也已了解如何檢查索引子狀態，以及在重新執行管線之前必須刪除的物件。

## <a name="clean-up-resources"></a>清除資源

如果您使用自己的訂用帳戶，當專案結束時，建議您移除不再需要的資源。 讓資源繼續執行可能會產生費用。 您可以個別刪除資源，或刪除資源群組以刪除整組資源。

您可以使用左導覽窗格中的 [所有資源] 或 [資源群組] 連結，在入口網站中尋找和管理資源。

## <a name="next-steps"></a>後續步驟

現在您已熟悉 AI 擴充管線中的所有物件，接下來我們將進一步了解技能集定義和個別技能。

> [!div class="nextstepaction"]
> [如何建立技能集](cognitive-search-defining-skillset.md)