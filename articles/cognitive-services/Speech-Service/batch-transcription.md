---
title: 什麼是批次轉譯-語音服務
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
ms.openlocfilehash: 46bfabfb2ccf091fd5dc0fcf0e9b447bad7c34d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/29/2020
ms.locfileid: "82208613"
---
# <a name="what-is-batch-transcription"></a>什麼是批次轉譯？

批次轉譯是一組 REST API 作業，可讓您在儲存體中轉譯大量的音訊。 您可以使用共用存取簽章（SAS） URI 來指向音訊檔案，並以非同步方式接收轉譯結果。

非同步語音轉換文字轉譯只是其中一項功能。 您可以使用批次轉譯 REST Api 來呼叫下列方法：



|    批次轉譯作業                                             |    方法    |    REST API 呼叫                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    建立新的轉譯。                                              |    POST      |    api/speechtotext/v2.0/轉譯            |
|    抓取已驗證之訂用帳戶的轉譯清單。    |    GET       |    api/speechtotext/v2.0/轉譯            |
|    取得離線轉譯支援的地區設定清單。              |    GET       |    api/speechtotext/v2.0/轉譯/地區設定    |
|    更新識別碼所識別之轉譯的可變詳細資料。    |    PATCH     |    api/speechtotext/v2.0/轉譯/{id}       |
|    刪除指定的轉譯工作。                                 |    刪除    |    api/speechtotext/v2.0/轉譯/{id}       |
|    取得指定識別碼所識別的轉譯。                        |    GET       |    api/speechtotext/v2.0/轉譯/{id}       |




您可以在標題`Custom Speech transcriptions`底下，檢查並測試詳細的 API，這是以[Swagger 檔](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)的形式提供。

批次轉譯作業是以最大的方式排定。 目前，當作業變更為執行中狀態時，不會有任何估計值。 在一般系統負載下，應該會在幾分鐘內發生。 一旦處於 [執行中] 狀態，實際轉譯的處理速度會比音訊即時長。

在容易使用的 API 旁，您不需要部署自訂端點，也不會有任何要觀察的並行需求。

## <a name="prerequisites"></a>先決條件

### <a name="subscription-key"></a>訂用帳戶金鑰

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。

>[!NOTE]
> 需要「語音服務」的標準訂用帳戶（S0）才能使用批次轉譯。 免費訂用帳戶金鑰（F0）無法使用。 如需詳細資訊，請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自訂模型

如果您打算自訂聲場或語言模型，請依照[自訂聲場模型](how-to-customize-acoustic-models.md)和[設計自訂語言模型](how-to-customize-language-model.md)中的步驟進行。 若要在批次轉譯中使用已建立的模型，您需要其模型識別碼。 您可以在檢查模型的詳細資料時，取得模型識別碼。 批次轉譯服務不需要已部署的自訂端點。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| 格式 | 轉碼器 | Bitrate | 採樣速率                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位元  | 8 kHz 或 16 kHz、單聲道或身歷聲 |
| MP3    | PCM   | 16 位元  | 8 kHz 或 16 kHz、單聲道或身歷聲 |
| OGG    | OPUS  | 16 位元  | 8 kHz 或 16 kHz、單聲道或身歷聲 |

針對身歷聲音訊資料流程，左右聲道會在轉譯期間分割。 針對每個通道，會建立 JSON 結果檔案。 每個語句產生的時間戳記可讓開發人員建立排序的最終文字記錄。

### <a name="configuration"></a>設定

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

:::row:::
   :::column span="1":::
      **參數**
   :::column-end:::
   :::column span="2":::
      **說明**
:::row-end:::
:::row:::
   :::column span="1":::
      `ProfanityFilterMode`
   :::column-end:::
   :::column span="2":::
      指定如何處理辨識結果中的不雅內容。 接受的值`None`為停用不雅`Masked`內容篩選、以星號`Removed`取代不雅內容、移除結果中的所有`Tags`不雅內容，或新增「不雅內容」標記。 預設值是 `Masked`。
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      指定如何處理辨識結果中的標點符號。 接受的值`None`為停用標點符號`Dictated` 、表示明確（讀出）標點符號、 `Automatic`讓解碼器處理標點符號，或`DictatedAndAutomatic`使用聽寫和自動標點符號。 預設值是 `DictatedAndAutomatic`。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      指定是否將字組層級時間戳記新增至輸出。 接受的值`true`是啟用 word 層級時間`false`戳和（預設值）來停用它。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      指定情感分析是否應套用至語句。 接受的值`true`是啟用和`false` （預設值）來停用它。 如需詳細資訊，請參閱[情感分析](#sentiment-analysis)。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      指定應該在輸入上執行 diarization 分析，這應該是包含兩個語音的 mono 通道。 接受的值`true`會啟用 diarization `false`和（預設值）來停用它。 它也需要`AddWordLevelTimestamps`設定為 true。
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      選擇性的 URL，其中包含[服務 SAS](../../storage/common/storage-sas-overview.md)至 Azure 中可寫入的容器。 結果會儲存在此容器中。
:::row-end:::

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
          "Offset": number                                  'time in ticks (1 tick is 100 nanosec)'
          "Duration": number                                'time in ticks (1 tick is 100 nanosec)'
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
                  "Offset": number                          'time in ticks (1 tick is 100 nanosec)'
                  "Duration": number                        'time in ticks (1 tick is 100 nanosec)'
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

:::row:::
   :::column span="1":::
      **表單**
   :::column-end:::
   :::column span="2":::
      **內容**
:::row-end:::
:::row:::
   :::column span="1":::
      `Lexical`
   :::column-end:::
   :::column span="2":::
      已辨識的實際文字。
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      已辨識文字的反向文字正規化形式。 會套用縮寫（"醫生 smith" 至 "dr smith"）、電話號碼和其他轉換。
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      套用不雅內容遮罩的 ITN 表單。
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      已辨識文字的顯示形式。 包含已新增的標點符號和大小寫。
:::row-end:::

## <a name="speaker-separation-diarization"></a>說話者分隔（Diarization）

Diarization 是在一段音訊中分隔喇叭的程式。 我們的批次管線支援 diarization，而且能夠在 mono 通道錄製上辨識兩個喇叭。 這項功能不適用於身歷聲記錄。

所有轉譯輸出都包含`SpeakerId`。 如果未使用 diarization，它會顯示`"SpeakerId": null`在 JSON 輸出中。 針對 diarization，我們支援兩種語音，因此會將喇叭`"1"`識別`"2"`為或。

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

## <a name="sentiment-analysis"></a>情感分析

情感功能會估計以音訊表示的情感。 情感是以介於0和1之間的值表示`Negative`、 `Neutral`和`Positive`情感。 例如，情感分析可用於撥接中心案例中：

- 取得客戶滿意度的深入解析
- 取得代理程式效能的深入解析（接受呼叫的小組）
- 尋找呼叫採取負面方向的確切時間點
- 將否定的呼叫轉換為正方向時，會發生什麼狀況
- 識別客戶喜歡什麼，以及他們對產品或服務不喜歡什麼

情感是根據詞彙表單，依據每個音訊區段進行評分。 該音訊區段內的整個文字會用來計算情感。 未計算整個轉譯的匯總情感。 情感分析目前僅提供英文版。

> [!NOTE]
> 我們建議改為使用 Microsoft 文字分析 API。 它除了情感分析之外，還提供更先進的功能，像是關鍵字組解壓縮、自動語言偵測等等。 您可以在[文字分析檔](https://azure.microsoft.com/services/cognitive-services/text-analytics/)中找到資訊和範例。
>

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

## <a name="best-practices"></a>最佳作法

轉譯服務可以處理大量的已提交轉譯。 您可以透過`GET` [轉譯方法](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)上的來查詢轉譯的狀態。 藉由指定`take`參數（數百個），讓資訊傳回合理的大小。 一旦您取得結果之後，請定期從服務[刪除轉譯](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)。 這可保證轉譯管理呼叫的快速回復。

## <a name="sample-code"></a>範例程式碼

您可以在`samples/batch`子目錄內的[GitHub 範例存放庫](https://aka.ms/csspeech/samples)中取得完整範例。

您自訂的範例程式碼要有訂用帳戶資訊、服務區域、指向音訊檔的 SAS URI 以轉譯，以及模型識別碼，以防您想要使用自訂原音或語言模型。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

範例程式碼會設定用戶端，並提交轉譯要求。 然後，它會輪詢狀態資訊並列印有關轉譯進度的詳細資料。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

如需上述呼叫的完整詳細資料，請參閱我們的[Swagger 檔](https://westus.cris.ai/swagger/ui/index)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。 如果您只有指定語言模型識別碼 (且沒有任何的原音模型識別碼)，會自動選取相符的原音模型。 如果您只有指定原音模型識別碼，則會自動選取相符的語言模型。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
