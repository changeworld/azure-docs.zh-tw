---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/29/2020
ms.author: aahi
ms.openlocfilehash: e5f38da6a18e8a4c59bea77f4ddd5a68db1e1902
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610732"
---
### <a name="speech-to-text-or-custom-speech-to-text"></a>語音轉換文字或自訂語音轉換文字

容器提供以 websocket 為基礎的查詢端點 Api，可透過[語音 SDK](../index.yml)存取。 根據預設，語音 SDK 會使用線上語音服務。 若要使用容器，您必須變更初始化方法。

> [!TIP]
> 搭配使用語音 SDK 與容器時，您不需要提供 Azure 語音資源訂用帳戶[金鑰或驗證持有人權杖](../rest-speech-to-text.md#authentication)。

請參閱以下範例。

# <a name="c"></a>[C#](#tab/csharp)

從使用此 Azure 雲端初始化呼叫變更：

```csharp
var config = SpeechConfig.FromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

若要使用容器[主機](https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet)進行此呼叫：

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
