---
title: '設定快取和累加擴充 (預覽) '
titleSuffix: Azure Cognitive Search
description: 啟用快取和保留擴充內容的狀態，以在認知技能集中進行受控制的處理。 此功能目前為公開預覽狀態。
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90971622"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>如何在 Azure 認知搜尋中設定累加式擴充的快取

> [!IMPORTANT] 
> 增量擴充目前處於公開預覽狀態。 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。 
> [REST API 預覽版本](search-api-preview.md) 提供此功能。 目前沒有入口網站或 .NET SDK 支援。

本文說明如何將快取新增至擴充管線，讓您可以在不需要每次重建的情況下，以累加方式修改步驟。 根據預設，技能集是無狀態的，而且變更其任何組合的任何部分，都需要完整重新執行索引子。 利用累加式擴充，索引子可以根據在技能集或索引子定義中偵測到的變更，判斷要重新整理檔樹狀結構的哪些部分。 現有已處理的輸出會盡可能保留並重複使用。 

使用您提供的帳戶資訊，將快取的內容放在 Azure 儲存體中。 `ms-az-search-indexercache-<alpha-numerc-string>`當您執行索引子時，會建立名為的容器。 它應該被視為您的搜尋服務所管理的內部元件，而且不得修改。

如果您不熟悉如何設定索引子，請從 [索引子總覽](search-indexer-overview.md) 開始，然後繼續進行 [技能集](cognitive-search-working-with-skillsets.md) ，以瞭解擴充管線。 如需重要概念的詳細背景，請參閱 [增量擴充](cognitive-search-incremental-indexing-conceptual.md)。

## <a name="enable-caching-on-an-existing-indexer"></a>在現有的索引子上啟用快取

如果您有現有的索引子已經有技能集，請遵循本節中的步驟來新增快取。 在單次作業中，您必須先重設並重新執行索引子，然後累加式處理才會生效。

> [!TIP]
> 概念證明，您可以透過這個 [入口網站快速入門](cognitive-search-quickstart-blob.md) 來建立必要的物件，然後使用 Postman 或入口網站進行更新。 您可能會想要附加可計費的認知服務資源。 多次執行索引子會耗盡免費的每日配置，您才能完成所有步驟。

### <a name="step-1-get-the-indexer-definition"></a>步驟1：取得索引子定義

從具有下列元件的有效現有索引子開始：資料來源、技能集、索引。 您的索引子應可執行。 

使用 API 用戶端，建立 [取得索引子要求](/rest/api/searchservice/get-indexer) 以取得目前的索引子設定。 當您使用預覽 API 版本取得索引子時， `cache` 會將設定為 null 的屬性加入定義中。

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

從回應複製索引子定義。

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>步驟2：修改索引子定義中的快取屬性

依預設，此 `cache` 屬性為 null。 使用 API 用戶端設定快取設定 (入口網站不支援此 particulate 更新) 。 

修改快取物件，以包含下列必要和選擇性屬性： 

+ `storageConnectionString`是必要的，而且必須設定為 Azure 儲存體連接字串。 
+ `enableReprocessing`根據預設，布林值屬性是選擇性的 (`true`) ，它表示累加擴充已啟用。 如有需要，您可以將它設定為， `false` 以在其他需要大量資源的作業（例如編制新檔的索引）正在進行時暫停累加處理，然後將其回復回 `true` 稍後。

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

為現有的索引子設定累加式擴充，以確保所有檔都處於一致狀態時，需要重設索引子。 您可以使用入口網站或 API 用戶端，以及這項工作的 [重設索引子 REST API](/rest/api/searchservice/reset-indexer) 。

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>步驟4：儲存更新的定義

使用 PUT 要求來[更新索引子](/rest/api/searchservice/preview-api/update-indexer)，要求的主體應該包含具有快取屬性的已更新索引子定義。 如果您收到400，請檢查索引子定義，以確保符合所有需求， (資料來源、技能集、索引) 。

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

如果您現在在索引子上發出另一個 GET 要求，服務的回應將會 `ID` 在 cache 物件中包含屬性。 英數位元字串會附加至容器名稱，其中包含此索引子所處理之每份檔的所有快取結果和中繼狀態。 此識別碼將用來唯一命名 Blob 儲存體中的快取。

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>步驟5：執行索引子

若要執行索引子，您可以使用入口網站或 API。 在入口網站中，從 [索引子] 清單中選取索引子，然後按一下 [ **執行**]。 使用入口網站的其中一個優點是您可以監視索引子狀態、記下作業的持續時間，以及處理多少份檔。 入口網站頁面會每隔幾分鐘重新整理一次。

或者，您可以使用 REST 來 [執行索引子](/rest/api/searchservice/run-indexer)：

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

在索引子執行之後，您可以在 Azure Blob 儲存體中找到快取。 容器名稱的格式如下： `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> 重設並重新執行索引子會產生完整重建，以便快取內容。 所有的認知擴充都將在所有檔上重新執行。
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>步驟6：修改技能集並確認增量擴充

若要修改技能集，您可以使用入口網站或 API。 例如，如果您使用文字翻譯，則從或其他語言進行的簡單內嵌變更， `en` `es` 就足以進行累加擴充的概念證明測試。

重新執行索引子。 只有擴充檔樹狀目錄的部分會更新。 如果您使用 [入口網站快速入門](cognitive-search-quickstart-blob.md) 作為概念證明，將文字翻譯技能修改為 ' es '，您將會注意到只有8份檔會更新，而不是原始的14個。 轉譯程式不受影響的影像檔案會從快取中重複使用。

## <a name="enable-caching-on-new-indexers"></a>在新的索引子上啟用快取

若要設定新索引子的累加式擴充，您只需要在 `cache` 呼叫 Create 索引子時，將屬性包含在索引子定義承載中（ [ (2020-06-30-Preview) ](/rest/api/searchservice/preview-api/create-indexer)）。 


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

快取是由索引子建立、使用及管理，而其內容不是以人類可讀取的格式表示。 若要判斷是否使用快取，最好的方式是執行索引子，並比較執行時間與檔計數之前和之後的度量。 

例如，假設技能集是以影像分析和光學字元辨識為開頭， (OCR) 掃描的檔，接著是所產生文字的下游分析。 如果您修改下游文字技能，索引子可以從快取中取出所有先前處理過的影像和 OCR 內容，並只更新和處理您的編輯所表示的文字相關變更。 您可以預期會在檔計數中看到較少的檔 (例如8/8，而不是原始執行) 中的14/14、縮短執行時間，以及帳單上較少的費用。

## <a name="working-with-the-cache"></a>使用快取

一旦快取可運作，索引子會在每次呼叫 [執行索引子](/rest/api/searchservice/run-indexer) 時檢查快取，以查看可以使用現有輸出的哪些部分。 

下表摘要說明各種 Api 與快取的關聯性：

| API           | 快取影響     |
|---------------|------------------|
| [建立索引子 (2020-06-30-預覽) ](/rest/api/searchservice/preview-api/create-indexer) | 會在第一次使用時建立並執行索引子，包括在索引子定義指定時建立快取。 |
| [執行索引子](/rest/api/searchservice/run-indexer) | 視需要執行擴充管線。 此 API 會從快取讀取（如果有的話），如果您將快取新增至更新的索引子定義，則會建立快取。 當您執行已啟用快取的索引子時，索引子會在使用快取輸出時省略步驟。 您可以使用此 API 的正式推出或預覽 API 版本。|
| [重設索引子](/rest/api/searchservice/reset-indexer)| 清除任何累加式索引編制資訊的索引子。 下一個索引子執行 (視需要或排程) 從頭開始完整重新處理，包括重新執行所有技能和重建快取。 它在功能上相當於刪除並重新建立索引子。 您可以使用此 API 的正式推出或預覽 API 版本。|
| [重設技能](/rest/api/searchservice/preview-api/reset-skills) | 指定在下一次索引子執行時要重新執行的技能，即使您還沒有修改任何技能也一樣。 快取會隨之更新。 系統會使用快取中可重複使用的資料，以及每個更新技能的新內容，來重新整理輸出，例如知識存放區或搜尋索引。 |

如需控制快取會發生什麼事的詳細資訊，請參閱快取 [管理](cognitive-search-incremental-indexing-conceptual.md#cache-management)。

## <a name="next-steps"></a>後續步驟

增量擴充適用于包含技能集的索引子。 在下一個步驟中，請造訪技能集檔以瞭解概念和組合。 

此外，當您啟用快取之後，您會想要知道可納入快取的參數和 Api，包括如何覆寫或強制執行特定行為。 如需詳細資訊，請參閱下列連結。

+ [技能集概念和組合](cognitive-search-working-with-skillsets.md)
+ [如何建立技能集](cognitive-search-defining-skillset.md)
+ [增量擴充和快取簡介](cognitive-search-incremental-indexing-conceptual.md)