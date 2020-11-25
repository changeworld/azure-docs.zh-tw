---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: dd1552dda28291112a2412bdf956bc49a0b541d7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95996383"
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

若要對容器 [主機](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?preserve-view=true&view=azure-dotnet)使用此呼叫：

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

若要搭配容器 [端點](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-python)使用此呼叫：

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---