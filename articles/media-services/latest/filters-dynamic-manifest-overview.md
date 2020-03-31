---
title: 使用動態打包器篩選清單
titleSuffix: Azure Media Services
description: 瞭解如何使用動態打包器創建篩選器來篩選和有選擇地資料流清單。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 07/11/2019
ms.author: juliako
ms.openlocfilehash: cd955f97a2f26543f799d95b7dc0b1de235333c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186221"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>使用動態打包器篩選清單

當您向設備提供自我調整位元速率流內容時，有時需要發佈清單的多個版本，以定位特定裝置功能或可用的網路頻寬。 [動態打包器](dynamic-packaging-overview.md)允許您指定篩選器，這些篩選器可以動態篩選出特定的編解碼器、解析度、位元速率和音軌組合。 此篩選消除了創建多個副本的需要。 您只需發佈一個新 URL，並針對目標設備（iOS、Android、SmartTV 或瀏覽器）和網路功能（高頻寬、移動或低頻寬方案）配置了一組特定的篩選器。 在這種情況下，用戶端可以通過查詢字串操作內容流（通過指定可用的[資產篩選器或帳戶篩選器](filters-concept.md)），並使用篩選器資料流流的特定部分。

某些交付方案要求您確保客戶無法訪問特定軌道。 例如，您可能不希望將包含 HD 軌道的清單發佈到特定訂閱者層。 或者，您可能希望刪除特定的自我調整位元速率 （ABR） 軌道，以降低交付特定設備的成本，而該設備不會從其他軌道中受益。 在這種情況下，您可以在創建時將預先創建的篩選器清單與[流式處理器](streaming-locators-concept.md)相關聯。 然後，用戶端無法操作內容的資料流方式，因為它由**流式處理器**定義。

您可以通過[在流式處理器上指定篩選器](filters-concept.md#associating-filters-with-streaming-locator)來組合篩選 - 用戶端在 URL 中指定的其他特定于設備的篩選器。 此組合可用於限制其他軌道，如中繼資料或事件流、音訊語言或描述性音訊軌道。

在流上指定不同篩選器的這種功能提供了強大的**動態清單**操作解決方案，用於針對目標設備的多個用例方案。 本主題介紹與**動態清單**相關的概念，並舉例說明了可以在其中使用此功能的方案。

> [!NOTE]
> 動態清單不會更改該資產的資產和預設清單。

## <a name="overview-of-manifests"></a>清單概述

Azure 媒體服務支援 HLS、MPEG DASH 和平滑流式處理協定。 作為[動態打包](dynamic-packaging-overview.md)的一部分，流式處理用戶端清單（HLS 主播放清單、DASH 媒體演示說明 [MPD] 和平滑流）根據 URL 中的格式選擇器動態生成。 有關詳細資訊，請參閱[公共按需工作流](dynamic-packaging-overview.md#delivery-protocols)中的傳遞協定。

### <a name="get-and-examine-manifest-files"></a>取得並檢查資訊清單檔案

您可以根據動態創建的清單中應包含流的跟蹤（即時或視頻點播 [VOD]）來指定篩選器跟蹤屬性條件的清單。 您必須先載入 Smooth Streaming 資訊清單，才能取得並檢查資料軌的屬性。

使用[.NET 教程的上載、編碼和流檔](stream-files-tutorial-with-api.md#get-streaming-urls)演示如何使用 .NET 構建流式處理 URL。 如果您執行應用程式，其中一個 URL 指向 Smooth Streaming 資訊清單：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 複製該 URL 並將它貼上到瀏覽器的網址列中。 瀏覽器會下載該檔案。 您可以在任何文字編輯器中打開它。

有關 REST 示例，請參閱[使用 REST 上載、編碼和資料流檔](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>監視視訊資料流的位元速率

您可以使用 [Azure 媒體播放器示範頁面](https://aka.ms/azuremediaplayer) \(英文\) 來監視視訊資料流的位元速率。 演示頁面在 **"診斷"** 選項卡上顯示診斷資訊：

![Azure 媒體播放器診斷][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>示例：查詢字串中具有篩選器的 URL

您可以將篩選器應用於 ABR 流協定：HLS、MPEG-DASH 和平滑流式處理。 下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>轉譯篩選

您可以選擇將資產編碼為多個編碼設定檔（H.264 基線、H.264 高、AACL、AACH、杜比數位加數）和多個品質位元速率。 不過，並非所有的用戶端裝置都支援您所有的資產和位元速率。 例如，較舊的 Android 設備僅支援 H.264 基線+AACL。 向無法獲得好處的設備發送更高的位元速率會浪費頻寬和設備計算。 此類設備必須解碼所有給定的資訊，才能將其縮小以進行顯示。

使用動態清單，您可以創建設備設定檔（如移動、主控台或 HD/SD），並包括您希望成為每個設定檔一部分的軌道和品質。 這稱為格式副本篩選。 下圖顯示了它的示例。

![使用動態清單進行格式副本篩選的示例][renditions2]

下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼的資產可以[動態打包](dynamic-packaging-overview.md)到以下任何流協定：HLS、MPEG DASH 和平滑。

下圖的頂部顯示了沒有篩選器的資產的 HLS 清單。 （它包含所有七個格式副本。 在左下角，該圖顯示了一個 HLS 清單，其中應用了名為"ott"的篩選器。 "ott"篩選器指定刪除低於 1 Mbps 的所有位元速率，因此在回應中剝離了最底層的兩個品質級別。 在右下角，該圖顯示了對名為"移動"的篩選器應用的 HLS 清單。 "移動"篩選器指定刪除解析度大於 720p 的格式副本，因此兩個 1080p 格式副本被剝離。

![具有動態清單的格式篩選][renditions1]

## <a name="removing-language-tracks"></a>移除語言資料軌
您的資產可能包括多種語言，如英語、西班牙文、法語等。 通常，播放機 SDK 管理預設音軌選擇和每個使用者選擇的可用音軌。

開發此類播放機 SDK 具有挑戰性，因為它需要跨特定于設備的播放機框架實現不同的實現。 此外，在某些平臺上，Player API 是有限的，不包括使用者無法選擇或更改預設音軌的音訊選擇功能。使用資產篩選器，您可以通過創建僅包含所需音訊語言的篩選器來控制行為。

![使用動態清單篩選語言軌道][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪資產的開頭

在大部分的即時資料流事件中，操作人員必須在實際的事件前先進行測試。 例如，在活動開始之前，它們可能包含這樣的石板："程式將暫時啟動。

如果程式正在進行封存，則測試和靜態圖像資料也會一併封存並包含在簡報中。 但是，此資訊不應顯示給用戶端。 透過動態資訊清單，您可以建立開始時間篩選器，並從資訊清單中移除不必要的資料。

![使用動態清單修剪資產的開始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>從即時封存中建立子剪輯 (檢視)

許多即時事件的執行時間長，且即時封存可能包括多個事件。 即時事件結束後，廣播者可能想要將即時封存分解成邏輯程式的啟動和停止序列。

您可以單獨發佈這些虛擬程式，而無需對即時存檔進行後期處理，也不會創建單獨的資產（這不能從 CDN 中現有的緩存片段中獲益）。 例如足球或籃球比賽的球季、棒球賽局，或是任何體育節目的個別事件，都是此類虛擬程式的範例。

使用動態清單，您可以使用開始/結束時間創建篩選器，並在即時存檔的頂部創建虛擬視圖。

![帶動態清單的子剪輯篩選器][subclip_filter]

下面是篩選的資產：

![使用動態清單篩選資產][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>調整演示視窗 （DVR）

目前，Azure 媒體服務提供迴圈存檔，其中持續時間可配置為 1 分鐘 - 25 小時。 資訊清單篩選可以在封存頂端建立循環 DVR 視窗封存，而不會刪除媒體。 在許多情況下，廣播者想要提供受限制的 DVR 時段以隨著即時邊緣移動，並同時保留更大的封存視窗。 廣播公司可能希望使用 DVR 視窗外的資料來突出顯示剪輯，或者他們可能希望為不同的設備提供不同的 DVR 視窗。 例如，大多數的行動裝置不處理大型 DVR 視窗 (您可以讓行動裝置有 2 分鐘長的 DVR 視窗，而桌上型用戶端的有一小時長)。

![帶動態清單的 DVR 視窗][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff (即時位置)

資訊清單篩選可用來移除即時程式其即時邊緣幾秒鐘的時間。 篩選允許廣播機構在預覽發佈點上觀看演示文稿，並在觀看者收到流之前創建廣告插入點（30 秒退去）。 然後，廣播公司可以將這些廣告推送到其用戶端框架，以便他們在廣告機會之前接收和處理資訊。

除了廣告支援之外，即時後退設置還可用於調整查看者的位置，以便當用戶端漂移並命中即時邊緣時，他們仍然可以從伺服器獲取片段。 這樣，用戶端就不會收到 HTTP 404 或 412 錯誤。

![使用動態清單進行即時回退的篩檢程式][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>將多個規則結合成單一篩選器

您可以將多個篩選規則結合成單一篩選器。 例如，您可以定義"範圍規則"，以便從即時存檔中刪除石板，還可以篩選出可用的位元速率。 應用多個篩選規則時，最終結果是所有規則的交集。

![具有動態清單的多個篩選規則][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>結合多個篩選條件 (篩選器組合)

您也可以將多個篩選條件結合成單一 URL。 下列案例示範您可能會想結合篩選條件的理由：

1. 您需要針對行動裝置（如 Android 或 iPad）過濾視頻品質（以限制視頻品質）。 要刪除不需要的品質，您將創建一個適合設備設定檔的帳戶篩選器。 您可以在同一媒體服務帳戶下對所有資產使用帳戶篩選器，而無需任何進一步關聯。
1. 您也想要修剪資產的開始和結束時間。 要進行修剪，您將創建一個資產篩選器並設置開始/結束時間。
1. 您希望合併這兩個篩選器。 如果沒有組合，則需要向修剪篩選器添加品質篩選，這將使篩選器的使用更加困難。

要合併篩選器，請將篩選器名稱設置為分號分隔格式的清單/播放清單 URL。 假設您有一個名為*MyMobileDevice*的篩選器來篩選品質，並且您有另一個名為*MyStartTime*來設置特定的開始時間。 您可以結合最多三個篩選。

有關詳細資訊，請參閱[此博客文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>考量與限制

- 不應為 VOD 篩選器設置**forceEndTimestamp、****表示視窗持續時間**和**即時 Backoff 持續時間的值**。 它們僅用於即時篩選器方案。
- 動態清單在 GOP 邊界（關鍵幀）中運行，因此修剪具有 GOP 準確性。
- 您可以對帳戶和資產篩選器使用相同的篩選器名稱。 資產篩選器具有更高的優先順序，並將覆蓋帳戶篩選器。
- 如果更新篩選器，流式處理終結點最多可能需要 2 分鐘才能刷新規則。 如果使用篩選器為內容提供服務（並將內容緩存在代理和 CDN 緩存中），則更新這些篩選器可能會導致播放機失敗。 我們建議您在更新篩選器後清除緩存。 如果無法使用此選項，請考慮使用其他篩選器。
- 客戶需要手動下載清單並分析確切的開始時間戳和時間刻度。

    - 要確定資產中軌道的屬性，[請獲取並檢查清單檔](#get-and-examine-manifest-files)。
    - 設置資產篩選器時間戳記屬性的公式是： <br/>開始時間戳&lt;= 清單&gt; +  &lt;預期篩選器開始時間（以秒&gt;為單位）和時間刻度

## <a name="next-steps"></a>後續步驟

以下文章演示如何以程式設計方式創建篩選器：  

- [使用 REST API 建立篩選器](filters-dynamic-manifest-rest-howto.md)
- [使用 .NET 建立篩選器](filters-dynamic-manifest-dotnet-howto.md)
- [使用 CLI 建立篩選器](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
