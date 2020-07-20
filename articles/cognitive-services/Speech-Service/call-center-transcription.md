---
title: 話務中心轉譯-語音服務
titleSuffix: Azure Cognitive Services
description: 語音轉換文字的常見案例是轉譯來自各種系統（例如互動式語音回應（IVR））的大量電話語音資料。 使用語音服務與整合語音模型，企業可以透過音訊捕獲系統取得高品質的轉譯。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 77573ac4240eeded1f803d88f218aaf4d4c5a929
ms.sourcegitcommit: ce44069e729fce0cf67c8f3c0c932342c350d890
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 06/09/2020
ms.locfileid: "84636121"
---
# <a name="speech-service-for-telephony-data"></a>電話語音資料的語音服務

透過有線電話、行動電話和無線電所產生的電話語音資料通常為低品質且在 8 KHz 範圍內的窄頻，其會在語音轉換文字時建立查問。 來自語音服務 excel 的最新語音辨識模型會轉譯此電話語音資料，即使是資料很容易瞭解的情況也是如此。 這些模型是使用大量電話語音資料進行定型，並具有最佳市場辨識準確度，即使是在嘈雜的環境中也是如此。

語音轉換文字的常見案例是轉譯可能來自各種系統 (例如互動式語音回應 (IVR)) 的大量電話語音資料。 這些系統提供的音訊可以是立體聲或單聲道，而且是幾乎沒有對訊號進行後處理的原始狀態。 企業可以使用語音服務和統一的語音模型，取得高品質的轉譯，不論是使用哪種系統來捕獲音訊。

電話語音資料可用來進一步了解您客戶的需求、找出新的行銷商機，或評估話務中心服務	專員的績效。 在轉譯資料之後，企業可以將輸出用於改善遙測、識別關鍵字組或分析客戶情感等用途。

此頁面中概述的技術都是由 Microsoft 在內部針對各種支援通話處理服務 (即時和批次模式) 提供。

讓我們來回顧語音服務所提供的一些技術和相關功能。

> [!IMPORTANT]
> 語音服務統一模型會使用各種資料進行定型，並提供從聽寫到電話語音分析的多種案例的單一模型解決方案。

## <a name="azure-technology-for-call-centers"></a>適用於話務中心的 Azure 技術

除了語音服務功能的功能層面外，其主要目的是在適用于撥打電話中心時，改善客戶體驗。 這方面有三個清楚的領域存在：

- 呼叫後分析，這基本上是呼叫之後的呼叫錄製批次處理。
- 即時分析，這會處理音訊信號，以便在進行呼叫時，將各種深入解析解壓縮（情感是一個顯著的使用案例）。
- 語音助理（bot），可以在客戶與 bot 之間駕駛對話，以嘗試解決客戶的問題，而不需要參加任何代理程式，或使用人工智慧（AI）通訊協定來協助代理程式。

![話務中心轉譯架構](media/scenarios/call-center-transcription-architecture.png)底下的圖片描繪批次案例實作的典型架構圖。

## <a name="speech-analytics-technology-components"></a>語音分析技術元件

無論網域是呼叫後或即時，Azure 都提供一組成熟且新興的技術，來改善客戶體驗。

### <a name="speech-to-text-stt"></a>語音轉換文字 (STT)

[語音轉換文字](speech-to-text.md)是任何撥接中心解決方案中最容易搜尋的功能。 由於許多下游分析程式都依賴轉譯的文字，因此，文字錯誤率（_WER_）的重要性最重要。 話務中心轉譯的重要挑戰之一就是話務中心常見的雜訊 (例如，其他服務專員在背景中說話)、各種豐富的語言地區設定和方言，以及低品質的實際電話訊號。 WER 與聲音和語言模型針對指定的地區設定定型的程度具有高度關聯性，因此自訂模型到地區設定的能力很重要。 我們最新的整合 4.x 版模型是轉譯精確度和延遲的解決方案。 整合模型是以數十個小時的聲場資料和數十億個詞彙資訊進行定型，因此市場中最精確的模型是轉譯撥接中心資料。

### <a name="sentiment"></a>情感

運用於話務中心空間時，測量客戶是否有良好的體驗是語音分析的最重要領域之一。 我們的[批次轉譯 API](batch-transcription.md)會提供每個語句的情感分析。 您可以彙總在通話文字記錄中取得的一組值，以判斷您的服務專員和客戶的通話情感。

### <a name="silence-non-talk"></a>無聲 (未交談)

35% 的支援通話通常會是我們所謂的未交談時間。 發生非交談的某些情況如下：代理程式向客戶查閱先前的案例歷程記錄、使用可讓他們存取客戶桌面並執行功能的工具、等待轉移的客戶，等等。 請務必在呼叫中進行無聲的時機，因為這類案例和在呼叫中發生的重要客戶敏感性數。

### <a name="translation"></a>翻譯

有些公司會試驗從外國語言支援電話提供翻譯的文字記錄，讓傳遞管理員能夠瞭解其客戶的全球經驗。 我們可提供卓越的[翻譯](translation.md)功能。 我們可以針對大量的地區設定轉譯音訊到音訊或音訊到文字。

### <a name="text-to-speech"></a>文字轉語音

實作可與客戶互動的 Bot 時，[文字轉換語音](text-to-speech.md)是另一個重要領域。 典型的路徑是客戶說話、其語音會轉譯為文字、分析文字的意圖、根據所辨識的意圖來合成回應，然後將資產呈現給客戶或產生合成的語音回應。 當然，這一切都必須快速進行，因此，低延遲是這些系統成功的重要元件。

對於涉及的各種技術（例如[語音轉換文字](speech-to-text.md)、 [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/)、 [Bot Framework](https://dev.botframework.com/)、[文字轉換語音](text-to-speech.md)）而言，我們的端對端延遲會大幅降低。

我們的新語音也難以與人聲辨別。 您可以使用我們的語音，為您的 bot 提供獨特的個人化。

### <a name="search"></a>搜尋

分析的另一個主要部分是要識別發生特定事件或體驗時的互動。 這通常是透過下列兩種方法的其中一種來完成：臨機操作搜尋，使用者只需輸入片語和系統回應，或是更具結構化的查詢，讓分析師可以建立一組可在呼叫中識別案例的邏輯語句，然後每個呼叫都可以針對該查詢集合進行索引。 一個很好的搜尋範例就是普遍的合規性聲明「此呼叫應針對品質目的進行記錄 .。。". 許多公司想要確保其代理程式在實際記錄通話之前，將此免責聲明提供給客戶。 大部分的分析系統都能夠為查詢/搜尋演算法所找到的行為提供趨勢，而此趨勢的報告最終是分析系統中最重要的功能之一。 透過[認知服務目錄](https://azure.microsoft.com/services/cognitive-services/directory/search/)，可以利用編製索引和搜尋功能大幅增強端對端解決方案。

### <a name="key-phrase-extraction"></a>關鍵片語擷取

這個領域是一個更具挑戰性的分析應用程式，另一個則受益于 AI 和機器學習服務的應用程式。 在此情況下，主要案例是推斷客戶意圖。 客戶為何來電？ 客戶有何問題？ 客戶為何有負面的體驗？ 我們的[文字分析服務](https://azure.microsoft.com/services/cognitive-services/text-analytics/)提供一組現成的分析，以快速升級您的端對端解決方案，以解壓縮這些重要的關鍵字或片語。

我們現在可以更仔細地看看語音辨識的批次處理和即時管線。

## <a name="batch-transcription-of-call-center-data"></a>話務中心資料的批次轉譯

針對轉譯大量音訊，我們開發了[Batch 轉譯 API](batch-transcription.md)。 批次轉譯 API 的開發用意是要以非同步方式轉譯大量的音訊資料。 相對於轉譯撥打電話中心資料，我們的解決方案是以下列要素為基礎：

- **精確度**-透過第四代的整合模型，我們提供卓越的轉譯品質。
- **延遲**-我們瞭解在進行大量轉譯時，需要快速轉譯。 透過[批次轉譯 API](batch-transcription.md) 起始的轉譯作業會立即排入佇列，而一旦作業開始執行，其執行速度比即時轉譯還要快。
- **安全性**-我們瞭解呼叫可能包含敏感性資料。 請放心，安全性是我們的最高優先順序之一。 我們的服務已取得 ISO、SOC、HIPAA、PCI 認證。

話務中心會每天產生大量的音訊資料。 如果您的企業將電話語音資料儲存在集中位置 (例如 Azure 儲存體)，您可使用[批次轉譯 API](batch-transcription.md)以非同步方式要求及接收轉譯。

典型的解決方案會使用下列服務：

- 語音服務是用來轉譯語音轉換文字。 需要語音服務的標準訂用帳戶（S0），才能使用批次轉譯 API。 免費訂用帳戶 (F0) 將無法運作。
- [Azure 儲存體](https://azure.microsoft.com/services/storage/)用來儲存電話語音資料，以及批次轉譯 API 所傳回的文字記錄。 此儲存體帳戶應該使用通知，特別是在有新檔案新增的時候。 這些通知用來觸發轉譯程序。
- [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 用來建立每次錄音的共用存取簽章 (SAS) URI，並觸發 HTTP POST 要求以開始轉譯。 此外，Azure Functions 用來建立使用批次轉譯 API 擷取和刪除轉譯的要求。

我們在內部使用上述技術來支援批次模式的 Microsoft 客戶通話。
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="用來支援批次模式中的 Microsoft 客戶呼叫的技術。":::

## <a name="real-time-transcription-for-call-center-data"></a>話務中心資料的即時轉譯

有些企業需要即時轉譯交談。 即時轉譯可用來識別關鍵字組及觸發交談相關內容和資源的搜尋，以便監視情感，進而改善可存取性，或為不是母語人士的客戶和服務專員提供翻譯。

對於需要即時轉譯的案例，我們建議使用[語音 SDK](speech-sdk.md)。 語音轉換文字目前以[超過 20 種語言](language-support.md)提供，而 SDK 則以 C++、C#、Java、Python、Node.js、Objective-C 和 JavaScript 提供。 您可以在 [GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) 上取得每種語言的範例。 如需最新消息和更新，請參閱[版本資訊](releasenotes.md)。

我們在內部使用上述技術來分析即時的 Microsoft 客戶電話，如下列圖表所示。

![批次架構](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>IVR 上的字組

語音服務可以使用[語音 SDK](speech-sdk.md)或[REST API](rest-apis.md)，輕鬆地整合到任何解決方案中。 不過，話務中心轉譯可能需要額外的技術。 通常，IVR 系統與 Azure 之間需要連線。 雖然我們不提供這類元件，但以下是與 IVR 的連線有何關聯的描述。

數個 IVR 或電話語音服務產品（例如 Genesys 或 AudioCodes）提供整合功能，可讓您用來啟用 Azure 服務的輸入和輸出音訊傳遞。 基本上，自訂 Azure 服務可能會提供特定的介面來定義通話會話（例如呼叫開始或呼叫端），並公開 WebSocket API 來接收與語音服務搭配使用的輸入資料流程音訊。 輸出回應 (例如交談轉譯或與 Bot Framework 的連線) 可與 Microsoft 的文字轉換語音服務合成並傳回給 IVR 進行播放。

另一個案例是與會話初始通訊協定（SIP）的直接整合。 Azure 服務連線到 SIP 伺服器，從而取得輸入資料流和輸出資料流，其用於語音轉換文字和文字轉換語音階段。 為了連線到 SIP 伺服器，因而有一些商業軟體供應項目，例如 Ozeki SDK，或 [Teams 通話與會議 API](/graph/api/resources/communications-api-overview) (目前為搶鮮版 (Beta))，其設計訴求是要支援這種音訊通話案例。

## <a name="customize-existing-experiences"></a>自訂現有的體驗

 語音服務適用于內建模型。 不過，您可能會想要進一步自訂及調整產品或環境的體驗。 從原音模型調整到專屬於自身品牌的獨特聲音音調，都是自訂選項的範圍。 建立自訂模型之後，您可以在即時或批次模式中，將它與任何語音服務功能搭配使用。

| 語音服務 | 型號 | 描述 |
| -------------- | ----- | ----------- |
| 語音轉文字 | [原音模型](how-to-customize-acoustic-models.md) | 針對用於特定環境 (例如汽車或工廠) 的應用程式、工具或裝置建立自訂原音模型，而這每一個的錄音條件都較特殊。 例如，帶有口音的語音、特定背景雜音或使用特定麥克風來錄音。 |
|                | [語言模型](how-to-customize-language-model.md) | 建立自訂語言模型來提升特定產業的詞彙和文法轉譯，例如醫療術語或 IT 專業術語。 |
|                | [發音模型](how-to-customize-pronunciation.md) | 使用自訂發音模型，您可以定義語音形式，並顯示單字或詞彙。 它可用於處理自訂的字詞，如產品名稱或縮略字。 您需要開始使用的是一個發音檔案，這是一個簡單的檔案 `.txt` 。 |
| 文字轉換語音 | [聲音音調](how-to-customize-voice-font.md) | 自訂聲音音調可讓您為自己的品牌建立可辨識的獨特聲音。 只需少量資料即可開始建立。 提供的資料愈多，您的聲音音調聽起來就愈自然且愈像真人。 |

## <a name="sample-code"></a>範例程式碼

GitHub 上有提供每個語音服務功能的範例程式碼。 這些範例包含常見案例，例如：從檔案或資料流讀取音訊、連續辨識、一次性辨識及使用自訂模型。 使用下列連結來檢視 SDK 和 REST 範例：

- [語音轉文字及語音翻譯範例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批次轉譯範例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [文字轉語音範例 (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>參考文件

- [語音 SDK](speech-sdk-reference.md)
- [語音裝置 SDK](speech-devices-sdk.md)
- [REST API：語音轉文字](rest-speech-to-text.md)
- [REST API：文字轉語音](rest-text-to-speech.md)
- [REST API：批次轉譯與自訂](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>後續步驟

> [!div class="nextstepaction"]
> [免費取得語音服務的訂用帳戶金鑰](get-started.md)
