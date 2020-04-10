---
title: Azure 媒體服務 v3 中的即時事件和即時輸出概念
titleSuffix: Azure Media Services
description: 本主題概述了 Azure 媒體服務 v3 中的即時事件和即時輸出。
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
ms.date: 04/08/2020
ms.author: juliako
ms.openlocfilehash: 8eca95f9fca47fca4d54bacbab35f3a0ffc3ba31
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010574"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>媒體服務中的即時事件與即時輸出

Azure 媒體服務允許您在 Azure 雲端上向客戶傳遞即時事件。 要在媒體服務 v3 中設定即時流事件,您需要瞭解本文中討論的概念。

> [!TIP]
> 對於從媒體服務 v2 API 移轉的客戶,**即時事件**實體會取代 v2 中的**頻道**,**而即時輸出**將取代**程式**。

## <a name="live-events"></a>即時活動

[實況活動](https://docs.microsoft.com/rest/api/media/liveevents)負責內嵌和處理即時視訊摘要。 創建即時事件時,將創建一個主輸入終結點,可用於從遠端編碼器發送即時信號。 遠端即時編碼器使用[RTMP](https://www.adobe.com/devnet/rtmp.html)或[平滑流(](https://msdn.microsoft.com/library/ff469518.aspx)碎片-MP4)輸入協定將饋送發送到該輸入終結點。 對於 RTMP 引入協議,內容可以以`rtmp://`清除 ( ) 傳送或`rtmps://`安全地加密在電線上 ( )。 針對 Smooth Streaming 內嵌通訊協定，支援的 URL 配置為 `http://` 或 `https://`。  

## <a name="live-event-types"></a>實況活動類型

[即時事件](https://docs.microsoft.com/rest/api/media/liveevents)可以設置為*傳遞*(本地即時編碼器發送多個比特率流)或*即時編碼*(本地即時編碼器發送單個比特率流)。 類型在建立期間使用[LiveEvent 編碼類型](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype)設定 :

* **LiveEvent編碼類型.無**:本地即時編碼器發送多個比特率流。 引入的流通過即時事件,無需任何進一步處理。 也稱為直通模式。
* **LiveEvent編碼類型.標準**:本地即時編碼器向即時事件發送單個比特率流,媒體服務創建多個比特率流。 如果貢獻源的解析度為 720p 或更高,**則 Default720p**預設將對一組 6 個解析度/比特率對進行編碼。
* **LiveEventEncodeType.Premium1080p**:本地即時編碼器向即時事件發送單個比特率流,媒體服務創建多個比特率流。 Default1080p 預設指定解析度/比特率對的輸出集。

### <a name="pass-through"></a>傳遞

![透過媒體服務傳遞即時事件範例圖](./media/live-streaming/pass-through.svg)

使用傳遞**實況活動**時，您會依賴內部部署即時編碼器來產生多重位元速率視訊資料流，然後將其當作發佈摘要傳送給「實況活動」(使用 RTMP 或分散式 MP4 通訊協定)。 「實況活動」會接著完成傳入的視訊資料流，而不會再進一步處理。 這種傳遞即時事件針對長時間運行的即時事件或 24x365 線性即時流進行了優化。 建立這類型的「實況活動」時，請指定 [無] (LiveEventEncodingType.None)。

您可以使用 H.264/AVC 或 H.265/HEVC 視訊轉碼器以及 AAC (AAC-LC、HE-AACv1 或 HE-AACv2) 音訊轉碼器，以高達 4K 的解析度和每秒 60 個畫面的畫面播放速率傳送發佈摘要。 有關詳細資訊,請參閱[即時事件類型比較](live-event-types-comparison.md)。

> [!NOTE]
> 在長時間執行多個事件且已投資於本地編碼器時,使用直通方法是執行即時流式處理的最經濟的方法。 請參閱 [價格](https://azure.microsoft.com/pricing/details/media-services/) 詳細資料。
>

請參閱 [MediaV3LiveApp](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/blob/master/NETCore/Live/MediaV3LiveApp/Program.cs#L126).中的 .NET 程式碼範例。

### <a name="live-encoding"></a>即時編碼  

![使用媒體服務範例圖進行即時編碼](./media/live-streaming/live-encoding.svg)

將即時編碼與媒體服務一起使用時,將本地即時編碼器配置為向即時事件發送單個比特率視頻作為貢獻源(使用 RTMP 或碎片-Mp4 協定)。 然後設置即時事件,以便它將傳入的單比特率流編碼到[多個比特率視頻流](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming),並使輸出可用於通過 MPEG-DASH、HLS 和平滑流等協定播放設備。

使用即時編碼時,您只能以高達 1080p 解析度的解析度以 30 幀/秒的幀速率發送饋送,使用 H.264/AVC 視頻編解碼器和 AAC(AAC-LC、HE-AACv1 或 HE-ACv2)音訊編解碼器。 請注意,傳遞即時事件可在 60 幀/秒時支持高達 4K 的解析度。 有關詳細資訊,請參閱[即時事件類型比較](live-event-types-comparison.md)。

即時編碼器輸出中包含的解析度和比特率由預設確定。 如果使用**標準**即時編碼器(LiveEventEncodeType.Standard),則*Default720p*預設指定一組六個解析度/比特速率對,從 3.5 Mbps 的 720p 下降到 200 kbp 的 192p。 否則,如果使用高級**1080p**即時編碼器(LiveEventEncodeTypeType.Premium1080p),則*預設 1080p*預設指定一組六個解析度/比特速率對,從 3.5 Mbps 的 1080p 下降到 200 kbp 的 180p。 如需相關資訊，請參閱[系統預設值](live-event-types-comparison.md#system-presets)。

> [!NOTE]
> 如果需要自定義即時編碼預設,請通過 Azure 門戶打開支援票證。 指定所需的解析度和比特率表。 驗證 720p 處只有一個圖層(如果為標準即時編碼器請求預設)或 1080p(如果請求高級 1080p 實時編碼器的預設),最多 6 層。

## <a name="creating-live-events"></a>建立即時活動

### <a name="options"></a>選項。

建立「實況活動」時，您可以指定下列選項：

* 「實況活動」的串流通訊協定 (目前支援 RTMP 和 Smooth Streaming 通訊協定)。<br/>當「即時事件」或其相關「即時輸出」正在執行時，您無法變更通訊協定選項。 如果需要不同的協定,請為每個流式處理協議創建單獨的即時事件。  
* 在建立事件時，您可以指定要自動啟動它。 <br/>當自動啟動設為 true 時，即時事件將會在建立後隨即啟動。 只要即時事件一執行，就會立即開始計費。 您必須對「實況活動」資源明確呼叫「停止」，才能終止進一步計費。 或者,您可以在準備好開始流式傳輸時啟動事件。

    如需詳細資訊，請參閱[實況活動狀態和計費](live-event-states-billing.md)。

* 內嵌和預覽的 IP 限制。 您可以定義獲允許將視訊內嵌到這個「實況活動」的 IP 位址。 允許的 IP 位址可以指定為單一 IP 位址 (例如 ‘10.0.0.1’)、使用 IP 位址和 CIDR 子網路遮罩的 IP 範圍 (例如 ‘10.0.0.1/22’)，或是使用 IP 位址和小數點十進位子網路遮罩的 IP 範圍 (例如 '10.0.0.1(255.255.252.0)')。<br/>如果未指定 IP 位址而且也未定義規則，則任何 IP 位址都不允許。 若要允許任何 IP 位址，請建立規則，並設定 0.0.0.0/0。<br/>IP 位址必須採用以下格式之一:IpV4 位址,包含四個數位或 CIDR 位址範圍。

    如果要在自己的防火牆上啟用某些 IP,或者希望將即時事件的輸入約束到 Azure IP 位址,請從[Azure 數據中心 IP 位址範圍](https://www.microsoft.com/download/details.aspx?id=41653)下載 JSON 檔。 有關此檔的詳細資訊,請選擇頁面上的 **「詳細資訊**」部分。
    
* 建立事件時,您可以選擇打開即時轉錄。 <br/> 默認情況下,即時轉錄被禁用。 在「即時事件」或其關聯的即時輸出執行時,無法更改此屬性。 

### <a name="naming-rules"></a>命名規則

* 最大即時事件名稱為 32 個字元。
* 名稱應遵循此[regex](https://docs.microsoft.com/dotnet/standard/base-types/regular-expression-language-quick-reference)`^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$`模式: 。

另請參閱[串流式處理終結點命名約定](streaming-endpoint-concept.md#naming-convention)。

> [!TIP]
> 為了保證即時事件名稱的唯一性,可以生成 GUID,然後刪除所有連字元和捲曲括弧(如果有)。 該字串在所有即時事件中是唯一的,並且其長度保證為 32。

## <a name="live-event-ingest-urls"></a>實況活動內嵌 URL

建立即時事件後,您可以獲取要提供給即時本地編碼器的 URL。 即時編碼器會使用這些 URL 來輸入即時資料流。 如需詳細資訊，請參閱[建議的內部部署即時編碼器](recommended-on-premises-live-encoders.md)。

您可以使用非虛名 URL 或虛名 URL。

> [!NOTE] 
> 針對要預測的內嵌 URL，設定「虛名」模式。

* 非虛名 URL

    非虛榮 URL 是媒體服務 v3 中的預設模式。 您可能會快速取得「實況活動」，但只有在實況活動開始時，才會知道內嵌 URL。 如果您停止/開始「實況活動」，URL 將會變更。 <br/>當最終使用者希望使用應用希望儘快獲取即時事件且具有動態引入 URL 不是問題的應用時,非 Vanity 非常有用。

    如果用戶端應用在創建即時事件之前不需要預先生成引入 URL,請讓媒體服務自動生成即時事件的訪問權杖。

* 虛名 URL

    虛榮模式是使用硬體廣播編碼器且不想在啟動即時事件時重新配置其編碼器的大型媒體廣播公司的首選。 這些廣播公司想要一個預測性引入 URL,不會隨時間而變化。
    
    > [!NOTE]
    > 在 Azure 門戶中,虛榮人 URL 名為「*持久輸入 URL」。。*

    在 API 中指定`vanityUrl`此`true`模式, 請在建立時`false`設定為 (預設值為 )。 您必須在建立時傳遞自己的存取權`LiveEventInput.accessToken`杖 ( ) 。 指定權杖值以避免 URL 中的隨機權杖。 訪問權杖必須是有效的GUID字串(有或沒有連字元)。 設置模式后,無法更新該模式。

    訪問權杖在資料中心中需要是唯一的。 如果應用需要使用虛榮名 URL,建議始終為訪問權杖創建新的 GUID 實例(而不是重用任何現有的 GUID)。

    使用以下 API 啟用 Vanity URL 並將訪問權杖設定為有效的`"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`GUID(例如。  

    |Language|開啟虛榮名網址|設定存取權杖|
    |---|---|---|
    |REST|[屬性.vanityUrl](https://docs.microsoft.com/rest/api/media/liveevents/create#liveevent)|[即時事件輸入存取權杖](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--虛榮](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#az-ams-live-event-create)|[--訪問權杖](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest#optional-parameters)|
    |.NET|[現場活動.VanityUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveevent.vanityurl?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_VanityUrl)|[即時事件輸入存取權杖](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|
    
### <a name="live-ingest-url-naming-rules"></a>即時內嵌 URL 命名規則

* 下方的 *random* \(隨機\) 字串是 128 位元的十六進位數字 (由 32 個字元的 0-9 a-f 所組成)。
* *存取權杖*:使用虛榮模式時設定的有效 GUID 字串。 例如： `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` 。
* *流名稱*:指示特定連接的流名稱。 流名稱值通常由您使用的即時編碼器添加。 您可以將即時編碼器配置為使用任何名稱來描述連接,例如:「video1_audio1」、「video2_audio1」、「流」。。

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

即時事件開始接收投稿源後,您可以使用其預覽終結點在進一步發佈之前預覽和驗證您是否接收了即時流。 檢查預覽流是否良好後,可以使用即時事件使即時流可用於通過一個或多個(預先創建)流式處理終結點傳遞。 此選項,請在即時事件上建立新[的即時輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)。

> [!IMPORTANT]
> 請先確定視訊流向預覽 URL，再繼續操作！

## <a name="live-event-long-running-operations"></a>即時事件長時間執行的作業

有關詳細資訊,請參閱[長時間執行的操作](media-services-apis-overview.md#long-running-operations)。

## <a name="live-outputs"></a>即時輸出

讓資料流流入「實況活動」之後，您即可建立[資產](https://docs.microsoft.com/rest/api/media/assets)、[實況輸出](https://docs.microsoft.com/rest/api/media/liveoutputs)及[串流定位器](https://docs.microsoft.com/rest/api/media/streaminglocators)來開始串流事件。 「即時事件」會封存資料流，並透過[串流端點](https://docs.microsoft.com/rest/api/media/streamingendpoints)將它提供給檢視者。  

有關即時輸出的詳細資訊,請參閱[使用雲 DVR](live-event-cloud-dvr.md)。

## <a name="frequently-asked-questions"></a>常見問題集

請參閱[常見問題](frequently-asked-questions.md#live-streaming)文章。

## <a name="ask-questions-and-get-updates"></a>提出問題並取得更新

請參閱 [Azure 媒體服務社群](media-services-community.md)文章，以了解詢問問題、提供意見反應及取得媒體服務相關更新的不同方式。

## <a name="next-steps"></a>後續步驟

[即時串流教學課程](stream-live-tutorial-with-api.md)
