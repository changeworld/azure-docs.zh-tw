---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 167e33ff4a3af463e2537e2714e9e9bf5e125b61
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948406"
---
容器會提供以 websocket 為基礎的查詢端點 Api，可透過 [語音 SDK](../index.yml)來存取。 語音 SDK 預設會使用線上語音服務。 若要使用容器，您必須變更初始化方法。

> [!TIP]
> 使用具有容器的語音 SDK 時，您不需要提供 Azure 語音資源訂用帳戶 [金鑰或驗證持有人權杖](../rest-speech-to-text.md#authentication)。

請參閱以下的範例。

# <a name="c"></a>[C#](#tab/csharp)

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

若要對容器 [主機](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost)使用此呼叫：

```csharp
var config = SpeechConfig.FromHost(
    new Uri("ws://localhost:5000"));
```

# <a name="python"></a>[Python](#tab/python)

從使用此 Azure 雲端初始化呼叫變更：

```python
speech_config = speechsdk.SpeechConfig(
    subscription=speech_key, region=service_region)
```

若要搭配容器 [端點](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig)使用此呼叫：

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
