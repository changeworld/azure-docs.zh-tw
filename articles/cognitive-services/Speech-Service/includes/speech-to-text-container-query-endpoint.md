---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c029165a4994e5e3f73399fb00a1775b67128e44
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879003"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>語音到文字或自訂語音到文字

該容器提供基於 Websocket 的查詢終結點 API,這些 API 透過[語音 SDK](../index.yml)進行訪問。 默認情況下,語音 SDK 使用連線語音服務。 若要使用容器，您必須變更初始化方法。

> [!TIP]
> 將語音 SDK 與容器一起使用時,不需要提供 Azure 語音資源[訂閱金鑰或身份驗證承載權杖](../rest-speech-to-text.md#authentication)。

請參閱以下範例。

# <a name="c"></a>[C#](#tab/csharp)

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

使用此容器[主機](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)呼叫 :

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

使用此容器[主機](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)呼叫 :

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
