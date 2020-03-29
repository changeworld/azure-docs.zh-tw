---
title: 區域 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務的可用區域和終結點的清單，包括語音到文本、文本到語音轉換和語音翻譯。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 560575ca7f51218e472abecb4319f4a3db69b1ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221415"
---
# <a name="speech-service-supported-regions"></a>語音服務支援的區域

「語音服務」可讓您的應用程式將音訊轉換為文字、執行語音翻譯，以及將文字轉換為語音。 此服務在多個區域中提供，針對語音 SDK 與 REST API 有唯一的端點。

此處提供了"語音門戶"，用於對所有區域的語音體驗執行自訂配置：https://speech.microsoft.com

對於語音服務的調用，請確保呼叫與訂閱的區域匹配。

## <a name="speech-sdk"></a>語音 SDK

在[語音 SDK](speech-sdk.md) 中，區域會指定為字串 (例如，在適用於 C# 的語音 SDK 中，作為 `SpeechConfig.FromSubscription` 的參數)。

### <a name="speech-to-text-text-to-speech-and-translation"></a>語音到文本、文本到語音和翻譯

語音自訂門戶可在此處找到：https://speech.microsoft.com

語音服務可在這些區域提供**語音辨識**、**文本到語音**和**翻譯**：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果使用[語音 SDK，](speech-sdk.md)區域由**區域識別碼**指定（例如，作為 的`SpeechConfig.FromSubscription`參數。 確保區域與訂閱區域匹配。

### <a name="intent-recognition"></a>意圖辨識

透過語音 SDK 進行**意圖辨識**的可用區域如下所示：

| 全球區域 | 區域           | 區域識別碼 |
| ------------- | ---------------- | -------------------- |
| Asia          | 東亞        | `eastasia`           |
| Asia          | 東南亞   | `southeastasia`      |
| 澳大利亞     | 澳大利亞東部   | `australiaeast`      |
| 歐洲        | 北歐     | `northeurope`        |
| 歐洲        | 西歐      | `westeurope`         |
| 北美洲 | 美國東部          | `eastus`             |
| 北美洲 | 美國東部 2        | `eastus2`            |
| 北美洲 | 美國中南部 | `southcentralus`     |
| 北美洲 | 美國中西部  | `westcentralus`      |
| 北美洲 | 美國西部          | `westus`             |
| 北美洲 | 美國西部 2        | `westus2`            |
| 南美洲 | 巴西南部     | `brazilsouth`        |

這是 [Language Understanding 服務 (LUIS)](/azure/cognitive-services/luis/luis-reference-regions)所支援的發行區域子集。

### <a name="voice-assistants"></a>語音助理

[語音 SDK](speech-sdk.md)支援以下區域的**語音助理**功能：

| 區域         | 區域識別碼 |
| -------------- | -------------------- |
| 美國西部        | `westus`             |
| 美國西部 2      | `westus2`            |
| 美國東部        | `eastus`             |
| 美國東部 2      | `eastus2`            |
| 西歐    | `westeurope`         |
| 北歐   | `northeurope`        |
| 東南亞 | `southeastasia`      |

## <a name="rest-apis"></a>REST API

語音服務也會針對語音轉換文字與文字轉換語音要求公開 REST 端點。

### <a name="speech-to-text"></a>語音轉文字

有關語音到文本的參考文檔，請參閱[語音到文本 REST API](rest-speech-to-text.md)。

REST API 的終結點具有以下格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

替換`<REGION_IDENTIFIER>`與匹配此表中訂閱區域的識別碼：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必須將語言參數附加到 URL 後方，以避免收到 4xx HTTP 錯誤。 例如，使用美國西部端點設定為美式英文的語言是：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文字轉換語音

有關文本到語音轉換的參考文檔，請參閱[文本到語音轉換 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
