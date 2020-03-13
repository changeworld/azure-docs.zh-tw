---
title: Azure 媒體服務 v3 中的實況活動和即時輸出概念
titleSuffix: Azure Media Services
description: 本主題概要說明 Azure 媒體服務 v3 中的即時事件和即時輸出。
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
ms.date: 09/30/2019
ms.author: juliako
ms.openlocfilehash: 96b3e602011f4d3f237f29ce9b2fcad8bd0b8300
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251384"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>媒體服務中的即時事件和即時輸出

Azure 媒體服務可讓您在 Azure 雲端上將實況活動傳遞給客戶。 若要在媒體服務 v3 中設定即時串流事件，您必須瞭解這篇文章中所討論的概念。

> [!TIP]
> 針對從媒體服務 v2 Api 進行遷移的客戶，**即時事件**實體會取代 v2 中的**通道**，而**即時輸出**則會取代**程式**。

## <a name="live-events"></a>即時活動

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)負責內嵌和處理即時視訊摘要。 當您建立即時事件時，系統會建立主要和次要輸入端點，讓您用來從遠端編碼器傳送即時信號。 遠端即時編碼器會使用[RTMP](https://www.adobe.com/devnet/rtmp.html)或[Smooth Streaming](https://msdn.microsoft.com/library/ff469518.aspx) （分散式）輸入通訊協定，將發佈摘要傳送至該輸入端點。 對於 RTMP 內嵌通訊協定，內容可以用純文字（`rtmp://`）傳送，或在網路上安全地加密（`rtmps://`）。 針對 Smooth Streaming 內嵌通訊協定，支援的 URL 配置為 `http://` 或 `https://`。  

## <a name="live-event-types"></a>實況活動類型

[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)可以設定為傳遞（內部部署即時編碼器會傳送多位元率串流）或*即時編碼*（內部部署即時編碼器會*傳送*單一位元速率串流）。 在建立期間，會使用[LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)來設定類型：

* **LiveEventEncodingType。 None**：內部部署即時編碼器會傳送多位元率串流。 內嵌資料流程會通過即時事件，而不需要進行任何進一步的處理。 也稱為「傳遞」模式。
* **LiveEventEncodingType**：內部部署即時編碼器會將單一位元速率串流傳送至即時事件，媒體服務會建立多位元率串流。 如果「貢獻摘要」是720p 或更高的解析度， **Default720p**預設值將會編碼一組6個解析/位元速率配對。
* **LiveEventEncodingType. Premium1080p**：內部部署即時編碼器會將單一位元速率串流傳送至即時事件，媒體服務會建立多位元率串流。 Default1080p 預設值指定解析/位元速率配對的輸出集。

### <a name="pass-through"></a>傳遞

![以媒體服務範例圖表傳遞即時事件](./media/live-streaming/pass-through.svg)

使用傳遞**實況活動**時，您會依賴內部部署即時編碼器來產生多重位元速率視訊資料流，然後將其當作發佈摘要傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會接著完成傳入的視訊資料流，而不會再進一步處理。 這類傳遞即時事件已針對長時間執行的即時事件或24x365 線性即時串流進行優化。 建立這類型的「實況活動」時，請指定 [無] (LiveEventEncodingType.None)。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。 如需詳細資訊，請參閱[即時事件種類比較](live-event-types-comparison.md)。

> [!NOTE]
> 當您長時間執行多個事件，且您已投資內部部署編碼器時，使用傳遞方法是進行即時串流的最經濟實惠方式。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
>

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).中的 .NET 程式碼範例。

### <a name="live-encoding"></a>即時編碼  

![使用媒體服務範例圖表進行即時編碼](./media/live-streaming/live-encoding.svg)

搭配媒體服務使用即時編碼時，您會設定內部部署即時編碼器將單一位元速率的影片當做發佈摘要傳送至即時事件（使用 RTMP 或分散的通訊協定）。 接著，您可以設定即時事件，讓它將該傳入的單一位元速率串流編碼為[多位元率影片串流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)，並讓輸出可供傳遞，以透過 MPEG 破折號、HLS 和 Smooth Streaming 等通訊協定播放裝置。

當您使用即時編碼時，您只能以每秒30個畫面的畫面播放速率（使用 h.264/AVC video 編解碼器和 AAC （AAC-LC、他 He-aacv1 或他-He-aacv2）音訊編解碼器），傳送貢獻摘要，最高可達1080p 解析度。 請注意，傳遞即時事件可以支援每秒60個畫面格的解析度上限為4K。 如需詳細資訊，請參閱[即時事件種類比較](live-event-types-comparison.md)。

即時編碼器的輸出中所包含的解析度和位元速率是由預設值所決定。 如果使用**標準**即時編碼器（LiveEventEncodingType），則*Default720p*預設值會指定一組六個解析/位元速率配對，從 3.5 Mbps 到192p 的 200 kbps。 否則，如果使用**Premium1080p**即時編碼器（LiveEventEncodingType. Premium1080p），則*Default1080p*預設值會指定一組六個解析/位元速率配對，從 3.5 Mbps 到180p （200 kbps）。 如需相關資訊，請參閱[系統預設值](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果您需要自訂即時編碼預設值，請透過 Azure 入口網站開啟支援票證。 指定所需的解析和位元速率表。 確認只有一個圖層位於720p （如果要求標準即時編碼器的預設值）或1080p （如果要求 Premium1080p 即時編碼器的預設值），以及最多6個層級。

## <a name="creating-live-events"></a>建立實況活動

### <a name="options"></a>選項。

建立「實況活動」時，您可以指定下列選項：

* 「實況活動」的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)。<br/>當「即時事件」或其相關「即時輸出」正在執行時，您無法變更通訊協定選項。 如果您需要不同的通訊協定，請為每個串流通訊協定建立個別的實況活動。  
* 在建立事件時，您可以指定要自動啟動它。 <br/>當自動啟動設為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 或者，您可以在準備好要開始串流處理時啟動事件。

    如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。

* 內嵌和預覽的 IP 限制。 您可以定義獲允許將視訊內嵌到這個「實況活動」的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。<br/>如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須採用下列其中一種格式：具有四個數字或 CIDR 位址範圍的 IpV4 位址。

    如果您想要在自己的防火牆上啟用特定 Ip，或想要將即時事件的輸入限制為 Azure IP 位址，請從[Azure 資料中心 IP 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653)下載 JSON 檔案。 如需此檔案的詳細資訊，請選取頁面上的 [**詳細資料**] 區段。
    
* 建立事件時，您可以選擇開啟即時轉譯。 <br/> 根據預設，會停用即時轉譯。 當即時事件或其相關聯的即時輸出正在執行時，您無法變更此屬性。 

### <a name="naming-rules"></a>命名規則

* Live event name 的最大值為32個字元。
* 名稱應遵循此[RegEx](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)模式： `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`。

另請參閱[串流端點命名慣例](streaming-endpoint-concept.md#naming-convention)。

> [!TIP]
> 為確保您的即時事件名稱是唯一的，您可以產生 GUID，然後移除所有的連字號和大括弧（如果有的話）。 此字串在所有即時事件中都是唯一的，而且其長度保證為32。

## <a name="live-event-ingest-urls"></a>實況活動內嵌 URL

一旦建立即時事件之後，您就可以取得將提供給即時內部部署編碼器的內嵌 Url。 即時編碼器會使用這些 URL 來輸入即時資料流。 如需詳細資訊，請參閱[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)。

您可以使用非虛名 URL 或虛名 URL。

> [!NOTE] 
> 針對要預測的內嵌 URL，設定「虛名」模式。

* 非虛名 URL

    [非虛名 URL] 是媒體服務 v3 中的預設模式。 您可能會快速取得「實況活動」，但只有在實況活動開始時，才會知道內嵌 URL。 如果您停止/開始「實況活動」，URL 將會變更。 <br/>當使用者想要使用應用程式進行串流，而應用程式想要儘快取得即時事件，且動態內嵌 URL 不是問題時，非虛名就很有用。

    如果用戶端應用程式不需要在即時事件建立之前預先產生內嵌 URL，讓媒體服務自動產生即時事件的存取權杖。

* 虛名 URL

    使用硬體廣播編碼器的大型媒體廣播者慣用虛名模式，而不想要在啟動實況活動時重新設定其編碼器。 他們想要預測性內嵌 URL，這不會隨著時間而改變。

    若要指定此模式，您可以將 `vanityUrl` 設定為在建立時 `true` （預設值為 `false`）。 您也必須在建立時，傳遞您自己的存取權杖（`LiveEventInput.accessToken`）。 您可以指定權杖值，以避免在 URL 中使用隨機 token。 存取權杖必須是有效的 GUID 字串（包含或不含連字號）。 一旦設定模式，就無法更新。

    存取權杖在您的資料中心內必須是唯一的。 如果您的應用程式需要使用虛名 URL，建議一律為您的存取權杖建立新的 GUID 實例（而不是重複使用任何現有的 GUID）。

    使用下列 Api 來啟用虛名 URL，並將存取權杖設定為有效的 GUID （例如 `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`）。  

    |Language|啟用虛名 URL|設定存取權杖|
    |---|---|---|
    |REST|[屬性。 vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[LiveEventInput. accessToken](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--虛名-url](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--存取權杖](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[LiveEvent. VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[LiveEventInput. AccessToken](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>即時內嵌 URL 命名規則

* 下方的 *random* \(隨機\) 字串是 128 位元的十六進位數字 (由 32 個字元的 0-9 a-f 所組成)。
* *您的存取權杖*：您在使用虛名模式時所設定的有效 GUID 字串。 例如： `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 。
* *資料流程名稱*：表示特定連接的資料流程名稱。 資料流程名稱值通常是由您所使用的即時編碼器所新增。 您可以設定即時編碼器使用任何名稱來描述連線，例如：「video1_audio1」、「video2_audio1」、「串流」。

#### <a name="non-vanity-url"></a>非虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="vanity-url"></a>虛名 URL

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smooth Streaming

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>實況活動預覽 URL

當實況活動開始接收發布摘要之後，您就可以使用其預覽端點來預覽及驗證您是否收到即時串流，再進行進一步的發佈。 當您檢查過預覽資料流程是否良好之後，您可以使用即時事件，讓即時串流可透過一或多個（預先建立）的串流端點傳遞。 若要完成此動作，請在實況活動上建立新的[即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)。

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

## <a name="live-event-long-running-operations"></a>即時事件長時間執行的作業

如需詳細資訊，請參閱[長時間執行的作業](media-services-apis-overview.md#long-running-operations)。

## <a name="live-outputs"></a>即時輸出

讓資料流流入「實況活動」之後，您即可建立[資產](https://docs.microsoft.com/rest/api/media/assets)、[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)及[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)來開始串流事件。 「即時事件」會封存資料流，並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)將它提供給檢視者。  

如需有關即時輸出的詳細資訊，請參閱[使用雲端 DVR](live-event-cloud-dvr.md)。

## <a name="ask-questions-give-feedback-get-updates"></a>提出問題、提供意見反應、取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[即時串流教學課程](stream-live-tutorial-with-api.md)
