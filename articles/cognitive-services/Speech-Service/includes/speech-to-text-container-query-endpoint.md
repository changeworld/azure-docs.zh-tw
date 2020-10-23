---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: e920ea3cd91f6275c4cb52dc070b0df60312d525
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/23/2020
ms.locfileid: "92470847"
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

若要對容器 [主機](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet&preserve-view=true)使用此呼叫：

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

若要搭配容器 [端點](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python&preserve-view=true)使用此呼叫：

```python
speech_config = speechsdk.SpeechConfig(
    endpoint="ws://localhost:5000/speech/recognition/conversation/cognitiveservices/v1"
```

---
