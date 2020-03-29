---
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/21/2020
ms.author: dapine
ms.openlocfilehash: 9b4317064196c4ea3d761fd1a0bd43a764054fe6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77563279"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>語音到文本或自訂語音到文本

該容器提供基於 Websocket 的查詢終結點 API，這些 API 通過[語音 SDK](../index.yml)進行訪問。 預設情況下，語音 SDK 使用連線語音服務。 若要使用容器，您必須變更初始化方法。

> [!TIP]
> 將語音 SDK 與容器一起使用時，不需要提供 Azure 語音資源[訂閱金鑰或身份驗證承載權杖](../rest-speech-to-text.md#authentication)。

請參閱以下範例。

# <a name="c"></a>[C#](#tab/csharp)

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

使用此容器[主機](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)調用 ：

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

使用此容器[主機](https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python)調用 ：

```python
speech_config = speechsdk.SpeechConfig(
    host="ws://localhost:5000")
```

***
