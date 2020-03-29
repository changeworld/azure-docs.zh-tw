---
title: 話務中心轉錄 - 語音服務
titleSuffix: Azure Cognitive Services
description: 語音到文本的常見方案是轉錄來自各種系統（如互動式語音回應 （IVR） 的大量電話資料。 使用語音服務和統一語音模型，企業可以通過音訊捕獲系統獲得高品質的轉錄。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: d959f4948d6b848f3b399c1310add06991d72012
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806315"
---
# <a name="speech-service-for-telephony-data"></a>電話資料的語音服務

透過有線電話、行動電話和無線電所產生的電話語音資料通常為低品質且在 8 KHz 範圍內的窄頻，其會在語音轉換文字時建立查問。 語音服務的最新語音辨識模型擅長轉錄此電話資料，即使在資料難以理解的情況下也是如此。 這些型號經過大量電話資料培訓，即使在嘈雜的環境中也能獲得最佳的市場識別精度。

語音轉換文字的常見案例是轉譯可能來自各種系統 (例如互動式語音回應 (IVR)) 的大量電話語音資料。 這些系統提供的音訊可以是立體聲或單聲道，而且是幾乎沒有對訊號進行後處理的原始狀態。 使用語音服務和統一語音模型，企業可以獲取高品質的轉錄，無論使用什麼系統來捕獲音訊。

電話語音資料可用來進一步了解您客戶的需求、找出新的行銷商機，或評估話務中心服務	專員的績效。 資料轉錄後，企業可以將輸出用於改進遙測、識別關鍵短語或分析客戶情緒等目的。

此頁面中概述的技術都是由 Microsoft 在內部針對各種支援通話處理服務 (即時和批次模式) 提供。

讓我們回顧一下語音服務提供的一些技術和相關功能。

> [!IMPORTANT]
> 語音服務 Unified 模型經過各種資料培訓，為從聽寫到電話分析等多種方案提供了單一模型解決方案。

## <a name="azure-technology-for-call-centers"></a>適用於話務中心的 Azure 技術

除了語音服務功能的功能方面，它們的主要目的（應用於話務中心）是改善客戶體驗。 這方面有三個清楚的領域存在：

- 呼叫後分析，本質上是呼叫後呼叫錄音的批次處理。
- 即時分析，即處理音訊信號，以在呼叫發生時提取各種見解（情緒是一個突出的用例）。
- 語音助理（機器人），要麼推動客戶和機器人之間的對話，試圖在沒有代理參與的情況下解決客戶的問題，要麼是應用人工智慧 （AI） 協定來説明代理。

![話務中心轉譯架構](media/scenarios/call-center-transcription-architecture.png)底下的圖片描繪批次案例實作的典型架構圖。

## <a name="speech-analytics-technology-components"></a>語音分析技術元件

無論域是呼叫後還是即時，Azure 都提供一套成熟和新興技術來改善客戶體驗。

### <a name="speech-to-text-stt"></a>語音轉換文字 (STT)

[語音到文本](speech-to-text.md)是任何話務中心解決方案中最搶手的功能。 由於許多下游分析過程依賴于轉錄的文本，因此單詞錯誤率 （_WER_） 至關重要。 話務中心轉譯的重要挑戰之一就是話務中心常見的雜訊 (例如，其他服務專員在背景中說話)、各種豐富的語言地區設定和方言，以及低品質的實際電話訊號。 WER 與聲學和語言模型對給定地區設定的培訓程度高度相關，因此，根據地區設定自訂模型的能力非常重要。 我們最新的整合 4.x 版模型是轉譯精確度和延遲的解決方案。 Unified 模型經過數萬小時的聲學資料和數十億條詞彙資訊的培訓，是市場上抄錄話務中心資料的最準確模型。

### <a name="sentiment"></a>情感

運用於話務中心空間時，測量客戶是否有良好的體驗是語音分析的最重要領域之一。 我們的[批次轉譯 API](batch-transcription.md)會提供每個語句的情感分析。 您可以彙總在通話文字記錄中取得的一組值，以判斷您的服務專員和客戶的通話情感。

### <a name="silence-non-talk"></a>無聲 (未交談)

35% 的支援通話通常會是我們所謂的未交談時間。 發生不對話的一些方案包括：代理查找客戶以前的案例歷史記錄，使用工具允許他們訪問客戶的桌面並執行功能的代理，等待轉移的客戶等。 極為重要的是，在呼叫中判斷何時發生沉默，因為這些類型的方案以及調用中發生的位置存在許多重要的客戶敏感性。

### <a name="translation"></a>翻譯

一些公司正在嘗試提供外語支援電話的翻譯記錄，以便交付經理能夠瞭解其客戶的世界性體驗。 我們可提供卓越的[翻譯](translation.md)功能。 我們可以為大量的地區設定翻譯音訊到音訊或音訊到文本。

### <a name="text-to-speech"></a>文字轉語音

實作可與客戶互動的 Bot 時，[文字轉換語音](text-to-speech.md)是另一個重要領域。 典型的路徑是客戶說話、其語音會轉譯為文字、分析文字的意圖、根據所辨識的意圖來合成回應，然後將資產呈現給客戶或產生合成的語音回應。 當然，所有這些都必須迅速發生，因此低延遲是這些系統成功的重要組成部分。

我們的端到端延遲對於涉及的各種技術來說相當低，例如[語音到文本](speech-to-text.md)[、LUIS、Bot](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)[框架](https://dev.botframework.com/)、[文本到語音](text-to-speech.md)。

我們的新語音也難以與人聲辨別。 您可以使用我們的聲音給你的機器人其獨特的個性。

### <a name="search"></a>搜尋

分析的另一個主要部分是要識別發生特定事件或體驗時的互動。 這通常用兩種方法之一完成;使用者只需鍵入短語和系統回應的 ad hoc 搜索，也可以是更結構化的查詢，分析人員可以創建一組邏輯語句，用於標識調用中的方案，然後每個調用都可以針對該查詢集編制索引。 一個很好的搜索示例是無處不在的合規性聲明"此調用應記錄用於品質目的...". 許多公司都希望確保其代理在實際記錄呼叫之前向客戶提供此免責聲明。 大多數分析系統都能夠預測查詢/搜索演算法發現的行為，而這種趨勢報告最終是分析系統最重要的功能之一。 透過[認知服務目錄](https://azure.microsoft.com/services/cognitive-services/directory/search/)，可以利用編製索引和搜尋功能大幅增強端對端解決方案。

### <a name="key-phrase-extraction"></a>關鍵片語擷取

這一領域是更具挑戰性的分析應用程式之一，也受益于 AI 和機器學習的應用。 在這種情況下，主要方案是推斷客戶意圖。 客戶為何來電？ 客戶有何問題？ 客戶為何有負面的體驗？ 我們的[文本分析服務](https://azure.microsoft.com/services/cognitive-services/text-analytics/)提供一套開箱即用的分析，以便快速升級用於提取這些重要關鍵字或短語的端到端解決方案。

我們現在可以更仔細地看看語音辨識的批次處理和即時管線。

## <a name="batch-transcription-of-call-center-data"></a>話務中心資料的批次轉譯

對於轉錄批量音訊，我們開發了[批量轉錄 API](batch-transcription.md)。 批次轉譯 API 的開發用意是要以非同步方式轉譯大量的音訊資料。 關於話務中心資料的轉錄，我們的解決方案基於以下支柱：

- **精度**- 第四代統一型號，我們提供無與倫比的轉錄品質。
- **延遲**- 我們理解，在進行批量轉錄時，需要快速的轉錄。 透過[批次轉譯 API](batch-transcription.md) 起始的轉譯作業會立即排入佇列，而一旦作業開始執行，其執行速度比即時轉譯還要快。
- **安全性**- 我們理解呼叫可能包含敏感性資料。 請放心，安全性是我們的最高優先順序之一。 我們的服務已取得 ISO、SOC、HIPAA、PCI 認證。

話務中心每天生成大量音訊資料。 如果您的企業將電話語音資料儲存在集中位置 (例如 Azure 儲存體)，您可使用[批次轉譯 API](batch-transcription.md)以非同步方式要求及接收轉譯。

典型的解決方案會使用下列服務：

- 語音服務用於轉錄語音到文本。 使用批次處理轉錄 API 需要語音服務的標準訂閱 （S0）。 免費訂用帳戶 (F0) 將無法運作。
- [Azure 儲存體](https://azure.microsoft.com/services/storage/)用來儲存電話語音資料，以及批次轉譯 API 所傳回的文字記錄。 此儲存體帳戶應該使用通知，特別是在有新檔案新增的時候。 這些通知用來觸發轉譯程序。
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 用來建立每次錄音的共用存取簽章 (SAS) URI，並觸發 HTTP POST 要求以開始轉譯。 此外，Azure Functions 用來建立使用批次轉譯 API 擷取和刪除轉譯的要求。

我們在內部使用上述技術來支援批次模式的 Microsoft 客戶通話。
![批次架構](media/scenarios/call-center-batch-pipeline.png)

## <a name="real-time-transcription-for-call-center-data"></a>話務中心資料的即時轉譯

有些企業需要即時轉譯交談。 即時轉譯可用來識別關鍵字組及觸發交談相關內容和資源的搜尋，以便監視情感，進而改善可存取性，或為不是母語人士的客戶和服務專員提供翻譯。

對於需要即時轉譯的案例，我們建議使用[語音 SDK](speech-sdk.md)。 語音轉換文字目前以[超過 20 種語言](language-support.md)提供，而 SDK 則以 C++、C#、Java、Python、Node.js、Objective-C 和 JavaScript 提供。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 上取得每種語言的範例。 如需最新消息和更新，請參閱[版本資訊](releasenotes.md)。

在內部，我們使用上述技術即時分析 Microsoft 客戶呼叫發生的時間，如下圖所示。

![批次架構](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IVR 上的字組

語音服務可以使用[語音 SDK](speech-sdk.md)或[REST API](rest-apis.md)輕鬆集成到任何解決方案中。 不過，話務中心轉譯可能需要額外的技術。 通常，IVR 系統與 Azure 之間需要連線。 儘管我們不提供此類元件，但以下是與 IVR 的連接需要的描述。

多個 IVR 或電話語音產品（如 Genesys 或 AudioCodes）提供集成功能，可以利用這些功能啟用入站和出站音訊傳遞到 Azure 服務。 基本上，自訂 Azure 服務可能提供特定介面來定義撥打電話會話（如呼叫開始或通話結束），並公開 WebSocket API 以接收與語音服務一起使用的入站流音訊。 輸出回應 (例如交談轉譯或與 Bot Framework 的連線) 可與 Microsoft 的文字轉換語音服務合成並傳回給 IVR 進行播放。

另一種方案是直接與會話啟動協定 （SIP） 集成。 Azure 服務連線到 SIP 伺服器，從而取得輸入資料流和輸出資料流，其用於語音轉換文字和文字轉換語音階段。 為了連線到 SIP 伺服器，因而有一些商業軟體供應項目，例如 Ozeki SDK，或 [Teams 通話與會議 API](/graph/api/resources/communications-api-overview) (目前為搶鮮版 (Beta))，其設計訴求是要支援這種音訊通話案例。

## <a name="customize-existing-experiences"></a>自訂現有的體驗

 語音服務與內置模型配合良好。 但是，您可能需要進一步自訂和調整產品或環境的體驗。 從原音模型調整到專屬於自身品牌的獨特聲音音調，都是自訂選項的範圍。 構建自訂模型後，可以在即時或批次處理模式下將其與任何語音服務功能一起使用。

| 語音服務 | 模型 | 描述 |
| -------------- | ----- | ----------- |
| 語音轉文字 | [原音模型](how-to-customize-acoustic-models.md) | 針對用於特定環境 (例如汽車或工廠) 的應用程式、工具或裝置建立自訂原音模型，而這每一個的錄音條件都較特殊。 例如，帶有口音的語音、特定背景雜音或使用特定麥克風來錄音。 |
|                | [語言模型](how-to-customize-language-model.md) | 建立自訂語言模型來提升特定產業的詞彙和文法轉譯，例如醫療術語或 IT 專業術語。 |
|                | [發音模型](how-to-customize-pronunciation.md) | 使用自訂發音模型，您可以定義拼音形式並顯示單詞或術語。 它可用於處理自訂的字詞，如產品名稱或縮略字。 所有你需要開始是一個發音檔，這是一個簡單的`.txt`檔。 |
| 文字轉換語音 | [聲音音調](how-to-customize-voice-font.md) | 自訂聲音音調可讓您為自己的品牌建立可辨識的獨特聲音。 只需少量資料即可開始建立。 提供的資料愈多，您的聲音音調聽起來就愈自然且愈像真人。 |

## <a name="sample-code"></a>範例程式碼

對於每個語音服務功能，GitHub 上都有示例代碼。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

- [語音轉文字及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk-reference.md)
- [語音裝置 SDK](speech-devices-sdk.md)
- [REST API：語音到文本](rest-speech-to-text.md)
- [REST API：文本到語音](rest-text-to-speech.md)
- [REST API：批次處理轉錄和自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
