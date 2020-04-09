---
title: 什麼是批次處理轉錄 - 語音服務
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
ms.openlocfilehash: 1f88df186526c2f9903337bb3331940be0989c3d
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80892456"
---
# <a name="what-is-batch-transcription"></a>什麼是批次轉錄?

批次處理轉錄是一組 REST API 操作,使您能夠在儲存中轉錄大量音訊。 您可以使用共享訪問簽名 (SAS) URI 指向音訊檔,並非同步接收轉錄結果。

非同步語音轉文字轉錄只是其中一個功能。 您可以使用批次處理轉錄 REST API 呼叫以下方法:



|    批次轉錄作業                                             |    方法    |    REST API 呼叫                                   |
|------------------------------------------------------------------------------|--------------|----------------------------------------------------|
|    創建新的轉錄。                                              |    POST      |    api/語音文字/v2.0/轉錄            |
|    檢索已身份驗證訂閱的轉錄清單。    |    GET       |    api/語音文字/v2.0/轉錄            |
|    獲取離線轉錄受支援區域設置的清單。              |    GET       |    api/語音文字/v2.0/轉錄/區域設定    |
|    更新其 ID 標識的轉錄的可變詳細資訊。    |    PATCH     |    api/語音文字/v2.0/轉錄/{id}       |
|    刪除指定的轉錄任務。                                 |    刪除    |    api/語音文字/v2.0/轉錄/{id}       |
|    獲取給定 ID 標識的轉錄。                        |    GET       |    api/語音文字/v2.0/轉錄/{id}       |




您可以在標題`Custom Speech transcriptions`下查看和測試詳細的 API,該 API 可作為[Swagger 文件](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A)提供。

批處理轉錄作業是盡最大努力安排的。 當前,無法估計作業何時更改為運行狀態。 在正常系統負載下,應在幾分鐘內發生。 一旦處於運行狀態,實際轉錄的處理速度將快於音訊即時處理。

在易於使用的 API 旁邊,您不需要部署自定義終結點,並且沒有任何併發要求需要遵守。

## <a name="prerequisites"></a>Prerequisites

### <a name="subscription-key"></a>訂用帳戶金鑰

如同語音服務的所有功能，您可以依照我們的[快速入門指南](get-started.md)從 [Azure 入口網站](https://portal.azure.com)建立訂用帳戶金鑰。

>[!NOTE]
> 語音服務的標準訂閱 (S0) 需要使用批處理轉錄。 免費訂閱密鑰 (F0) 不起作用。 有關詳細資訊,請參閱[定價和限制](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

### <a name="custom-models"></a>自訂模型

如果您計劃自定義聲學或語言模型,請按照[自定義聲學模型](how-to-customize-acoustic-models.md)和[設計自定義語言模型](how-to-customize-language-model.md)中的步驟操作。 要在批次處理轉錄中使用建立的模型,您需要其模型 I。 在檢查模型的詳細資訊時,可以檢索模型 ID。 批處理轉錄服務不需要部署的自定義終結點。

## <a name="the-batch-transcription-api"></a>Batch 轉譯 API

### <a name="supported-formats"></a>支援的格式

Batch 轉譯 API 支援下列格式：

| [格式] | 轉碼器 | Bitrate | 採樣速率                     |
|--------|-------|---------|---------------------------------|
| WAV    | PCM   | 16 位元  | 8 kHz 或 16 kHz,單聲道或立體聲 |
| MP3    | PCM   | 16 位元  | 8 kHz 或 16 kHz,單聲道或立體聲 |
| OGG    | OPUS  | 16 位元  | 8 kHz 或 16 kHz,單聲道或立體聲 |

對於立體聲音頻流,在轉錄過程中將拆分左右聲道。 對於每個通道,正在創建一個 JSON 結果檔。 通過每個陳述生成的時間戳,開發人員可以創建有序的最終成績單。

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

使用這些選擇屬性設定轉錄:

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
      指定如何處理辨識結果中的不雅內容。 接受的值是`None`禁用褻瀆篩選`Masked`, 用星號取代褻`Removed`瀆, 從結果中刪除所有褻`Tags`瀆,或添加"褻瀆"標記。 預設值是 `Masked`。
:::row-end:::
:::row:::
   :::column span="1":::
      `PunctuationMode`
   :::column-end:::
   :::column span="2":::
      指定如何處理辨識結果中的標點符號。 接受的值是`None`關閉標點符號,`Dictated`表示顯示式(口頭)標點符號`Automatic`, 讓解碼器處理標點符號,`DictatedAndAutomatic`或使用聽寫和自動標點符號。 預設值是 `DictatedAndAutomatic`。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddWordLevelTimestamps`
   :::column-end:::
   :::column span="2":::
      指定是否將字組層級時間戳記新增至輸出。 接受的值是`true`啟用單詞級時間戳`false`和 (預設值)來禁用它。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddSentiment`
   :::column-end:::
   :::column span="2":::
      指定是否應將情緒分析應用於陳述。 接受的值是`true`啟用`false`和 (預設值)來禁用它。 有關詳細資訊[,請參閱情緒分析](#sentiment-analysis)。
:::row-end:::
:::row:::
   :::column span="1":::
      `AddDiarization`
   :::column-end:::
   :::column span="2":::
      指定應在輸入上執行二分化分析,該輸入預期是包含兩個語音的單聲道。 接受的值啟用`true`二分化`false`和 (預設值)來禁用它。 它還需要`AddWordLevelTimestamps`設置為 true。
:::row-end:::
:::row:::
   :::column span="1":::
      `TranscriptionResultsContainerUrl`
   :::column-end:::
   :::column span="2":::
      具有服務[SAS 的](../../storage/common/storage-sas-overview.md)可選 URL 到 Azure 中的可寫入容器。 結果存儲在此容器中。
:::row-end:::

### <a name="storage"></a>儲存體

批次處理轉錄支援[Azure Blob 儲存](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview),用於讀取音訊並將轉錄寫入儲存。

## <a name="the-batch-transcription-result"></a>批次處理轉錄結果

對於單聲道輸入音訊,正在創建一個轉錄結果檔。 對於立體聲輸入音訊,正在創建兩個轉錄結果檔。 每個結構都有此結構:

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

結果包含以下表單:

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
      識別的實際單詞。
:::row-end:::
:::row:::
   :::column span="1":::
      `ITN`
   :::column-end:::
   :::column span="2":::
      識別文本的反向文本規範化形式。 應用縮寫("醫生史密斯"到"醫生史密斯"),電話號碼和其他轉換。
:::row-end:::
:::row:::
   :::column span="1":::
      `MaskedITN`
   :::column-end:::
   :::column span="2":::
      應用了帶有褻瀆性蒙版的 ITN 形式。
:::row-end:::
:::row:::
   :::column span="1":::
      `Display`
   :::column-end:::
   :::column span="2":::
      識別文本的顯示形式。 包括添加的標點符號和大小寫。
:::row-end:::

## <a name="speaker-separation-diarization"></a>揚聲器分離(分離)

二化是將揚聲器分離到音訊片段的過程。 我們的 Batch 管道支援二分化,並能夠在單通道錄製上識別兩個揚聲器。 該功能在立體聲錄製中不可用。

所有轉錄輸出都包含`SpeakerId`。 如果未使用二分法,則它在 JSON 輸出`"SpeakerId": null`中顯示 。 對於二分化,我們支援兩個聲音,因此揚聲器被標識為`"1"``"2"`或 。

要請求二分化,只需在 HTTP 請求中添加相關參數,如下所示。

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

Word 級別時間戳也必須"打開",如上述請求中的參數所示。

## <a name="sentiment-analysis"></a>情感分析

情緒特徵估計音訊中表達的情緒。 情緒由 0 和`Negative`1`Neutral`之間的`Positive`值表示 ,和 情緒。 例如,情緒分析可用於呼叫中心方案:

- 深入瞭解客戶滿意度
- 深入瞭解代理的性能(團隊接聽電話)
- 尋找撥號轉到負方向的確切時間點
- 把負呼叫變成積極方向時,什麼進展順利
- 確定客戶喜歡什麼,不喜歡什麼產品或服務

根據詞法形式,每個音訊段對情緒進行評分。 該音訊段中的整個文本用於計算情緒。 沒有計算整個轉錄的總情緒。 目前情緒分析僅適用於英語。

> [!NOTE]
> 我們建議改用 Microsoft 文本分析 API。 它提供了超越情緒分析的更高級的功能,如關鍵短語提取、自動語言檢測等。 您可以在[文字分析文件中](https://azure.microsoft.com/services/cognitive-services/text-analytics/)找到資訊和範例。
>

JSON 輸出示例如下所示:

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

轉錄服務可以處理大量提交的轉錄。 您可以`GET`通過[轉錄方法](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/GetTranscriptions)查詢轉錄的狀態。 通過指定`take`參數(幾百個),使資訊返回到合理的大小。 檢索結果後,定期從服務[中刪除轉錄](https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20transcriptions%3A/DeleteTranscription)。 這保證了從轉錄管理調用的快速答覆。

## <a name="sample-code"></a>範例程式碼

完整的範例可在子目錄中的`samples/batch` [GitHub 範例儲存庫](https://aka.ms/csspeech/samples)中找到。

您自訂的範例程式碼要有訂用帳戶資訊、服務區域、指向音訊檔的 SAS URI 以轉譯，以及模型識別碼，以防您想要使用自訂原音或語言模型。

[!code-csharp[Configuration variables for batch transcription](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchdefinition)]

範例代碼設置用戶端並提交轉錄請求。 然後,它會輪詢狀態資訊並列印有關轉錄進度的詳細資訊。

[!code-csharp[Code to check batch transcription status](~/samples-cognitive-services-speech-sdk/samples/batch/csharp/program.cs#batchstatus)]

有關上述話務的完整詳細資訊,請參閱我們的[斯瓦格文件](https://westus.cris.ai/swagger/ui/index)。 如需此處所顯示的完整範例，請前往 `samples/batch` 子目錄中的 [GitHub](https://aka.ms/csspeech/samples)。

請留意張貼音訊和接收轉譯狀態的非同步設定。 您建立的用戶端是 .NET HTTP 用戶端。 `PostTranscriptions` 方法可傳送音訊檔案詳細資料，而 `GetTranscriptions` 方法可接收結果。 `PostTranscriptions` 會傳回控制代碼，然後 `GetTranscriptions` 使用它來取得轉譯狀態。

目前的範例程式碼沒有指定自訂模型。 該服務會使用基準模型來轉譯一或多個檔案。 若要指定模型，您可以傳遞相同的方法，如同原音和語言模型的模型識別碼。

> [!NOTE]
> 針對基準轉譯，您不需要宣告基準模型的識別碼。 如果您只有指定語言模型識別碼 (且沒有任何的原音模型識別碼)，會自動選取相符的原音模型。 如果您只有指定原音模型識別碼，則會自動選取相符的語言模型。

## <a name="download-the-sample"></a>下載範例

您可以在 [GitHub 範例存放庫](https://aka.ms/csspeech/samples)中的 `samples/batch` 目錄找到範例。

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
