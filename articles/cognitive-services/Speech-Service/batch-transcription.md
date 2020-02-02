---
title: 如何使用批次轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: 如果您想要轉譯儲存體 (例如 Azure Blob) 中數量龐大的音訊，則適用批次轉譯。 透過使用該專屬 REST API，您可以使用共用存取簽章 (SAS) URI 來指向音訊檔案，並以非同步方式接收轉譯。
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: panosper
ms.openlocfilehash: 8a53f1cfbde2f518848e7ef1104bf41ba4996961
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/01/2020
ms.locfileid: "76936397"
---
# <a name="how-to-use-batch-transcription"></a>如何使用批次轉譯

批次轉譯非常適合用來在儲存體中轉譯大量的音訊。 藉由使用專用的 REST API，您可以使用共用存取簽章（SAS） URI 來指向音訊檔案，並以非同步方式接收轉譯結果。

此 API 提供非同步語音轉換文字轉譯及其他功能。 您可以使用 REST API 來公開方法來執行下列動作：

- 建立批次處理要求
- 查詢狀態
- 下載轉譯結果
- 從服務中刪除轉譯資訊

詳細的 API 可於 `Custom Speech transcriptions` 標題底下的 [Swagger 文件](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)中取得。

批次轉譯作業是以最大的方式排定。 目前不會估計作業何時會變更為「執行中」狀態。 在一般系統負載下，應該會在幾分鐘內發生。 一旦處於 [執行中] 狀態，實際轉譯的處理速度會比音訊即時長。

在容易使用的 API 旁，您不需要部署自訂端點，也不會有任何要觀察的並行需求。

## <a name="prerequisites"></a>必要條件

### <a name="subscription-key"></a>訂用帳戶金鑰

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。

>[!NOTE]
> 需要「語音服務」的標準訂用帳戶（S0）才能使用批次轉譯。 免費訂用帳戶金鑰 (F0) 將無法運作。 如需詳細資訊，請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自訂模型

如果您打算自訂聲場或語言模型，請依照[自訂聲場模型](how-to-customize-acoustic-models.md)和[設計自訂語言模型](how-to-customize-language-model.md)中的步驟進行。 若要在批次轉譯中使用已建立的模型，您需要其模型識別碼。 您可以在檢查模型的詳細資料時，取得模型識別碼。 批次轉譯服務不需要已部署的自訂端點。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| [格式] | 轉碼器 | Bitrate | 採樣速率 |
|--------|-------|---------|-------------|
| WAV | PCM | 16 位元 | 8 kHz 或 16 kHz、單聲道或身歷聲 |
| MP3 | PCM | 16 位元 | 8 kHz 或 16 kHz、單聲道或身歷聲 |
| OGG | OPUS | 16 位元 | 8 kHz 或 16 kHz、單聲道或身歷聲 |

針對身歷聲音訊資料流程，左右聲道會在轉譯期間分割。 針對每個通道，會建立 JSON 結果檔案。 每個語句產生的時間戳記可讓開發人員建立排序的最終文字記錄。

### <a name="configuration"></a>組態

組態參數以 JSON 格式提供：

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to use, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "None | Removed | Tags | Masked",
    "PunctuationMode": "None | Dictated | Automatic | DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True | False",
    "AddSentiment" : "True | False",
    "AddDiarization" : "True | False",
    "TranscriptionResultsContainerUrl" : "<service SAS URI to Azure container to store results into (write permission required)>"
  }
}
```

### <a name="configuration-properties"></a>設定屬性

使用這些選擇性屬性來設定轉譯：

| 參數 | 說明 |
|-----------|-------------|
| `ProfanityFilterMode` | 指定如何處理辨識結果中的不雅內容。 接受的值為 `None` (會停用不雅內容過濾)、`Masked` (為以星號取代不雅內容)、`Removed` (會移除結果中的所有不雅內容) 或 `Tags` (會新增「不雅內容」標記)。 預設值是 `Masked`。 |
| `PunctuationMode` | 指定如何處理辨識結果中的標點符號。 接受的值為`None` (會停用標點符號)、`Dictated` (暗示明確的標點符號)、`Automatic` (會讓解碼器處理標點符號) 或 `DictatedAndAutomatic` (暗示口述的標點符號或自動)。 |
| `AddWordLevelTimestamps` | 指定是否將字組層級時間戳記新增至輸出。 接受的值為`true` 會啟用字組層級時間戳記，而 `false` (預設值) 會停用。 |
| `AddSentiment` | 指定應該將情感新增至語句。 接受的值為 `true`，可讓每個語句的情感和 `false` （預設值）停用它。 |
| `AddDiarization` | 指定應該在輸入上執行 diarization 分析，這應該是包含兩個語音的 mono 通道。 接受的值為 `true`，可讓 diarization 和 `false` （預設值）停用它。 它也需要 `AddWordLevelTimestamps` 設定為 true。|
|`TranscriptionResultsContainerUrl`|選擇性的 URL，其中包含[服務 SAS](../../storage/common/storage-sas-overview.md)至 Azure 中可寫入的容器。 結果將會儲存在此容器中。

### <a name="storage"></a>儲存體

批次轉譯支援[Azure Blob 儲存體](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview)來讀取音訊，並將轉譯寫入儲存體。

## <a name="the-batch-transcription-result"></a>批次轉譯結果

若為 mono 輸入音訊，則會建立一個轉譯結果檔案。 若為身歷聲輸入音訊，則會建立兩個轉譯結果檔案。 每個都有此結構：

```json
{
  "AudioFileResults":[ 
    {
      "AudioFileName": "Channel.0.wav | Channel.1.wav"      'maximum of 2 channels supported'
      "AudioFileUrl": null                                  'always null'
      "AudioLengthInSeconds": number                        'Real number. Two decimal places'
      "CombinedResults": [
        {
          "ChannelNumber": null                             'always null'
          "Lexical": string
          "ITN": string
          "MaskedITN": string
          "Display": string
        }
      ]
      SegmentResults:[                                      'for each individual segment'
        {
          "RecognitionStatus": "Success | Failure"
          "ChannelNumber": null
          "SpeakerId": null | "1 | 2"                       'null if no diarization
                                                             or stereo input file, the
                                                             speakerId as a string if
                                                             diarization requested for
                                                             mono audio file'
          "Offset": number                                  'time in milliseconds'
          "Duration": number                                'time in milliseconds'
          "OffsetInSeconds" : number                        'Real number. Two decimal places'
          "DurationInSeconds" : number                      'Real number. Two decimal places'
          "NBest": [
            {
              "Confidence": number                          'between 0 and 1'
              "Lexical": string
              "ITN": string
              "MaskedITN": string
              "Display": string
              "Sentiment":
                {                                           'this is omitted if sentiment is
                                                             not requested'
                  "Negative": number                        'between 0 and 1'
                  "Neutral": number                         'between 0 and 1'
                  "Positive": number                        'between 0 and 1'
                }
              "Words": [
                {
                  "Word": string
                  "Offset": number                          'time in milliseconds'
                  "Duration": number                        'time in milliseconds'
                  "OffsetInSeconds": number                 'Real number. Two decimal places'
                  "DurationInSeconds": number               'Real number. Two decimal places'
                  "Confidence": number                      'between 0 and 1'
                }
              ]
            }
          ]
        }
      ]
    }
  ]
}
```

結果包含下列表單：

|表單|內容|
|-|-|
|`Lexical`|已辨識的實際文字。
|`ITN`|已辨識文字的反向文字正規化形式。 會套用縮寫（"醫生 smith" 至 "dr smith"）、電話號碼和其他轉換。
|`MaskedITN`|套用不雅內容遮罩的 ITN 表單。
|`Display`|已辨識文字的顯示形式。 這包括新增的標點符號和大小寫。

## <a name="speaker-separation-diarization"></a>說話者分隔（Diarization）

Diarization 是在一段音訊中分隔喇叭的程式。 我們的批次管線支援 diarization，而且能夠在 mono 通道錄製上辨識兩個喇叭。 這項功能不適用於身歷聲記錄。

所有轉譯輸出都包含 `SpeakerId`。 如果未使用 diarization，它會在 JSON 輸出中顯示 `"SpeakerId": null`。 針對 diarization，我們支援兩種語音，因此會將喇叭識別為 `"1"` 或 `"2"`。

若要要求 diarization，您只需要在 HTTP 要求中新增相關的參數，如下所示。

 ```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": [{"Id":"<optional acoustic model ID>"},{"Id":"<optional language model ID>"}],
  "locale": "<locale to us, for example en-US>",
  "name": "<user defined name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "AddWordLevelTimestamps" : "True",
    "AddDiarization" : "True"
  }
}
```

字層級時間戳記也必須「開啟」，因為上述要求中的參數表示。

## <a name="sentiment-analysis"></a>情緒分析

情感功能會估計以音訊表示的情感。 情感是以介於0和1之間的值表示，用於 `Negative`、`Neutral`和 `Positive` 情感。 例如，情感分析可用於撥接中心案例中：

- 取得客戶滿意度的深入解析
- 取得代理程式效能的深入解析（接受呼叫的小組）
- 尋找呼叫採取負面方向的確切時間點
- 將否定的呼叫轉換為正方向時，會發生什麼狀況
- 識別客戶喜歡什麼，以及他們對產品或服務不喜歡什麼

情感是根據詞彙表單，依據每個音訊區段進行評分。 該音訊區段內的整個文字會用來計算情感。 未計算整個轉譯的匯總情感。

JSON 輸出範例如下所示：

```json
{
  "AudioFileResults": [
    {
      "AudioFileName": "Channel.0.wav",
      "AudioFileUrl": null,
      "SegmentResults": [
        {
          "RecognitionStatus": "Success",
          "ChannelNumber": null,
          "Offset": 400000,
          "Duration": 13300000,
          "NBest": [
            {
              "Confidence": 0.976174,
              "Lexical": "what's the weather like",
              "ITN": "what's the weather like",
              "MaskedITN": "what's the weather like",
              "Display": "What's the weather like?",
              "Words": null,
              "Sentiment": {
                "Negative": 0.206194,
                "Neutral": 0.793785,
                "Positive": 0.0
              }
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="best-practices"></a>最佳做法

轉譯服務可以處理大量的已提交轉譯。 您可以透過[轉譯方法](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)上的 `GET` 來查詢轉譯的狀態。 藉由指定 `take` 參數（數百個），讓資訊傳回合理的大小。 一旦您取得結果之後，請定期從服務[刪除轉譯](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)。 這可確保快速回復轉譯管理呼叫。

## <a name="sample-code"></a>範例程式碼

`samples/batch` 子目錄內的[GitHub 範例存放庫](https://aka.ms/csspeech/samples)中提供完整範例。

您自訂的範例程式碼要有訂用帳戶資訊、服務區域、指向音訊檔的 SAS URI 以轉譯，以及模型識別碼，以防您想要使用自訂原音或語言模型。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

範例程式碼會設定用戶端，並提交轉譯要求。 接著會輪詢狀態資訊並列印轉譯進度的詳細資料。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

如需上述呼叫的完整詳細資訊，請參閱我們的 [Swagger 文件](https://westus.cris.ai/swagger/ui/index)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。 如果您只有指定語言模型識別碼 (且沒有任何的原音模型識別碼)，會自動選取相符的原音模型。 如果您只有指定原音模型識別碼，則會自動選取相符的語言模型。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
