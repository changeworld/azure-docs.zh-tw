---
title: 語音轉換文字 API 參考（REST）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音轉換文字 REST API。 在本文中，您會了解到授權選項、查詢選項，以及如何建構要求與接收回應。
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: yinhew
ms.openlocfilehash: 555ae9e48f538c1100bab8b35ce61742baa88451
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659830"
---
# <a name="speech-to-text-rest-api"></a>語音轉換文字 REST API

語音服務可讓您使用 REST API 將語音轉換成文字，做為[語音 SDK](speech-sdk.md)的替代方案。 每個可存取的端點皆與區域相關聯。 您的應用程式需要您打算使用之端點的訂用帳戶金鑰。 REST API 非常有限，而且應該僅用於[語音 SDK](speech-sdk.md)無法使用的情況。

使用語音轉換文字 REST API 之前，請先瞭解：

* 使用 REST API 並直接傳輸音訊的要求最多隻能包含60秒的音訊。
* 語音轉文字 REST API 只會傳回最終結果， 不提供部分的結果。

如果您的應用程式需要傳送較長的音訊，請考慮使用[語音 SDK](speech-sdk.md)或以檔案為基礎的 REST API，例如[批次](batch-transcription.md)轉譯。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>區域與端點

REST API 的端點具有下列格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

取代 `<REGION_IDENTIFIER>` 為符合此資料表中訂用帳戶區域的識別碼：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必須將語言參數附加到 URL 後方，以避免收到 4xx HTTP 錯誤。 例如，使用美國西部端點設定為美式英文的語言是：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

## <a name="query-parameters"></a>查詢參數

REST 要求的查詢字串中可能包括這些參數。

| 參數 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `language` | 識別正在辨識的口說語言。 請參閱[支援的語言](language-support.md#speech-to-text)。 | 必要 |
| `format` | 指定結果格式。 接受的值為 `simple` 和 `detailed`。 簡單的結果包含 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 詳細的回應包含四種不同的顯示文字標記法。 預設設定是 `simple`。 | 選擇性 |
| `profanity` | 指定如何處理辨識結果中的不雅內容。 接受的值為，以星號取代不雅內容， `masked` `removed` 這會移除結果中的所有不雅內容，或 `raw` （包含結果中的不雅內容）。 預設設定是 `masked`。 | 選擇性 |
| `cid` | 使用[自訂語音入口網站](how-to-custom-speech.md)建立自訂模型時，您可以透過 [**部署**] 頁面上找到的**端點識別碼**來使用自訂模型。 使用**端點識別碼**作為 `cid` 查詢字串參數的引數。 | 選擇性 |

## <a name="request-headers"></a>要求標頭

下表列出了語音轉文字要求的必要標頭和選用標頭。

|頁首| 描述 | 必要/選用 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 您的語音服務訂用帳戶金鑰。 | 必須有此標頭或 `Authorization`。 |
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必須有此標頭或 `Ocp-Apim-Subscription-Key`。 |
| `Pronunciation-Assessment` | 指定用來在辨識結果中顯示發音分數的參數，其會評估語音輸入的發音品質，以及精確度、順暢、完整性等等的指示器。此參數是 base64 編碼的 json，其中包含多個詳細參數。 如需如何建立此標頭的詳細資料，請參閱[發音評估參數](#pronunciation-assessment-parameters)。 | 選擇性 |
| `Content-type` | 描述所提供音訊資料的格式和轉碼器。 接受的值為 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必要 |
| `Transfer-Encoding` | 指定正在傳送的音訊資料區塊，而不是單一檔案。 只有在以區塊處理音訊資料時，才能使用此標頭。 | 選擇性 |
| `Expect` | 如果使用區塊傳輸，請傳送 `Expect: 100-continue`。 語音服務會確認初始要求並等候其他資料。| 如果傳送的是音訊資料區塊，則為必要。 |
| `Accept` | 如果提供，則必須是 `application/json`。 語音服務會以 JSON 提供結果。 某些要求架構會提供不相容的預設值。 最佳做法是一律包含 `Accept` 。 | 此為選用步驟，但建議執行。 |

## <a name="audio-formats"></a>自動格式

音訊是在 HTTP `POST` 要求的主體中傳送。 它必須是此表格中的格式之一：

| 格式 | 轉碼器 | 位元速率 | 採樣速率  |
|--------|-------|----------|--------------|
| WAV    | PCM   | 256 kbps | 16 kHz，單聲道 |
| OGG    | OPUS  | 256 kpbs | 16 kHz，單聲道 |

>[!NOTE]
>您可以透過語音服務中的 REST API 和 WebSocket 來支援上述格式。 [語音 SDK](speech-sdk.md)目前支援具有 PCM 編解碼器和[其他格式](how-to-use-codec-compressed-audio-input-streams.md)的 WAV 格式。

## <a name="pronunciation-assessment-parameters"></a>發音評估參數

下表列出發音評估的必要和選擇性參數。

| 參數 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| ReferenceText | 要針對發音進行評估的文字。 | 必要 |
| GradingSystem | 用於評分校正的點系統。 接受的值為 `FivePoint` 和 `HundredMark`。 預設設定是 `FivePoint`。 | 選擇性 |
| 細微度 | 評估資料細微性。 接受的值為 `Phoneme` ，它會顯示全文檢索單字和音素層級的分數，這會顯示全文檢索 `Word` 和字層級的分數，這 `FullText` 只會顯示全文檢索層級的分數。 預設設定是 `Phoneme`。 | 選擇性 |
| 維度 | 定義輸出準則。 接受的值為 `Basic` （僅顯示精確度分數），會 `Comprehensive` 顯示更多維度的分數（例如，在全文檢索層級上順暢分數和完整性分數，word 層級的錯誤類型）。 請檢查[回應參數](#response-parameters)，以查看不同分數維度和 word 錯誤類型的定義。 預設設定是 `Basic`。 | 選擇性 |
| EnableMiscue | 啟用 miscue 計算。 啟用此功能時，發音會與參考文字進行比較，而且會根據比較來標示省略/插入。 接受的值為 `False` 和 `True`。 預設設定是 `False`。 | 選擇性 |
| ScenarioId | 指出自訂點系統的 GUID。 | 選擇性 |

以下是包含發音評估參數的 JSON 範例：

```json
{
  "ReferenceText": "Good morning.",
  "GradingSystem": "HundredMark",
  "Granularity": "FullText",
  "Dimension": "Comprehensive"
}
```

下列範例程式碼示範如何在標頭中建立發音評估參數 `Pronunciation-Assessment` ：

```csharp
var pronAssessmentParamsJson = $"{{\"ReferenceText\":\"Good morning.\",\"GradingSystem\":\"HundredMark\",\"Granularity\":\"FullText\",\"Dimension\":\"Comprehensive\"}}";
var pronAssessmentParamsBytes = Encoding.UTF8.GetBytes(pronAssessmentParamsJson);
var pronAssessmentHeader = Convert.ToBase64String(pronAssessmentParamsBytes);
```

>[!NOTE]
>發音評估功能目前僅適用于 `westus` 和 `eastasia` 區域。 這項功能目前僅適用于 `en-US` 語言。

## <a name="sample-request"></a>範例要求

下列範例包含主機名稱和必要標頭。 請務必注意，此服務也預期會有此範例中未包含的音訊資料。 如先前所述，建議以區塊處理，但非必要。

```HTTP
POST speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY
Host: westus.stt.speech.microsoft.com
Transfer-Encoding: chunked
Expect: 100-continue
```

若要啟用發音評量，您可以新增下列標頭。 如需如何建立此標頭的詳細資料，請參閱[發音評估參數](#pronunciation-assessment-parameters)。

```HTTP
Pronunciation-Assessment: eyJSZWZlcm...
```

## <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| `100` | 繼續 | 已接受初始要求。 繼續傳送其餘的資料。 （搭配區區塊轉送使用） |
| `200` | 確定 | 要求成功；回應主體是 JSON 物件。 |
| `400` | 不正確的要求 | 未提供語言代碼，而不是支援的語言、不正確音訊檔案等。 |
| `401` | 未經授權 | 訂用帳戶金鑰或授權權杖在指定的區域中無效，或是無效的端點。 |
| `403` | 禁止 | 遺漏訂用帳戶金鑰或授權權杖。 |

## <a name="chunked-transfer"></a>區塊傳輸

區塊傳輸（ `Transfer-Encoding: chunked` ）可協助減少辨識延遲。 它可讓語音服務在音訊檔案進行傳輸時開始處理。 REST API 不會提供部分或中間的結果。

此程式碼範例說明如何以區塊傳送音訊。 只有第一個區塊應該包含音訊檔案的標頭。 `request`是 `HttpWebRequest` 連接到適當 REST 端點的物件。 `audioFile` 是音訊檔案在磁碟上的路徑。

```csharp
var request = (HttpWebRequest)HttpWebRequest.Create(requestUri);
request.SendChunked = true;
request.Accept = @"application/json;text/xml";
request.Method = "POST";
request.ProtocolVersion = HttpVersion.Version11;
request.Host = host;
request.ContentType = @"audio/wav; codecs=audio/pcm; samplerate=16000";
request.Headers["Ocp-Apim-Subscription-Key"] = "YOUR_SUBSCRIPTION_KEY";
request.AllowWriteStreamBuffering = false;

using (var fs = new FileStream(audioFile, FileMode.Open, FileAccess.Read))
{
    // Open a request stream and write 1024 byte chunks in the stream one at a time.
    byte[] buffer = null;
    int bytesRead = 0;
    using (var requestStream = request.GetRequestStream())
    {
        // Read 1024 raw bytes from the input audio file.
        buffer = new Byte[checked((uint)Math.Min(1024, (int)fs.Length))];
        while ((bytesRead = fs.Read(buffer, 0, buffer.Length)) != 0)
        {
            requestStream.Write(buffer, 0, bytesRead);
        }

        requestStream.Flush();
    }
}
```

## <a name="response-parameters"></a>回應參數

結果以 JSON 格式提供。 `simple` 格式包含以下的最上層欄位。

| 參數 | 描述  |
|-----------|--------------|
|`RecognitionStatus`|狀態，例如 `Success` 代表辨識成功。 請參閱下一個表格。|
|`DisplayText`|大小寫、標點符號、反向文字正規化後的已辨識文字（將語音文字轉換成較短的表單，例如200代表 "200" 或 "Dr. smith" 代表 "醫生 Smith"），以及不雅內容遮罩。 只會在成功時呈現。|
|`Offset`|辨識的語音在音訊資料流中開始的時間 (以 100 奈秒為單位)。|
|`Duration`|辨識的語音在音訊資料流中的持續時間 (以 100 奈秒為單位)。|

`RecognitionStatus` 欄位可包含下列的值：

| 狀態 | 描述 |
|--------|-------------|
| `Success` | 辨識成功並顯示 `DisplayText` 欄位。 |
| `NoMatch` | 音訊串流中偵測到語音，但目標語言中沒有符合的字組。 通常表示辨識語言與使用者的口語語言不同。 |
| `InitialSilenceTimeout` | 音訊串流的開頭沒有任何聲音，而且等候語音的服務已逾時。 |
| `BabbleTimeout` | 音訊串流的開頭只有雜音，而且等候語音的服務已逾時。 |
| `Error` | 辨識服務發生內部錯誤，無法繼續。 可能的話，再試一次。 |

> [!NOTE]
> 如果音訊只包含不雅內容，而且 `profanity` 查詢參數設為 `remove`，則服務不會傳回語音結果。

`detailed`格式包括其他形式的已辨識結果。
使用 `detailed` 格式時，系統會提供 `DisplayText` 作為 `NBest` 清單中每個結果的 `Display`。

清單中的物件 `NBest` 可以包括：

| 參數 | 描述 |
|-----------|-------------|
| `Confidence` | 項目的信賴分數從 0.0 (不信賴) 到 1.0 (完全信賴) |
| `Lexical` | 已辨識文字的語彙形式：已辨識的實際文字。 |
| `ITN` | 已辨識文字的反向文字正規化 (「標準」) 形式，包含電話號碼、數字、縮寫 ("doctor smith" 縮短為 "dr smith")，以及其他已套件的轉換。 |
| `MaskedITN` | 如果要求，已套用不雅內容遮罩的 ITN 形式。 |
| `Display` | 已辨識文字的顯示形式，已新增標點符號和大寫。 此參數與當格式設定為 `simple` 時，所提供的 `DisplayText` 相同。 |
| `AccuracyScore` | 表示指定語音之發音精確度的分數。 |
| `FluencyScore` | 表示指定之語音順暢的分數。 |
| `CompletenessScore` | 表示指定語音之完整性的分數，其方式是計算發音與整個輸入的比例。 |
| `PronScore` | 表示指定語音之發音品質的整體分數。 這是從 `AccuracyScore` 、 `FluencyScore` 和加權計算而來 `CompletenessScore` 。 |
| `ErrorType` | 這個值會指出相較于，是否省略、插入或發音不正確的字 `ReferenceText` 。 可能的值為 `None` （表示此單字上沒有錯誤） `Omission` 、 `Insertion` 和 `Mispronunciation` 。 |

## <a name="sample-responses"></a>回應範例

辨識的一般回應 `simple` ：

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

辨識的一般回應 `detailed` ：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "1236645672289",
  "Duration": "1236645672289",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "remind me to buy five pencils",
        "ITN" : "remind me to buy 5 pencils",
        "MaskedITN" : "remind me to buy 5 pencils",
        "Display" : "Remind me to buy 5 pencils.",
      }
  ]
}
```

使用發音評量進行辨識的一般回應：

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronScore" : 84.4,
        "AccuracyScore" : 100.0,
        "FluencyScore" : 74.0,
        "CompletenessScore" : 100.0,
        "Words": [
            {
              "Word" : "Good",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 500000,
              "Duration" : 2700000
            },
            {
              "Word" : "morning",
              "AccuracyScore" : 100.0,
              "ErrorType" : "None",
              "Offset" : 5300000,
              "Duration" : 900000
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
