---
title: 設定快取和增量擴充（預覽）
titleSuffix: Azure Cognitive Search
description: 啟用快取並保留擴充內容的狀態，以供認知技能集中的控管處理之用。 此功能目前為公開預覽狀態。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 75b533368a01b2e98bece0751f45b12f36796658
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85556256"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中設定增量擴充的快取

> [!IMPORTANT] 
> 增量擴充目前為公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 [REST API 版本 2019-05-06-preview 和 2020-06-30-preview](search-api-preview.md)提供這項功能。 目前沒有入口網站或 .NET SDK 支援。

本文說明如何將快取新增至擴充管線，讓您可以累加地修改步驟，而不需要每次重建。 根據預設，技能集是無狀態的，而且變更其組合的任何部分，都需要完整重新執行索引子。 使用累加擴充時，索引子可以根據在技能集或索引子定義中偵測到的變更，決定檔樹狀結構的哪些部分需要重新整理。 現有處理的輸出會保留下來，並在可能的情況下重複使用。 

快取的內容會使用您提供的帳戶資訊，放在 Azure 儲存體中。 `ms-az-search-indexercache-<alpha-numerc-string>`當您執行索引子時，會建立名為的容器。 它應該被視為您的搜尋服務所管理的內部元件，不得修改。

如果您不熟悉如何設定索引子，請從[索引子總覽](search-indexer-overview.md)開始，然後繼續[技能集](cognitive-search-working-with-skillsets.md)以瞭解擴充管線。 如需重要概念的詳細背景，請參閱累加[擴充](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>在現有的索引子上啟用快取

如果您有現有的索引子，而且已經有技能集，請遵循本節中的步驟來新增快取。 您必須在單次作業中重設並重新執行索引子，增量處理才會生效。

> [!TIP]
> 作為概念證明，您可以執行此[入口網站快速入門](cognitive-search-quickstart-blob.md)來建立必要的物件，然後使用 Postman 或入口網站來進行更新。 您可能想要附加可計費的認知服務資源。 執行索引子多次會耗盡免費的每日配置，您才能完成所有步驟。

### <a name="step-1-get-the-indexer-definition"></a>步驟1：取得索引子定義

從具有下列元件的有效現有索引子開始：資料來源、技能集、索引。 您的索引子應該是可執行檔。 

使用 API 用戶端，建立[取得索引子要求](https://docs.microsoft.com/rest/api/searchservice/get-indexer)以取得索引子的目前設定。 當您使用預覽 API 版本來取得索引子時，會將 `cache` 屬性設定為 null，並加入至定義中。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

複製回應中的索引子定義。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步驟2：修改索引子定義中的 cache 屬性

根據預設，此 `cache` 屬性為 null。 使用 API 用戶端來設定快取設定（入口網站不支援此 particulate 更新）。 

修改快取物件，以包含下列必要和選擇性屬性： 

+ `storageConnectionString`是必要的，而且必須設定為 Azure 儲存體連接字串。 
+ `enableReprocessing`布林值屬性是選擇性的（ `true` 預設值），表示已啟用累加擴充。 如有需要，您可以將它設定為， `false` 以在其他需要大量資源的作業（例如編制新檔的索引）正在進行時暫停累加處理，然後再將其回復至 `true` 稍後。

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>步驟3：重設索引子

設定現有索引子的累加擴充時，需要重設索引子，以確保所有檔都處於一致的狀態。 您可以使用入口網站或 API 用戶端，以及這項工作的 [[重設索引子] REST API](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) 。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>步驟4：儲存已更新的定義

使用 PUT 要求[更新索引子](https://docs.microsoft.com/rest/api/searchservice/preview-api/update-indexer)，要求的主體應該包含已更新的索引子定義，其中包含 cache 屬性。 如果您收到400，請檢查索引子定義，確定符合所有需求（資料來源、技能集、索引）。

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

如果您現在在索引子上發出另一個 GET 要求，服務的回應將會 `ID` 在 cache 物件中包含一個屬性。 英數位元字串會附加至容器的名稱，其中包含此索引子所處理之每份檔的所有快取結果和中繼狀態。 此識別碼將用來以唯一的方式命名 Blob 儲存體中的快取。

    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }

### <a name="step-5-run-the-indexer"></a>步驟5：執行索引子

若要執行索引子，您可以使用入口網站或 API。 在入口網站的 [索引子] 清單中，選取索引子，然後按一下 [**執行**]。 使用入口網站的優點之一，是您可以監視索引子狀態、記下作業的持續時間，以及處理的檔數目。 入口網站頁面每隔幾分鐘就會重新整理一次。

或者，您可以使用 REST 來[執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

執行索引子之後，您可以在 Azure Blob 儲存體中找到快取。 容器名稱的格式如下：`ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重設並重新執行索引子會產生完整重建，以便快取內容。 所有的認知擴充都會在所有檔上重新執行。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>步驟6：修改技能集並確認增量擴充

若要修改技能集，您可以使用入口網站或 API。 例如，如果您使用文字轉譯，則從 `en` 到 `es` 或另一種語言的簡單內嵌變更就足以進行累加式擴充的概念證明測試。

再次執行索引子。 只有擴充檔樹狀結構的部分會更新。 如果您使用[入口網站快速入門](cognitive-search-quickstart-blob.md)做為概念證明，將文字翻譯技能修改為「es」，您會發現只有8份檔會更新，而不是原始的14個。 轉譯程式不影響的影像檔案會從快取重複使用。

## <a name="enable-caching-on-new-indexers"></a>在新索引子上啟用快取

若要設定新索引子的累加擴充，您只需要在 `cache` 呼叫[建立索引子（2020-06-30-Preview）](https://docs.microsoft.com/rest/api/searchservice/preview-api/create-indexer)時，將屬性包含在索引子定義裝載中。 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>檢查快取的輸出

快取是由索引子所建立、使用及管理，而且其內容不會以人類可讀取的格式表示。 判斷是否使用快取的最佳方式是執行索引子，並比較執行時間和檔計數的之前和之後的計量。 

例如，假設技能集是以影像分析和掃描檔的光學字元辨識（OCR）為開頭，接著是所產生文字的下游分析。 如果您修改下游文字技能，索引子可以從快取中取出先前處理過的所有影像和 OCR 內容，只更新和處理您的編輯所指出的文字相關變更。 您可以預期在檔計數中看到較少的檔（例如8/8，而不是原始回合中的14/14）、較短的執行時間，以及帳單上較少的費用。

## <a name="working-with-the-cache"></a>使用快取

一旦快取可運作，每次呼叫[執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer)時，索引子就會檢查快取，以查看可以使用現有輸出的哪些部分。 

下表摘要說明各種不同的 Api 與快取之間的關聯：

| API           | 快取影響     |
|---------------|------------------|
| [建立索引子（2020-06-30-預覽）](https://docs.microsoft.com/rest/api/searchservice/preview-api/create-indexer) | 在第一次使用時建立並執行索引子，包括在索引子定義指定快取時建立快取。 |
| [執行索引子](https://docs.microsoft.com/rest/api/searchservice/run-indexer) | 視需要執行擴充管線。 此 API 會讀取快取（如果有的話），如果您已將快取新增至更新的索引子定義，則會建立快取。 當您執行已啟用快取的索引子時，如果可以使用快取的輸出，則索引子會省略步驟。 您可以使用此 API 的正式推出或預覽 API 版本。|
| [重設索引子](https://docs.microsoft.com/rest/api/searchservice/reset-indexer)| 清除任何增量索引編制資訊的索引子。 下一個索引子執行（視需要或排程）會從頭開始完整重新處理，包括重新執行所有技能和重建快取。 在功能上相當於刪除索引子並重新建立它。 您可以使用此 API 的正式推出或預覽 API 版本。|
| [重設技能](https://docs.microsoft.com/rest/api/searchservice/reset-skills) | 指定在下一個索引子執行時要重新執行的技能，即使您未修改任何技能也一樣。 快取會隨之更新。 系統會根據快取中的可重複使用資料，以及每項更新技能的新內容，來重新整理輸出，例如知識存放區或搜尋索引。 |

如需控制快取發生狀況的詳細資訊，請參閱快取[管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>後續步驟

增量擴充適用于包含技能集的索引子。 在下一個步驟中，請造訪技能集檔，以瞭解概念和組合。 

此外，一旦您啟用快取，您會想要知道納入快取的參數和 Api，包括如何覆寫或強制執行特定行為。 如需詳細資訊，請參閱下列連結。

+ [技能集概念與撰寫](cognitive-search-working-with-skillsets.md)
+ [如何建立技能集](cognitive-search-defining-skillset.md)
+ [增量擴充和快取簡介](cognitive-search-incremental-indexing-conceptual.md)
