---
title: 針對語音 SDK 進行疑難排解-語音服務
titleSuffix: Azure Cognitive Services
description: 本文提供的資訊可協助您解決在使用語音 SDK 時可能會遇到的問題。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.openlocfilehash: 421b9adf4ae5d2c641484e646bea096716d46cca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "74815406"
---
# <a name="troubleshoot-the-speech-sdk"></a>針對語音 SDK 進行疑難排解

本文提供的資訊可協助您解決在使用語音 SDK 時可能會遇到的問題。

## <a name="error-websocket-upgrade-failed-with-an-authentication-error-403"></a>錯誤：WebSocket 升級失敗，發生驗證錯誤 (403)

您的區域或服務可能有錯誤的端點。 請檢查 URI 以確保其正確無誤。

此外，您的訂用帳戶金鑰或授權權杖也可能有問題。 如需詳細資訊，請參閱下一節。

## <a name="error-http-403-forbidden-or-http-401-unauthorized"></a>錯誤：HTTP 403 禁止或 HTTP 401 未授權

此錯誤通常是由驗證問題引起的。 連線要求如果沒有有效的 `Ocp-Apim-Subscription-Key` 或 `Authorization` 標頭，就會被服務以 401 或 403 的狀態拒絕。

* 如果您是使用訂用帳戶金鑰來進行驗證，您看見錯誤的原因可能如下：

    - 訂用帳戶金鑰遺失或無效
    - 您已超過訂用帳戶的使用量配額

* 如果您是使用授權權杖來進行驗證，您看見錯誤的原因可能如下：

    - 授權權杖無效
    - 授權權杖已過期

### <a name="validate-your-subscription-key"></a>驗證您的訂用帳戶金鑰

您可以執行下列其中一項命令，來確定自己具備有效的訂用帳戶金鑰。

> [!NOTE]
> 將 `YOUR_SUBSCRIPTION_KEY` 和 `YOUR_REGION` 取代為您自己的訂用帳戶金鑰和相關區域。

* PowerShell

    ```Powershell
    $FetchTokenHeader = @{
      'Content-type'='application/x-www-form-urlencoded'
      'Content-Length'= '0'
      'Ocp-Apim-Subscription-Key' = 'YOUR_SUBSCRIPTION_KEY'
    }
    $OAuthToken = Invoke-RestMethod -Method POST -Uri https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken -Headers $FetchTokenHeader
    $OAuthToken
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" -H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY" -H "Content-type: application/x-www-form-urlencoded" -H "Content-Length: 0"
    ```

如果您已輸入有效的訂用帳戶金鑰，命令會傳回授權權杖，否則會傳回錯誤。

### <a name="validate-an-authorization-token"></a>驗證授權權杖

如果您是使用授權權杖進行驗證，請執行下列其中一項命令，驗證授權權杖是否仍然有效。 權杖的有效時間為 10 分鐘。

> [!NOTE]
> 請以您的預錄音訊檔路徑取代 `YOUR_AUDIO_FILE`。 將 `YOUR_ACCESS_TOKEN` 取代為在先前步驟中傳回的授權權杖。 將 `YOUR_REGION` 取代為正確的區域。

* PowerShell

    ```Powershell
    $SpeechServiceURI =
    'https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US'

    # $OAuthToken is the authorization token returned by the token service.
    $RecoRequestHeader = @{
      'Authorization' = 'Bearer '+ $OAuthToken
      'Transfer-Encoding' = 'chunked'
      'Content-type' = 'audio/wav; codec=audio/pcm; samplerate=16000'
    }

    # Read audio into byte array.
    $audioBytes = [System.IO.File]::ReadAllBytes("YOUR_AUDIO_FILE")

    $RecoResponse = Invoke-RestMethod -Method POST -Uri $SpeechServiceURI -Headers $RecoRequestHeader -Body $audioBytes

    # Show the result.
    $RecoResponse
    ```

* cURL

    ```
    curl -v -X POST "https://YOUR_REGION.stt.speech.microsoft.com/speech/recognition/interactive/cognitiveservices/v1?language=en-US" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -H "Transfer-Encoding: chunked" -H "Content-type: audio/wav; codec=audio/pcm; samplerate=16000" --data-binary @YOUR_AUDIO_FILE
    ```

如果您已輸入有效的授權權杖，命令會傳回音訊檔案的轉譯，否則會傳回錯誤。

---

## <a name="error-http-400-bad-request"></a>錯誤：HTTP 400 不正確的要求

此錯誤通常發生在要求本文包含無效的音訊資料時。 僅支援 WAV 格式。 此外，請檢查要求的標頭，以確保針對 `Content-Type` 和 `Content-Length` 指定適當的值。

## <a name="error-http-408-request-timeout"></a>錯誤：HTTP 408 要求逾時

此錯誤最有可能的原因，是因為沒有將任何音訊資料傳送至服務。 此錯誤也可能是由網路問題造成的。

## <a name="recognitionstatus-in-the-response-is-initialsilencetimeout"></a>回應中的 "RecognitionStatus" 是 "InitialSilenceTimeout"

此問題通常是由音訊資料所造成。 您會看到此錯誤的原因可能如下：

* 音訊開頭有一段長時間的靜音。 在此情況下，服務會在幾秒後停止辨識，並傳回 `InitialSilenceTimeout`。

* 音訊使用不支援的轉碼器格式，其造成音訊視為無回應的音訊資料。

## <a name="next-steps"></a>後續步驟

* [檢閱版本資訊](releasenotes.md)
