---
title: 關於語音 SDK-語音服務
titleSuffix: Azure Cognitive Services
description: 語音軟體發展工具組 (SDK) 公開許多語音服務功能，可讓您更輕鬆地開發具備語音功能的應用程式。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: ad26fe0d869a2e892a419b1732727c3ff1d1e9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870709"
---
# <a name="about-the-speech-sdk"></a>關於語音 SDK

語音軟體發展工具組 (SDK) 公開許多語音服務功能，可讓您開發具備語音功能的應用程式。 語音 SDK 提供許多程式設計語言以及所有平臺。

[!INCLUDE [Speech SDK Platforms](../../../includes/cognitive-services-speech-service-speech-sdk-platforms.md)]

## <a name="scenario-capabilities"></a>案例功能

語音 SDK 會公開來自語音服務的許多功能，但並非全部。 語音 SDK 的功能通常與案例相關聯。 語音 SDK 適用于即時和非即時案例，使用本機裝置、檔案、Azure blob 儲存體，甚至是輸入和輸出資料流程。 當無法使用語音 SDK 達成案例時，請尋找 REST API 替代方案。

### <a name="speech-to-text"></a>語音轉文字

[語音轉換文字](speech-to-text.md) (也稱為 *語音辨識*) 將音訊串流轉譯至您的應用程式、工具或裝置可以取用或顯示的文字。 若搭配 [Language Understanding (LUIS)](../luis/index.yml) 使用語音轉文字，即可從轉譯的語音衍生使用者意圖，以及根據語音命令執行動作。 使用 [語音翻譯](speech-translation.md) 透過單一呼叫，將語音輸入翻譯成不同的語言。 如需詳細資訊，請參閱 [語音轉換文字基本概念](speech-to-text-basics.md)。

**語音辨識 (SR) 、片語清單、意圖、轉譯和內部部署容器** 可在下列平臺上取得：

  - C + +/Windows & Linux & macOS
  - C # (Framework & .NET Core) /Windows & UWP & Unity & Xamarin & Linux & macOS
  - JAVA (Jre 和 Android) 
  - JavaScript (瀏覽器和 NodeJS) 
  - Python
  - Swift
  - Objective-C  
  - 移 (SR) 

### <a name="text-to-speech"></a>文字轉換語音

[文字轉換語音](text-to-speech.md) (也稱為 *語音合成*) 將文字轉換成類似人類的合成語音。 輸入文字可以是字串常值，或使用 [語音合成標記語言 (SSML) ](speech-synthesis-markup.md)。 如需有關標準或神經語音的詳細資訊，請參閱 [文字轉換語音語言和語音支援](language-support.md#text-to-speech)。

**文字轉換語音 (TTS) ** 適用于下列平臺：

  - C + +/Windows & Linux
  - & UWP & Unity 的 c #/Windows
  - JAVA (Jre 和 Android) 
  - Python
  - Swift
  - Objective-C
  - TTS REST API 可用於所有其他情況。

### <a name="voice-assistants"></a>語音助理

使用語音 SDK 的[語音助理](voice-assistants.md)可讓開發人員為其應用程式和體驗建立自然、類似人類的對話式介面。 語音助理服務可在裝置與助理之間提供快速且可靠的互動。 此程式會使用 Bot Framework 的 Direct Line 語音通道或整合式自訂命令 (Preview) 服務來完成工作。 此外，語音助理可以使用在 [自訂語音入口網站](https://aka.ms/customvoice) 中建立的自訂語音，來新增唯一的語音輸出體驗。

**語音助理** 適用于下列平臺：

  - C + +/Windows & Linux & macOS
  - C#/Windows
  - JAVA/Windows & Linux & macOS & Android (語音裝置 SDK) 

#### <a name="keyword-spotting"></a>關鍵字找出

語音 SDK 支援 [關鍵字找出](speech-devices-sdk-create-kws.md) 的概念。 關鍵字找出是在語音中識別關鍵字的動作，接著是在聽到關鍵字時的動作。 例如，"嗨 Cortana" 會啟用 Cortana 助理。

**關鍵字找出 (KWS) ** 適用于下列平臺：

  - C + +/Windows & Linux
  - C #/Windows & Linux
  - Python/Windows & Linux
  - JAVA/Windows & Linux & Android (語音裝置 SDK) 
  - 關鍵字找出 (KWS) 功能可能適用于任何麥克風類型、官方 KWS 支援，但目前僅限於 Azure Kinect DK 硬體或語音裝置 SDK 中的麥克風陣列

### <a name="meeting-scenarios"></a>會議案例

語音 SDK 適用于轉譯會議案例，無論是來自單一裝置或多裝置交談。

#### <a name="conversation-transcription"></a>對話轉譯

[對話](conversation-transcription.md) 轉譯可針對每個說話者提供即時 (和非同步) 語音辨識、說話者辨識和句子屬性， (也稱為 *diarization*) 。 非常適合利用辨識說話者的能力來轉譯面對面會議。

**對話** 轉譯適用于下列平臺：

  - C + +/Windows & Linux
  - C # (Framework & .NET Core) /Windows & UWP & Linux
  - JAVA/Windows & Linux & Android (語音裝置 SDK) 

#### <a name="multi-device-conversation"></a>多裝置交談

使用 [多裝置對話](multi-device-conversation.md)時，可連接對話中的多個裝置或用戶端，以傳送以語音或文字為基礎的訊息，並輕鬆支援轉譯和轉譯。

您可以在下列平臺上取得**多裝置對話**：

  - C + +/Windows
  - C # (Framework & .NET Core) /Windows

### <a name="custom--agent-scenarios"></a>自訂/代理程式案例

語音 SDK 可以用於轉譯電話中心案例，其中會產生電話語音資料。

#### <a name="call-center-transcription"></a>話務中心轉譯

[撥置中心](call-center-transcription.md) 轉譯是語音轉換文字的常見案例，可轉譯可能來自各種系統的大量電話語音資料，例如互動式語音回應 (IVR) 。 語音服務 excel 在轉譯此電話語音資料的最新語音辨識模型，即使在資料難以理解的情況下也是如此。

**撥接中心** 轉譯可透過 REST API 的 Batch 語音服務取得，並且可用於任何情況。

### <a name="codec-compressed-audio-input"></a>編解碼器壓縮音訊輸入

數種語音 SDK 程式設計語言支援編解碼器壓縮的音訊輸入資料流程。 如需詳細資訊，請參閱<a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-use-codec-compressed-audio-input-streams" target="_blank">使用壓縮的 <span class="docon docon-navigate-external x-hidden-focus"></span> 音訊輸入格式</a>。

**編解碼器壓縮音訊輸入** 適用于下列平臺：

  - C + +/Linux
  - C #/Linux
  - JAVA/Linux、Android 和 iOS

## <a name="rest-api"></a>REST API

雖然語音 SDK 涵蓋了語音服務的許多功能，但在某些情況下，您可能會想要使用 REST API。

### <a name="batch-transcription"></a>批次轉譯

[批次](batch-transcription.md) 轉譯可進行大量資料的非同步語音轉換文字轉譯。 只有 REST API 才可以進行批次轉譯。 除了將語音音訊轉換成文字，batch 語音轉換文字也允許 diarization 和情感分析。

## <a name="customization"></a>自訂

語音服務可透過語音轉換文字、文字轉換語音和語音翻譯等預設模型，提供絕佳的功能。 有時候您可能會想要增加基準效能，以便更妥善地使用您的唯一使用案例。 語音服務有各式各樣的無程式碼自訂工具，可讓您輕鬆地使用自訂模型，並根據您自己的資料來建立競爭優勢。 這些模型將僅供您和您的組織使用。

### <a name="custom-speech-to-text"></a>自訂語音轉換文字

在獨特的環境中使用語音轉換文字進行辨識和轉譯時，您可以建立並定型自訂聲場、語言和發音模型，以解決環境雜訊或產業特定詞彙。 您可以透過 [自訂語音入口網站](https://aka.ms/customspeech)來建立及管理無程式碼的自訂語音模型。 自訂語音模型發佈之後，語音 SDK 就可以使用它。

### <a name="custom-text-to-speech"></a>自訂文字轉換語音

自訂文字轉換語音（亦稱為自訂語音）是一組線上工具，可讓您為品牌建立可辨識且獨一無二的語音。 您可以透過 [自訂語音入口網站](https://aka.ms/customvoice)來建立及管理無程式碼自訂語音模型。 一旦發行自訂語音模型，語音 SDK 就可以使用它。

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

## <a name="next-steps"></a>接下來的步驟

* [建立 Azure 免費帳戶](https://azure.microsoft.com/free/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet) (英文)
