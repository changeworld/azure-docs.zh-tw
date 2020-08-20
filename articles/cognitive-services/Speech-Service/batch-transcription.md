---
title: 如何使用批次轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: 如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: wolfma
ms.openlocfilehash: df1266070e9fb69ec94811a3120412d9b238e470
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640152"
---
# <a name="how-to-use-batch-transcription"></a>如何使用批次轉譯

批次轉譯是一組 REST API 作業，可讓您在儲存體中轉譯大量音訊。 您可以使用共用存取簽章來指向音訊檔案 (SAS) URI，並以非同步方式接收轉譯結果。 使用新的 v3.0 API，您可以選擇轉譯一或多個音訊檔案，或處理整個儲存體容器。

非同步語音轉換文字轉譯只是其中一項功能。 您可以使用批次轉譯 REST Api 來呼叫下列方法：

|    批次轉譯作業                                             |    方法    |    REST API 呼叫                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    建立新的轉譯。                                              |    POST      |    speechtotext/v3.0/轉譯            |
|    抓取已驗證訂閱的轉譯清單。    |    GET       |    speechtotext/v3.0/轉譯            |
|    取得支援的離線轉譯地區設定清單。              |    GET       |    speechtotext/v3.0/轉譯/地區設定    |
|    更新識別碼所識別之轉譯的可變詳細資料。    |    PATCH     |    speechtotext/v3.0/轉譯/{id}       |
|    刪除指定的轉譯工作。                                 |    刪除    |    speechtotext/v3.0/轉譯/{id}       |
|    取得指定之識別碼所識別的轉譯。                        |    GET       |    speechtotext/v3.0/轉譯/{id}       |
|    取得指定之識別碼所識別之轉譯的結果檔。    |    GET       |    speechtotext/v3.0/轉譯/{id}/files |




您可以檢查並測試詳細的 API，這是以 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)的形式提供。

批次轉譯作業會以最大量的方式進行排程。 目前，當工作變更為「執行中」狀態時，不會進行任何估計。 在正常的系統負載下，應該會在幾分鐘內發生。 一旦處於執行中狀態，實際轉譯的處理速度會比音訊即時更快。

在容易使用的 API 旁，您不需要部署自訂端點，也不需要觀察任何平行存取需求。

## <a name="prerequisites"></a>Prerequisites

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。

>[!NOTE]
> 需要適用于語音服務的標準訂用帳戶 (S0) ，才能使用批次轉譯。 免費的訂用帳戶金鑰 (F0) 無法運作。 如需詳細資訊，請參閱 [定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

如果您打算自訂模型，請依照聲場 [自訂](how-to-customize-acoustic-models.md) 和 [語言自訂](how-to-customize-language-model.md)中的步驟進行。 若要在批次轉譯中使用已建立的模型，您需要其模型位置。 當您檢查模型 (屬性) 的詳細資料時，可以取出模型位置 `self` 。 批次轉譯服務 *不需要* 已部署的自訂端點。

## <a name="batch-transcription-api"></a>批次轉譯 API

批次轉譯 API 支援下列格式：

| 格式 | 轉碼器 | 每個樣本的位數 | 採樣速率             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |
| MP3    | PCM   | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |
| OGG    | OPUS  | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |

針對身歷聲音訊串流，會在轉譯期間分割左邊和右邊的通道。 針對每個通道，正在建立 JSON 結果檔。 每個語句產生的時間戳記，可讓開發人員建立已排序的最終文字記錄。

### <a name="configuration"></a>組態

設定參數會以 JSON 形式提供 (一或多個個別檔案) ：

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

設定參數提供為 JSON (處理整個儲存體容器) ：

```json
{
  "contentContainerUrl": "<SAS URL to the Azure blob container to transcribe>",
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of container using default model for en-US"
}
```

若要在 batch 轉譯中使用自訂定型模型，可以參考這些模型，如下所示：

```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "model": {
    "self": "https://westus.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}"
  },
  "displayName": "Transcription of file using default model for en-US"
}
```


### <a name="configuration-properties"></a>設定屬性

使用這些選用屬性來設定轉譯：

:::row:::
   :::column span="1":::
      **參數**
   :::column-end:::
   :::column span="2":::
      **說明**
:::row-end:::
:::row:::
   :::column span="1":::
      `profanityFilterMode`
   :::column-end:::
   :::column span="2":::
      指定如何處理辨識結果中的不雅內容。 接受的值是停用不雅內容 `None` 篩選、將不雅內容取代為 `Masked` 星號、 `Removed` 從結果中移除所有不雅內容，或新增「不雅內容」 `Tags` 標記。 預設設定是 `Masked`。
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      指定如何處理辨識結果中的標點符號。 接受的值是 `None` 停用標點符號，表示 `Dictated` 明確 (讀出) 標點符號， `Automatic` 讓解碼器處理標點符號，或 `DictatedAndAutomatic` 使用聽寫和自動標點符號。 預設設定是 `DictatedAndAutomatic`。
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      指定是否將字組層級時間戳記新增至輸出。 接受的值為 `true` 啟用字組層級時間戳記，並 `false` (預設值) 停用它。
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      指定應在輸入上執行 diarization 分析，這應該是包含兩個語音的 mono 通道。 接受的值會 `true` 啟用 diarization， `false` (預設值) 停用它。 它也必須 `wordLevelTimestampsEnabled` 設定為 true。
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      要處理的選擇性通道號碼陣列。 您可以指定音訊檔案中可用通道的子集來進行處理， (例如 `0` 僅) 。 如果未指定，則 `0` `1` 會將通道和轉譯為預設值。
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      在完成轉譯之後自動刪除轉譯的選擇性持續時間。 `timeToLive`很適合用於大量處理轉譯，以確保最終會將它們刪除 (例如 `PT12H`) 。 如果未指定或設定為 `PT0H` ，將不會自動刪除轉譯。
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      具有 [服務 SAS](../../storage/common/storage-sas-overview.md) 的選擇性 URL，可供 Azure 中的可寫入容器使用。 結果會儲存在此容器中。 如果未指定，Microsoft 會將結果儲存在 Microsoft 所管理的儲存體容器中。 藉由呼叫 [刪除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)轉譯來刪除轉譯時，也會一併刪除結果資料。
:::row-end:::

### <a name="storage"></a>儲存體

批次轉譯支援 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) ，以讀取音訊及將轉譯寫入儲存體。

## <a name="batch-transcription-result"></a>批次轉譯結果

針對每個輸入音訊，會建立一個轉譯結果檔。 您可以藉由呼叫 [get 轉譯 files](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)來取得結果檔的清單。 這個方法會傳回此轉譯結果檔的清單。 若要尋找特定輸入檔的轉譯檔案，請使用和來篩選所有傳回的檔案 `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` 。

每個轉譯結果檔案的格式如下：

```json
{
  "source": "...",                                                 // the sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",                             // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,                                     // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                                           // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [                                   // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                                                // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                                           // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",                              // recognition state, e.g. "Success", "Failure"
      "channel": 0,                                                // channel number of the result
      "offset": "PT0.07S",                                         // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",                                       // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,                                   // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,                               // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,                               // confidence value for the recognition of the whole phrase
          "speaker": 1,                                            // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
          "lexical": "hello world",
          "itn": "hello world",
          "maskedITN": "hello world",
          "display": "Hello world.",
          
          // if wordLevelTimestampsEnabled is `true`, there will be a result for each word of the phrase, otherwise this property is not present
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

結果包含下列表單：

:::row:::
   :::column span="1":::
      **表單**
   :::column-end:::
   :::column span="2":::
      **內容**
:::row-end:::
:::row:::
   :::column span="1":::
      `lexical`
   :::column-end:::
   :::column span="2":::
      實際辨識的文字。
:::row-end:::
:::row:::
   :::column span="1":::
      `itn`
   :::column-end:::
   :::column span="2":::
      已辨識文字的反向文字正規化形式。 縮寫 ( "醫生 smith" 至 "dr smith" ) 、電話號碼和其他轉換。
:::row-end:::
:::row:::
   :::column span="1":::
      `maskedITN`
   :::column-end:::
   :::column span="2":::
      已套用不雅內容遮罩的 ITN 表單。
:::row-end:::
:::row:::
   :::column span="1":::
      `display`
   :::column-end:::
   :::column span="2":::
      已辨識文字的顯示形式。 包含已新增的標點符號和大小寫。
:::row-end:::

## <a name="speaker-separation-diarization"></a>Diarization) 的喇叭分隔 (

Diarization 是在一段音訊中分隔喇叭的流程。 Batch 管線支援 diarization，而且能夠辨識 mono 通道錄製上的兩個喇叭。 這項功能不適用於身歷聲記錄。

啟用 diarization 之轉譯的輸出會包含 `Speaker` 每個轉譯片語的專案。 如果未使用 diarization，則屬性不 `Speaker` 會出現在 JSON 輸出中。 針對 diarization，我們支援兩種語音，因此會將說話者識別為 `1` 或 `2` 。

若要要求 diarization，您只需要在 HTTP 要求中新增相關的參數，如下所示。

 ```json
{
  "contentUrls": [
    "<URL to an audio file to transcribe>",
  ],
  "properties": {
    "diarizationEnabled": true,
    "wordLevelTimestampsEnabled": true,
    "punctuationMode": "DictatedAndAutomatic",
    "profanityFilterMode": "Masked"
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

必須啟用文字層級時間戳記，因為上述要求中的參數表示。

## <a name="best-practices"></a>最佳作法

批次轉譯服務可以處理大量的已提交轉譯。 您可以透過 `GET` [取得轉譯](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)上的來查詢轉譯的狀態。 在您抓取結果之後，從服務定期呼叫 [刪除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) 轉譯。 也可以將 `timeToLive` 屬性設定為合理的值，以確保最終刪除結果。

## <a name="sample-code"></a>範例程式碼

完整範例可從子目錄內的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) 中取得 `samples/batch` 。

請將範例程式碼更新為您的訂用帳戶資訊、服務區域、指向要轉譯之音訊檔案的 SAS URI，並在您想要使用自訂模型的情況下使用模型位置。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

範例程式碼會設定用戶端，並提交轉譯要求。 然後，它會輪詢狀態資訊，並列印轉譯進度的詳細資料。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptionstatus)]

如需上述呼叫的完整詳細資料，請參閱我們的 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 有一 `PostTranscriptions` 種方法可傳送音訊檔案詳細資料，以及用 `GetTranscriptions` 來接收狀態的方法。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 服務會使用基準模型來轉譯檔案。 若要指定模型，您可以對自訂模型的模型參考傳遞相同的方法。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

## <a name="next-steps"></a>後續步驟

- [語音轉換文字 v3 API 參考](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
