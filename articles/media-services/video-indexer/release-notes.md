---
title: Azure 媒體服務影片索引子的版本資訊 |Microsoft Docs
description: 為了隨時掌握最新的開發時間，本文提供您 Azure 媒體服務影片索引子的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 09/03/2020
ms.author: juliako
ms.openlocfilehash: e2b45ebf9acea7334678110015d8cfd3022675f4
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505332"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務影片索引子版本資訊

>將 URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` 複製並貼到 RSS 摘要閱讀程式中，以獲知何時該重新造訪此頁面來取得最新消息。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="august-2020"></a>2020 年 8 月

### <a name="mobile-design-for-the-video-indexer-website"></a>影片索引子網站的行動設計

影片索引子網站體驗現在支援行動裝置。 使用者體驗可因應您行動畫面大小的回應， (不包括自訂 Ui) 。 

### <a name="accessibility-improvements-and-bug-fixes"></a>協助工具改善和 bug 修正 

作為 WCAG (Web 內容協助工具指導方針的一部分) ，影片索引子網站體驗與 Microsoft 協助工具標準的第 C 等級一致。 已解決與鍵盤導覽、程式設計存取和螢幕閱讀程式相關的數個 bug 和改進。 

## <a name="july-2020"></a>2020 年 7 月

### <a name="ga-for-multi-language-identification"></a>適用于多語言識別的 GA

多國語言的識別會從預覽移至 GA，並且可供有效率地使用。

「預覽至 GA」轉換沒有任何相關的價格影響。

### <a name="video-indexer-website-improvements"></a>影片索引子網站改進

#### <a name="adjustments-in-the-video-gallery"></a>影片庫中的調整

新增了新的搜尋列，以提供其他篩選功能的深入見解搜尋。 也增強了搜尋結果。

新的清單視圖，可使用多個檔案來排序和管理影片封存。

#### <a name="new-panel-for-easy-selection-and-configuration"></a>可方便選取和設定的新面板

已新增可方便選取和使用者設定的側邊面板，可讓您輕鬆建立及快速建立帳戶和進行共用，以及進行設定。

側邊面板也可用於使用者喜好設定和協助。

## <a name="june-2020"></a>2020 年 6 月

### <a name="search-by-topics"></a>依主題搜尋

您現在可以使用搜尋 API，只)  (API 的特定主題來搜尋影片。

主題會新增為 `textScope` (選擇性參數) 的一部分。 如需詳細資訊，請參閱 [API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Search-Videos) 。  

### <a name="labels-enhancement"></a>標籤增強功能

標籤標記已升級，現在包含更多可辨識的視覺標籤。

## <a name="may-2020"></a>2020 年 5 月

### <a name="video-indexer-deployed-in-the-east-us"></a>在美國東部部署的影片索引子

您現在可以在美國東部區域建立影片索引子付費帳戶。
 
### <a name="video-indexer-url"></a>影片索引子 URL

影片索引子區域端點全都整合到僅使用 www 來啟動。 不需要任何動作專案。

從現在開始，您可以觸達 www.videoindexer.ai，不論是用於內嵌小工具或登入影片索引子 web 應用程式。

此外，wus.videoindexer.ai 會重新導向至 www。 您可以在 [應用程式中內嵌影片索引子小](video-indexer-embed-widgets.md)工具來取得詳細資訊。

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新的 widget 參數功能

**深入**解析小工具包含新的參數： `language` 和 `control` 。

**播放**程式 widget 有新的 `locale` 參數。 `locale`和參數都能 `language` 控制播放機的語言。

如需詳細資訊，請參閱 [widget 類型](video-indexer-embed-widgets.md#widget-types) 一節。 

### <a name="new-player-skin"></a>新播放機面板

以更新的設計啟動的新播放機面板。

### <a name="prepare-for-upcoming-changes"></a>準備即將進行的變更

* 目前，下列 Api 會傳回 account 物件：

    * [建立-付費帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [取得帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [取得帳戶-授權](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [取得-帳戶-使用-Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    帳戶物件具有 `Url` 指向 [影片索引子網站](https://www.videoindexer.ai/)位置的欄位。
針對付費帳戶，此 `Url` 欄位目前指向內部 URL 而非公用網站。
接下來的幾周，我們將會變更它，並傳回所有帳戶的 [影片索引子網站](https://www.videoindexer.ai/) URL (試用版和付費) 。

    請勿使用內部 Url，您應該使用 [影片索引子公用 api](https://api-portal.videoindexer.ai/)。
* 如果您要在應用程式中內嵌影片索引子 Url，且 Url 未指向 [影片索引子網站](https://www.videoindexer.ai/) 或影片索引子 API 端點 (`https://api.videoindexer.ai`) 但不是指向區域端點 (例如 `https://wus2.videoindexer.ai`) ，請重新產生 url。

   您可以透過下列任一方式來執行此動作：

    * 使用指向影片索引子小工具 Api 的 URL 來取代 URL (例如，深入解析 [小工具](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget)) 
    * 使用影片索引子網站來產生新的內嵌 URL：
         
         按下 [**播放**] 以進入您的影片頁面-> 按一下 [ ** &lt; / &gt; 內嵌**] 按鈕-> 將 URL 複製到您的應用程式中：
   
    區域 Url 不受支援，將在未來幾周內封鎖。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>其他語言的自訂語言支援

影片索引子現在支援 `ar-SY` 、和 (API 的自訂語言模型 `en-UK` `en-AU`) 。
 
### <a name="delete-account-timeframe-action-update"></a>刪除帳戶時間範圍動作更新

刪除帳戶動作現在會在90天內刪除帳戶，而不是在48小時內刪除。
 
### <a name="new-video-indexer-github-repository"></a>新的影片索引子 GitHub 存放庫

新的影片索引子 GitHub 具有不同的專案、快速入門手冊和程式碼範例現在已可供使用： https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger 更新

影片索引子整合 **驗證** 和 **作業** 成為單一 [影片索引子 OpenAPI 規格 (swagger) ](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)。 開發人員可以在 [影片索引子開發人員入口網站](https://api-portal.videoindexer.ai/)中找到 api。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>使用新的 API 更新文字記錄

使用 [更新-影片-索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update) API 來更新文字記錄中的特定區段。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>從影片索引子入口網站修正帳戶設定

您現在可以更新媒體服務連線設定，以協助解決下列問題： 

* 不正確的 Azure 媒體服務資源
* 密碼變更
* 媒體服務資源已在訂用帳戶之間移動  

若要修正帳戶設定，請在影片索引子入口網站中，流覽至 [設定] > 帳戶] 索引標籤 (為擁有者) 。

### <a name="configure-the-custom-vision-account"></a>設定自訂視覺帳戶

使用影片索引子入口網站 (在付費帳戶上設定自訂視覺帳戶，這僅受 API) 支援。 若要這樣做，請登入影片索引子入口網站，選擇 [模型自訂] > 動畫字元 > 設定]。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>場景、照片和主要畫面格-現在位於一個深入解析窗格中

幕後、照片和主要畫面格現在合併成一個深入解析，讓您更容易使用和流覽。 當您選取所需的場景時，您可以看到它所包含的螢幕擷取畫面和主要畫面格。 

### <a name="notification-about-a-long-video-name"></a>長影片名稱的通知

當影片名稱的長度超過80個字元時，影片索引子會在上傳時顯示描述性錯誤。

### <a name="streaming-endpoint-is-disabled-notification"></a>串流端點已停用通知

當串流端點停用時，影片索引子會在 [播放程式] 頁面上顯示描述性錯誤。

### <a name="error-handling-improvement"></a>錯誤處理改進

如果影片正在進行編制索引，狀態碼409現在將會從 [重新建立索引的影片](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?) 傳回，並 [更新影片索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?) api，以防止意外覆寫目前的重新索引變更。

## <a name="november-2019"></a>2019 年 11 月
 
* 韓文自訂語言模型支援

    影片索引子現在支援在 `ko-KR` API 和入口網站中以韓文 () 的自訂語言模型。 
* 語音轉換文字 (STT) 支援的新語言

    影片索引子 Api 現在支援阿拉伯文 Levantine 的 STT (ar-SY) 、英文 UK 方言 (en-us) 和英文澳大利亞用語 (en-us) 。
    
    針對影片上傳，我們將 zh-HANS 取代為 zh-CN，但這兩者都受到支援，但 zh-CN 建議且更準確。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 在資源庫中搜尋動畫字元

    編制動畫字元的索引時，您現在可以在帳戶的影片條樣中搜尋它們。 如需詳細資訊，請參閱 [動畫字元識別](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 宣佈多項改進：
 
*  (公開預覽) 的動畫字元識別

    透過與自訂視覺的整合，能夠偵測動畫內容中的群組 ad 辨識字元。 如需詳細資訊，請參閱[動畫角色偵測](animated-characters-recognition.md)。
* 多語言識別 (公開預覽) 

    在音訊播放軌中偵測多種語言的區段，並根據這些語言建立多語系文字記錄。 初始支援：英文、西班牙文、德文和法文。 如需詳細資訊，請參閱[自動識別並轉譯多語言內容](multi-language-identification-transcription.md)。
* 人員和位置的命名實體解壓縮

    透過自然語言處理 (NLP) ，將語音和視覺文字中的品牌、位置和人物解壓縮。
* 編輯照片類型分類

    標記具有編輯類型的照片，例如封閉、中度、雙拍攝、室內、室外等。如需詳細資訊，請參閱 [編輯照片類型偵測](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主題推斷增強功能-現在涵蓋層級2
    
    主題推斷模型現在可支援更深入的 IPTC 分類法。 閱讀 [Azure 媒體服務新的 AI 技術創新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)的完整詳細資料。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英國南部中部署的影片索引子

您現在可以在英國南部區域建立影片索引子付費帳戶。

### <a name="new-editorial-shot-type-insights-available"></a>提供新的編輯照片類型見解

新增至影片快照的新標籤會提供「拍攝類型」，以使用內容建立工作流程中所用的一般編輯片語來識別它們，例如：極端特寫、特寫、室內、左表面和右邊臉部 (可在 JSON) 中取得。

### <a name="new-people-and-locations-entities-extraction-available"></a>新的人員和位置實體可供提取

影片索引子會透過自然語言處理 (NLP) 從影片的 OCR 和轉譯，來識別已命名的位置和人員。 影片索引子會使用機器學習演算法來辨識特定位置 (例如，Eiffel 塔) 或人員 (例如，在影片中呼叫 John Doe) 。

### <a name="keyframes-extraction-in-native-resolution"></a>原生解析度中的主要畫面格解壓縮

影片索引子所解壓縮的主要畫面格可透過影片的原始解析度取得。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>從影像定型自訂臉部模型的 GA

將影像中的臉部從預覽模式移至 GA (可透過 API 和入口網站) 取得。

> [!NOTE]
> 「預覽至 GA」轉換沒有任何相關的價格影響。

### <a name="hide-gallery-toggle-option"></a>隱藏資源庫切換選項

使用者可以選擇隱藏入口網站中的 [資源庫] 索引標籤 (類似于隱藏 [範例] 索引標籤) 。
 
### <a name="maximum-url-size-increased"></a>URL 大小上限增加

對影片編制索引時，支援 4096 (的 URL 查詢字串，而不是 2048) 。
 
### <a name="support-for-multi-lingual-projects"></a>支援多語言專案

您現在可以根據以不同語言編制索引的影片建立專案， (API) 。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>編輯器做為小工具

影片索引子 AI 編輯器現在可作為要內嵌在客戶應用程式中的小工具。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>從入口網站更新隱藏式輔助字幕檔案的自訂語言模型

客戶可以在入口網站的 [自訂] 頁面中，提供 VTT、SRT 和 TTML 檔案格式做為語言模型的輸入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>將影片索引子部署到日本東部

您現在可以在日本東部區域中建立影片索引子付費帳戶。

### <a name="create-and-repair-account-api-preview"></a>建立並修復帳戶 API (預覽版) 

已新增新的 API，可讓您 [更新 Azure 媒體服務連接端點或金鑰](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改善上傳時的錯誤處理 

當基礎 Azure 媒體服務帳戶的設定不正確時，會傳回描述性訊息。

### <a name="player-timeline-keyframes-preview"></a>Player 時間軸主要畫面格預覽 

您現在可以在播放程式的時間軸上，看到每次的影像預覽。

### <a name="editor-semi-select"></a>編輯器半選取

您現在可以在編輯器中選擇特定的深入解析時間範圍之後，查看所選的所有見解預覽。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>從隱藏式輔助字幕檔案更新自訂語言模型

[建立自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag) 和 [更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag) api 現在支援 VTT、SRT 和 TTML 檔案格式做為語言模型的輸入。

呼叫 [更新影片文字記錄 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)時，會自動新增文字記錄。 與影片相關聯的定型模型也會自動更新。 如需如何自訂和定型語言模型的相關資訊，請參閱 [使用影片索引子自訂語言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下載文字記錄格式-TXT 和 CSV

除了已支援的隱藏式字幕格式 (SRT、VTT 和 TTML) ，影片索引子現在支援以 TXT 和 CSV 格式下載文字記錄。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
