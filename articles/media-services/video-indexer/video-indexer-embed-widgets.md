---
title: 在您的應用程式中內嵌影片索引子小工具
titleSuffix: Azure Media Services
description: 瞭解如何在您的應用程式中內嵌影片索引子小工具。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 1bc751ae293abbb7aa330a99a4b66a917d150906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91268616"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>在您的應用程式中內嵌影片索引子小工具

本文說明如何將影片索引子小工具內嵌到您的應用程式。 影片索引器支援將三種類型的小工具內嵌到應用程式中：*認知深入解析*、*播放機*以及*編輯器*。

從第2版開始，小工具基底 URL 包含指定之帳戶的區域。 例如，美國西部區域中的帳戶會產生：`https://www.videoindexer.ai/embed/insights/.../?location=westus2`。

## <a name="widget-types"></a>小工具類型

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

認知深入解析小工具包含從您的影片索引編製程序中擷取的所有視覺化深入解析。 認知深入解析小工具支援下列選擇性 URL 參數：

|名稱|定義|描述|
|---|---|---|
|`widgets` | 以逗號分隔的字串 | 可讓您控制您想要呈現的見解。<br/>範例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` 只呈現人員和關鍵字 UI 見解。<br/>可用的選項：人員、animatedCharacters、關鍵字、標籤、情緒、表情、主題、主要畫面格、文字記錄、ocr、喇叭、場景和 namedEntities。|
|`controls`|以逗號分隔的字串|可讓您控制要轉譯的控制項。<br/>範例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` 只呈現搜尋選項和下載按鈕。<br/>可用的選項：搜尋、下載、預設語言。|
|`language`|簡短的語言代碼 (語言名稱) |控制見解語言。<br/>範例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>或 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 簡短的語言代碼 | 控制 UI 的語言。 預設值是 `en`。 <br/>範例： `locale=de`.|
|`tab` | 預設的選取索引標籤 | 控制預設呈現的 [ **見解** ] 索引標籤。 <br/>範例： `tab=timeline` 呈現已選取 [ **時程表** ] 索引標籤的見解。|
|`location` ||`location`參數必須包含在內嵌連結中，請參閱[如何取得您的區功能變數名稱稱](regions.md)。 如果您的帳戶處於預覽狀態，則應該將其 `trial` 用於位置值。 `trial` 這是參數的預設值 `location` 。| 

### <a name="player-widget"></a>播放器小工具

您可以使用播放程式 widget，利用彈性位元速率來串流影片。 播放機小工具支援下列選擇性 URL 參數。

|名稱|定義|描述|
|---|---|---|
|`t` | 開始的秒數 | 讓播放玩家從指定的時間點開始播放。<br/> 範例： `t=60`. |
|`captions` | 語言代碼 | 在 widget 載入時，以指定的語言提取標題，以便在 **標題** 功能表上使用。<br/> 範例： `captions=en-US`. |
|`showCaptions` | 布林值 | 可讓播放器載入已啟用的標題。<br/> 範例： `showCaptions=true`. |
|`type`| | 啟用音訊播放程式面板 () 移除影片部分。<br/> 範例： `type=audio`. |
|`autoplay` | 布林值 | 指出玩家是否應在載入時開始播放影片。 預設值是 `true`。<br/> 範例： `autoplay=false`. |
|`language`/`locale` | 語言代碼 | 控制播放機的語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`.|
|`location` ||`location`參數必須包含在內嵌連結中，請參閱[如何取得您的區功能變數名稱稱](regions.md)。 如果您的帳戶處於預覽狀態，則應該將其 `trial` 用於位置值。 `trial` 這是參數的預設值 `location` 。| 

### <a name="editor-widget"></a>編輯器 widget

您可以使用編輯器 widget 來建立新的專案，以及管理影片的見解。 編輯器 widget 支援下列選擇性 URL 參數。

|名稱|定義|描述|
|---|---|---|
|`accessToken`<sup>*</sup> | String | 提供只位於用來內嵌 widget 之帳戶的影片存取權。<br> 編輯器 widget 需要 `accessToken` 參數。 |
|`language` | 語言代碼 | 控制播放機的語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`. |
|`locale` | 簡短的語言代碼 | 控制見解語言。 預設值是 `en`。<br/>範例： `language=de`. |
|`location` ||`location`參數必須包含在內嵌連結中，請參閱[如何取得您的區功能變數名稱稱](regions.md)。 如果您的帳戶處於預覽狀態，則應該將其 `trial` 用於位置值。 `trial` 這是參數的預設值 `location` 。| 

<sup>*</sup>擁有者應 `accessToken` 謹慎提供。

## <a name="embedding-videos"></a>內嵌影片

本節討論如何將公開和私用內容內嵌至應用程式。

`location`參數必須包含在內嵌連結中，請參閱[如何取得您的區功能變數名稱稱](regions.md)。 如果您的帳戶處於預覽狀態，則應該將其 `trial` 用於位置值。 `trial` 這是參數的預設值 `location` 。 例如： `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial` 。

> [!IMPORTANT]
> 共用 **播放機** 或 **見解** widget 的連結將會包含存取權杖，並將唯讀許可權授與您的帳戶。

### <a name="public-content"></a>公開內容

1. 登入 [影片索引子](https://www.videoindexer.ai/) 網站。
1. 選取您要使用的影片，然後按下 [ **播放**]。
1. 選取您要 (**認知見解**、 **播放機**或 **編輯器**) 的 widget 類型。
1. 按一下 [ ** &lt; / &gt; 內嵌**]。
5. 複製內嵌程式碼 (會出現在 [**共用 &** 內嵌] 對話方塊) 的 [**複製內嵌程式碼**] 中。
6. 將程式碼新增至您的應用程式。

### <a name="private-content"></a>私用內容

若要內嵌私用影片，您必須在 iframe 的屬性中傳遞存取權杖 `src` ：

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
若要取得認知深入解析小工具內容，請使用下列其中一種方法：

- [取得深入解析小工具](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)API。<br/>
- [取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?)。 將它新增為 URL 的查詢參數。 將此 URL 指定為 `src` iframe 的值，如先前所示。

若要在內嵌 widget 中提供編輯見解功能，您必須傳遞包含編輯許可權的存取權杖。 使用 [Get Insights Widget](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) 或使用 [取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) `&allowEdit=true` 。

## <a name="widgets-interaction"></a>小工具互動

認知深入解析小工具可與您應用程式上的影片互動。 本節說明如何進行此互動。

![認知見解 widget 影片索引子](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>流程概觀

當您編輯文字記錄時，會發生下列流程：

1. 您可以編輯時間軸中的文字記錄。
1. 影片索引子會取得這些更新，並將它們儲存在語言模型中的 [ [從文字記錄編輯](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) ]。
1. 標題會更新：

    * 如果您使用影片索引子的播放程式 widget，則會自動更新。
    * 如果您使用的是外部播放程式，您會看到新的字幕檔案使用者 **取得影片標題** 的呼叫。

### <a name="cross-origin-communications"></a>跨來源通訊

若要讓影片索引子小工具與其他元件通訊，請使用影片索引子服務：

- 使用跨原始通訊 HTML5 方法 `postMessage` 。
- 驗證 VideoIndexer.ai 來源之間的訊息。

如果您執行自己的播放機程式碼並與認知見解 widget 整合，您必須負責驗證來自 VideoIndexer.ai 的訊息來源。

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>在您的應用程式或 blog 中內嵌 widget (建議的) 

本節說明如何在兩個影片索引子小工具之間達成互動，讓使用者在您的應用程式上選取見解控制項時，播放程式會跳到相關的時刻。

1. 複製播放器小工具的內嵌程式碼。
2. 複製認知深入解析的內嵌程式碼。
3. 新增[中繼程序檔案](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)以處理兩個小工具之間的通訊：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

現在，當使用者選取您應用程式上的深入解析控制項時，播放程式會跳到相關的時刻。

如需詳細資訊，請參閱 [影片索引子-內嵌兩個小工具示範](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>內嵌認知深入解析小工具，並使用 Azure 媒體播放器來播放內容

本節說明如何使用 [AMP 外掛程式](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)，達成認知見解 widget 與 Azure 媒體播放機實例之間的互動。

1. 為 AMP 播放機新增影片索引子外掛程式：<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用影片索引子外掛程式將 Azure 媒體播放機具現化。

    ```javascript
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
    ```

3. 複製認知深入解析的內嵌程式碼。

您現在可以與 Azure 媒體播放機進行通訊。

如需詳細資訊，請參閱 [Azure 媒體播放機 + VI 深入解析示範](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>內嵌影片索引子認知深入解析小工具，並使用不同的影片播放工具

如果您使用 Azure 媒體播放機以外的影片播放機，您必須手動操作影片播放機以達成通訊。

1. 插入影片播放器。

    例如，標準 HTML5 播放機：

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. 內嵌認知深入解析小工具。
3. 接聽「訊息」事件，以實作播放器的通訊。 例如：

    ```javascript
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
    ```

如需詳細資訊，請參閱 [Azure 媒體播放機 + VI 深入解析示範](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>新增字幕

如果您使用自己的 [Azure 媒體播放機](https://aka.ms/azuremediaplayer)內嵌影片索引子深入解析，您可以使用 `GetVttUrl` 方法來取得隱藏式輔助字幕 (字幕) 。 您也可以從影片索引子 AMP 外掛程式 (呼叫 JavaScript 方法 `getSubtitlesUrl` ，如先前的) 所示。

## <a name="customizing-embeddable-widgets"></a>自訂可內嵌的小工具

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

您可以選擇想要的深入解析類型。 若要這樣做，請將其指定為下列 URL 參數的值，此參數會新增至您從 API 或 web 應用程式) 中 (的內嵌程式碼： `&widgets=<list of wanted widgets>` 。

可能的值為：、、、、、、、、、、、 `people` `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` 和 `namedEntities` 。

例如，如果您想要內嵌僅包含人員和關鍵字見解的 widget，iframe 內嵌 URL 會如下所示：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

iframe 視窗的標題也可透過將 `&title=<YourTitle>` 提供給 iframe URL 來自訂。  (它會自訂 HTML `<title>` 值) 。
   
例如，若您想要為您的 iframe 視窗指定標題 "MyInsights"，URL w看起來會像這樣：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

請注意，只有您需要在新視窗中開啟深入解析時，才適用此選項。

### <a name="player-widget"></a>播放器小工具

在內嵌影片索引子播放器時，您可以藉由指定 iframe 的大小來選擇播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

根據預設，影片索引子播放程式會根據影片的文字記錄自動產生隱藏式輔助字幕。 文字記錄是使用上傳影片時選取的來源語言從影片中解壓縮。

如果您想要使用不同的語言來內嵌，可以新增 `&captions=<Language Code>` 至內嵌播放機 URL。 如果您想要依預設顯示標題，您可以傳遞 &showCaptions = true。

內嵌 URL 將顯示如下︰

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>自動播放

根據預設，播放程式會開始播放影片。 您可以藉由傳遞 `&autoplay=false` 至先前的內嵌 URL，選擇不使用。

## <a name="code-samples"></a>程式碼範例

請參閱包含影片索引子 API 和小工具範例的程式 [代碼範例](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) 存放庫：

| 檔案/資料夾                       | 描述                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 在自訂 Azure 媒體播放機中載入影片索引子影片。                        |
| `azure-media-player-vi-insights`  | 使用自訂 Azure 媒體播放機內嵌 VI 見解。                             |
| `control-vi-embedded-player`      | 內嵌 VI Player 並從外部進行控制。                                    |
| `custom-index-location`           | 從自訂外部位置內嵌 VI 見解 (可以是客戶的 blob) 。     |
| `embed-both-insights`             | VI 深入解析播放程式和見解的基本用法。                            |
| `embed-insights-with-AMP`         | 使用自訂 Azure 媒體播放機內嵌 VI Insights widget。                      |
| `customize-the-widgets`           | 使用自訂選項內嵌 VI widget。                                     |
| `embed-both-widgets`              | 內嵌 VI 播放程式和深入解析，並在其間進行通訊。                      |
| `url-generator`                   | 根據使用者指定的選項產生 widget 自訂內嵌 URL。             |
| `html5-player`                    | 使用預設的 HTML5 影片播放機內嵌 VI 見解。                           |

## <a name="next-steps"></a>後續步驟

如需如何查看和編輯影片索引子深入解析的詳細資訊，請參閱 [觀看和編輯影片索引子深入](video-indexer-view-edit.md)解析。

此外，請參閱 [影片索引子 >codepen](https://codepen.io/videoindexer/pen/eGxebZ)。
