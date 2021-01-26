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
ms.date: 08/20/2020
ms.author: panosper
ms.custom: seodec18,references_regions
ms.openlocfilehash: 646d29e72b91cd6afcde8e70ad8fd8715442b88e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98786786"
---
# <a name="speech-service-supported-regions"></a>語音服務支援的區域

「語音服務」可讓您的應用程式將音訊轉換為文字、執行語音翻譯，以及將文字轉換為語音。 此服務在多個區域中提供，針對語音 SDK 與 REST API 有唯一的端點。

您可以從以下位置取得語音入口網站，為所有區域的語音體驗執行自訂設定： https://speech.microsoft.com

考慮區域時請記住下列幾點：

* 如果您的應用程式使用 [語音 SDK](speech-sdk.md)，您可以在建立語音設定時提供區域識別碼，例如 `westus` 。
* 如果您的應用程式使用其中一個語音服務 [REST API](./overview.md#reference-docs)，則區域會是您提出要求時所用端點 URI 的一部份。
* 為某區域建立的金鑰，就只能在該區域中使用。 若嘗試在其他區域使用，將會導致驗證錯誤。

## <a name="speech-sdk"></a>語音 SDK

在[語音 SDK](speech-sdk.md) 中，區域會指定為字串 (例如，在適用於 C# 的語音 SDK 中，作為 `SpeechConfig.FromSubscription` 的參數)。

### <a name="speech-to-text-text-to-speech-and-translation"></a>語音轉換文字、文字轉換語音和翻譯

您可以從這裡取得語音自訂入口網站： https://speech.microsoft.com

語音服務可在這些區域中使用，以進行 **語音辨識**、 **文字到語音** 轉換和 **轉譯**：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

如果您使用 [語音 SDK](speech-sdk.md)，區域會由 **區域識別碼** 指定 (例如，作為) 的參數 `SpeechConfig.FromSubscription` 。 請確定區域符合您訂用帳戶的區域。

如果您打算使用音訊資料來定型自訂模型，請使用其中一個 [區域搭配專用硬體](custom-speech-overview.md#set-up-your-azure-account) ，以加快定型速度。 您可以使用 [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) ，稍後再將完整定型的模型複製到另一個區域。

### <a name="intent-recognition"></a>意圖辨識

透過語音 SDK 進行 **意圖辨識** 的可用區域如下所示：

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

這是 [Language Understanding 服務 (LUIS)](../luis/luis-reference-regions.md)所支援的發行區域子集。

### <a name="voice-assistants"></a>語音助理

[語音 SDK](speech-sdk.md)支援透過下欄區域中的語音 **助理** 功能 [Direct Line 語音](./direct-line-speech.md)：

| 全球區域 | 區域           | 區域識別碼    |
| ------------- | ---------------- | -------------------- |
| 北美洲 | 美國西部          | `westus`             |
| 北美洲 | 美國西部 2        | `westus2`            |
| 北美洲 | 美國東部          | `eastus`             |
| 北美洲 | 美國東部 2        | `eastus2`            |
| 北美洲 | 美國中西部  | `westcentralus`      |
| 北美洲 | 美國中南部 | `southcentralus`     |
| 歐洲        | 西歐      | `westeurope`         |
| 歐洲        | 北歐     | `northeurope`        |
| Asia          | 東亞        | `eastasia`           |
| Asia          | 東南亞   | `southeastasia`      |
| 印度         | 印度中部    | `centralindia`       |

### <a name="speaker-recognition"></a>說話者辨識

說話者辨識目前只能在區域中使用 `westus` 。

## <a name="rest-apis"></a>REST API

語音服務也會針對語音轉換文字與文字轉換語音要求公開 REST 端點。

### <a name="speech-to-text"></a>語音轉文字

如需語音轉換文字參考檔，請參閱 [語音轉換文字 REST API](rest-speech-to-text.md)。

REST API 的端點的格式如下：

```
https://<REGION_IDENTIFIER>.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1
```

取代 `<REGION_IDENTIFIER>` 為與此資料表中的訂用帳戶區域相符的識別碼：

[!INCLUDE [](../../../includes/cognitive-services-speech-service-region-identifier.md)]

> [!NOTE]
> 必須將語言參數附加到 URL 後方，以避免收到 4xx HTTP 錯誤。 例如，使用美國西部端點設定為美式英文的語言是：`https://westus.stt.speech.microsoft.com/speech/recognition/conversation/cognitiveservices/v1?language=en-US`。

### <a name="text-to-speech"></a>文字轉換語音

如需文字轉換語音的參考檔，請參閱 [文字轉換語音 REST API](rest-text-to-speech.md)。

[!INCLUDE [](../../../includes/cognitive-services-speech-service-endpoints-text-to-speech.md)]