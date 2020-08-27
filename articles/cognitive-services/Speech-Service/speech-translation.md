---
title: 使用語音服務的語音翻譯
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 670d7f160285283bd44371b893c63904b2685926
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934492"
---
# <a name="what-is-speech-translation"></a>什麼是語音翻譯？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

語音服務的語音翻譯可讓您即時、多語言的語音轉換語音及語音轉換文字轉譯音訊串流。 使用語音 SDK 時，您的應用程式、工具和裝置可存取所提供音訊的來源轉譯和轉譯輸出。 當偵測到語音時，會傳回過渡轉譯和轉譯結果，而且總決賽結果可以轉換為合成語音。

Microsoft 的翻譯引擎支援兩種不同的方法：統計機器翻譯 (SMT) 和類神經機器翻譯 (NMT) 。 SMT 使用先進的統計分析，根據幾個字的內容來估計最可能的翻譯。 使用 NMT 時，會使用類神經網路來提供更精確且自然發音的翻譯，方法是使用句子的完整內容來轉譯單字。

Microsoft 現在使用 NMT 來翻譯為最受歡迎的語言。 所有[可用於語音轉換語音翻譯的語言](language-support.md#speech-translation)都是採用 NMT 技術。 根據語言配對，語音轉換文字翻譯可能會使用 SMT 或 NMT。 NMT 支援目的語言時，完整翻譯為 NMT 驅動。 NMT 不支援目的語言時，轉譯是 NMT 和 SMT 的混合式，並使用英文做為這兩種語言之間的「pivot」。

## <a name="core-features"></a>核心功能

以下是透過語音 SDK 和 REST Api 提供的功能：

| 使用案例 | SDK | REST |
|----------|-----|------|
| 具有辨識結果的語音轉換文字翻譯。 | 是 | 否 |
| 語音轉換語音翻譯。 | 是 | 否 |
| 過渡期辨識和轉譯結果。 | 是 | 否 |

## <a name="get-started-with-speech-translation"></a>開始使用語音翻譯

我們提供的快速入門設計可讓您在10分鐘內執行程式碼。 下表包含依語言組織的語音翻譯快速入門清單。

| 快速入門 | 平台 | API 參考資料 |
|------------|----------|---------------|
| [C #，.NET Core](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C #、.NET Framework](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C #、UWP](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-csharp&tabs=uwp) | Windows | [瀏覽](https://aka.ms/csspeech/csharpref) |
| [C++](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-cpp&tabs=windows) | Windows | [瀏覽](https://aka.ms/csspeech/cppref)|
| [Java](~/articles/cognitive-services/Speech-Service/quickstarts/translate-speech-to-text.md?pivots=programming-language-java&tabs=jre) | Windows、Linux、macOS | [瀏覽](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得適用于語音 SDK 的範例程式碼。 這些範例涵蓋了常見的案例，例如從檔案或資料流程讀取音訊、連續和一次性的辨識/轉譯，以及使用自訂模型。

* [ (SDK) 的語音轉換文字和翻譯範例 ](https://github.com/Azure-Samples/cognitive-services-speech-sdk)

## <a name="migration-guides"></a>移轉指南

如果您的應用程式、工具或產品使用 [翻譯工具語音 API](https://docs.microsoft.com/azure/cognitive-services/translator-speech/overview)，我們已建立可協助您遷移至語音服務的指南。

* [從翻譯工具語音 API 遷移至語音服務](how-to-migrate-from-translator-speech-api.md)

## <a name="reference-docs"></a>參考文件

* [語音 SDK](speech-sdk-reference.md)
* [語音裝置 SDK](speech-devices-sdk.md)
* [REST API：語音轉文字](rest-speech-to-text.md)
* [REST API：文字轉語音](rest-text-to-speech.md)
* [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
