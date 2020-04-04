---
title: 關於語音 SDK - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音軟體開發工具套件 (SDK) 公開了許多語音服務功能,從而更輕鬆地開發啟用語音的應用程式。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a2ff4a94c1b2941f645cd7032ef476d33dffdb00
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656596"
---
# <a name="about-the-speech-sdk"></a>關於語音 SDK

語音軟體開發工具套件 (SDK) 公開了許多語音服務功能,使您能夠開發支援語音的應用程式。 語音 SDK 提供許多程式設計語言和所有平臺。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>專案功能

語音 SDK 公開了語音服務中的許多功能,但不是全部功能。 語音 SDK 的功能通常與方案相關聯。 語音 SDK 非常適合使用本地設備、檔、Azure Blob 儲存,甚至輸入和輸出流的即時和非即時方案。 當語音 SDK 無法實現方案時,請查找 REST API 替代方案。

### <a name="speech-to-text"></a>語音轉文字

[語音到文字](speech-to-text.md)(也稱為*語音辨識*)將音訊流轉錄到應用程式、工具或設備可以使用或顯示的文本。 若搭配 [Language Understanding (LUIS)](../luis/index.yml) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 使用[語音翻譯](speech-translation.md)將語音輸入轉換為具有單個調用的不同語言。 有關詳細資訊,請參閱[語音到文字基礎知識](speech-to-text-basics.md)。

### <a name="text-to-speech"></a>文字轉換語音

[文字到語音](text-to-speech.md)(也稱為*語音合成*)將文字轉換為類似人類的合成語音。 輸入文字是字串文字或使用[語音合成標記語言 (SSML)。](speech-synthesis-markup.md) 有關標準語音或神經語音的詳細資訊,請參閱[文字到語音語言和語音支援](language-support.md#text-to-speech)。

### <a name="voice-assistants"></a>語音助理

使用語音 SDK 的語音助理使開發人員能夠為其應用程式和體驗創建自然的、類似於人的對話介面。 語音助理服務提供設備和助手之間快速、可靠的交互。 實現使用機器人框架的直線語音通道或集成的自定義命令(預覽)服務來完成任務。 此外,可以使用[自訂語音門戶](https://aka.ms/customvoice)創建語音助理,以創建獨特的語音體驗。

#### <a name="keyword-spotting"></a>關鍵字發現

語音 SDK 中支援[關鍵字發現](speech-devices-sdk-create-kws.md)的概念。 關鍵字識別是識別語音中關鍵字的行為,隨後在聽到關鍵字時執行操作。 例如,「嘿,Cortana」將啟動 Cortana 助手。

### <a name="meeting-scenarios"></a>會議機制

語音 SDK 非常適合從單個設備或多設備對話轉錄會議方案。

#### <a name="conversation-transcription"></a>對話轉譯

[對話轉錄](conversation-transcription.md)支援對每個演講者的即時(和非同步)語音辨識、揚聲器辨識和句子歸屬(也稱為*二分化*)。 非常適合利用辨識說話者的能力來轉譯面對面會議。

#### <a name="multi-device-conversation"></a>多裝置交談

使用[多設備對話](multi-device-conversation.md),在對話中連接多個設備或用戶端,發送基於語音或基於文本的消息,輕鬆支援轉錄和翻譯。

### <a name="custom--agent-scenarios"></a>自訂/代理方案

語音 SDK 可用於轉錄呼叫中心方案,其中生成電話數據。

#### <a name="call-center-transcription"></a>話務中心轉譯

[呼叫中心轉錄](call-center-transcription.md)是語音到文本的常見方案,用於轉錄可能來自各種系統(如互動式語音回應 (IVR))的大量電話數據。 語音服務的最新語音辨識模型擅長轉錄此電話數據,即使在數據難以理解的情況下也是如此。

### <a name="codec-compressed-audio-input"></a>編解碼器壓縮音訊輸入

一些語音 SDK 程式設計語言支援編解碼器壓縮音訊輸入流。 關於詳細資訊,請參考<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">來壓縮音訊輸入格式<span class="docon docon-navigate-external x-hidden-focus"></span></a>。

## <a name="rest-api"></a>REST API

雖然語音 SDK 涵蓋語音服務的許多功能功能,但對於某些方案,您可能需要使用 REST API。 例如,終結點管理僅通過 REST API 公開。

> [!TIP]
> 依賴 REST API 時,使用 Swagger 編輯器自動生成用戶端庫。 例如,生成批處理轉錄用戶端庫。
> 1. 複製下面的範例網址:
>     ```http
>     https://westus.cris.ai/docs/v2.0/swagger
>     ```
> 1. 瀏覽到<a href="https://editor.swagger.io/" target="_blank">斯瓦格編輯<span class="docon docon-navigate-external x-hidden-focus"></span>器</a>
> 1. 選擇**檔案** > **匯入網址**並貼上網網網網
> 1. 選擇 **'產生用戶端**' 並選擇所需的程式設計語言

### <a name="batch-transcription"></a>批次轉譯

[批處理轉錄](batch-transcription.md)支援對大量數據的非同步語音轉錄。 批處理轉錄只能從 REST API 進行。

## <a name="customization"></a>自訂

語音服務具有跨語音到文本、文本到語音轉換和語音轉換的預設模型,提供了出色的功能。 有時,您可能希望提高基準性能,以更好地使用獨特的用例。 語音服務具有各種無代碼自定義工具,使之變得簡單,並允許您利用基於您自己的數據的自定義模型創建競爭優勢。 這些模型將僅對您和您的組織可用。

### <a name="custom-speech-to-text"></a>自訂語音到文字

在獨特的環境中使用語音到文本進行識別和轉錄時,您可以創建並訓練自定義聲學、語言和發音模型,以解決環境噪音或行業特定的詞彙。 創建和管理無代碼自定義語音模型可通過[自定義語音門戶](https://aka.ms/customspeech)獲得。 發佈自定義語音模型後,語音 SDK 就可以使用它。

### <a name="custom-text-to-speech"></a>自訂文字到語音

自訂文字到語音,也稱為自訂語音是一組線上工具,允許您為您的品牌創建可識別的、一種類型的聲音。 創建和管理無代碼自定義語音模型可通過[自定義語音門戶](https://aka.ms/customvoice)獲得。 發佈自定義語音模型後,語音 SDK 就可以使用它。

## <a name="get-the-speech-sdk"></a>取得語音 SDK

# <a name="windows"></a>[Windows](#tab/windows)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-windows.md)]

# <a name="linux"></a>[Linux](#tab/linux)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-linux.md)]

# <a name="ios"></a>[iOS](#tab/ios)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-ios.md)]

# <a name="macos"></a>[macOS](#tab/macos)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-macos.md)]

# <a name="android"></a>[Android](#tab/android)

[!INCLUDE [Get the Speech SDK](includes/get-speech-sdk-android.md)]

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

[!INCLUDE [Get the Node.js Speech SDK](includes/get-speech-sdk-nodejs.md)]

# <a name="browser"></a>[瀏覽器](#tab/browser)

[!INCLUDE [Get the Browser Speech SDK](includes/get-speech-sdk-browser.md)]

---

[!INCLUDE [License notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

[!INCLUDE [Sample source code](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
