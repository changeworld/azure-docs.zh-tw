---
title: 對話轉錄 (預覽) - 語音服務
titleSuffix: Azure Cognitive Services
description: 對話轉錄是會議的解決方案,它結合了識別、演講者 ID 和二分化,以提供任何會話的轉錄。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402504"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>什麼是會議中的對話轉錄(預覽)?

對話轉錄是一種[語音轉文本](speech-to-text.md)解決方案,它結合了語音辨識、揚聲器識別和句子歸因到每個演講者(也稱為_二分化_),以提供任何會話的即時和/或異步轉錄。 對話轉錄在對話中區分演講者,以確定誰說了什麼和什麼時候,並且開發人員可以輕鬆地將語音到文本添加到執行多揚聲器二化的應用程式。

## <a name="key-features"></a>主要功能

- **時間戳**- 每個演講者的話語都有一個時間戳,這樣你可以很容易地找到什麼時候說一個短語。
- **可讀抄本**- 抄本會自動添加格式和標點符號,以確保文本與所說的內容緊密匹配。
- **使用者設定檔**- 使用者設定檔通過收集使用者語音樣本並將其發送到簽名生成來生成。
- **喇叭識別**- 使用使用者設定檔識別揚聲器,並為每個揚聲器分配_一個揚聲器識別碼_。
- **多揚聲器二分化**- 通過將音訊流與每個揚聲器標識符合成來確定誰說了什麼。
- **即時轉錄**– 提供誰在說話的內容和時間進行對話的即時記錄。
- **非同步轉錄**– 透過使用多聲道音訊串流提供更高精度的轉錄。

> [!NOTE]
> 儘管對話轉錄不會限制室內的揚聲器數量,但它針對每會話 2-10 個揚聲器進行了優化。

## <a name="use-cases"></a>使用案例

### <a name="inclusive-meetings"></a>包容性會議

為了使會議包容每個人,如失聰和聽力障礙的參與者,即時進行轉錄非常重要。 即時模式下的對話轉錄會獲取會議音訊並確定誰在說什麼,允許所有會議參與者遵循成績單並毫不拖延地參加會議。

### <a name="improved-efficiency"></a>提高效率

會議參與者可以集中討論會議,並將筆記留給對話轉錄。 參與者可以積極參與會議,並快速跟進後續步驟,使用成績單,而不是做筆記,並可能在會議期間遺漏某些內容。

## <a name="how-it-works"></a>運作方式

這是對話轉錄工作原理的高級概述。

![匯入對話轉錄圖](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>預期輸入

- **多聲道音訊流**– 有關規格和設計詳細資訊,請參閱[Microsofts SDK 麥克風](https://aka.ms/cts/microphone)。 要瞭解更多資訊或購買開發工具組,請參閱獲取[Microsoft 語音裝置 SDK](https://aka.ms/cts/getsdk)。
- **使用者語音示例**– 對話轉錄需要在對話之前提供使用者配置檔。 您需要從每個使用者收集音訊錄製,然後將錄音發送到[簽名生成服務](https://aka.ms/cts/signaturegenservice)以驗證音訊並生成使用者配置檔。

## <a name="real-time-vs-asynchronous"></a>即時與非同步

對話轉錄提供三種轉錄模式:

### <a name="real-time"></a>即時

音訊數據即時處理,以返回揚聲器標識符和腳本。 如果您的轉錄解決方案要求是向對話參與者提供其正在進行的對話的即時記錄視圖,請選擇此模式。 例如,構建應用程式使會議更容易被聾啞和聽力障礙的參與者更容易訪問,是即時轉錄的理想用例。

### <a name="asynchronous"></a>非同步的

音訊數據被批處理為返回揚聲器標識符和腳本。 如果您的轉錄解決方案要求提供更高的精度,而無需即時記錄視圖,請選擇此模式。 例如,如果要構建應用程式以允許會議參與者輕鬆趕上錯過的會議,請使用非同步轉錄模式獲取高精度轉錄結果。

### <a name="real-time-plus-asynchronous"></a>即時加異步

音訊數據進行即時處理,以返回揚聲器標識符 + 腳本,此外,還會創建一個請求,以便通過異步處理獲得高精度腳本。 如果應用程式需要即時轉錄,但還需要更高的精度轉錄,請在對話或會議發生后使用,請選擇此模式。

## <a name="language-support"></a>語言支援

目前,對話轉錄支援以下區域的「美國」和「zh-CN」: *中亞*和 *東亞*。 如果您需要其他區域設定支援,請聯絡[對話轉錄群組](mailto:CTSFeatureCrew@microsoft.com)。

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [即時轉錄對話](how-to-use-conversation-transcription-service.md)
