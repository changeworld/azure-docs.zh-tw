---
title: 文字轉換語音-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務中的文字轉換語音功能，可讓您的應用程式、工具或裝置將文字轉換成自然的人類合成語音。 選擇 [預設語音] 或建立您自己的自訂語音。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: f939619cbbc69e9fc9cdbc4b2085a0b9d805511d
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371200"
---
# <a name="what-is-text-to-speech"></a>什麼是文字轉換語音？

語音服務中的文字轉換語音可讓您的應用程式、工具或裝置將文字轉換成類似人類的合成語音。 從標準和類神經語音中選擇，或為您的產品或品牌建立獨特的自訂語音。 75 + standard 語音提供45以上的語言和地區設定，而5類神經語音則提供一系列精選的語言和地區設定。 如需支援的語音、語言和地區設定的完整清單，請參閱[支援的語言](language-support.md#text-to-speech)。

> [!NOTE]
> Bing 語音已于2019年10月15日解除委任。 如果您的應用程式、工具或產品使用 Bing 語音 Api 或自訂語音，我們建立了指南，協助您遷移至語音服務。
> - [從 Bing 語音遷移至語音服務](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>核心功能 

* 語音合成-使用[語音 SDK](quickstarts/text-to-speech-audio-file.md)或[REST API](rest-text-to-speech.md) ，使用標準、類神經或自訂語音將文字轉換為語音。

* 非同步合成長音訊-使用[長音訊 API](long-audio-api.md)以非同步方式合成長達10分鐘的文字轉換語音檔案（例如，音訊書籍或演講）。 不同于使用語音 SDK 或語音轉換文字 REST API 執行的合成，回應不會即時傳回。 預期的情況是要求會以非同步方式傳送、針對輪詢回應，並在從服務提供時下載合成的音訊。 僅支援類神經語音。

* 標準語音-使用統計參數合成和（或）串連合成技術所建立。 這些聲音具有高度可理解和自然的音效。 您可以輕鬆地讓您的應用程式以多種語音選項，在45以上的語言中說話。 這些聲音提供高發音精確度，包括縮寫、縮寫擴充、日期/時間解讀、polyphones 等等的支援。 如需標準語音的完整清單，請參閱[支援的語言](language-support.md#text-to-speech)。

* 類神經語音-深度類神經網路可用來克服傳統語音合成的限制，以及語音語言的壓力和聲調。 韻律預測和語音合成會同時執行，這會導致更流暢且自然發音的輸出。 神經語音可以用來與聊天機器人和語音助理互動，使其更自然且吸引人、將數位文字（例如電子書）轉換成有聲書;，以及增強汽車內流覽系統。 有了人類的自然韻律和清楚的單字文字清晰度，神經語音會在您與 AI 系統互動時，大幅減少聆聽的疲勞。 如需神經語音的完整清單，請參閱[支援的語言](language-support.md#text-to-speech)。

* 語音合成標記語言（SSML）-以 XML 為基礎的標記語言，用來自訂語音轉換文字輸出。 透過 SSML，您可以調整音調、新增暫停、改善發音、加速或減緩說話速率、增加或減少音量，以及將多個聲音屬性設為單一檔。 請參閱[SSML](speech-synthesis-markup.md)。

## <a name="get-started"></a>開始使用

文字轉換語音服務可透過[語音 SDK](speech-sdk.md)取得。 在各種不同的語言和平臺中，有幾個常見的案例可做為快速入門：

* [將語音合成至音訊檔案](quickstarts/text-to-speech-audio-file.md)
* [將語音合成至喇叭](quickstarts/text-to-speech.md)
* [以非同步方式合成長格式的音訊](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

如果您想要的話，文字轉換語音服務可以透過[REST](rest-text-to-speech.md)來存取。

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上取得文字轉換語音的範例程式碼。 這些範例涵蓋了最受歡迎的程式設計語言的文字轉換語音。

- [文字轉換語音範例（SDK）](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>自訂 

除了標準和類神經語音以外，您還可以建立及微調您產品或品牌特有的自訂語音。 開始使用是一些音訊檔案和相關聯的轉譯。 如需詳細資訊，請參閱[開始使用自訂語音](how-to-custom-voice.md)

## <a name="pricing-note"></a>定價注意事項

使用文字轉換語音服務時，會向您收取轉換成語音的每個字元的費用，包括標點符號。 雖然 SSML 檔本身無法計費，但用來調整文字轉換成語音的選擇性元素（例如音素和音調）會視為計費字元。 以下是可計費的清單：

- 在要求的 SSML 主體中傳遞至文字轉換語音服務的文字
- 在要求本文的文字欄位中，所有的標記都是 SSML 格式，但 `<speak>` 和 `<voice>` 標記除外
- 字母、標點符號、空格、定位字元、標記和所有空白字元
- 以 Unicode 定義的每個字碼指標

如需詳細資訊，請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每個中文、日文和韓文語言字元會視為兩個要計費的字元。

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk.md)
- [REST API：文字轉換語音](rest-text-to-speech.md)

## <a name="next-steps"></a>後續步驟

- [取得免費的語音服務訂用帳戶](get-started.md)
- [取得語音 SDK](speech-sdk.md)
