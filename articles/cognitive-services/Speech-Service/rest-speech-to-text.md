---
title: 語音到文本 API 引用 （REST） - 語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用語音到文本 REST API。 在本文中，您會了解到授權選項、查詢選項，以及如何建構要求與接收回應。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: dapine
ms.openlocfilehash: 759ea697e4093da5bfc1c082c886c6dfda636f42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474793"
---
# <a name="speech-to-text-rest-api"></a>語音轉換文字 REST API

作為[語音 SDK](speech-sdk.md)的替代方法，語音服務允許您使用 REST API 將語音轉換為文本。 每個可存取的端點皆與區域相關聯。 您的應用程式需要您打算使用之端點的訂用帳戶金鑰。 REST API 非常有限，只有在[語音 SDK](speech-sdk.md)不能的情況下才應使用它。

在使用語音到文本 REST API 之前，請瞭解：

* 使用 REST API 並直接傳輸音訊的請求只能包含長達 60 秒的音訊。
* 語音轉文字 REST API 只會傳回最終結果， 不提供部分的結果。

如果發送較長的音訊是應用程式的要求，請考慮使用[語音 SDK](speech-sdk.md)或基於檔的 REST API，如[批次處理轉錄](batch-transcription.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="regions-and-endpoints"></a>區域與端點

REST API 的終結點具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

替換`<REGION_IDENTIFIER>`與匹配此表中訂閱區域的識別碼：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必須將語言參數附加到 URL 後方，以避免收到 4xx HTTP 錯誤。 例如，使用美國西部端點設定為美式英文的語言是：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

## <a name="query-parameters"></a>查詢參數

REST 要求的查詢字串中可能包括這些參數。

| 參數 | 描述 | 必要/選用 |
|-----------|-------------|---------------------|
| `language` | 識別正在辨識的口說語言。 請參閱[支援的語言](language-support.md#speech-to-text)。 | 必要 |
| `format` | 指定結果格式。 接受的值為 `simple` 和 `detailed`。 簡單的結果包含 `RecognitionStatus`、`DisplayText`、`Offset` 和 `Duration`。 詳細的回應包含多個具有信賴值的結果和四個不同的表示法。 預設值是 `simple`。 | 選用 |
| `profanity` | 指定如何處理辨識結果中的不雅內容。 接受的值是`masked`，用 星號替換褻瀆，`removed`從結果中刪除所有褻瀆， 或`raw`， 包括結果中的褻瀆。 預設值是 `masked`。 | 選用 |
| `cid` | 使用[自訂語音門戶](how-to-custom-speech.md)創建自訂模型時，可以通過 **"部署"** 頁上的**終結點 ID**使用自訂模型。 使用**終結點 ID**作為查詢字串參數`cid`的參數。 | 選用 |

## <a name="request-headers"></a>要求標頭

下表列出了語音轉文字要求的必要標頭和選用標頭。

|頁首| 描述 | 必要/選用 |
|------|-------------|---------------------|
| `Ocp-Apim-Subscription-Key` | 您的語音服務訂用帳戶金鑰。 | 必須有此標頭或 `Authorization`。 |
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必須有此標頭或 `Ocp-Apim-Subscription-Key`。 |
| `Content-type` | 描述所提供音訊資料的格式和轉碼器。 接受的值為 `audio/wav; codecs=audio/pcm; samplerate=16000` 和 `audio/ogg; codecs=opus`。 | 必要 |
| `Transfer-Encoding` | 指定正在傳送的音訊資料區塊，而不是單一檔案。 只有在以區塊處理音訊資料時，才能使用此標頭。 | 選用 |
| `Expect` | 如果使用區塊傳輸，請傳送 `Expect: 100-continue`。 語音服務會確認初始要求並等候其他資料。| 如果傳送的是音訊資料區塊，則為必要。 |
| `Accept` | 如果提供，則必須是 `application/json`。 語音服務在 JSON 中提供結果。 某些請求框架提供不相容的預設值。 最好總是包括`Accept`。 | 此為選用步驟，但建議執行。 |

## <a name="audio-formats"></a>自動格式

音訊是在 HTTP `POST` 要求的主體中傳送。 它必須是此表格中的格式之一：

| [格式] | 轉碼器 | Bitrate | 採樣速率  |
|--------|-------|---------|--------------|
| WAV    | PCM   | 16 位元  | 16 kHz，單聲道 |
| OGG    | OPUS  | 16 位元  | 16 kHz，單聲道 |

>[!NOTE]
>上述格式通過語音服務中的 REST API 和 WebSocket 支援。 [語音 SDK](speech-sdk.md)目前支援帶有 PCM 編解碼器[以及其他格式](how-to-use-codec-compressed-audio-input-streams.md)的 WAV 格式。

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

## <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| `100` | Continue | 已接受初始要求。 繼續傳送其餘的資料。 （與塊傳輸一起使用） |
| `200` | [確定] | 要求成功；回應主體是 JSON 物件。 |
| `400` | 不正確的要求 | 未提供語言代碼，不支援的語言、不正確音訊檔等。 |
| `401` | 未經授權 | 訂用帳戶金鑰或授權權杖在指定的區域中無效，或是無效的端點。 |
| `403` | 禁止 | 遺漏訂用帳戶金鑰或授權權杖。 |

## <a name="chunked-transfer"></a>區塊傳輸

塊狀傳輸`Transfer-Encoding: chunked`（ ） 有助於減少識別延遲。 它允許語音服務在傳輸音訊檔時開始處理該檔。 REST API 不會提供部分或中間的結果。

此程式碼範例說明如何以區塊傳送音訊。 只有第一個區塊應該包含音訊檔案的標頭。 `request`是`HttpWebRequest`連接到相應 REST 終結點的物件。 `audioFile` 是音訊檔案在磁碟上的路徑。

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
|`DisplayText`|大寫後識別的文本，標點符號，反文本正常化（將口語文本轉換為較短的形式，如200表示"200"或"史密斯博士"為"史密斯醫生"），以及褻瀆性掩蓋。 只會在成功時呈現。|
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

格式`detailed`包括與`simple`格式相同的資料，以及`NBest`同一識別結果的替代解釋清單。 這些結果的排名從最不可能到最不可能。 第一個項目與主要辨識結果相同。  使用 `detailed` 格式時，系統會提供 `DisplayText` 作為 `NBest` 清單中每個結果的 `Display`。

`NBest` 清單中的每個物件包括：

| 參數 | 描述 |
|-----------|-------------|
| `Confidence` | 項目的信賴分數從 0.0 (不信賴) 到 1.0 (完全信賴) |
| `Lexical` | 已辨識文字的語彙形式：已辨識的實際文字。 |
| `ITN` | 已辨識文字的反向文字正規化 (「標準」) 形式，包含電話號碼、數字、縮寫 ("doctor smith" 縮短為 "dr smith")，以及其他已套件的轉換。 |
| `MaskedITN` | 如果要求，已套用不雅內容遮罩的 ITN 形式。 |
| `Display` | 已辨識文字的顯示形式，已新增標點符號和大寫。 此參數與當格式設定為 `simple` 時，所提供的 `DisplayText` 相同。 |

## <a name="sample-responses"></a>回應範例

`simple`識別的典型回應：

```json
{
  "RecognitionStatus": "Success",
  "DisplayText": "Remind me to buy 5 pencils.",
  "Offset": "1236645672289",
  "Duration": "1236645672289"
}
```

`detailed`識別的典型回應：

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
      },
      {
        "Confidence" : "0.54",
        "Lexical" : "rewind me to buy five pencils",
        "ITN" : "rewind me to buy 5 pencils",
        "MaskedITN" : "rewind me to buy 5 pencils",
        "Display" : "Rewind me to buy 5 pencils.",
      }
  ]
}
```

## <a name="next-steps"></a>後續步驟

- [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
