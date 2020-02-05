---
title: 文字轉換語音 API 參考（REST）-語音服務
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用文字轉換語音 REST API。 在本文中，您會了解到授權選項、查詢選項，以及如何建構要求與接收回應。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: erhopf
ms.openlocfilehash: ab0891653f449b13f50dc43b196cf16a2f71370e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975817"
---
# <a name="text-to-speech-rest-api"></a>文字轉換語音 REST API

語音服務可讓您[將文字轉換成合成的語音](#convert-text-to-speech)，並使用一組 REST api 取得區域的[支援語音清單](#get-a-list-of-voices)。 每個可用的端點都會與某個區域相關聯。 您打算使用的端點/區域必須要有訂用帳戶金鑰。

文字轉語音 API 支援類神經和標準文字轉語音，且各支援依地區設定所識別的特定語言和方言。

* 如語音的完整清單，請參閱[ 語言支援](language-support.md#text-to-speech)。
* 如需區域可用性的詳細資訊，請參閱[區域](regions.md#text-to-speech)。

> [!IMPORTANT]
> 標準、自訂和神經語音的成本各不相同。 如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

使用此 API 之前，請先瞭解：

* 文字轉語音 REST API 需要授權標頭。 這表示需要完成權杖交換，才能存取服務。 如需詳細資訊，請參閱[驗證](#authentication)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-rest-auth.md)]

## <a name="get-a-list-of-voices"></a>取得語音清單

`voices/list` 端點可讓您取得特定地區/端點的完整語音清單。

### <a name="regions-and-endpoints"></a>區域與端點

| 地區 | 端點 |
|--------|----------|
| 澳大利亞東部 | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 巴西南部 | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 加拿大中部 | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國中部 | `https://centralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 東亞 | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國東部 | `https://eastus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國東部 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 法國中部 | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 印度中部 | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 日本東部 | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 南韓中部 | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國中北部 | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 歐洲北部 | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國中南部 | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 東南亞 | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 英國南部 | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 歐洲西部 | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國西部 | `https://westus.tts.speech.microsoft.com/cognitiveservices/voices/list` |
| 美國西部 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/voices/list` |

### <a name="request-headers"></a>要求標頭

下表列出文字轉換語音要求的必要和選擇性標頭。

| 標頭 | 描述 | 必要/選用 |
|--------|-------------|---------------------|
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必要項 |

### <a name="request-body"></a>Request body

`GET` 此端點的要求不需要主體。

### <a name="sample-request"></a>範例要求

此要求只需要授權標頭。

```http
GET /cognitiveservices/voices/list HTTP/1.1

Host: westus.tts.speech.microsoft.com
Authorization: Bearer [Base64 access_token]
```

### <a name="sample-response"></a>範例回應

此回應已被截斷，以說明回應的結構。

> [!NOTE]
> 語音可用性因地區/端點而異。

```json
[
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)",
        "ShortName": "ar-EG-Hoda",
        "Gender": "Female",
        "Locale": "ar-EG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)",
        "ShortName": "ar-SA-Naayf",
        "Gender": "Male",
        "Locale": "ar-SA"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)",
        "ShortName": "bg-BG-Ivan",
        "Gender": "Male",
        "Locale": "bg-BG"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)",
        "ShortName": "ca-ES-HerenaRUS",
        "Gender": "Female",
        "Locale": "ca-ES"
    },
    {
        "Name": "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)",
        "ShortName": "cs-CZ-Jakub",
        "Gender": "Male",
        "Locale": "cs-CZ"
    },

    ...

]
```

### <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | 確定 | 要求成功。 |
| 400 | 不正確的要求 | 必要的參數遺失、為空白或 Null。 或者，傳遞至必要或選用參數的值無效。 常見的問題是標頭太長。 |
| 401 | 未經授權 | 要求未經授權。 請檢查以確定您的訂用帳戶金鑰或權杖有效，並且位於正確的區域。 |
| 429 | 太多要求 | 您已超出訂用帳戶允許的配額或要求率。 |
| 502 | 錯誤的閘道 | 網路或伺服器端問題。 也可能表示標頭無效。 |


## <a name="convert-text-to-speech"></a>將文字轉換成語音

`v1` 端點可讓您使用[語音合成標記語言（SSML）](speech-synthesis-markup.md)將文字轉換為語音。

### <a name="regions-and-endpoints"></a>區域與端點

支援使用 REST API 對以下區域進行文字轉語音。 請確定選取的是符合您訂用帳戶區域的端點。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]

### <a name="request-headers"></a>要求標頭

下表列出文字轉換語音要求的必要和選擇性標頭。

| 標頭 | 描述 | 必要/選用 |
|--------|-------------|---------------------|
| `Authorization` | 前面加入 `Bearer` 這個字的授權權杖。 如需詳細資訊，請參閱[驗證](#authentication)。 | 必要項 |
| `Content-Type` | 指定所提供文字的內容類型。 接受的值為 `application/ssml+xml`。 | 必要項 |
| `X-Microsoft-OutputFormat` | 指定音訊輸出格式。 如需接受值的完整清單，請參閱[音訊輸出](#audio-outputs)。 | 必要項 |
| `User-Agent` | 應用程式名稱。 提供的值必須少於255個字元。 | 必要項 |

### <a name="audio-outputs"></a>音訊輸出

此清單列出了每個要求中系統做為 `X-Microsoft-OutputFormat` 標頭的傳送的支援音訊格式。 每個格式皆包含位元速率和編碼類型。 語音服務支援 24 kHz、16 kHz 和 8 kHz 音訊輸出。

|||
|-|-|
| `raw-16khz-16bit-mono-pcm` | `raw-8khz-8bit-mono-mulaw` |
| `riff-8khz-8bit-mono-alaw` | `riff-8khz-8bit-mono-mulaw` |
| `riff-16khz-16bit-mono-pcm` | `audio-16khz-128kbitrate-mono-mp3` |
| `audio-16khz-64kbitrate-mono-mp3` | `audio-16khz-32kbitrate-mono-mp3` |
| `raw-24khz-16bit-mono-pcm` | `riff-24khz-16bit-mono-pcm` |
| `audio-24khz-160kbitrate-mono-mp3` | `audio-24khz-96kbitrate-mono-mp3` |
| `audio-24khz-48kbitrate-mono-mp3` | |

> [!NOTE]
> 如果您選取的語音和輸出格式具有不同的位元速率，則會視需要重新進行音訊取樣。 不過，24 kHz 語音不支援 `audio-16khz-16kbps-mono-siren` 和 `riff-16khz-16kbps-mono-siren` 輸出格式。

### <a name="request-body"></a>Request body

每個 `POST` 要求的本文都會以[語音合成標記語言 (SSML)](speech-synthesis-markup.md) 形式傳送。 SSML 可讓您選擇文字轉換語音服務所傳回合成語音的語音和語言。 如需支援的完整語音清單，請參閱[語言支援](language-support.md#text-to-speech)。

> [!NOTE]
> 如果使用自訂語音，可以純文字 (ASCII 或 UTF-8) 形式傳送要求本文。

### <a name="sample-request"></a>範例要求

此 HTTP 要求使用 SSML 指定語音與語言。 本文不能超過 1,000 個字元。

```http
POST /cognitiveservices/v1 HTTP/1.1

X-Microsoft-OutputFormat: raw-16khz-16bit-mono-pcm
Content-Type: application/ssml+xml
Host: westus.tts.speech.microsoft.com
Content-Length: 225
Authorization: Bearer [Base64 access_token]

<speak version='1.0' xml:lang='en-US'><voice xml:lang='en-US' xml:gender='Female'
    name='en-US-JessaRUS'>
        Microsoft Speech Service Text-to-Speech API
</voice></speak>
```

如需特定語言的範例，請參閱快速入門：

* [.NET Core、C#](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-csharp&tabs=dotnetcore)
* [Python](~/articles/cognitive-services/Speech-Service/quickstarts/text-to-speech.md?pivots=programming-language-python)
* [Node.js](quickstart-nodejs-text-to-speech.md)

### <a name="http-status-codes"></a>HTTP 狀態碼

每個回應的 HTTP 狀態碼會指出成功或常見的錯誤。

| HTTP 狀態碼 | 描述 | 可能的原因 |
|------------------|-------------|-----------------|
| 200 | 確定 | 要求成功；回應主體是音訊檔案。 |
| 400 | 不正確的要求 | 必要的參數遺失、為空白或 Null。 或者，傳遞至必要或選用參數的值無效。 常見的問題是標頭太長。 |
| 401 | 未經授權 | 要求未經授權。 請檢查以確定您的訂用帳戶金鑰或權杖有效，並且位於正確的區域。 |
| 413 | 要求實體太大 | SSML 輸入的長度大於 1024 個字元。 |
| 415 | 不支援的媒體類型 | 有可能是提供了錯誤的 `Content-Type`。 `Content-Type` 應該設定為 [`application/ssml+xml`]。 |
| 429 | 太多要求 | 您已超出訂用帳戶允許的配額或要求率。 |
| 502 | 錯誤的閘道 | 網路或伺服器端問題。 也可能表示標頭無效。 |

如果 HTTP 狀態為 `200 OK`，則回應主體會包含所要求格式的音訊檔案。 會在將此檔案傳輸、儲存到緩衝區或儲存到檔案時播放。

## <a name="next-steps"></a>後續步驟

- [取得語音試用訂用帳戶](https://azure.microsoft.com/try/cognitive-services/)
- [自訂原音模型](how-to-customize-acoustic-models.md)
- [自訂語言模型](how-to-customize-language-model.md)
