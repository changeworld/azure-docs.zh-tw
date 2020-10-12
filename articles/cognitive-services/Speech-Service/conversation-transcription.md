---
title: 對話轉譯 (預覽) -語音服務
titleSuffix: Azure Cognitive Services
description: 對話轉譯是會議的解決方案，結合辨識、說話者識別碼和 diarization 來提供任何交談的轉譯。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81402504"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>什麼是會議中的交談轉譯 (Preview) ？

對話轉譯是 [語音轉換文字](speech-to-text.md) 解決方案，可將語音辨識、說話者識別和句子屬性結合到每個說話者 (也稱為 _Diarization_) ，以提供任何交談的即時和/或非同步轉譯。 交談轉譯會區分對話中的說話者，以判斷誰說什麼，以及如何讓開發人員輕鬆地將語音轉換文字新增至執行多個說話者 diarization 的應用程式。

## <a name="key-features"></a>主要功能

- **時間戳記** -每個說話者語句都有時間戳記，因此您可以輕鬆地找出片語。
- **可讀取** 的文字記錄-文字記錄已自動新增格式設定和標點符號，以確保文字與所說的內容緊密相符。
- **使用者設定檔** -使用者設定檔的產生方式是收集使用者語音範例，並將其傳送到簽章產生。
- **說話者辨識** -使用使用者設定檔識別說話者，並將 _說話者識別碼_ 指派給每個使用者設定檔。
- **多喇叭 diarization** -使用每個說話者識別碼合成音訊串流，判斷誰說了什麼。
- **即時** 轉譯：提供使用者的即時文字記錄，指出交談發生的內容和時間。
- **非同步** 轉譯-使用多頻道音訊串流提供更高精確度的文字記錄。

> [!NOTE]
> 雖然交談轉譯不會限制房間內的喇叭數目，但它已針對每個會話2-10 的喇叭進行優化。

## <a name="use-cases"></a>使用案例

### <a name="inclusive-meetings"></a>內含會議

若要讓所有人都能參與會議，例如失聰和聽力很難的參與者，請務必即時轉譯。 即時模式的對話轉譯會接受音訊，並決定誰說什麼，讓所有會議參與者都能遵循文字記錄並參與會議，而不會有延遲。

### <a name="improved-efficiency"></a>提升效率

會議參與者可專注于會議，並在交談轉譯時留下筆記。 參與者可以主動參與會議，並使用文字記錄來快速跟進後續步驟，並使用文字記錄，而不是在會議期間拍攝筆記和可能遺失的東西。

## <a name="how-it-works"></a>運作方式

這是交談轉譯運作方式的高階總覽。

![匯入對話轉譯圖表](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>預期的輸入

- **多頻道音訊串流** –如需規格和設計詳細資料，請參閱 [MICROSOFT 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要深入瞭解或購買開發工具組，請參閱 [取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。
- **User voice 範例** –對話轉譯在交談之前需要使用者設定檔。 您必須從每位使用者收集音訊錄製，然後將記錄傳送到簽章 [產生服務](https://aka.ms/cts/signaturegenservice) ，以驗證音訊並產生使用者設定檔。

## <a name="real-time-vs-asynchronous"></a>即時與非同步

對話轉譯提供三種轉譯模式：

### <a name="real-time"></a>即時

音訊資料會即時處理，以傳回喇叭識別碼 + 文字記錄。 如果您的轉譯解決方案需求是為交談參與者提供進行中交談的即時文字記錄視圖，請選取此模式。 例如，建立應用程式以讓會議更容易存取，聽力障礙的人很難使用，這是即時轉譯的理想使用案例。

### <a name="asynchronous"></a>非同步

音訊資料會經過批次處理，以傳回喇叭識別碼和文字記錄。 如果您的轉譯解決方案需求是在沒有即時文字記錄視圖的情況下提供更高的精確度，請選取此模式。 例如，如果您想要建立應用程式，讓會議參與者能夠輕鬆趕上錯過的會議，請使用非同步轉譯模式來取得高精確度的轉譯結果。

### <a name="real-time-plus-asynchronous"></a>即時加上非同步

音訊資料會即時處理以傳回喇叭識別碼 + 文字記錄，此外，也會建立要求以透過非同步處理取得高精確度的文字記錄。 如果您的應用程式需要即時轉譯，但也需要較高的精確度文字記錄，以便在交談或會議發生之後使用，請選取此模式。

## <a name="language-support"></a>語言支援

目前，對話轉譯支援下欄區域中的 "en-us" 和 "zh-CN"： *centralus* 和 *eastasia*。 如果您需要其他地區設定支援，請洽詢 [交談轉譯功能](mailto:CTSFeatureCrew@microsoft.com)小組。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [即時轉譯對話](how-to-use-conversation-transcription-service.md)
