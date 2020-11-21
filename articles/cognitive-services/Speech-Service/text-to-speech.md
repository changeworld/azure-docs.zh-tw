---
title: 文字轉換語音總覽-語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務中的文字轉換語音功能，可讓您的應用程式、工具或裝置將文字轉換成自然人類的合成語音。 本文概述文字轉換語音服務的優點和功能。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: 文字轉換語音
ms.openlocfilehash: af0c9338cb9661402c3a76bab55f34b01304c7b6
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024512"
---
# <a name="what-is-text-to-speech"></a>什麼是文字轉換語音？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

在此總覽中，您將瞭解文字轉語音服務的優點和功能，可讓您的應用程式、工具或裝置將文字轉換成類似人類的合成語音。 從標準和類神經語音中選擇，或為您的產品或品牌建立獨特的自訂語音。 75以上的標準語音提供45種以上的語言和地區設定，而5個類神經語音則可供選擇的語言和地區設定使用。 如需支援的語音、語言和地區設定的完整清單，請參閱 [支援的語言](language-support.md#text-to-speech)。

> [!NOTE]
> Bing 語音已于2019年10月15日解除委任。 如果您的應用程式、工具或產品使用 Bing 語音 Api 或自訂語音，我們已建立可協助您遷移至語音服務的指南。
> - [從 Bing 語音遷移至語音服務](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>核心功能

* 語音合成-使用 [語音 SDK](./get-started-text-to-speech.md) 或 [REST API](rest-text-to-speech.md) ，以使用標準、類神經或自訂語音將文字轉換為語音。

* 大量音訊的非同步合成-使用 [長音訊 API](long-audio-api.md) ，以非同步方式將文字轉換語音檔案合成超過10分鐘， (例如音訊書籍或演講) 。 不同于使用語音 SDK 或語音轉換文字 REST API 所執行的合成，回應不會即時傳回。 預期會以非同步方式傳送要求、輪詢回應，以及從服務提供合成音訊時進行下載。 僅支援自訂神經語音。

* 標準語音-使用統計參數合成和/或串連合成技術來建立。 這些聲音高度智慧型且自然。 您可以輕鬆地讓您的應用程式以多種不同的語音選項，在45以上的語言中說話。 這些語音提供高發音精確度，包括縮寫、縮略字擴充、日期/時間解讀、polyphones 等的支援。 如需標準語音的完整清單，請參閱 [支援的語言](language-support.md#text-to-speech)。

* 類神經語音-深度類神經網路是用來克服傳統語音合成的限制，以及說出語言的壓力和聲調。 韻律預測和語音合成會同時執行，這會導致更流暢且自然的輸出。 類神經語音可以用來與聊天機器人和語音助理互動，使其更自然且吸引人，將數位文字（例如電子書）轉換成有聲書;，以及增強汽車內的導覽系統。 使用類似人類的自然韻律和清楚表達的單字，類神經語音可大幅減少在與 AI 系統互動時的聆聽疲勞。 如需神經語音的完整清單，請參閱 [支援的語言](language-support.md#text-to-speech)。

* 語音合成標記語言 (SSML) -用來自訂語音轉換文字輸出的 XML 標記語言。 有了 SSML，您可以調整音調、新增暫停、改善發音、加速或減緩說話率、增加或減少音量，以及將多個聲音屬性設定為單一檔。 請參閱 [SSML](speech-synthesis-markup.md)。

## <a name="get-started"></a>開始使用

請參閱 [快速入門](get-started-text-to-speech.md) 以開始使用文字轉語音。 文字轉換語音服務可透過[語音 SDK](speech-sdk.md)、 [REST API](rest-text-to-speech.md)和[語音 CLI](spx-overview.md)取得

## <a name="sample-code"></a>範例程式碼

您可以在 GitHub 上找到文字轉換語音的範例程式碼。 這些範例涵蓋最受歡迎的程式設計語言中的文字轉換語音轉換。

- [ (SDK) 的文字轉換語音範例 ](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>自訂

除了標準和類神經語音之外，您還可以建立及微調您產品或品牌特有的自訂語音。 開始使用的只是少數音訊檔案和相關聯的轉譯。 如需詳細資訊，請參閱 [開始使用自訂語音](how-to-custom-voice.md)

## <a name="pricing-note"></a>定價注意事項

使用文字轉換語音服務時，您需支付轉換成語音的每個字元，包括標點符號。 雖然 SSML 檔本身不是可計費的，但用來調整文字轉換成語音的選擇性元素（例如音素和音調）會計算為可計費的字元。 以下是可計費的清單：

- 在要求的 SSML 主體中傳遞給文字轉換語音服務的文字
- SSML 格式的要求主體文字欄位內的所有標記，除了 `<speak>` 和 `<voice>` 標記之外
- 字母、標點符號、空格、定位字元、標記和所有空白字元
- 以 Unicode 定義的每個字碼指標

如需詳細資訊，請參閱 [定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每個中文、日文和韓文語言字元都會計為兩個字元以進行計費。

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk.md)
- [REST API：文字轉語音](rest-text-to-speech.md)

## <a name="next-steps"></a>下一步

- [取得免費的語音服務訂用帳戶](overview.md#try-the-speech-service-for-free)
- [取得語音 SDK](speech-sdk.md)