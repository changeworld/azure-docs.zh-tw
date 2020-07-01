---
title: 關於語音 SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 語音軟體發展工具組（SDK）公開許多語音服務功能，可讓您更輕鬆地開發啟用語音的應用程式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 5dc0a7fc9797948e834b8b3cb802bb92fce0eb59
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610756"
---
# <a name="about-the-speech-sdk"></a>關於語音 SDK

語音軟體發展工具組（SDK）公開許多語音服務功能，可讓您開發具備語音功能的應用程式。 語音 SDK 適用于許多程式設計語言，以及跨所有平臺。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>案例功能

語音 SDK 會公開來自語音服務的許多功能，但不是全部。 語音 SDK 的功能通常會與案例相關聯。 語音 SDK 適用于即時和非即時案例，使用本機裝置、檔案、Azure blob 儲存體，甚至是輸入和輸出資料流程。 當使用語音 SDK 無法達成案例時，請尋找 REST API 替代方案。

### <a name="speech-to-text"></a>語音轉文字

[語音轉換文字](speech-to-text.md)（也稱為*語音辨識*）可將您的應用程式、工具或裝置可以取用或顯示的文字串流。 若搭配 [Language Understanding (LUIS)](../luis/index.yml) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 使用[語音翻譯](speech-translation.md)，透過單一呼叫將語音輸入翻譯成不同的語言。 如需詳細資訊，請參閱[語音轉換文字的基本概念](speech-to-text-basics.md)。

### <a name="text-to-speech"></a>文字轉換語音

[文字到語音](text-to-speech.md)轉換（也稱為*語音合成*）會將文字轉換成類似人類的合成語音。 輸入文字可以是字串常值，或使用[語音合成標記語言（SSML）](speech-synthesis-markup.md)。 如需標準或類神經語音的詳細資訊，請參閱[文字轉換語音語言和語音支援](language-support.md#text-to-speech)。

### <a name="voice-assistants"></a>語音助理

[語音助理](voice-assistants.md)使用語音 SDK，可讓開發人員為其應用程式和體驗建立自然、人類類似的對話介面。 語音助理服務可提供裝置與助理之間快速可靠的互動。 執行作業會使用 Bot Framework 的 Direct Line 語音通道或整合式自訂命令（預覽）服務來完成工作。 此外，語音助理可以使用[自訂語音入口網站](https://aka.ms/customvoice)中所建立的自訂語音，來新增唯一的語音輸出體驗。

#### <a name="keyword-spotting"></a>關鍵字找出

語音 SDK 支援[關鍵字找出](speech-devices-sdk-create-kws.md)的概念。 關鍵字找出是用來識別語音中關鍵字的動作，後面接著在聽到關鍵字時的動作。 例如，「嘿 Cortana」會啟用 Cortana assistant。

### <a name="meeting-scenarios"></a>會議案例

無論是從單一裝置或多裝置交談，語音 SDK 都適用于轉譯會議案例。

#### <a name="conversation-transcription"></a>對話轉譯

[對話](conversation-transcription.md)轉譯可對每個說話者（也稱為*diarization*）進行即時（和非同步）語音辨識、說話者識別和句子特性。 非常適合利用辨識說話者的能力來轉譯面對面會議。

#### <a name="multi-device-conversation"></a>多裝置交談

透過[多重裝置交談](multi-device-conversation.md)，連接多個裝置或交談中的用戶端以傳送以語音為基礎或以文字為基礎的訊息，輕鬆地支援轉譯和翻譯。

### <a name="custom--agent-scenarios"></a>自訂/代理程式案例

語音 SDK 可用於轉譯撥打電話中心案例，其中會產生電話語音資料。

#### <a name="call-center-transcription"></a>話務中心轉譯

[話務中心](call-center-transcription.md)轉譯是語音轉換文字的常見案例，用於轉譯可能來自各種系統（例如互動式語音回應（IVR））的大量電話語音資料。 來自語音服務 excel 的最新語音辨識模型會轉譯此電話語音資料，即使是資料很容易瞭解的情況也是如此。

### <a name="codec-compressed-audio-input"></a>編解碼器壓縮的音訊輸入

有數個語音 SDK 程式設計語言支援編解碼器壓縮的音訊輸入資料流程。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">使用壓縮的 <span class="docon docon-navigate-external x-hidden-focus"></span> 音訊輸入格式</a>。

## <a name="rest-api"></a>REST API

雖然語音 SDK 涵蓋語音服務的許多功能，但在某些情況下，您可能會想要使用 REST API。

### <a name="batch-transcription"></a>批次轉譯

[批次](batch-transcription.md)轉譯可進行大量資料的非同步語音轉換文字轉譯。 只有 REST API 可以進行批次轉譯。 除了將語音音訊轉換成文字外，batch 語音轉換文字也允許進行 diarization 和情感分析。

## <a name="customization"></a>自訂

語音服務會在語音轉換文字、文字轉換語音和語音翻譯之間，提供其預設模型的絕佳功能。 有時候，您可能會想要增加基準效能，使其更適合您的獨特使用案例。 語音服務具有各種無程式碼自訂工具，可讓您輕鬆地進行，並可讓您利用以自己的資料為基礎的自訂模型，建立競爭優勢。 這些模型只會提供給您和您的組織使用。

### <a name="custom-speech-to-text"></a>自訂語音轉換文字

在獨特的環境中使用語音轉換文字進行辨識和轉譯時，您可以建立及定型自訂聲場、語言和發音模型，以解決環境雜訊或產業特定的詞彙。 建立和管理無程式碼自訂語音模型可透過[自訂語音入口網站](https://aka.ms/customspeech)取得。 一旦自訂語音模型發佈之後，語音 SDK 就可以使用它。

### <a name="custom-text-to-speech"></a>自訂文字轉換語音

自訂文字轉換語音（也稱為「自訂語音」）是一組線上工具，可讓您為您的品牌建立可辨識的一種語音。 您可以透過[自訂語音入口網站](https://aka.ms/customvoice)來建立和管理無程式碼自訂語音模型。 一旦自訂語音模型發佈之後，語音 SDK 就可以使用它。

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
