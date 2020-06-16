---
title: 區域-語音服務
titleSuffix: Azure Cognitive Services
description: 適用于語音服務的可用區域和端點清單，包括語音轉換文字、文字轉換語音和語音翻譯。
services: cognitive-services
author: mahilleb-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: panosper
ms.custom: seodec18
ms.openlocfilehash: 27e26bb37b444b49797d46dd4e12b61f8fe11b16
ms.sourcegitcommit: 52d2f06ecec82977a1463d54a9000a68ff26b572
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/15/2020
ms.locfileid: "84782529"
---
# <a name="speech-service-supported-regions"></a>語音服務支援的區域

「語音服務」可讓您的應用程式將音訊轉換為文字、執行語音翻譯，以及將文字轉換為語音。 此服務在多個區域中提供，針對語音 SDK 與 REST API 有唯一的端點。

這裡提供的語音入口網站可讓您為所有區域的語音體驗執行自訂設定：https://speech.microsoft.com

針對語音服務的調用，請確定呼叫符合您訂用帳戶的區域。

## <a name="speech-sdk"></a>語音 SDK

在[語音 SDK](speech-sdk.md) 中，區域會指定為字串 (例如，在適用於 C# 的語音 SDK 中，作為 `SpeechConfig.FromSubscription` 的參數)。

### <a name="speech-to-text-text-to-speech-and-translation"></a>語音轉換文字、文字轉換語音和翻譯

您可以在這裡取得語音自訂入口網站：https://speech.microsoft.com

語音服務適用于**語音辨識**、**文字到語音**轉換和**翻譯**這兩個區域：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果您使用[語音 SDK](speech-sdk.md)，區域會由**區域識別碼**指定（例如，做為的參數 `SpeechConfig.FromSubscription` ）。 請確定該區域符合您訂用帳戶的區域。

### <a name="intent-recognition"></a>意圖辨識

透過語音 SDK 進行**意圖辨識**的可用區域如下所示：

| 全球區域 | 區域           | 區域識別碼 |
| ------------- | ---------------- | -------------------- |
| 亞洲          | 東亞        | `eastasia`           |
| Asia          | 東南亞   | `southeastasia`      |
| 澳洲     | 澳大利亞東部   | `australiaeast`      |
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

[語音 SDK](speech-sdk.md)支援下欄區域中的**語音助理**功能：

| 區域         | 區域識別碼 |
| -------------- | -------------------- |
| 美國西部        | `westus`             |
| 美國西部 2      | `westus2`            |
| 美國東部        | `eastus`             |
| 美國東部 2      | `eastus2`            |
| 西歐    | `westeurope`         |
| 北歐   | `northeurope`        |
| 東南亞 | `southeastasia`      |

### <a name="speaker-recognition"></a>說話者辨識

說話者辨識目前僅適用于該 `westus` 區域。

## <a name="rest-apis"></a>REST API

語音服務也會針對語音轉換文字與文字轉換語音要求公開 REST 端點。

### <a name="speech-to-text"></a>語音轉文字

如需語音轉換文字的參考檔，請參閱[語音轉換文字 REST API](rest-speech-to-text.md)。

REST API 的端點具有下列格式：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

取代 `<REGION_IDENTIFIER>` 為符合此資料表中訂用帳戶區域的識別碼：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必須將語言參數附加到 URL 後方，以避免收到 4xx HTTP 錯誤。 例如，使用美國西部端點設定為美式英文的語言是：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文字轉換語音

如需文字轉換語音的參考檔，請參閱[文字轉換語音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]
