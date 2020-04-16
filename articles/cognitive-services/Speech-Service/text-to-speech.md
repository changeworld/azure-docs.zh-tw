---
title: 文字到語音 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音服務中的文字到語音功能使應用程式、工具或設備能夠將文字轉換為自然的類似人類的合成語音。 選擇預設的聲音或創建自己的自定義語音。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: trbye
ms.openlocfilehash: 1a8b458eb004b44d0045f36b18d88e11e019c4d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399509"
---
# <a name="what-is-text-to-speech"></a>什麼是文字轉換語音？

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

語音服務中的文字到語音轉換使應用程式、工具或設備能夠將文字轉換為類似人類的合成語音。 從標準和神經語音中進行選擇,或創建產品或品牌獨有的自定義語音。 75+ 標準語音提供超過 45 種語言和區域設置,5 個神經語音以精選的語言和區域設置提供。 有關支援的聲音、語言和區域設定的完整清單,請參閱[支援的語言](language-support.md#text-to-speech)。

> [!NOTE]
> 必應演講於2019年10月15日退役。 如果您的應用程式、工具或產品正在使用必應語音 API 或自訂語音,則我們創建了指南以説明您遷移到語音服務。
> - [從必應語音遷移到語音服務](how-to-migrate-from-bing-speech.md)

## <a name="core-features"></a>核心功能

* 語音合成 - 使用[語音 SDK](quickstarts/text-to-speech-audio-file.md)或 REST [API](rest-text-to-speech.md)使用標準語音、神經語音或自訂語音將文字轉換為語音。

* 長音訊的非同步合成 - 使用[長音訊 API](long-audio-api.md)非同步合成文字到語音檔案的時間超過 10 分鐘(例如有聲讀物或講座)。 與使用語音 SDK 或語音到文字 REST API 執行的合成不同,回應不會即時返回。 期望以非同步方式發送請求,輪詢回應,並在從服務中提供時下載合成的音訊。 僅支援自定義神經語音。

* 標準語音 - 使用統計參數合成和/或串聯合成技術創建。 這些聲音是高度易聽和聲音自然。 您可以輕鬆使應用程式能夠使用超過 45 種語言,並提供廣泛的語音選項。 這些語音提供高發音精度,包括支援縮寫、首字母縮略詞擴展、日期/時間解釋、多聽器等。 有關標準語音的完整清單,請參閱[支援的語言](language-support.md#text-to-speech)。

* 神經語音 - 深度神經網路用於克服傳統語音合成在口語中壓力和語調的極限。 前列腺預測和語音合成同時執行,從而產生更流暢和自然的聲音輸出。 神經語音可用於使與聊天機器人和語音助理的互動更自然、更吸引人,將電子書等數位文本轉換為有聲讀物,並增強車載導航系統。 通過類似人類的自然表達和清晰的詞表達,神經聲音顯著減少聽力疲勞,當你與AI系統互動。 有關神經語音的完整清單,請參閱[支援的語言](language-support.md#text-to-speech)。

* 語音合成標記語言 (SSML) - 一種基於 XML 的標記語言,用於自訂語音到文字輸出。 使用 SSML,您可以調整音調、添加暫停、提高發音、加快或減慢說話速率、增加或減少音量,並將多個語音歸因於單個文檔。 請參考[SSML](speech-synthesis-markup.md)。

## <a name="get-started"></a>開始使用

文本到語音服務可透過[語音 SDK](speech-sdk.md)獲得。 有多種常見方案可作為快速入門,使用各種語言和平臺:

* [將語音合成至音訊檔案](quickstarts/text-to-speech-audio-file.md)
* [將語音合成至喇叭](quickstarts/text-to-speech.md)
* [非同步合成長格式音訊](quickstarts/text-to-speech/async-synthesis-long-form-audio.md)

如果您願意,可通過[REST](rest-text-to-speech.md)可以訪問文本到語音轉換服務。

## <a name="sample-code"></a>範例程式碼

GitHub 上提供了文字到語音轉換的範例碼。 這些示例涵蓋了最流行的程式設計語言中的文本到語音轉換。

- [文字到語音轉換範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="customization"></a>自訂

除了標準和神經語音之外,您還可以創建和微調產品或品牌特有的自定義聲音。 入門只需一小撮音頻檔和相關的轉錄。 有關詳細資訊,請參閱[開始使用自訂語音](how-to-custom-voice.md)

## <a name="pricing-note"></a>定價說明

使用文字到語音服務時,將針對轉換為語音的每個字元(包括標點符號)計費。 雖然 SSML 文檔本身不可計費,但用於調整文本轉換為語音方式的可選元素(如音詞和音高)將計為計費字元。 下面是可計費內容的清單:

- 文字傳遞到要求的 SSML 本文的文字到語音轉換服務
- 要求正文的文字欄位中以 SSML 格式進行的所有標籤,`<speak>`除`<voice>`與標籤外
- 字母、標點符號、空白、制表元、標記和所有空白字元
- 以 Unicode 定義的每個字碼指標

有關詳細資訊,請參閱[定價](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

> [!IMPORTANT]
> 每個中文、日語和韓語字元都計為兩個字元進行計費。

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk.md)
- [REST API：文字轉語音](rest-text-to-speech.md)

## <a name="next-steps"></a>後續步驟

- [取得免費語音服務訂閱](get-started.md)
- [取得語音 SDK](speech-sdk.md)
