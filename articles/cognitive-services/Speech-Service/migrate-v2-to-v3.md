---
title: 從 v2 遷移至 v3 REST API-語音服務
titleSuffix: Azure Cognitive Services
description: 相較于 v2，新的 API 版本 v3 包含一組較小的中斷性變更。 這份檔可協助您在不需要時遷移至新的主要版本。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1dae963781cc0caacc25938e700a4c70a1f51a
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/05/2020
ms.locfileid: "96737971"
---
# <a name="migration-from-v20-to-v30-of-speech-to-text-rest-api"></a>從 v2.0 遷移至 v3.0 的語音轉換文字 REST API

第3版的語音 REST API 可改善舊版 API 的可靠性和易用性。 API 版面配置會與其他 Azure 或認知服務 API 更緊密地對齊。 這可協助您在使用我們的語音 API 時，運用現有的技能。

API 總覽以 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)的形式提供。 這非常適合提供您的 API 總覽，並測試新的 API。

我們正在提供 c # 和 Python 的範例。 針對 batch 轉譯，您會在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) 中的子目錄內找到範例 `samples/batch` 。

## <a name="forward-compatibility"></a>往後相容性

為了確保順利遷移至 v3，v2 中的所有實體也可以在 v3 API 中的相同身分識別下找到。 如果有結果架構變更 (例如，轉譯) ，則在 v3 版本的 API 中 GET 的回應將會在 v3 架構中。 如果您在第2版的 API 中執行 GET，則結果架構會是 v2 格式。 第2版 **無法** 使用 v3 上新建立的實體。

## <a name="breaking-changes"></a>重大變更

中斷性變更清單已依調整所需的變更量來排序。 只有一些變更需要在呼叫程式碼中進行非簡單的變更。 大部分的變更都需要簡單的重新命名。 小組從 v2 遷移至 v3 所花費的時間，會在幾個小時到數天之間變化。 不過，增加穩定性、更簡單程式碼的優點、更快速的回應會加速投資。 

### <a name="host-name-changes"></a>主機名稱變更

主機名稱已從 {region} cri 至 {region} 變更為 {region}。 在這項變更中，路徑不會再包含 "api/"，因為它是主機名稱的一部分。 如需區域和路徑的完整說明，請參閱 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) 。

### <a name="identity-of-an-entity"></a>實體的身分識別

屬性 `id` 已被取代 `self` 。 在 v2 中，API 使用者必須知道如何建立 API 上的路徑。 這是不具擴充性且需要的使用者不必要的工作。 屬性 `id` (uuid) 會取代 `self` 為 (字串) ，也就是實體 (URL) 的位置。 在您所有的實體之間，該值仍是唯一的。 如果在 `id` 您的程式碼中儲存為字串，簡單的重新命名就足以支援新的架構。 您現在可以使用 `self` 內容做為實體的所有 REST 呼叫的 URL， (取得、修補、刪除) 。

如果實體在其他路徑下有其他可用的功能，則會列在底下 `links` 。 轉譯是一個很好的例子，它對轉譯的內容有不同的方法 `GET` 。

v2 轉譯：

```json
{
    "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US",
    "name": "Transcription using locale en-US"
}
```

v3 轉譯：

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "createdDateTime": "2019-01-07T11:34:12Z",
    "lastActionDateTime": "2019-01-07T11:36:07Z",
    "status": "Succeeded",
    "locale": "en-US", 
    "displayName": "Transcription using locale en-US",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    }
}
```

視您的用戶端執行而定，可能不足以重新命名屬性。 建議您利用，並使用的傳回值做 `self` `links` 為 rest 呼叫的目標 url，而不是在用戶端中產生路徑。 藉由使用傳回的 Url，您可以確定路徑中的未來變更將不會中斷您的用戶端程式代碼。

### <a name="working-with-collections-of-entities"></a>使用實體集合

先前 v2 API 在回應中傳回所有可用的實體。 為了讓您更精細地控制預期的回應大小，在 v3 中，集合的所有回應都會編頁。 您可以控制傳回的實體計數和頁面的位移。 此行為可讓您輕鬆地預測回應處理器的執行時間，並與其他 Azure Api 保持一致。

所有集合的回應的基本圖形都相同：

```json
{
    "values": [
        {     
        }
    ],
    "@nextLink": "https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/{collection}?skip=100&top=100"
}
```

屬性 `values` 包含可用集合實體的子集。 您可以使用查詢參數和來控制計數和位移 `skip` `top` 。 當不 `@nextLink` 是 null 時，有更多可用的資料，而且可以藉由執行 GET 來抓取下一個批次的資料 `$.@nextLink` 。

這項變更需要 `GET` 在傳回所有元素之前，在迴圈中呼叫集合的。

### <a name="creating-transcriptions"></a>建立轉譯

有關如何建立轉譯的詳細說明，可以在 [批次](./batch-transcription.md)轉譯操作說明中找到。

V3 中的建立轉譯已變更，以明確地設定特定轉譯選項。 所有 (選用的) 設定屬性現在都可以在屬性中設定 `properties` 。
此外，版本 v3 現在支援多個輸入檔案，因此需要 Url 清單，而不是 v2 所要求的單一 URL。 屬性名稱已從重新命名 `recordingsUrl` 為 `contentUrls` 。 在轉譯中分析情感的功能已在 v3 上移除。 建議改用 Microsoft 認知服務 [文字分析](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 。

下的新 `timeToLive` 屬性 `properties` 可協助您剪除現有的已完成實體。 `timeToLive`指定將自動刪除已完成實體的持續時間。 將它設定為較高的值 (例如 `PT12H`) 當實體持續追蹤、取用及刪除時，通常會在經過12小時之前處理。

v2 轉譯 POST 要求主體：

```json
{
  "locale": "en-US",
  "name": "Transcription using locale en-US",
  "recordingsUrl": "https://contoso.com/mystoragelocation",
  "properties": {
    "AddDiarization": "False",
    "AddWordLevelTimestamps": "False",
    "PunctuationMode": "DictatedAndAutomatic",
    "ProfanityFilterMode": "Masked"
  }
}
```

v3 轉譯 POST 要求主體：

```json
{
  "locale": "en-US",
  "displayName": "Transcription using locale en-US",
  "contentUrls": [
    "https://contoso.com/mystoragelocation",
    "https://contoso.com/myotherstoragelocation"
  ],
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  }
}
```

### <a name="format-of-v3-transcription-results"></a>V3 轉譯結果的格式

轉譯結果的架構已稍微變更，以配合即時端點所建立的轉譯。 您可以在 [批次](./batch-transcription.md)轉譯操作說明中找到新格式的深入說明。 在的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) 中，會發佈結果的架構 `samples/batch/transcriptionresult_v3.schema.json` 。

屬性名稱現在的格式為小寫，通道和說話者的值則使用整數類型。 為了將持續時間的格式與其他 Azure Api 保持一致，它現在的格式如下： ISO 8601 所述。

V3 轉譯結果的範例。 這些差異描述于批註中。

```json
{
  "source": "...",                      // (new in v3) was AudioFileName / AudioFileUrl
  "timestamp": "2020-06-16T09:30:21Z",  // (new in v3)
  "durationInTicks": 41200000,          // (new in v3) was AudioLengthInSeconds
  "duration": "PT4.12S",                // (new in v3)
  "combinedRecognizedPhrases": [        // (new in v3) was CombinedResults
    {
      "channel": 0,                     // (new in v3) was ChannelNumber
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // (new in v3) was SegmentResults
    {
      "recognitionStatus": "Success",   // 
      "channel": 0,                     // (new in v3) was ChannelNumber
      "offset": "PT0.07S",              // (new in v3) new format, was OffsetInSeconds
      "duration": "PT1.59S",            // (new in v3) new format, was DurationInSeconds
      "offsetInTicks": 700000.0,        // (new in v3) was Offset
      "durationInTicks": 15900000.0,    // (new in v3) was Duration

      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,phrase
          "speaker": 1,
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",

          "words": [
            {
              "word": "hello",
              "offset": "PT0.09S",
              "duration": "PT0.48S",
              "offsetInTicks": 900000.0,
              "durationInTicks": 4800000.0,
              "confidence": 0.987572
            },
            {
              "word": "world",
              "offset": "PT0.59S",
              "duration": "PT0.16S",
              "offsetInTicks": 5900000.0,
              "durationInTicks": 1600000.0,
              "confidence": 0.906032
            }
          ]
        }
      ]
    }
  ]
}
```

### <a name="getting-the-content-of-entities-and-the-results"></a>取得實體的內容和結果

在 v2 中，輸入或結果檔案的連結已與其余的實體中繼資料一起內嵌。 在 v3 的改進中，實體中繼資料（由 GET 所傳回） `$.self` 和詳細資料和認證（用以存取結果檔）之間有清楚的分隔。 這種隔離有助於保護客戶資料，並可讓您更精確地控制認證的有效期間。

在 v3 中， `files` 如果實體公開資料 (資料集、轉譯、端點、評估) ，則會在連結下呼叫屬性。 GET 會傳回檔案 `$.links.files` 清單和 SAS URL，以存取每個檔案的內容。 若要控制 SAS Url 的有效期間，查詢參數 `sasValidityInSeconds` 可以用來指定存留期。

v2 轉譯：

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "status": "Succeeded",
  "reportFileUrl": "https://contoso.com/report.txt?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f728327c53b5",
  "resultsUrls": {
    "channel_0": "https://contoso.com/audiofile1.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=6c044930-3926-4be4-be76-f72832e6600c",
    "channel_1": "https://contoso.com/audiofile2.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=3e0163f1-0029-4d4a-988d-3fba7d7c53b5"
  }
}
```

v3 轉譯：

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

然後，GET `$.links.files` 會導致：

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/f23e54f5-ed74-4c31-9730-2f1a3ef83ce8",
      "name": "Name",
      "kind": "Transcription",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/mywavefile1.wav.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    },
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files/28bc946b-c251-4a86-84f6-ea0f0a2373ef",
      "name": "Name",
      "kind": "TranscriptionReport",
      "properties": {
        "size": 200
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/report.json?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files?skip=2&top=2"
}
```

`kind`表示檔案內容的格式。 針對轉譯，類型的檔案 `TranscriptionReport` 是作業的摘要，而類型的檔案則 `Transcription` 是作業本身的結果。

### <a name="customizing-models"></a>自訂模型

在 v3 之前，定型模型時，「聲場模型」和「語言模型」之間有一些差異。 這項區別會導致在建立端點或轉譯時，必須指定多個模型。 為了簡化呼叫者的這個程式，我們移除了差異，並使所有相依于用於模型定型的資料集內容。 透過這種變更，模型建立現在支援混合的資料集 (語言資料和聲場資料) 。 端點和轉譯現在只需要一個模型。

有了這項變更， `kind` 就會移除 POST 中的需求，而且 `datasets[]` 現在可以包含相同或混合類型的多個資料集。

為了改善定型模型的結果，系統會自動在內部使用聲場資料來進行語言定型。 一般情況下，透過 v3 API 建立的模型會比使用 v2 API 建立的模型提供更精確的結果。

### <a name="retrieving-base-and-custom-models"></a>正在抓取基底和自訂模型

為了簡化取得可用的模型，v3 已將「基底模型」的集合與客戶擁有的「自訂模型」區隔開。 這兩個路由現在是 `GET /speechtotext/v3.0/models/base` 和 `GET /speechtotext/v3.0/models/` 。

先前所有模型都已在單一回應中一起傳回。

### <a name="name-of-an-entity"></a>實體的名稱

屬性名稱 `name` 已重新命名為 `displayName` 。 這會與其他 Azure Api 對齊，而不會指出身分識別屬性。 這個屬性的值不得重複，而且可以在建立實體之後變更 `PATCH` 。

v2 轉譯：

```json
{
    "name": "Transcription using locale en-US"
}
```

v3 轉譯：

```json
{
    "displayName": "Transcription using locale en-US"
}
```

### <a name="accessing-referenced-entities"></a>存取參考的實體

在 v2 中，參考的實體一律已內嵌，例如端點使用的模型。 實體的嵌套會導致大型回應，而且取用者很少耗用嵌套的內容。 為了縮減回應大小並改善所有 API 使用者的效能，參考的實體不再內嵌于回應中。 改為使用其他實體的參考，您可以直接使用它。 這個參考可以用於後續的 (也就是 `GET` URL，也) ，遵循與連結相同的模式 `self` 。

v2 轉譯：

```json
{
  "id": "9891c965-bb32-4880-b14b-6d44efb158f3",
  "models": [
    {
      "id": "827712a5-f942-4997-91c3-7c6cde35600b",
      "modelKind": "Language",
      "lastActionDateTime": "2019-01-07T11:36:07Z",
      "status": "Running",
      "createdDateTime": "2019-01-07T11:34:12Z",
      "locale": "en-US",
      "name": "Acoustic model",
      "description": "Example for an acoustic model",
      "datasets": [
        {
          "id": "702d913a-8ba6-4f66-ad5c-897400b081fb",
          "dataImportKind": "Language",
          "lastActionDateTime": "2019-01-07T11:36:07Z",
          "status": "Succeeded",
          "createdDateTime": "2019-01-07T11:34:12Z",
          "locale": "en-US",
          "name": "Language dataset",
          
        }
      ]
    },
  ]
}
```

v3 轉譯：

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

如果您需要使用參考模型的詳細資料（如上述範例所示），請簡化 GET 的問題 `$.model.self` 。

### <a name="retrieving-endpoint-logs"></a>正在抓取端點記錄

服務的 v2 版本支援記錄端點的回應。 若要使用 v2 取出端點的結果，您必須建立「資料匯出」，以代表時間範圍所定義的結果快照。 匯出資料批次的程式會變成非彈性。 V3 API 會提供每個個別檔案的存取權，並允許透過這些檔案進行反復專案。

已成功執行 v3 端點：

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

GET 的回應 `$.links.logs` ：

```json
{
  "values": [
    {
      "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/6d72ad7e-f286-4a6f-b81b-a0532ca6bcaa/files/logs/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "name": "2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav",
      "kind": "Audio",
      "properties": {
        "size": 12345
      },
      "createdDateTime": "2020-01-13T08:00:00Z",
      "links": {
        "contentUrl": "https://customspeech-usw.blob.core.windows.net/artifacts/2019-09-20_080000_3b5f4628-e225-439d-bd27-8804f9eed13f.wav?st=2018-02-09T18%3A07%3A00Z&se=2018-02-10T18%3A07%3A00Z&sp=rl&sv=2017-04-17&sr=b&sig=e05d8d56-9675-448b-820c-4318ae64c8d5"
      }
    }    
  ],
  "@nextLink": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs?top=2&SkipToken=2!188!MDAwMDk1ITZhMjhiMDllLTg0MDYtNDViMi1hMGRkLWFlNzRlOGRhZWJkNi8yMDIwLTA0LTAxLzEyNDY0M182MzI5NGRkMi1mZGYzLTRhZmEtOTA0NC1mODU5ZTcxOWJiYzYud2F2ITAwMDAyOCE5OTk5LTEyLTMxVDIzOjU5OjU5Ljk5OTk5OTlaIQ--"
}
```

端點記錄的分頁運作方式類似于其他所有集合，但不能指定位移。 由於有大量可用的資料，因此必須執行伺服器驅動的分頁。

在 v3 中，每個端點記錄檔都可以藉由在檔案的上發出刪除 `self` ，或是在上使用 delete 來個別刪除 `$.links.logs` 。 若要指定結束資料，可將查詢參數 `endDate` 加入至要求。

### <a name="using-custom-properties"></a>使用「自訂」屬性

為了將自訂屬性與選擇性的設定屬性分開，所有明確命名的屬性現在都位於屬性中， `properties` 而定義呼叫端的所有屬性現在都位於 `customProperties` 屬性中。

v2 轉譯實體

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

v3 轉譯實體

```json
{
  "properties": {
    "diarizationEnabled": false,
    "wordLevelTimestampsEnabled": false 
  },
  "customProperties": {
    "customerDefinedKey": "value"
  }
}
```

這項變更也可讓您在 (上明確命名的所有屬性 `properties` （例如 bool）（而不是字串) ）上使用正確的類型。

### <a name="response-headers"></a>回應標頭

v3 `Operation-Location` 除了 POST 要求上的標頭之外，也不會再傳回標頭 `Location` 。 這兩個標頭的值都是完全相同的。 現在只 `Location` 會傳回。

因為新的 API 版本現在是由 Azure API 管理所管理 (APIM) ，所以不會 `X-RateLimit-Limit` `X-RateLimit-Remaining` `X-RateLimit-Reset` 在回應標頭中包含節流相關的標頭、和。

### <a name="accuracy-tests"></a>精確度測試

精確度測試已重新命名為「評估」，因為新名稱會描述更好的表示。 新聞路徑例如 "HTTPs：//{region}. r a r/speechtotext/v3.0/評估」。
