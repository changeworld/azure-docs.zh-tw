---
title: Azure 媒體服務視頻索引器發佈說明 |微軟文件
description: 為了瞭解最新動態,本文為您提供了 Azure 媒體服務視頻索引器的最新更新。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.date: 04/20/2020
ms.author: juliako
ms.openlocfilehash: 5aa7abf7aafc14e71af5618cec892ef9f843d88a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733059"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure 媒體服務視訊索引器發佈說明

>以複製和貼上此網址`https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us`: 到 RSS 源閱讀器,獲得有關何時重新存取此頁面以進行更新的通知。

為了讓您隨時掌握最新的開發訊息，本文提供下列相關資訊：

* 最新版本
* 已知問題
* 錯誤修正
* 已被取代的功能

## <a name="april-2020"></a>2020 年 4 月

### <a name="new-widget-parameters-capabilities"></a>新的小元件參數功能

**見解**小元件包括新的參數`language`:`control`與 。

**播放器**小部件有一個`locale`新的 參數。 和`locale``language`參數都控制玩家的語言。

有關詳細資訊,請參閱[小部件類型](video-indexer-embed-widgets.md#widget-types)部分。 

## <a name="new-player-skin"></a>新玩家外觀

一個新的玩家皮膚推出與更新的設計。

## <a name="january-2020"></a>2020 年 1 月
 
### <a name="custom-language-support-for-additional-languages"></a>對其他語言的自訂語言支援

視訊索引器現在`ar-SY`支援 的自`en-UK`訂語言`en-AU`模型, 和 (僅限 API)。
 
### <a name="delete-account-timeframe-action-update"></a>移除帳號時間範圍操作更新

刪除帳戶操作現在在 90 天內刪除帳戶,而不是 48 小時。
 
### <a name="new-video-indexer-github-repository"></a>新的視訊索引器 GitHub 儲存庫

新的視訊索引器 GitHub 具有不同的專案、入門指南和代碼範例,現已推出:https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>斯瓦格更新

視頻索引器將**身份驗證**和**操作**統一到單一視頻[索引器 OpenAPI 規範(搖號)](https://api-portal.videoindexer.ai/docs/services/Operations/export?DocumentFormat=OpenApiJson)中。 開發人員可以在[視頻索引器開發人員門戶](https://api-portal.videoindexer.ai/)中找到 API。

## <a name="december-2019"></a>2019 年 12 月

### <a name="update-transcript-with-the-new-api"></a>使用新的 API 更新文稿

使用[更新視頻索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?&pattern=update)API 更新腳本中的特定部分。

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>從視訊索引器門戶修復帳戶設定

您現在可以更新媒體服務連線設定,以便自助解決諸如: 

* 不正確的 Azure 媒體服務資源
* 密碼變更
* 媒體服務資源在訂閱之間移動  

要修復帳戶配置,在「視頻索引器」門戶中導航到「設置>帳戶」選項卡(作為擁有者)。

### <a name="configure-the-custom-vision-account"></a>設定自訂視覺帳戶

使用視頻索引器門戶在付費帳戶上配置自定義視覺帳戶(以前,僅由 API 支援)。 為此,請登錄到視頻索引器門戶,選擇"模型自定義">动画角色>配置。 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>場景、鏡頭和關鍵幀 – 現在在一個洞察窗格中

場景、鏡頭和關鍵幀現在合併為一個見解,以便更輕鬆地使用和導航。 當您選擇所需的場景時,您可以看到它包含的鏡頭和關鍵幀。 

### <a name="notification-about-a-long-video-name"></a>有關長視訊名稱的通知

當視頻名稱超過 80 個字元時,視頻索引器在上載時顯示描述性錯誤。

### <a name="streaming-endpoint-is-disabled-notification"></a>串流式處理終結點已關閉通知

禁用流式處理終結點后,視頻索引機將在播放機頁面上顯示描述性錯誤。

### <a name="error-handling-improvement"></a>錯誤處理改進

狀態代碼 409 現在將從[重新索引視頻和](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?https://api-portal.videoindexer.ai/docs/services/Operations/operations/Re-Index-Video?)[更新視頻索引](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Index?)API 返回,以防視頻被主動編製索引,以防止意外覆蓋當前的重新索引更改。

## <a name="november-2019"></a>2019 年 11 月
 
* 韓國自訂語言模型支援

    視訊索引器現在在 API 和門`ko-KR`戶中支援 韓語 ( 的自訂語言模型. 
* 語音到文字 (STT) 支援新語言

    視頻索引器 API 現在支援阿拉伯文 Levantine (ar-SY)、英語英國方言 (en-GB) 和英語澳大利亞方言 (en-AU) 的 STT。
    
    對於視頻上傳,我們將 zh-HANS 替換為 zh-CN,兩者都受支援,但建議 zh-CN 更準確。
    
## <a name="october-2019"></a>2019 年 10 月
 
* 在庫中搜尋動畫角色

    索引動畫角色時,現在可以在帳戶的視頻廚房中搜索它們。 有關詳細資訊,請參閱[動畫字元識別](animated-characters-recognition.md)。

## <a name="september-2019"></a>2019 年 9 月
 
IBC 2019 年宣佈的多項進展:
 
* 動畫角色識別(公共預覽)

    通過與自定義視覺的集成,能夠檢測群廣告識別動畫內容中的字元。 有關詳細資訊,請參閱[動畫角色偵測](animated-characters-recognition.md)。
* 多語言辨識 (公共預覽)

    檢測音軌中多種語言的段,並基於這些片段創建多語言腳本。 初步支援:英語、西班牙文、德語和法語。 有關詳細資訊,請參閱[自動辨識和轉錄多語言內容](multi-language-identification-transcription.md)。
* 在人員與位置的命名實體

    通過自然語言處理 (NLP) 從語音和視覺文本中提取品牌、位置和人員。
* 編輯拍攝類型類別

    標記具有編輯類型(如特寫、中拍、兩次拍攝、室內、室外等)的拍攝。有關詳細資訊,請參閱[編輯拍攝類型偵測](scenes-shots-keyframes.md#editorial-shot-type-detection)。
* 主題推斷增強 - 現在涵蓋等級 2
    
    主題推斷模型現在支援 IPTC 分類的更深層粒度。 閱讀 Azure[媒體服務新 AI 支援的創新](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/)的完整詳細資訊。

## <a name="august-2019"></a>2019 年 8 月
 
### <a name="video-indexer-deployed-in-uk-south"></a>部署在英國南部的視訊索引器

您現在可以在英國南部區域創建視頻索引器付費帳戶。

### <a name="new-editorial-shot-type-insights-available"></a>提供新的編輯拍攝類型見解

添加到視頻拍攝的新標籤提供了編輯"拍攝類型",用於識別內容創建工作流中使用的常見編輯短語,例如:極端特寫、特寫、寬、中、兩次拍攝、室外、室內、左臉和右臉(在 JSON 中可用)。

### <a name="new-people-and-locations-entities-extraction-available"></a>提供新人員點和地點實體擷取

視頻索引器通過視頻的OCR和轉錄通過自然語言處理 (NLP) 標識命名位置和人員。 視頻索引器使用機器學習演算法來識別特定位置(例如埃菲爾鐵塔)或人員(例如,John Doe)在視頻中被叫出來。

### <a name="keyframes-extraction-in-native-resolution"></a>原生解析度的關鍵幀提取

視頻索引器提取的關鍵幀以視頻的原始解析度提供。
 
### <a name="ga-for-training-custom-face-models-from-images"></a>從影像中訓練自訂人臉模型的 GA

從從預覽模式移動到 GA 的圖像(可通過 API 和門戶提供)訓練人臉。

> [!NOTE]
> 與「預覽到 GA」過渡無關的定價影響。

### <a name="hide-gallery-toggle-option"></a>隱藏式庫切換選項

用戶可以選擇從門戶隱藏庫選項卡(類似於隱藏示例選項卡)。
 
### <a name="maximum-url-size-increased"></a>最大網址大小增加

支援對視頻索引的 URL 查詢字串 4096(而不是 2048)。
 
### <a name="support-for-multi-lingual-projects"></a>支援多語言項目

現在可以基於以不同語言(僅限 API)索引的視頻創建專案。

## <a name="july-2019"></a>2019 年 7 月

### <a name="editor-as-a-widget"></a>編輯器作為小元件

視頻索引器 AI 編輯器現在可作為小部件嵌入到客戶應用程式中。

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>從門戶從隱藏字幕檔更新自訂語言模型

客戶可以提供 VTT、SRT 和 TTML 檔案格式作為門戶自定義頁中語言模型的輸入。

## <a name="june-2019"></a>2019 年 6 月

### <a name="video-indexer-deployed-to-japan-east"></a>部署到日本東部的視訊索引器

您現在可以在日本東部區域創建視頻索引器付費帳戶。

### <a name="create-and-repair-account-api-preview"></a>建立與修復帳戶 API(預覽)

新增一個新的 API,讓您能夠[更新 Azure 媒體服務連線終結點或金鑰](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Paid-Account-Azure-Media-Services?&groupBy=tag)。

### <a name="improve-error-handling-on-upload"></a>改進上傳時的錯誤處理 

如果基礎 Azure 媒體服務帳戶配置錯誤,將返回描述性消息。

### <a name="player-timeline-keyframes-preview"></a>玩家時間線關鍵圖格預覽 

現在,您可以在播放器的時間線上看到每次的圖像預覽。

### <a name="editor-semi-select"></a>編輯器半選擇

現在,您可以在編輯器中查看選擇特定見解時間範圍后選擇的所有見解的預覽。

## <a name="may-2019"></a>2019 年 5 月

### <a name="update-custom-language-model-from-closed-caption-file"></a>從隱藏字幕檔更新自訂語言模型

[創建自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?&groupBy=tag)並[更新自訂語言模型](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Language-Model?&groupBy=tag)API 現在支援 VTT、SRT 和 TTML 檔案格式作為語言模型的輸入。

調用[更新視頻腳本 API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Update-Video-Transcript?&pattern=transcript)時,將自動添加腳本。 與視頻關聯的訓練模型也會自動更新。 有關如何自定義和訓練語言模型的資訊,請參閱[使用影片索引器自訂語言模型](customize-language-model-overview.md)。

### <a name="new-download-transcript-formats--txt-and-csv"></a>新的下載文稿格式 – TXT 與 CSV

除了已經支援的隱藏字幕格式(SRT、VTT 和 TTML),影片索引器現在支援以 TXT 和 CSV 格式下載腳稿。

## <a name="next-steps"></a>後續步驟

[概觀](video-indexer-overview.md)
