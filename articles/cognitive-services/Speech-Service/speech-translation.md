---
title: 語音翻譯與語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務允許您向應用程式、工具和設備添加端到端、即時、多語言的語音翻譯。 相同的 API 可以用於語音轉換語音和語音轉換文字翻譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f51288da6af3580ba7592950cde4f17d7adad529
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80052627"
---
# <a name="what-is-speech-translation"></a>什麼是語音翻譯？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

語音服務的語音翻譯支援音訊流的即時、多語言語音語音轉換和語音到文本的翻譯。 借助語音 SDK，您的應用程式、工具和設備可以訪問源轉錄和提供音訊的翻譯輸出。 在檢測到語音時返回中期轉錄和翻譯結果，並將最終結果轉換為合成語音。

微軟的翻譯引擎由兩種不同的方法提供支援：統計機翻譯 （SMT） 和神經機器轉換 （NMT）。 SMT 使用高級統計分析來估計給定幾個單詞上下文的最佳翻譯。 使用 NMT，神經網路使用句子的完整上下文來翻譯單詞，從而提供更準確、更自然的翻譯。

如今，微軟使用 NMT 翻譯到最流行的語言。 所有[可用於語音轉換語音翻譯的語言](language-support.md#speech-translation)都是採用 NMT 技術。 根據語言配對，語音轉換文字翻譯可能會使用 SMT 或 NMT。 當 NMT 支援目的語言時，完整翻譯由 NMT 提供支援。 當 NMT 不支援目的語言時，翻譯是 NMT 和 SMT 的混合，使用英語作為兩種語言之間的"支點"。

## <a name="core-features"></a>核心功能

以下是通過語音 SDK 和 REST API 提供的功能：

| 使用案例 | SDK | REST |
|----------|-----|------|
| 語音到文本翻譯，具有識別結果。 | 是 | 否 |
| 語音到語音翻譯。 | 是 | 否 |
| 臨時確認和翻譯結果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>開始翻譯語音

我們提供快速入門，旨在讓您在 10 分鐘內運行代碼。 此表包括按語言組織的語音翻譯快速入門清單。

| 快速入門 | Platform | 應用程式開發介面參考 |
|------------|----------|---------------|
| [C#，.NET 核心](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C#，.NET 框架](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C#，UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [瀏覽](https://aka.ms/csspeech/cppref)|
| [JAVA](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

在 GitHub 上提供了語音 SDK 的示例代碼。 這些示例涵蓋常見方案，如從檔或流讀取音訊、連續和單次識別/翻譯以及使用自訂模型。

* [語音到文本和翻譯示例 （SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移轉指南

如果您的應用程式、工具或產品正在使用[翻譯語音 API，](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)我們創建了指南以説明您遷移到語音服務。

* [從翻譯語音 API 遷移到語音服務](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音到文本](rest-speech-to-text.md)
* [REST API：文本到語音](rest-text-to-speech.md)
* [REST API：批次處理轉錄和自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
