---
title: 使用動態封裝工具篩選您的資訊清單
titleSuffix: Azure Media Services
description: 瞭解如何使用動態封裝工具來建立篩選器，以篩選並選擇性地串流您的資訊清單。
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
ms.openlocfilehash: cb7a399258dcab679468d2b8f699487b1ec5406b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "84705197"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>使用動態封裝工具篩選您的資訊清單

當您將自動調整位元速率串流內容傳遞至裝置時，您有時需要發佈多個版本的資訊清單，以將特定裝置功能或可用的網路頻寬作為目標。 [動態](dynamic-packaging-overview.md)封裝程式可讓您指定篩選器，以便即時篩選出特定的編解碼器、解析度、位元速率和音訊播放軌組合。 此篩選不需要建立多個複本。 您只需要使用一組設定至目標裝置（iOS、Android、Smarttv 產品或瀏覽器）的特定篩選，以及網路功能（高頻寬、行動或低頻寬案例），來發行新的 URL。 在此情況下，用戶端可以透過查詢字串（藉由指定可用的[資產篩選器或帳戶篩選](filters-concept.md)器）來管理內容的串流，並使用篩選來串流處理資料流程的特定區段。

某些傳遞案例會要求您確保客戶無法存取特定的追蹤。 例如，您可能不想要將包含 HD 軌的資訊清單發行到特定的訂閱者層。 或者，您可能想要移除特定的自動調整位元速率（ABR）追蹤，以降低傳遞至特定裝置的成本，而不會受益于額外的追蹤。 在此情況下，您可以在建立時將預先建立的篩選清單與[串流定位器](streaming-locators-concept.md)建立關聯。 接著，用戶端無法操控內容的資料流程處理方式，因為它是由**串流定位器**所定義。

您可以透過在[串流定位器上指定篩選器](filters-concept.md#associating-filters-with-streaming-locator)，以及用戶端在 URL 中指定的其他裝置特定篩選器來結合篩選。 這種組合有助於限制額外的追蹤，例如中繼資料或事件資料流程、音訊語言或描述的音訊播放軌。

在您的串流上指定不同篩選器的這項功能，可提供功能強大的**動態資訊清單**操作解決方案，以目標裝置的多個使用案例。 本主題說明與**動態資訊清單**相關的概念，並提供您可以使用這項功能的案例範例。

> [!NOTE]
> 動態資訊清單不會變更資產和該資產的預設資訊清單。

## <a name="overview-of-manifests"></a>資訊清單總覽

Azure 媒體服務支援 HLS、MPEG 破折號和 Smooth Streaming 通訊協定。 作為[動態封裝](dynamic-packaging-overview.md)的一部分，串流用戶端資訊清單（HLS 主要播放清單、破折號媒體簡報描述 [MPD] 和 Smooth Streaming）是根據 URL 中的格式選取器動態產生的。 如需詳細資訊，請參閱[一般隨選工作流程](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery)中的傳遞通訊協定。

### <a name="get-and-examine-manifest-files"></a>取得並檢查資訊清單檔案

您可以根據您的串流追蹤（即時或隨選影片 [VOD]），指定要包含在動態建立的資訊清單中的篩選器追蹤屬性條件清單。 您必須先載入 Smooth Streaming 資訊清單，才能取得並檢查資料軌的屬性。

[使用 .Net 上傳、編碼和串流](stream-files-tutorial-with-api.md#get-streaming-urls)檔案教學課程會示範如何使用 .net 建立串流 url。 如果您執行應用程式，其中一個 URL 指向 Smooth Streaming 資訊清單：`https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest`。<br/> 複製該 URL 並將它貼上到瀏覽器的網址列中。 瀏覽器會下載該檔案。 您可以在任何文字編輯器中開啟它。

如需 REST 範例，請參閱[使用 Rest 上傳、編碼和串流](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls)檔案。

### <a name="monitor-the-bitrate-of-a-video-stream"></a>監視視訊資料流的位元速率

您可以使用 [Azure 媒體播放器示範頁面](https://aka.ms/azuremediaplayer) \(英文\) 來監視視訊資料流的位元速率。 [示範] 頁面會在 [**診斷**] 索引標籤上顯示診斷資訊：

![Azure 媒體播放器診斷][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>範例：查詢字串中含有篩選準則的 Url

您可以將篩選套用至 ABR 串流通訊協定： HLS、MPEG 和 Smooth Streaming。 下表顯示包含篩選器之 URL 的一些範例：

|通訊協定|範例|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>轉譯篩選

您可以選擇將資產編碼成多個編碼設定檔（h.264 基準、h.264 High、AACL、AACH、杜比數位 Plus）和多個品質位元速率。 不過，並非所有的用戶端裝置都支援您所有的資產和位元速率。 例如，較舊的 Android 裝置只支援 h.264 基準 + AACL。 將較高的位元速率傳送至無法取得優勢的裝置，會浪費頻寬和裝置計算。 這類裝置必須將所有指定的資訊解碼，才會將其相應縮小以供顯示。

使用動態資訊清單，您可以建立裝置設定檔（例如行動、主控台或 HD/SD），並包含您想要在每個設定檔中使用的曲目和品質。 這就是所謂的轉譯篩選。 下圖顯示它的範例。

![使用動態資訊清單進行格式轉譯篩選的範例][renditions2]

下列範例使用編碼器將夾層資產編碼成七個 ISO MP4 視訊轉譯 (從 180p 到 1080p)。 編碼的資產可以[動態地封裝](dynamic-packaging-overview.md)成下列任何一種串流通訊協定： HLS、MPEG 破折號和平滑。

下圖的頂端顯示沒有篩選之資產的 HLS 資訊清單。 （其中包含所有七個轉譯）。 在左下方，圖表會顯示名為 "ott" 的篩選器已套用的 HLS 資訊清單。 "Ott" 篩選器會指定移除所有位元速率低於 1 Mbps 的功能，因此會在回應中去除底部的兩個品質層級。 在右下方，圖表會顯示名為 "mobile" 的篩選器已套用的 HLS 資訊清單。 "Mobile" 篩選器會指定移除解析度大於720p 的轉譯，因此會去除兩個1080p 轉譯的格式。

![使用動態資訊清單進行格式轉譯篩選][renditions1]

## <a name="removing-language-tracks"></a>移除語言資料軌
您的資產可能包括多種音訊語言，例如英文、西班牙文、法文等等。 通常，Player SDK 會管理預設的音軌選取專案，以及每個使用者選取的可用音訊播放軌。

開發這類播放機的 Sdk 很有挑戰性，因為它需要跨裝置特定的播放框架進行不同的執行。 此外，在某些平臺上，播放機 Api 會受到限制，且不包含音訊選取功能，使用者無法選取或變更預設的音訊播放軌。透過資產篩選器，您可以藉由建立只包含所需音訊語言的篩選來控制行為。

![使用動態資訊清單篩選語言追蹤][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>修剪資產的開始

在大部分的即時資料流事件中，操作人員必須在實際的事件前先進行測試。 例如，在活動開始之前，它們可能會包含像這樣的一個候選項目：「程式會馬上開始。」

如果程式正在進行封存，則測試和靜態圖像資料也會一併封存並包含在簡報中。 不過，這種資訊不應該向用戶端顯示。 透過動態資訊清單，您可以建立開始時間篩選器，並從資訊清單中移除不必要的資料。

![使用動態資訊清單修剪資產的開始][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>從即時封存中建立子剪輯 (檢視)

許多即時事件的執行時間長，且即時封存可能包括多個事件。 即時事件結束後，廣播者可能想要將即時封存分解成邏輯程式的啟動和停止序列。

您可以個別發佈這些虛擬程式，而不需要對即時封存進行後續處理，也不會建立個別的資產（這不會獲得 Cdn 中現有快取片段的優點）。 例如足球或籃球比賽的球季、棒球賽局，或是任何體育節目的個別事件，都是此類虛擬程式的範例。

有了動態資訊清單，您就可以使用開始/結束時間建立篩選器，並在即時封存頂端建立虛擬視圖。

![使用動態資訊清單的子剪輯篩選][subclip_filter]

以下是已篩選的資產：

![使用動態資訊清單篩選的資產][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>調整簡報視窗（DVR）

目前，Azure 媒體服務提供迴圈封存，可在1分鐘-25 小時內設定持續時間。 資訊清單篩選可以在封存頂端建立循環 DVR 視窗封存，而不會刪除媒體。 在許多情況下，廣播者想要提供受限制的 DVR 時段以隨著即時邊緣移動，並同時保留更大的封存視窗。 廣播者可能會想要使用超出 DVR 視窗的資料來反白顯示剪輯，或可能想要為不同的裝置提供不同的 DVR 視窗。 例如，大多數的行動裝置不處理大型 DVR 視窗 (您可以讓行動裝置有 2 分鐘長的 DVR 視窗，而桌上型用戶端的有一小時長)。

![使用動態資訊清單的 DVR 視窗][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>調整 LiveBackoff (即時位置)

資訊清單篩選可用來移除即時程式其即時邊緣幾秒鐘的時間。 篩選可讓廣播者監看預覽發行端點上的簡報，並建立廣告插入點，檢視器才會收到資料流程（30秒後的備份）。 然後，廣播人員可以將這些廣告推播給他們的用戶端架構，以便在廣告機會之前接收和處理資訊。

除了通告支援以外，您還可以使用「即時輪詢」設定來調整檢視器的位置，讓用戶端漂移並觸及即時邊緣時，仍然可以從伺服器取得片段。 如此一來，用戶端就不會收到 HTTP 404 或412錯誤。

![使用動態資訊清單篩選即時輪詢][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>將多個規則結合成單一篩選器

您可以將多個篩選規則結合成單一篩選器。 例如，您可以定義「範圍規則」將平板從即時封存中移除，同時篩選出可用的位元速率。 當您套用多個篩選規則時，最後的結果會是所有規則的交集。

![使用動態資訊清單的多個篩選規則][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>結合多個篩選條件 (篩選器組合)

您也可以將多個篩選條件結合成單一 URL。 下列案例示範您可能會想結合篩選條件的理由：

1. 您必須針對行動裝置（例如 Android 或 iPad）篩選您的影片品質（以限制影片品質）。 若要移除不想要的品質，您將建立適用于裝置設定檔的帳戶篩選器。 您可以將帳戶篩選器用於相同媒體服務帳戶下的所有資產，而不需要任何進一步的關聯。
1. 您也想要修剪資產的開始和結束時間。 若要進行修剪，您將建立資產篩選器，並設定開始/結束時間。
1. 您想要結合這兩個篩選準則。 如果沒有組合，您就必須將品質篩選加入修剪篩選，這會使篩選器的使用變得更棘手。

若要合併篩選，請以分號分隔的格式，將篩選名稱設定為資訊清單/播放清單 URL。 假設您有一個名為*MyMobileDevice*的篩選準則，它會篩選品質，而且您有另一個名為*mystarttime 用*來設定特定的開始時間。 您可以結合最多三個篩選。

如需詳細資訊，請參閱[這篇部落格文章](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/)。

## <a name="considerations-and-limitations"></a>考量與限制

- **ForceEndTimestamp**、 **presentationWindowDuration**和**liveBackoffDuration**的值不應設定為 VOD 篩選器。 僅用於即時篩選案例。
- 動態資訊清單會在 GOP 邊界（主要畫面格）中運作，因此修剪具有 GOP 精確度。
- 您可以針對帳戶和資產篩選器使用相同的篩選器名稱。 資產篩選器的優先順序較高，將會覆寫帳戶篩選器。
- 如果您更新篩選器，串流端點最多可能需要2分鐘的時間來重新整理規則。 如果您使用篩選器來提供內容（而且您在 proxy 和 CDN 快取中快取內容），更新這些篩選可能會導致播放者失敗。 我們建議您在更新篩選器之後清除快取。 如果此選項不可行，請考慮使用不同的篩選準則。
- 客戶必須手動下載資訊清單，並剖析確切的開始時間戳和時間範圍。

    - 若要判斷資產中的追蹤屬性，請[取得並檢查資訊清單](#get-and-examine-manifest-files)檔案。
    - 用來設定資產篩選器時間戳記屬性的公式為： <br/>startTimestamp = &lt; 資訊清單中的開始時間 &gt;  +   &lt; 預期的篩選開始時間（以秒為單位） &gt; * 時間

## <a name="next-steps"></a>後續步驟

下列文章會示範如何以程式設計方式建立篩選：  

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
