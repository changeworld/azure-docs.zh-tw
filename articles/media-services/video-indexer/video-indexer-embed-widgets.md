---
title: 在您的應用程式中內嵌影片索引子小工具
titleSuffix: Azure Media Services
description: 本文示範如何在您的應用程式中內嵌 Azure 媒體服務影片索引子小工具。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/03/2020
ms.author: juliako
ms.openlocfilehash: ed3e2cf9830e3776886e662fd27f43f76728d6b2
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988714"
---
# <a name="embed-video-indexer-widgets-in-your-applications"></a>在您的應用程式中內嵌影片索引子小工具

本文說明如何在您的應用程式中內嵌影片索引子小工具。 影片索引子支援在您的應用程式中嵌入三種類型的 widget：*認知深入*解析、*播放機*和*編輯器*。 

從第2版開始，小工具基底 URL 會包含指定帳戶的區域。 例如，美國西部區域中的帳戶會產生：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小工具類型

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

認知深入解析 widget 包含所有從您的影片編制索引程式中解壓縮的視覺深入解析。 認知深入解析小工具支援下列選擇性 URL 參數。

|名稱|定義|說明|
|---|---|---|
|`widgets`|以逗號分隔的字串|可讓您控制要呈現的見解。 <br/> 範例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search` 只轉譯人員和品牌 UI 深入解析。<br/>可用的選項：人員、關鍵字、註解、品牌、情緒、文字記錄、搜尋。<br/>請注意，在第2版中不支援 `widgets` URL 參數。<br/>|
|`locale`|簡短的語言代碼|控制 insights 語言。 預設值是 `en`。 <br/> 範例： `locale=de`.|
|`tab`|預設選取的索引標籤|控制預設呈現的 [**深入**解析] 索引標籤。 <br/> 範例： `tab=timeline` 呈現已選取 [**時間軸**] 索引標籤的見解。|

### <a name="player-widget"></a>播放器小工具

您可以使用 [播放程式] 小工具，透過調適型位元速率來串流影片。 播放工具 widget 支援下列選擇性 URL 參數。

|名稱|定義|說明|
|---|---|---|
|`t`|開始的秒數|讓玩家從指定的時間點開始播放。<br/> 範例： `t=60`.|
|`captions`|語言代碼|在 widget 載入時，以指定的語言提取標題，以供 [**標題**] 功能表使用。<br/> 範例： `captions=en-US`.|
|`showCaptions`|布林值|可讓播放器載入已啟用的標題。<br/> 範例： `showCaptions=true`.|
|`type`||啟動音訊播放機面板（影片部分已移除）。<br/> 範例： `type=audio`.|
|`autoplay`|布林值|表示播放者是否應該在載入時開始播放影片。 預設值是 `true`。<br/> 範例： `autoplay=false`.|
|`language`|語言代碼|控制播放機語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`.|

### <a name="editor-widget"></a>編輯器 widget

您可以使用編輯器 widget 來建立新的專案，以及管理影片的深入解析。 編輯器 widget 支援下列選擇性 URL 參數。

|名稱|定義|說明|
|---|---|---|
|`accessToken`<sup>*</sup>|String|提供只在用來內嵌 widget 之帳戶中的影片存取。<br> 編輯器 widget 需要 `accessToken` 參數。|
|`language`|語言代碼|控制播放機語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`.|
|`locale`|簡短的語言代碼|控制 insights 語言。 預設值是 `en`。<br/>範例： `language=de`.|

<sup>*</sup>擁有者應謹慎提供 `accessToken`。

## <a name="embedding-public-content"></a>內嵌公用內容

1. 登入[影片索引子](https://www.videoindexer.ai/)網站。
2. 選取您想要使用的影片。
3. 選取顯示在影片底下的 [**內嵌**] 按鈕。

    ![小工具](./media/video-indexer-embed-widgets/video-indexer-widget01.png)

    選取 [**內嵌**] 按鈕之後，您可以選取要在應用程式中內嵌的 widget。 
4. 選取您想要的 widget 類型（[**認知深入**解析]、[**播放**程式] 或 [**編輯器**]）。
 
5. 複製內嵌程式碼，然後將它新增至您的應用程式。 

    ![小工具](./media/video-indexer-embed-widgets/video-indexer-widget02.png)

> [!NOTE]
> 如果您在共用影片 Url 時發生問題，請將 `location` 參數新增至連結。 參數應該設定為[影片索引子所在的 Azure 區域](regions.md)。 例如： `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial` 。

## <a name="embedding-private-content"></a>內嵌私人內容

若要內嵌私用影片，您必須在 iframe 的**src**屬性中傳遞存取權杖：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
若要取得認知深入解析小工具內容，請使用下列其中一項：<br/>
- [取得深入解析 Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) API。<br/>
- [取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?)。 將它當做查詢參數新增至 URL。 將此 URL 指定為 iframe 的**src**值，如先前所示。

若要在內嵌 widget 中提供編輯見解功能，您必須傳遞包含編輯許可權的存取權杖。 使用 [[取得深入解析 Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ] 或 [使用 `&allowEdit=true`[取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?)]。 

## <a name="widgets-interaction"></a>小工具互動

認知深入解析小工具可與您應用程式上的影片互動。 本節說明如何進行此互動。

![小工具](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨來源通訊

若要讓影片索引子 widget 與其他元件通訊，影片索引子服務：

- 使用跨原始來源通訊 HTML5 方法**postMessage**。 
- 驗證 VideoIndexer.ai 來源之間的訊息。 

如果您執行自己的播放機程式碼，並與認知深入解析小工具整合，您必須負責驗證來自 VideoIndexer.ai 的訊息來源。

### <a name="embed-widgets-in-your-application-or-blog-recommended"></a>將小工具內嵌至您的應用程式或 blog （建議選項） 

本節說明如何在兩個影片索引子 widget 之間達成互動，以便在使用者選取應用程式上的深入解析控制項時，player 會跳到相關的時間。

1. 複製播放程式 widget 內嵌程式碼。
2. 複製認知深入解析內嵌程式碼。
3. 新增[中繼程式](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)檔案來處理兩個小工具之間的通訊：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

現在當使用者選取應用程式上的深入解析控制項時，player 會跳到相關的時間。

如需詳細資訊，請參閱[影片索引子-內嵌這兩個小工具示範](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>內嵌認知深入解析小工具，並使用 Azure 媒體播放器來播放內容

本節說明如何使用[AMP 外掛程式](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)來達成認知深入解析 widget 與 Azure 媒體播放機實例之間的互動。
 
1. 新增適用于 AMP 播放程式的影片索引子外掛程式：<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用影片索引子外掛程式來具現化 Azure 媒體播放機。

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. 複製認知深入解析內嵌程式碼。

您現在應該能夠與 Azure 媒體播放機通訊。

如需詳細資訊，請參閱[Azure 媒體播放機 + VI 深入解析示範](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>內嵌影片索引子認知深入解析小工具，並使用不同的影片播放機

如果您使用 Azure 媒體播放機以外的影片播放機，您必須手動操作影片播放機以達成通訊。 

1. 插入影片播放器。

    例如，標準 HTML5 播放程式：

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. 內嵌認知深入解析小工具。
3. 接聽「訊息」事件，以實作播放器的通訊。 例如：

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

如需詳細資訊，請參閱[Azure 媒體播放機 + VI 深入解析示範](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>新增字幕

如果您使用自己的[Azure 媒體播放機](https://aka.ms/azuremediaplayer)內嵌影片索引子深入解析，您可以使用**GetVttUrl**方法來取得隱藏式輔助字幕（字幕）。 您也可以從影片索引子 AMP 外掛程式**getSubtitlesUrl**呼叫 JavaScript 方法（如先前所示）。 

## <a name="customizing-embeddable-widgets"></a>自訂可內嵌的小工具

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

您可以選擇您想要的深入解析類型。 若要執行這項操作，請將它們指定為下列 URL 參數的值，這會新增至您取得的內嵌程式碼（從 API 或 web 應用程式）： `&widgets=<list of wanted widgets>`。

可能的值為：**人員**、**關鍵字**、**情緒**、文字**記錄**和**搜尋**。

例如，如果您想要內嵌僅包含人員和搜尋深入解析的 widget，iframe 內嵌 URL 看起來會像這樣：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,search`

iframe 視窗的標題也可透過將 `&title=<YourTitle>` 提供給 iframe URL 來自訂。 （它會自訂 HTML \<標題 > 值）。
    
例如，若您想要為您的 iframe 視窗指定標題 "MyInsights"，URL w看起來會像這樣：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

請注意，只有您需要在新視窗中開啟深入解析時，才適用此選項。

### <a name="player-widget"></a>播放器小工具

在內嵌影片索引子播放器時，您可以藉由指定 iframe 的大小來選擇播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

根據預設，影片索引子播放程式會根據影片的文字記錄，自動產生隱藏式輔助字幕。 文字記錄是從影片中，使用影片上傳時所選取的來源語言進行解壓縮。

如果您想要使用不同的語言來內嵌，可以將 `&captions=< Language | "all" | "false" >` 新增至 [內嵌播放機 URL]。 如果您想要所有可用語言的標題中的標題，請使用 `all`的值。 如果您想要依預設顯示標題，您可以傳入 `&showCaptions=true`。

內嵌 URL 將顯示如下︰ 

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=italian`

如果您想要停用標題，您可以將 `captions` 參數值當做 `false`傳遞。

#### <a name="autoplay"></a>播放
根據預設，播放程式會開始播放影片。 您可以將 `&autoplay=false` 傳遞至先前的內嵌 URL，以選擇不進行。

## <a name="code-samples"></a>程式碼範例

請參閱程式[代碼範例](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets)存放庫，其中包含影片索引子 API 和小工具的範例：

| 檔案/資料夾                       | 說明                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 在自訂 Azure 媒體播放機中載入影片索引子影片                        |
| `azure-media-player-vi-insights`  | 使用自訂 Azure 媒體播放機內嵌 VI 深入解析                             |
| `control-vi-embedded-player`      | 內嵌 VI Player 並從外部加以控制                                    |
| `custom-index-location`           | 從自訂的外部位置內嵌 VI 深入解析（可以是客戶的 blob）     |
| `embed-both-insights`             | VI 深入解析播放機和深入解析的基本使用方式                            |
| `embed-insights-with-AMP`         | 使用自訂 Azure 媒體播放機內嵌 VI 深入解析 widget                      |
| `customize-the-widgets`           | 使用自訂選項內嵌 VI widget                                     |
| `embed-both-widgets`              | 內嵌 VI 播放程式和見解，並在其間進行通訊                      |
| `url-generator`                   | 根據使用者指定的選項產生 widget 自訂內嵌 url             |
| `html5-player`                    | 使用預設的 HTML5 影片播放機內嵌 VI 深入解析                            |

## <a name="next-steps"></a>後續步驟

如需如何觀看和編輯影片索引子深入解析的相關資訊，請參閱[觀看和編輯影片索引子深入](video-indexer-view-edit.md)解析。

此外，請參閱[影片索引子 CodePen](https://codepen.io/videoindexer/pen/eGxebZ)。
