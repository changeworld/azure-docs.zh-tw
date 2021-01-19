---
title: 從 v2 遷移至 v3 REST API-語音服務
titleSuffix: Azure Cognitive Services
description: 本檔可協助開發人員將程式碼從 v2 遷移至語音服務語音轉換文字 REST API 中的 v3。
services: cognitive-services
author: bexxx
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: rbeckers
ms.custom: devx-track-csharp
ms.openlocfilehash: 9c8016b566db8be1b7f5c5ddb8d92123d6673db5
ms.sourcegitcommit: 9d9221ba4bfdf8d8294cf56e12344ed05be82843
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/19/2021
ms.locfileid: "98569839"
---
# <a name="migrate-code-from-v20-to-v30-of-the-rest-api"></a>將2.0 版的程式碼遷移至 REST API 的 v3。0

相較于 v2，適用于語音轉換文字的語音服務 REST API v3 版本更可靠、更容易使用，而且與類似服務的 Api 更一致。 大部分的小組都可以在一天或兩天內從 v2 遷移至 v3。

## <a name="forward-compatibility"></a>往後相容性

V2 中的所有實體也都可以在 v3 API 中的相同身分識別下找到。 如果結果的架構已變更， (例如轉譯) ，則在 v3 版 API 中 GET 的結果會使用 v3 架構。 API v2 版本中 GET 的結果會使用相同的 v2 架構。 V2 Api 的回應中 **無法** 使用 v3 上新建立的實體   。 

## <a name="migration-steps"></a>移轉步驟

當您準備進行遷移時，這是您需要注意的專案摘要清單。 詳細資料可在個別連結中找到。 視您目前使用的 API 而定，此處所列的所有步驟都不適用。 只有少數變更需要呼叫程式碼中的非簡單變更。 大部分的變更只需要變更專案名稱。 

一般變更： 

1. [變更主機名稱](#host-name-changes)

1. [在您的用戶端程式代碼中將屬性識別碼重新命名為 self](#identity-of-an-entity) 

1. [變更程式碼以反復查看實體集合](#working-with-collections-of-entities)

1. [將屬性名稱重新命名為您用戶端程式代碼中的 displayName](#name-of-an-entity)

1. [調整參考實體的中繼資料抓取](#accessing-referenced-entities)

1. 如果您使用批次轉譯： 

    * [調整程式碼以建立 batch 轉譯](#creating-transcriptions) 

    * [將程式碼調整為新的轉譯結果架構](#format-of-v3-transcription-results)

    * [調整如何取得結果的程式碼](#getting-the-content-of-entities-and-the-results)

1. 如果您使用自訂模型定型/測試 Api： 

    * [將修改套用至自訂模型定型](#customizing-models)

    * [變更基礎和自訂模型的取出方式](#retrieving-base-and-custom-models)

    * [在用戶端程式代碼中將路徑區段 accuracytests 重新命名以進行評估](#accuracy-tests)

1. 如果您使用端點 Api：

    * [變更如何取出端點記錄](#retrieving-endpoint-logs)

1. 其他次要變更： 

    * [將所有自訂屬性傳遞為 customProperties，而不是 POST 要求中的屬性](#using-custom-properties)

    * [從回應標頭位置讀取位置，而不是操作位置](#response-headers)

## <a name="breaking-changes"></a>重大變更

### <a name="host-name-changes"></a>主機名稱變更

端點主機名稱已從變更 `{region}.cris.ai` 為 `{region}.api.cognitive.microsoft.com` 。 新端點的路徑已不再包含， `api/` 因為它是主機名稱的一部分。 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)會列出有效的區域和路徑。
>[!IMPORTANT]
>將主機名稱從變更 `{region}.cris.ai` 為 `{region}.api.cognitive.microsoft.com` 您的語音訂用帳戶區域的區域。 也請 `api/` 從用戶端程式代碼中的任何路徑移除。

### <a name="identity-of-an-entity"></a>實體的身分識別

屬性 `id` 現在是 `self` 。 在 v2 中，API 使用者必須知道如何建立 API 上的路徑。 這是不具擴充性且需要的使用者不必要的工作。 屬性 `id` (uuid) 會取代 `self` 為 (字串) ，也就是實體 (URL) 的位置。 在您所有的實體之間，該值仍是唯一的。 如果以 `id` 字串的形式儲存在程式碼中，重新命名就足以支援新的架構。 您現在可以使用 `self` 內容做為 `GET` 實體的、 `PATCH` 和 `DELETE` REST 呼叫的 URL。

如果實體具有可透過其他路徑使用的其他功能，則會列在底下 `links` 。 下列轉譯範例會向轉譯的內容顯示不同的方法 `GET` ：
>[!IMPORTANT]
>`id` `self` 在您的用戶端程式代碼中，將屬性重新命名為。 視需要將類型從變更 `uuid` 為 `string` 。 

**v2 轉譯：**

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

**v3 轉譯：**

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

根據程式碼的執行，可能不足以重新命名屬性。 我們建議使用傳回的 `self` 和 `links` 值做為 REST 呼叫的目標 url，而不是在用戶端中產生路徑。 藉由使用傳回的 Url，您可以確定路徑中的未來變更將不會中斷您的用戶端程式代碼。

### <a name="working-with-collections-of-entities"></a>使用實體集合

先前 v2 API 會傳回結果中所有可用的實體。 為了讓您更精細地控制 v3 中預期的回應大小，所有的集合結果都是編頁的。 您可以控制傳回的實體計數和頁面的起始位移。 此行為可讓您輕鬆地預測回應處理器的執行時間。

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

`values`屬性包含可用集合實體的子集。 您可以使用 `skip` 和查詢參數來控制計數和位移 `top` 。 如果 `@nextLink` 不是 `null` ，則會有更多可用的資料，而且可以藉由執行 GET 來抓取下一個批次的資料 `$.@nextLink` 。

這項變更需要 `GET` 在傳回所有元素之前，在迴圈中呼叫集合的。

>[!IMPORTANT]
>當 GET 的回應 `speechtotext/v3.0/{collection}` 包含中的值時 `$.@nextLink` ，繼續發出， `GETs` 直到未 `$.@nextLink` `$.@nextLink` 設定為取得該集合的所有專案為止。

### <a name="creating-transcriptions"></a>建立轉譯

有關如何建立轉譯批次的詳細說明，可在 [批次](./batch-transcription.md)轉譯操作說明中找到。

V3 轉譯 API 可讓您明確地設定特定轉譯選項。 所有 (選用的) 設定屬性現在都可以在屬性中設定 `properties` 。
V3 也支援多個輸入檔案，因此它需要 Url 清單，而不是單一 URL，因為 v2 已執行。 V2 屬性名稱 `recordingsUrl` 現在是 `contentUrls` 在 v3 中。 在轉譯中分析情感的功能已在 v3 中移除。 請參閱 Microsoft 認知服務 [文字分析](https://azure.microsoft.com/en-us/services/cognitive-services/text-analytics/) 的情感分析選項。

下的新 `timeToLive` 屬性 `properties` 可協助剪除現有的已完成實體。 `timeToLive`指定持續時間，已完成的實體將會自動刪除。 將它設定為較高的值 (例如 `PT12H`) 會持續追蹤、取用及刪除實體，因此通常會在經過12小時之前處理。

**v2 轉譯 POST 要求主體：**

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

**v3 轉譯 POST 要求主體：**

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
>[!IMPORTANT]
>將屬性重新命名 `recordingsUrl` 為 `contentUrls` ，並傳遞 url 的陣列，而不是單一 url。 傳遞或的設定， `diarizationEnabled` `wordLevelTimestampsEnabled` `bool` 而不是 `string` 。

### <a name="format-of-v3-transcription-results"></a>V3 轉譯結果的格式

轉譯結果的架構已稍微變更，以配合即時端點所建立的轉譯。 在 [批次](./batch-transcription.md)轉譯操作說明中尋找新格式的深入說明。 在的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) 中，會發佈結果的架構 `samples/batch/transcriptionresult_v3.schema.json` 。

屬性名稱現在以 camel 的大小寫，且的值 `channel` 和 `speaker` 現在都會使用整數類型。 持續時間的格式現在會使用 ISO 8601 中所述的結構，其符合其他 Azure Api 中使用的持續時間格式。

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
>[!IMPORTANT]
>將轉譯結果還原序列化為新的類型，如上所示。 您可以 `channel` 針對中的每個元素檢查的屬性值，來區別通道，而不是每個音訊通道的單一檔案 `recognizedPhrases` 。 每個輸入檔現在都有一個結果檔。


### <a name="getting-the-content-of-entities-and-the-results"></a>取得實體的內容和結果

在 v2 中，輸入或結果檔案的連結已與其余的實體中繼資料一起內嵌。 在 v3 的改進中，實體中繼資料之間會有明確的區隔 (在) 上取得 GET， `$.self` 以及用來存取結果檔的詳細資料和認證。 這種隔離有助於保護客戶資料，並可讓您在認證的有效期間內進行精確的控制。

在 v3 中， `links` `files` 如果實體公開資料 (資料集、轉譯、端點或評估) ，請包含名為的子屬性。 GET 會傳回檔案 `$.links.files` 清單和 SAS URL，以存取每個檔案的內容。 若要控制 SAS Url 的有效期間，查詢參數 `sasValidityInSeconds` 可以用來指定存留期。

**v2 轉譯：**

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

**v3 轉譯：**

```json
{
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
    "links": {
      "files": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3/files"
    } 
}
```

**GET `$.links.files` 會導致：**

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

`kind`屬性會指出檔案內容的格式。 針對轉譯，類型的檔案 `TranscriptionReport` 是作業的摘要，而類型的檔案則 `Transcription` 是作業本身的結果。

>[!IMPORTANT]
>若要取得作業的結果，請 `GET` 在上使用 `/speechtotext/v3.0/{collection}/{id}/files` ，這些結果不會再包含于或的回應中 `GET` `/speechtotext/v3.0/{collection}/{id}` `/speechtotext/v3.0/{collection}` 。

### <a name="customizing-models"></a>自訂模型

在 v3 之前，當模型正在定型時， _聲場模型_ 與 _語言模型_ 之間有一些差異。 這項區別會導致在建立端點或轉譯時，必須指定多個模型。 為了簡化呼叫者的這個程式，我們移除了差異，而所有專案都取決於用於模型定型之資料集的內容。 透過這種變更，模型建立現在支援混合的資料集 (語言資料和聲場資料) 。 端點和轉譯現在只需要一個模型。

有了這項變更，就會移除作業的需求， `kind` `POST` 而且 `datasets[]` 陣列現在可以包含相同或混合類型的多個資料集。

為了改善定型模型的結果，會在語言定型期間于內部自動使用聲場資料。 一般情況下，透過 v3 API 建立的模型會比使用 v2 API 建立的模型提供更精確的結果。

>[!IMPORTANT]
>若要自訂聲場和語言模型元件，請將 POST 中所有必要的語言和聲場資料集傳遞 `datasets[]` 至 `/speechtotext/v3.0/models` 。 這將會建立一個已自訂這兩個元件的單一模型。

### <a name="retrieving-base-and-custom-models"></a>正在抓取基底和自訂模型

為了簡化取得可用的模型，v3 已將「基底模型」的集合與客戶擁有的「自訂模型」分隔開來。 這兩個路由現在是 `GET /speechtotext/v3.0/models/base` 和 `GET /speechtotext/v3.0/models/` 。

在 v2 中，所有模型都是在單一回應中一起傳回。

>[!IMPORTANT]
>若要取得所提供的自訂基底模型清單，請使用 `GET` `/speechtotext/v3.0/models/base` 。 您可以使用來尋找自己的自訂 `GET` 模型 `/speechtotext/v3.0/models` 。

### <a name="name-of-an-entity"></a>實體的名稱

`name`屬性現在是 `displayName` 。 這與其他 Azure Api 一致，不表示身分識別屬性。 這個屬性的值不能是唯一的，而且可以在建立實體之後變更 `PATCH` 。

**v2 轉譯：**

```json
{
    "name": "Transcription using locale en-US"
}
```

**v3 轉譯：**

```json
{
    "displayName": "Transcription using locale en-US"
}
```

>[!IMPORTANT]
>`name` `displayName` 在您的用戶端程式代碼中，將屬性重新命名為。

### <a name="accessing-referenced-entities"></a>存取參考的實體

在 v2 中，參考的實體一律是內嵌的，例如端點使用的模型。 實體的嵌套會導致大型回應，而且取用者很少耗用嵌套的內容。 為了縮減回應大小並改善效能，參考的實體不再內嵌于回應中。 相反地，會出現另一個實體的參考，而且可以直接用於後續的 `GET` (也就是 URL，也) ，遵循與連結相同的模式 `self` 。

**v2 轉譯：**

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

**v3 轉譯：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/transcriptions/9891c965-bb32-4880-b14b-6d44efb158f3",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/021a72d0-54c4-43d3-8254-27336ead9037"
  }
}
```

如果您需要取用參考模型的詳細資料（如上述範例所示），請直接發出 GET `$.model.self` 。

>[!IMPORTANT]
>若要抓取參考實體的中繼資料，請發出 GET `$.{referencedEntity}.self` ，例如取出轉譯的模型 `GET` `$.model.self` 。


### <a name="retrieving-endpoint-logs"></a>正在抓取端點記錄

服務的 v2 版本支援記錄端點結果。 若要使用 v2 取出端點的結果，您可以建立「資料匯出」，以代表時間範圍所定義的結果快照。 匯出資料批次的程式沒有彈性。 V3 API 會提供每個個別檔案的存取權，並允許透過這些檔案進行反復專案。

**已成功執行 v3 端點：**

```json
{
  "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6",
  "links": {
    "logs": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/endpoints/afa0669c-a01e-4693-ae3a-93baf40f26d6/files/logs" 
  }
}
```

**GET 的回應 `$.links.logs` ：**

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

端點記錄的分頁運作方式類似于其他所有集合，但不能指定位移。 由於有大量可用的資料，因此分頁是由伺服器決定。

在 v3 中，每個端點記錄檔都可以藉由在檔案的 `DELETE` 上發出操作 `self` ，或使用 on 來個別刪除 `DELETE` `$.links.logs` 。 若要指定結束日期，可以將查詢參數 `endDate` 加入至要求。

>[!IMPORTANT]
>而不是使用建立記錄檔匯出 `/api/speechtotext/v2.0/endpoints/{id}/data` `/v3.0/endpoints/{id}/files/logs/` 來個別存取記錄檔。 

### <a name="using-custom-properties"></a>使用自訂屬性

為了將自訂屬性與選擇性的設定屬性分開，所有明確命名的屬性現在都位於屬性中， `properties` 而呼叫端定義的所有屬性現在都位於 `customProperties` 屬性中。

**v2 轉譯實體：**

```json
{
  "properties": {
    "customerDefinedKey": "value",
    "diarizationEnabled": "False",
    "wordLevelTimestampsEnabled": "False"
  }
}
```

**v3 轉譯實體：**

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

這項變更也可讓您在 (上明確命名的屬性 `properties` （例如布林值）（而不是字串) ）上使用正確的類型。

>[!IMPORTANT]
>傳遞所有自訂屬性， `customProperties` 而不是 `properties` 在您的 `POST` 要求中。

### <a name="response-headers"></a>回應標頭

v3 `Operation-Location` 除了 `Location` 要求上的標頭之外，不會再傳回標頭 `POST` 。 V2 中兩個標頭的值相同。 現在只 `Location` 會傳回。

因為新的 API 版本現在是由 Azure API 管理所管理 (APIM) ，所以不會 `X-RateLimit-Limit` `X-RateLimit-Remaining` `X-RateLimit-Reset` 在回應標頭中包含節流相關的標頭、和。

>[!IMPORTANT]
>從回應標頭讀取位置， `Location` 而不是 `Operation-Location` 。 如果是429回應碼，請讀取 `Retry-After` 標頭值，而不是 `X-RateLimit-Limit` 、 `X-RateLimit-Remaining` 或 `X-RateLimit-Reset` 。


### <a name="accuracy-tests"></a>精確度測試

精確度測試已重新命名為「評估」，因為新名稱會描述更好的表示。 新路徑為： `https://{region}.api.cognitive.microsoft.com/speechtotext/v3.0/evaluations` 。

>[!IMPORTANT]
>`accuracytests` `evaluations` 在您的用戶端程式代碼中，將路徑區段重新命名為。


## <a name="next-steps"></a>後續步驟

檢查語音服務所提供的這些常用 REST Api 的所有功能：

* [語音轉換文字 REST API](rest-speech-to-text.md)
* REST API v3 的[Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* 如需執行批次轉譯的範例程式碼，請參閱子目錄中的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) `samples/batch` 。
