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
ms.date: 11/03/2020
ms.author: wolfma
ms.custom: devx-track-csharp
ms.openlocfilehash: 5e4e5f4c1a50c814174dbbd5d419fe24b2e9f88e
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/04/2020
ms.locfileid: "93336675"
---
# <a name="how-to-use-batch-transcription"></a>如何使用批次轉譯

批次轉譯是一組 REST API 作業，可讓您在儲存體中轉譯大量音訊。 您可以使用一般 URI 或共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯結果。 使用 v3.0 API，您可以轉譯一或多個音訊檔案，或處理整個儲存體容器。

您可以使用批次轉譯 REST Api 來呼叫下列方法：

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

批次轉譯作業會以最大量的方式進行排程。
您無法預估工作何時會變更為「執行中」狀態，但它應該會在正常系統載入的幾分鐘內發生。 一旦處於執行中狀態，轉譯的速度會比音訊執行時間的播放速度快。

## <a name="prerequisites"></a>必要條件

如同語音服務的所有功能，您可以依照我們的[快速入門指南](overview.md#try-the-speech-service-for-free)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。

>[!NOTE]
> 需要適用于語音服務的標準訂用帳戶 (S0) ，才能使用批次轉譯。 免費訂用帳戶金鑰 (F0) 將無法運作。 如需詳細資訊，請參閱 [定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

如果您打算自訂模型，請依照聲場 [自訂](how-to-customize-acoustic-models.md) 和 [語言自訂](how-to-customize-language-model.md)中的步驟進行。 若要在批次轉譯中使用已建立的模型，您需要其模型位置。 當您檢查模型 (屬性) 的詳細資料時，可以取出模型位置 `self` 。 批次轉譯服務 *不需要* 已部署的自訂端點。

>[!NOTE]
> 在 REST API 過程中，批次轉譯會有一組 [配額和限制](speech-services-quotas-and-limits.md#speech-to-text-quotas-and-limits-per-speech-resource)，我們鼓勵您複習。 若要充分利用批次轉譯功能來有效率地轉譯大量音訊檔案，建議您一律針對每個要求傳送多個檔案，或指向包含要轉譯之音訊檔案的 Blob 儲存體容器。 服務會同時轉譯檔案，以同時減少周轉時間。 在單一要求中使用多個檔案相當簡單明瞭 [，請參閱](#configuration) 設定一節。 

## <a name="batch-transcription-api"></a>批次轉譯 API

批次轉譯 API 支援下列格式：

| 格式 | 轉碼器 | 每個樣本的位數 | 採樣速率             |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |
| MP3    | PCM   | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |
| OGG    | OPUS  | 16 位元  | 8 kHz 或 16 kHz （mono 或身歷聲） |

針對身歷聲音訊串流，會在轉譯期間分割左邊和右邊的通道。 正在為每個通道建立 JSON 結果檔。
若要建立已排序的最終文字記錄，請使用每個語句所產生的時間戳記。

### <a name="configuration"></a>組態

設定參數會以 JSON 形式提供。

**轉譯一或多個個別檔案。** 如果您有一個以上的檔案要轉譯，建議您在一個要求中傳送多個檔案。 下列範例會使用三個檔案：

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
  ],
  "properties": {
    "wordLevelTimestampsEnabled": true
  },
  "locale": "en-US",
  "displayName": "Transcription of file using default model for en-US"
}
```

**處理整個儲存體容器：**

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

**在批次轉譯中使用自訂的定型模型。** 此範例使用三個檔案：

```json
{
  "contentUrls": [
    "<URL to an audio file 1 to transcribe>",
    "<URL to an audio file 2 to transcribe>",
    "<URL to an audio file 3 to transcribe>"
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
      選擇性，預設為 `Masked` 。 指定如何處理辨識結果中的不雅內容。 接受的值是停用不雅內容 `None` 篩選、將不雅內容取代為 `Masked` 星號、 `Removed` 從結果中移除所有不雅內容，或新增「不雅內容」 `Tags` 標記。
:::row-end:::
:::row:::
   :::column span="1":::
      `punctuationMode`
   :::column-end:::
   :::column span="2":::
      選擇性，預設為 `DictatedAndAutomatic` 。 指定如何處理辨識結果中的標點符號。 接受的值是 `None` 停用標點符號，表示 `Dictated` 明確 (讀出) 標點符號， `Automatic` 讓解碼器處理標點符號，或 `DictatedAndAutomatic` 使用聽寫和自動標點符號。
:::row-end:::
:::row:::
   :::column span="1":::
      `wordLevelTimestampsEnabled`
   :::column-end:::
   :::column span="2":::
      選擇性， `false` 預設為。 指定是否將字組層級時間戳記新增至輸出。
:::row-end:::
:::row:::
   :::column span="1":::
      `diarizationEnabled`
   :::column-end:::
   :::column span="2":::
      選擇性， `false` 預設為。 指定應在輸入上執行 diarization 分析，這應該是包含兩個語音的 mono 通道。 注意： `wordLevelTimestampsEnabled` 必須設定為 `true` 。
:::row-end:::
:::row:::
   :::column span="1":::
      `channels`
   :::column-end:::
   :::column span="2":::
      選擇性， `0` `1` 預設為轉譯。 要處理的通道號碼陣列。 您可以指定音訊檔案中可用通道的子集，以進行處理 (例如 `0` 僅) 。
:::row-end:::
:::row:::
   :::column span="1":::
      `timeToLive`
   :::column-end:::
   :::column span="2":::
      選擇性，預設為不刪除。 完成轉譯之後自動刪除轉譯的持續時間。 `timeToLive`很適合用於大量處理轉譯，以確保最終會將它們刪除 (例如， `PT12H`) 的12小時。
:::row-end:::
:::row:::
   :::column span="1":::
      `destinationContainerUrl`
   :::column-end:::
   :::column span="2":::
      具有服務特定 [SAS](../../storage/common/storage-sas-overview.md) 的選擇性 URL，指向 Azure 中可寫入的容器。 結果會儲存在此容器中。 **不** 支援具有預存存取原則的 SAS。 如果未指定，Microsoft 會將結果儲存在 Microsoft 所管理的儲存體容器中。 藉由呼叫 [刪除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription)轉譯來刪除轉譯時，也會一併刪除結果資料。
:::row-end:::

### <a name="storage"></a>儲存體

批次轉譯可以從公開可見的網際網路 URI 讀取音訊，也可以使用 SAS URI 搭配 [Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)來讀取音訊或寫入轉譯。

## <a name="batch-transcription-result"></a>批次轉譯結果

針對每個音訊輸入，會建立一個轉譯結果檔。
[取得轉譯](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptionFiles)檔案作業會傳回此轉譯的結果檔清單。 若要尋找特定輸入檔的轉譯檔案，請使用和來篩選所有傳回的檔案 `kind`  ==  `Transcription` `name`  ==  `{originalInputName.suffix}.json` 。

每個轉譯結果檔案的格式如下：

```json
{
  "source": "...",                      // sas url of a given contentUrl or the path relative to the root of a given container
  "timestamp": "2020-06-16T09:30:21Z",  // creation time of the transcription, ISO 8601 encoded timestamp, combined date and time
  "durationInTicks": 41200000,          // total audio duration in ticks (1 tick is 100 nanoseconds)
  "duration": "PT4.12S",                // total audio duration, ISO 8601 encoded duration
  "combinedRecognizedPhrases": [        // concatenated results for simple access in single string for each channel
    {
      "channel": 0,                     // channel number of the concatenated results
      "lexical": "hello world",
      "itn": "hello world",
      "maskedITN": "hello world",
      "display": "Hello world."
    }
  ],
  "recognizedPhrases": [                // results for each phrase and each channel individually
    {
      "recognitionStatus": "Success",   // recognition state, e.g. "Success", "Failure"
      "channel": 0,                     // channel number of the result
      "offset": "PT0.07S",              // offset in audio of this phrase, ISO 8601 encoded duration 
      "duration": "PT1.59S",            // audio duration of this phrase, ISO 8601 encoded duration
      "offsetInTicks": 700000.0,        // offset in audio of this phrase in ticks (1 tick is 100 nanoseconds)
      "durationInTicks": 15900000.0,    // audio duration of this phrase in ticks (1 tick is 100 nanoseconds)
      
      // possible transcriptions of the current phrase with confidences
      "nBest": [
        {
          "confidence": 0.898652852,    // confidence value for the recognition of the whole phrase
          "speaker": 1,                 // if `diarizationEnabled` is `true`, this is the identified speaker (1 or 2), otherwise this property is not present
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

結果包含下欄欄位：

:::row:::
   :::column span="1":::
      **欄位**
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

啟用 diarization 之轉譯的輸出會包含 `Speaker` 每個轉譯片語的專案。 如果未使用 diarization，則 `Speaker` 屬性不會出現在 JSON 輸出中。 針對 diarization，我們支援兩種語音，因此會將說話者識別為 `1` 或 `2` 。

若要要求 diarization，請將 `diarizationEnabled` 屬性設定為， `true` 如下所示的 HTTP 要求。

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

批次轉譯服務可以處理大量的已提交轉譯。 您可以使用 [Get 轉譯](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetTranscriptions)查詢轉譯的狀態。
在您抓取結果之後，從服務定期呼叫 [刪除](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/DeleteTranscription) 轉譯。 也 `timeToLive` 可以設定屬性，以確保最終刪除結果。

## <a name="sample-code"></a>範例程式碼

完整範例可從子目錄內的 [GitHub 範例存放庫](https://aka.ms/csspeech/samples) 中取得 `samples/batch` 。

將範例程式碼更新為您的訂用帳戶資訊、服務區域、指向要轉譯之音訊檔案的 URI，以及使用自訂模型的模型位置。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#transcriptiondefinition)]

範例程式碼會設定用戶端，並提交轉譯要求。 然後，它會輪詢狀態資訊，並列印轉譯進度的詳細資料。

```csharp
// get the status of our transcriptions periodically and log results
int completed = 0, running = 0, notStarted = 0;
while (completed < 1)
{
    completed = 0; running = 0; notStarted = 0;

    // get all transcriptions for the user
    paginatedTranscriptions = null;
    do
    {
        // <transcriptionstatus>
        if (paginatedTranscriptions == null)
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync().ConfigureAwait(false);
        }
        else
        {
            paginatedTranscriptions = await client.GetTranscriptionsAsync(paginatedTranscriptions.NextLink).ConfigureAwait(false);
        }

        // delete all pre-existing completed transcriptions. If transcriptions are still running or not started, they will not be deleted
        foreach (var transcription in paginatedTranscriptions.Values)
        {
            switch (transcription.Status)
            {
                case "Failed":
                case "Succeeded":
                    // we check to see if it was one of the transcriptions we created from this client.
                    if (!createdTranscriptions.Contains(transcription.Self))
                    {
                        // not created form here, continue
                        continue;
                    }

                    completed++;

                    // if the transcription was successful, check the results
                    if (transcription.Status == "Succeeded")
                    {
                        var paginatedfiles = await client.GetTranscriptionFilesAsync(transcription.Links.Files).ConfigureAwait(false);

                        var resultFile = paginatedfiles.Values.FirstOrDefault(f => f.Kind == ArtifactKind.Transcription);
                        var result = await client.GetTranscriptionResultAsync(new Uri(resultFile.Links.ContentUrl)).ConfigureAwait(false);
                        Console.WriteLine("Transcription succeeded. Results: ");
                        Console.WriteLine(JsonConvert.SerializeObject(result, SpeechJsonContractResolver.WriterSettings));
                    }
                    else
                    {
                        Console.WriteLine("Transcription failed. Status: {0}", transcription.Properties.Error.Message);
                    }

                    break;

                case "Running":
                    running++;
                    break;

                case "NotStarted":
                    notStarted++;
                    break;
            }
        }

        // for each transcription in the list we check the status
        Console.WriteLine(string.Format("Transcriptions status: {0} completed, {1} running, {2} not started yet", completed, running, notStarted));
    }
    while (paginatedTranscriptions.NextLink != null);

    // </transcriptionstatus>
    // check again after 1 minute
    await Task.Delay(TimeSpan.FromMinutes(1)).ConfigureAwait(false);
}
```

如需上述呼叫的完整詳細資料，請參閱我們的 [Swagger 檔](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

此範例會使用非同步安裝來張貼音訊和接收轉譯狀態。
方法會傳送 `PostTranscriptions` 音訊檔案詳細資料，而 `GetTranscriptions` 方法會收到狀態。
`PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

此範例程式碼不會指定自訂模型。 服務會使用基準模型來轉譯檔案。 若要指定模型，您可以對自訂模型的模型參考傳遞相同的方法。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。

## <a name="next-steps"></a>後續步驟

- [語音轉換文字 v3 API 參考](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)
