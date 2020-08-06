---
title: Azure 媒體服務影片索引子版本資訊 |Microsoft Docs
description: 為了讓您隨時掌握最新的開發，本文提供 Azure 媒體服務影片索引子的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 08/05/2020
ms.author: juliako
ms.openlocfilehash: 1b11dc0ee17fd0686776a7262c4bc99bd98bcc3e
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/06/2020
ms.locfileid: "87837304"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務影片索引子版本資訊

>將 URL `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` 複製並貼到 RSS 摘要閱讀程式中，以獲知何時該重新造訪此頁面來取得最新消息。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="july-2020"></a>2020 年 7 月

### <a name="ga-for-multi-language-identification"></a>適用于多語言識別的 GA

多語言識別會從預覽移至 GA，並可供生產力使用。

「預覽至 GA」轉換沒有任何相關的定價影響。

### <a name="video-indexer-website-improvements"></a>影片索引子網站改良功能

#### <a name="adjustments-in-the-video-gallery"></a>影片庫中的調整

新增了新的搜尋列，以進行深度深入解析搜尋，並加入其他篩選功能。 也增強了搜尋結果。

新的清單視圖，能夠使用多個檔案來排序和管理影片封存。

#### <a name="new-panel-for-easy-selection-and-configuration"></a>用於輕鬆選取和設定的新面板

已新增用於輕鬆選取和使用者設定的側邊面板，可讓您進行簡單且快速的帳戶建立和共用，以及設定設定。

側邊面板也會用於使用者喜好設定和說明。

## <a name="june-2020"></a>2020 年 6 月

### <a name="search-by-topics"></a>依主題搜尋

您現在可以使用搜尋 API，以僅)  (API 的特定主題來搜尋影片。

主題會加入做為 `textScope` (選擇性參數) 的一部分。 如需詳細資訊，請參閱[API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Search-Videos) 。  

### <a name="labels-enhancement"></a>標籤增強功能

標籤標記已升級，現在包含更多可以識別的視覺標籤。

## <a name="may-2020"></a>2020 年 5 月

### <a name="video-indexer-deployed-in-the-east-us"></a>在美國東部部署的影片索引子

您現在可以在「美國東部」區域中建立影片索引子付費帳戶。
 
### <a name="video-indexer-url"></a>影片索引子 URL

影片索引子的區域端點全都是透過 www 開始整合。 不需要任何動作專案。

從現在開始，您可以 www.videoindexer.ai，不論它是用來內嵌 widget 或登入影片索引子 web 應用程式。

此外，wus.videoindexer.ai 也會重新導向至 www。 如需詳細資訊，請[觀看應用程式中的內嵌影片索引子小](video-indexer-embed-widgets.md)工具。

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新的 widget 參數功能

**深入**解析 widget 包含新的參數： `language` 和 `control` 。

**播放**程式 widget 有新的 `locale` 參數。 `locale`和 `language` 參數都控制播放機的語言。

如需詳細資訊，請參閱[widget 類型](video-indexer-embed-widgets.md#widget-types)一節。 

### <a name="new-player-skin"></a>新播放機外觀

以更新過的設計啟動的新播放機外觀。

### <a name="prepare-for-upcoming-changes"></a>準備即將進行的變更

* 目前，下列 Api 會傳回帳戶物件：

    * [建立-付費帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Paid-Account)
    * [取得帳戶](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Account)
    * [取得帳戶-授權](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-Authorization)
    * [取得帳戶-使用-Token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Accounts-With-Token)
 
    帳戶物件的 `Url` 欄位指向[影片索引子網站](https://www.videoindexer.ai/)的位置。
針對付費帳戶， `Url` 欄位目前指向內部 URL，而不是公用網站。
在接下來的幾周，我們將變更它，並傳回所有帳戶的[影片索引子網站](https://www.videoindexer.ai/)URL， (試用版和付費) 。

    請勿使用內部 Url，您應該使用[影片索引子公用 api](https://api-portal.videoindexer.ai/)。
* 如果您要在應用程式中內嵌影片索引子 Url，且 Url 未指向[影片索引子網站](https://www.videoindexer.ai/)或影片索引子 API 端點 (`https://api.videoindexer.ai`) 但改為區域端點 (例如 `https://wus2.videoindexer.ai`) ，請重新產生 url。

   您可以透過下列方式來執行此動作：

    * 以指向影片索引子 widget Api 的 URL 取代 URL (例如，[深入解析小工具](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Insights-Widget)) 
    * 使用影片索引子網站來產生新的內嵌 URL：
         
         按下 [**播放**] 以前往您的影片頁面-> 按一下 [ ** &lt; / &gt; 內嵌**] 按鈕-> 將 URL 複製到您的應用程式中：
   
    區域 Url 不受支援，將在未來幾周內遭到封鎖。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>其他語言的自訂語言支援

影片索引子現在支援、和的自訂語言模型 `ar-SY` ， `en-UK` `en-AU` 僅)  (API。
 
### <a name="delete-account-timeframe-action-update"></a>刪除帳戶時間範圍動作更新

[刪除帳戶] 動作現在會在90天內刪除帳戶，而不是48小時。
 
### <a name="new-video-indexer-github-repository"></a>新的影片索引子 GitHub 存放庫

新的影片索引子 GitHub 具有不同的專案，現在提供入門指南和程式碼範例：https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger 更新

影片索引子會將**驗證**和**作業**整合到單一[影片索引子 OpenAPI 規格中， (swagger) ](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)。 開發人員可以在[影片索引子開發人員入口網站](https://api-portal.videoindexer.ai/)中找到 api。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>使用新的 API 更新文字記錄

使用[更新-影片-索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)API 來更新文字記錄中的特定區段。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>從影片索引子入口網站修正帳戶設定

您現在可以更新媒體服務連線設定，以便自行協助解決下列問題： 

* 不正確的 Azure 媒體服務資源
* 密碼變更
* 已在訂用帳戶之間移動媒體服務資源  

若要修正帳戶設定，請在影片索引子入口網站中流覽至 [設定] > [帳戶] 索引標籤 (作為擁有者) 。

### <a name="configure-the-custom-vision-account"></a>設定自訂視覺帳戶

使用影片索引子入口網站 (之前在付費帳戶上設定自訂視覺帳戶，這僅供 API) 支援。 若要這麼做，請登入影片索引子入口網站，選擇 [模型自訂 > 動畫字元 > 設定]。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>場景、照片和主要畫面格–現在位於一個深入解析窗格

場景、快照和主要畫面格現在會合並成一個深入解析，讓您更容易取用和導覽。 當您選取所需的場景時，您可以看到其中所包含的螢幕擷取畫面和主要畫面格。 

### <a name="notification-about-a-long-video-name"></a>關於長影片名稱的通知

當影片名稱長度超過80個字元時，影片索引子會在上傳時顯示描述性錯誤。

### <a name="streaming-endpoint-is-disabled-notification"></a>串流端點已停用通知

當串流端點停用時，影片索引子會在 [播放] 頁面上顯示描述性錯誤。

### <a name="error-handling-improvement"></a>錯誤處理改善

現在會從[重新編制索引的影片](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?)傳回狀態碼409，並[更新影片索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?)api （如果影片正在進行索引編制），以避免意外覆寫目前的重新索引變更。

## <a name="november-2019"></a>2019 年 11 月
 
* 韓文自訂語言模型支援

    影片索引子現在支援在 `ko-KR` API 和入口網站中) 韓文 (的自訂語言模型。 
* 支援語音轉換文字的新語言 (STT) 

    影片索引子 Api 現在支援阿拉伯文 Levantine 中的 STT (ar-SY) 、英文英國方言 (en-us) 和英文澳大利亞方言 (en-us) 。
    
    針對影片上傳，我們將 zh-HANS 取代為 zh-CN，這兩者都受到支援，但 zh-CN 是建議且更精確的做法。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 在資源庫中搜尋動畫字元

    編制動畫字元的索引時，您現在可以在帳戶的影片條樣中搜尋它們。 如需詳細資訊，請參閱[動畫字元識別](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
在 IBC 2019 宣佈多項改進：
 
*  (公開預覽的動畫字元識別) 

    能夠透過與自訂視覺整合，偵測動畫內容中的群組廣告辨識字元。 如需詳細資訊，請參閱[動畫角色偵測](animated-characters-recognition.md)。
* 多語言識別 (公開預覽) 

    在音訊播放軌中偵測多種語言的區段，並根據它們建立多語系文字記錄。 初始支援：英文、西班牙文、德文和法文。 如需詳細資訊，請參閱[自動識別並轉譯多語言內容](multi-language-identification-transcription.md)。
* 人員和位置的命名實體解壓縮

    透過自然語言處理 (NLP) ，從語音和視覺文字中解壓縮品牌、位置和人員。
* 編輯照片類型分類

    以編輯類型（如近條、中度、兩次、室內、戶外等等）標記拍照。如需詳細資訊，請參閱[編輯快照類型偵測](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主題推斷增強功能-現在涵蓋層級2
    
    推斷模型的主題現在支援 IPTC 分類法更深入的資料細微性。 閱讀[Azure 媒體服務新的 AI 技術創新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)的完整詳細資料。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>英國南部中部署的影片索引子

您現在可以在英國南部區域建立影片索引子付費帳戶。

### <a name="new-editorial-shot-type-insights-available"></a>提供新的編輯快照類型深入解析

新增至影片快照的新標籤會提供編輯「拍攝類型」，以使用內容建立工作流程中常用的編輯片語來識別它們，例如：極端特寫、特寫、寬、中、兩次拍照、戶外、室內、左側臉部和右側臉部 (可在 JSON) 中取得。

### <a name="new-people-and-locations-entities-extraction-available"></a>新的人員和位置實體可供提取

影片索引子會透過自然語言處理來識別已命名的位置和人員， (NLP 從影片的 OCR 和轉譯) 。 影片索引子會使用機器學習演算法來辨識特定位置 (例如，Eiffel 塔式) 或人員 (例如，在影片中呼叫 John Doe) 。

### <a name="keyframes-extraction-in-native-resolution"></a>以原生解析度解壓縮的主要畫面格

影片索引子所解壓縮的主要畫面格可在影片的原始解析度中取得。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>針對從影像定型自訂臉部模型的 GA

從預覽模式移至 GA (可透過 API 和入口網站) 中的影像來定型臉部。

> [!NOTE]
> 「預覽至 GA」轉換沒有任何相關的定價影響。

### <a name="hide-gallery-toggle-option"></a>隱藏圖庫切換選項

使用者可以選擇隱藏入口網站中的 [圖庫] 索引標籤 (類似于隱藏 [範例] 索引標籤) 。
 
### <a name="maximum-url-size-increased"></a>增加的 URL 大小上限

支援 URL 查詢字串 4096 (，而不是2048，) 編制影片索引。
 
### <a name="support-for-multi-lingual-projects"></a>支援多語言專案

現在可以根據以不同語言編制索引的影片建立專案， (僅) API。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>編輯器做為 widget

影片索引子 AI 編輯器現在以 widget 的形式提供，可內嵌在客戶應用程式中。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>從入口網站更新隱藏式輔助字幕檔案中的自訂語言模型

客戶可以在入口網站的 [自訂] 頁面中，提供 VTT、SRT 和 TTML 檔案格式作為語言模型的輸入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>已部署至日本東部的影片索引子

您現在可以在日本東部地區建立影片索引子付費帳戶。

### <a name="create-and-repair-account-api-preview"></a>建立和修復帳戶 API (預覽) 

已加入新的 API，可讓您[更新 Azure 媒體服務連線端點或金鑰](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改善上傳時的錯誤處理 

當基礎 Azure 媒體服務帳戶設定錯誤時，會傳回描述性訊息。

### <a name="player-timeline-keyframes-preview"></a>播放玩家時程表主要畫面格預覽 

您現在可以在播放程式的時間軸上看到影像預覽。

### <a name="editor-semi-select"></a>編輯器半選取

您現在可以看到選取的所有深入解析的預覽，做為在編輯器中選擇特定的深入解析時間範圍的結果。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>從隱藏式輔助字幕檔案更新自訂語言模型

[建立自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)和[更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)api 現在支援 VTT、SRT 和 TTML 檔案格式，做為語言模型的輸入。

呼叫[更新影片文字記錄 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)時，會自動加入文字記錄。 與影片相關聯的定型模型也會自動更新。 如需如何自訂和定型語言模型的相關資訊，請參閱[使用影片索引子自訂語言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下載文字記錄格式– TXT 和 CSV

除了已支援 (SRT、VTT 和 TTML) 的隱藏式字幕格式之外，影片索引子現在支援以 TXT 和 CSV 格式下載文字記錄。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
