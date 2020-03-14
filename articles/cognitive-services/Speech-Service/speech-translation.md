---
title: 語音翻譯與語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務可讓您將端對端、即時、多語言的語音翻譯新增至您的應用程式、工具和裝置。 相同的 API 可以用於語音轉換語音和語音轉換文字翻譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f2f0b277fb9dc6270e9015b5bf3205cc8aceaa0a
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371234"
---
# <a name="what-is-speech-translation"></a>什麼是語音翻譯？

語音服務的語音翻譯可讓您即時、多語言的語音轉換語音和語音轉換文字轉譯音訊串流。 使用語音 SDK 時，您的應用程式、工具和裝置可存取所提供音訊的來源轉譯和轉譯輸出。 當偵測到語音時，會傳回暫時轉譯和翻譯結果，而總決賽結果可以轉換成合成語音。

Microsoft 的轉譯引擎支援兩種不同的方法：統計機器翻譯（SMT）和類神經機器翻譯（NMT）。 SMT 會使用 advanced 統計分析，根據幾個單字的內容來估計最佳的翻譯。 使用 NMT，類神經網路可透過使用句子的完整內容來轉譯單字，以提供更精確且自然發音的翻譯。

目前，Microsoft 使用 NMT 來轉譯成最熱門的語言。 所有[可用於語音轉換語音翻譯的語言](language-support.md#speech-translation)都是採用 NMT 技術。 根據語言配對，語音轉換文字翻譯可能會使用 SMT 或 NMT。 當 NMT 支援目的語言時，完整翻譯會以 NMT 為動力。 當 NMT 不支援目的語言時，翻譯是混合的 NMT 和 SMT，使用英文做為兩種語言之間的「pivot」。

## <a name="core-features"></a>核心功能

以下是透過語音 SDK 和 REST Api 提供的功能：

| 使用案例 | SDK | REST |
|----------|-----|------|
| 具有辨識結果的語音轉換文字翻譯。 | 是 | 否 |
| 語音轉換語音翻譯。 | 是 | 否 |
| 暫時辨識和轉譯結果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>開始使用語音翻譯

我們提供的快速入門是為了讓您在10分鐘內執行程式碼而設計的。 此表格包含依語言組織的語音翻譯快速入門清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C#、.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C#.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C#，UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [瀏覽](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

語音 SDK 的範例程式碼可在 GitHub 上取得。 這些範例涵蓋了常見的案例，例如從檔案或資料流程讀取音訊、連續和一次性的辨識/轉譯，以及使用自訂模型。

* [語音轉換文字和翻譯範例（SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移轉指南

如果您的應用程式、工具或產品使用[翻譯工具語音 API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)，我們建立了指南，協助您遷移至語音服務。

* [從翻譯工具語音 API 遷移至語音服務](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉換文字](rest-speech-to-text.md)
* [REST API：文字轉換語音](rest-text-to-speech.md)
* [REST API：批次轉譯和自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
