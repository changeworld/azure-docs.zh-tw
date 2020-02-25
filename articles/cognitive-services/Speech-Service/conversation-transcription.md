---
title: 關於交談轉譯（預覽）-語音服務
titleSuffix: Azure Cognitive Services
description: 對話轉譯是一種語音轉換文字的解決方案，可將語音辨識、說話者識別和句子屬性結合到每個說話者（也稱為 diarization），以提供任何交談的即時和/或非同步轉譯。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8e36a5c4d9ce2bc38565d6c9c256ed2cc7efe357
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561063"
---
# <a name="about-conversation-transcription-preview"></a>關於交談轉譯（預覽）

對話轉譯是一種[語音轉換文字](speech-to-text.md)的解決方案，可將語音辨識、說話者識別和句子屬性結合到每個說話者（也稱為_diarization_），以提供任何交談的即時和/或非同步轉譯。 對話轉譯可區別交談中的說話者，以判斷誰說什麼，以及如何讓開發人員輕鬆地將語音轉換文字新增至執行多喇叭 diarization 的應用程式。

## <a name="key-features"></a>主要功能

- **時間戳記**-每個說話者語句都有時間戳記，讓您可以輕鬆地找到片語的時機。
- **可讀取**的文字記錄-文字記錄會自動新增格式和標點符號，以確保文字與所說的內容完全相符。
- **使用者設定檔**-使用者設定檔的產生方式是收集使用者語音範例，並將其傳送至簽章產生。
- **說話者辨識**-使用使用者設定檔識別說話者，並將_喇叭識別碼_指派給每個。
- **多說話者 diarization** -藉由使用每個喇叭識別碼合成音訊串流，判斷誰說什麼。
- **即時**轉譯–提供交談發生時機和時間的即時文字記錄。
- **非同步**轉譯–藉由使用多通道音訊串流，提供更高準確度的文字記錄。

> [!NOTE]
> 雖然對話轉譯不會限制房間內的喇叭數目，但它已針對每個會話的2-10 喇叭進行優化。

## <a name="use-cases"></a>使用案例

### <a name="inclusive-meetings"></a>內含會議

若要讓所有人都能參與會議，像是失聰和聽力困難的出席者，請務必即時進行轉譯。 即時模式中的交談轉譯會接受會議音訊，並判斷誰會說什麼，讓所有會議參與者都能遵循文字記錄並參與會議，而不會有延遲。

### <a name="improved-efficiency"></a>提升效率

會議參與者可以專注在會議上，並留下記事來進行對話轉譯。 參與者可以積極參與會議，並使用文字記錄來快速追蹤後續步驟，而不是在會議期間接受便箋，也可能會遺漏某些內容。

## <a name="how-it-works"></a>運作方式

這是對話轉譯運作方式的高階總覽。

![匯入交談轉譯圖表](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>預期的輸入

- **多頻道音訊串流**–如需規格和設計的詳細資訊，請參閱[MICROSOFT 語音裝置 SDK 麥克風](https://aka.ms/cts/microphone)。 若要深入瞭解或購買開發工具組，請參閱[取得 Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。
- **使用者語音範例**-對話轉譯在交談之前需要使用者設定檔。 您必須從每個使用者收集音訊錄影，然後將錄製傳送到簽章[產生服務](https://aka.ms/cts/signaturegenservice)，以驗證音訊並產生使用者設定檔。

## <a name="real-time-vs-asynchronous"></a>即時與非同步比較

對話轉譯提供三種轉譯模式：

### <a name="real-time"></a>即時

音訊資料會即時處理，以傳回喇叭識別碼 + 文字記錄。 如果您的轉譯解決方案需求是要為對話參與者提供進行中交談的即時文字記錄，請選取此模式。 例如，建立應用程式讓會議更容易存取，失聰和困難的出席者是即時轉譯的理想使用案例。

### <a name="asynchronous"></a>非同步的

音訊資料會進行批次處理，以傳回喇叭識別碼和文字記錄。 如果您的轉譯解決方案需求是提供較高的精確度，而不需要即時文字記錄，請選取此模式。 例如，如果您想要建立應用程式，讓會議參與者能夠輕鬆地趕上錯過的會議，則請使用非同步轉譯模式來取得高準確度的轉譯結果。

### <a name="real-time-plus-asynchronous"></a>即時加上非同步

音訊資料會即時處理以傳回說話者識別碼和文字記錄，此外，也會建立一個要求，以透過非同步處理取得高準確度的文字記錄。 如果您的應用程式需要即時轉譯，但也需要更高的精確度文字記錄，以便在交談或會議發生之後使用，請選取此模式。

## <a name="language-support"></a>語言支援

目前，交談轉譯支援下欄區域中的 "en-us" 和 "zh-CN"： *centralus*和 *eastasia*。 如果您需要額外的地區設定支援，請聯絡[對話轉譯功能](mailto:CTSFeatureCrew@microsoft.com)小組。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [即時轉譯交談](how-to-use-conversation-transcription-service.md)
