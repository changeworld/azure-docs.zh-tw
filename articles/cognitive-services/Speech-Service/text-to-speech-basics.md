---
title: 語音合成基本概念 - 語音服務
titleSuffix: Azure Cognitive Services
description: 了解如何使用語音 SDK 將文字轉換為語音。 在本文中，您將了解物件建構、支援的音訊輸入格式，以及語音合成的自訂設定選項。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-two-with-js
ms.openlocfilehash: c5ceb76b8dcdad5d487e196cf1780703b7e34f17
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874515"
---
# <a name="learn-the-basics-of-speech-synthesis"></a>了解語音合成的基本概念

在本文中，您將了解如何使用語音 SDK 進行文字轉換語音合成的常見設計模式。 首先，您會進行基本設定與合成，並繼續處理更多用於自訂應用程式開發的高階範例，包括：

* 以記憶體內部資料流的形式取得回應
* 自訂輸出採樣速率和位元速率
* 使用 SSML 提交合成要求 (語音合成標記語言)
* 使用神經語音

> [!TIP]
> 如果您尚未完成我們的其中一個快速入門，建議您把握機會並自行嘗試文字轉換語音。
> * [將語音合成至喇叭](quickstarts/text-to-speech.md)

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

::: zone pivot="programming-language-more"
[!INCLUDE [More languages include](./includes/how-to/speech-to-text-basics/more.md)]
::: zone-end

## <a name="next-steps"></a>後續步驟

* [開始使用自訂語音](how-to-custom-voice.md)
* [使用 SSML 改善合成](speech-synthesis-markup.md)
