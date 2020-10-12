---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/18/2020
ms.author: aahi
ms.custom: devx-track-csharp
ms.openlocfilehash: 54ccaebd84c6af308ddcfa956add7f84b6e55832
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "89321005"
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

使用容器 [主機](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)進行此呼叫：

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

---
