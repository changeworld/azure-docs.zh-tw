---
title: 語音翻譯總覽-語音服務
titleSuffix: Azure Cognitive Services
description: 語音翻譯可讓您將端對端、即時、多語言的語音翻譯新增至您的應用程式、工具和裝置。 相同的 API 可以用於語音轉換語音和語音轉換文字翻譯。 本文概述語音翻譯服務的優點和功能。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: erhopf
ms.custom: devx-track-csharp, cog-serv-seo-aug-2020
keywords: 語音翻譯
ms.openlocfilehash: 67bb418926932ebb7e443e77c65dd12c7352049d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/02/2020
ms.locfileid: "89401209"
---
# <a name="what-is-speech-translation"></a>什麼是語音翻譯？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在此總覽中，您將瞭解語音翻譯服務的優點與功能，這項功能可讓您即時、多語言的語音轉換語音及語音轉換文字轉譯音訊串流。 使用語音 SDK 時，您的應用程式、工具和裝置可存取所提供音訊的來源轉譯和轉譯輸出。 當偵測到語音時，會傳回過渡轉譯和轉譯結果，而最終結果可以轉換為合成語音。

Microsoft 的翻譯引擎支援兩種不同的方法：統計機器翻譯 (SMT) 和類神經機器翻譯 (NMT) 。 SMT 使用先進的統計分析，根據幾個字的內容來估計最可能的翻譯。 使用 NMT 時，會使用類神經網路來提供更精確且自然發音的翻譯，方法是使用句子的完整內容來轉譯單字。

Microsoft 現在使用 NMT 來翻譯為最受歡迎的語言。 所有[可用於語音轉換語音翻譯的語言](language-support.md#speech-translation)都是採用 NMT 技術。 根據語言配對，語音轉換文字翻譯可能會使用 SMT 或 NMT。 NMT 支援目的語言時，完整翻譯為 NMT 驅動。 NMT 不支援目的語言時，轉譯是 NMT 和 SMT 的混合式，並使用英文做為這兩種語言之間的「pivot」。

## <a name="core-features"></a>核心功能

* 具有辨識結果的語音轉換文字翻譯。
* 語音轉換語音翻譯。
* 支援翻譯成多種目的語言。
* 過渡期辨識和轉譯結果。

## <a name="get-started"></a>開始使用 

請參閱 [快速入門](get-started-speech-translation.md) 以開始使用語音翻譯。 語音翻譯服務可透過 [語音 SDK](speech-sdk.md) 和 [語音 CLI](spx-overview.md)取得。

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

* 完成語音翻譯 [快速入門](get-started-speech-translation.md)
* [免費取得語音服務的訂用帳戶金鑰](get-started.md)
* [取得語音 SDK](speech-sdk.md)
