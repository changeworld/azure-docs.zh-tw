---
title: 文字轉換語音快速入門 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 將文字轉換為語音。 在本快速入門中，您將了解物件建構和設計模式、支援的音訊輸入格式、語音 CLI，以及語音合成的自訂設定選項。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: speech-full-stack
keywords: 文字轉換語音
ms.openlocfilehash: 79409f95d698e015d15d9131dcf1f27b34b03343
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89400891"
---
# <a name="get-started-with-text-to-speech"></a>開始使用文字轉換語音

在本快速入門中，您將了解如何使用語音 SDK 進行文字轉換語音合成的常見設計模式。 首先，您會進行基本設定與合成，並繼續處理更多用於自訂應用程式開發的高階範例，包括：

* 以記憶體內部資料流的形式取得回應
* 自訂輸出採樣速率和位元速率
* 使用 SSML 提交合成要求 (語音合成標記語言)
* 使用神經語音

> [!TIP]
> 如果要直接跳到範例程式碼，請參閱 GitHub 上的[快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)。

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-spx"
[!INCLUDE [CLI include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

* [開始使用自訂語音](how-to-custom-voice.md)
* [使用 SSML 改善合成](speech-synthesis-markup.md)
* 了解如何使用[長音訊 API](long-audio-api.md) 作為書籍和新聞文章等大量文字範例
* 請參閱 GitHub 上的[快速入門範例](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart)
