---
title: 在應用內嵌影像的索引器小元件
titleSuffix: Azure Media Services
description: 瞭解如何在應用中嵌入視頻索引器小部件。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 5134a262397676aa9b59de9b0c6de61c26d21523
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262905"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>在應用內嵌影像的索引器小元件

本文演示如何在應用中嵌入視頻索引器小部件。 視訊索引器支援將三種類型的小元件嵌入到你的應用程式中:*認知見解*,*播放器*與*編輯器*。

從版本 2 開始,小部件基本 URL 包括指定帳戶的區域。 例如，美國西部區域中的帳戶會產生：`https://wus2.videoindexer.ai/embed/insights/...`。

## <a name="widget-types"></a>小工具類型

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

認知深入解析小工具包含從您的影片索引編製程序中擷取的所有視覺化深入解析。 認知見解微件支援以下可選 URL 參數:

|名稱|定義|描述|
|---|---|---|
|`widgets` | 以逗號分隔的字串 | 允許您控制要呈現的見解。<br/>範例:`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`僅呈現人和關鍵字 UI 見解。<br/>可用選項:人物、動畫人物、關鍵字、標籤、情感、情感、主題、關鍵幀、腳本、ocr、揚聲器、場景和命名實體。|
|`controls`|以逗號分隔的字串|允許您控制要呈現的控制項。<br/>範例:`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download`僅呈現搜尋選項和下載按鈕。<br/>可用選項:搜索、下載、預設、語言。|
|`language`|短語言代碼 (語言名稱)|控制見解語言。<br/>範例： `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>或 `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | 短語言碼 | 控制 UI 的語言。 預設值是 `en`。 <br/>範例： `locale=de`.|
|`tab` | 預設選擇選項卡 | 控制預設情況下呈現的 **「見解」** 選項卡。 <br/>示例:`tab=timeline`在選擇 **「時間軸」** 選項卡時呈現見解。|

### <a name="player-widget"></a>播放器小工具

您可以使用播放機小部件使用自適應比特率流式傳輸視頻。 播放機小部件支援以下可選 URL 參數。

|名稱|定義|描述|
|---|---|---|
|`t` | 從開始開始的秒數 | 使玩家從指定的時間點開始播放。<br/> 範例： `t=60`. |
|`captions` | 語言代碼 | 在小部件載入期間以指定語言獲取標題,以在 **「字幕」** 選單上可用。<br/> 範例： `captions=en-US`. |
|`showCaptions` | 布林值 | 可讓播放器載入已啟用的標題。<br/> 範例： `showCaptions=true`. |
|`type`| | 啟動音訊播放器外觀(視頻部分被刪除)。<br/> 範例： `type=audio`. |
|`autoplay` | 布林值 | 指示播放機在載入時是否應開始播放視頻。 預設值是 `true`。<br/> 範例： `autoplay=false`. |
|`language`/`locale` | 語言代碼 | 控制玩家語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`.|

### <a name="editor-widget"></a>編輯器小元件

您可以使用編輯器小部件創建新專案並管理視頻的見解。 編輯器小部件支援以下可選 URL 參數。

|名稱|定義|描述|
|---|---|---|
|`accessToken`<sup>*</sup> | String | 提供對僅用於嵌入小部件的帳戶中的視頻的訪問。<br> 編輯器小元件需要參數`accessToken`。 |
|`language` | 語言代碼 | 控制玩家語言。 預設值是 `en-US`。<br/>範例： `language=de-DE`. |
|`locale` | 短語言碼 | 控制見解語言。 預設值是 `en`。<br/>範例： `language=de`. |

<sup>*</sup>業主應`accessToken`謹慎。

## <a name="embedding-public-content"></a>內嵌公用內容

1. 登錄[視頻索引器](https://www.videoindexer.ai/)網站。
2. 選擇要使用的視頻。
3. 選擇視頻下方顯示的**</>** 嵌入按鈕( )。

    選擇 **「嵌入」** 按鈕後,可以選擇要嵌入應用的小元件。
4. 選擇您想要的小元件類型(**認知見解**、**播放器**或**編輯器**)。
5. 複製嵌入代碼(顯示在'**共用&嵌入'** 對話框中**複製嵌入的代碼**)。
6. 將代碼添加到應用。

> [!NOTE]
> 如果您在共享視頻 URL 時出現問題,`location`請將 參數添加到連結中。 參數應設定為[存在視訊索引器的 Azure 區域](regions.md)。 例如： `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial` 。

## <a name="embedding-private-content"></a>內嵌私人內容

要嵌入專用影片,必須在 iframe`src`屬性中傳遞存取權杖:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
要取得認知見解小部件內容,請使用以下方法之一:

- [獲取見解小工具](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)API。<br/>
- [取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?)。 將其作為查詢參數添加到 URL。 指定此網`src`址 作為 iframe 的值,如前面所示。

要在嵌入式小部件中提供編輯見解功能,必須傳遞包含編輯許可權的訪問權杖。 使用[取得資料與抓取工具](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)或[取得影片存取權杖](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?)。`&allowEdit=true`

## <a name="widgets-interaction"></a>小工具互動

"認知見解"微件可以與應用上的視頻進行交互。 本節說明如何進行此互動。

![認知見解小元件視訊索引器](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>跨來源通訊

要讓視訊索引器小元件與其他元件通訊,視訊索引器服務:

- 使用跨源通訊HTML5方法`postMessage`。
- 驗證 VideoIndexer.ai 來源之間的訊息。

如果您實現自己的播放器代碼並與認知見解小部件集成,您有責任驗證來自VideoIndexer.ai的消息的來源。

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>在應用程式或部落格中嵌入小元件(建議)

本節演示如何實現兩個視頻索引器小部件之間的交互,以便當用戶選擇應用上的見解控件時,播放機將跳轉到相關時刻。

1. 複製播放器小工具的內嵌程式碼。
2. 複製認知深入解析的內嵌程式碼。
3. 新增[中繼程序檔案](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js)以處理兩個小工具之間的通訊：<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

現在,當用戶選擇應用上的見解控件時,播放機將跳轉到相關時刻。

有關詳細資訊,請參閱[影片索引器 - 嵌入兩個小工具示範](https://codepen.io/videoindexer/pen/NzJeOb)。

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>內嵌認知深入解析小工具，並使用 Azure 媒體播放器來播放內容

本節演示如何使用[AMP 外掛程式](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js)實現認知見解微件和 Azure 媒體播放器實例之間的交互。

1. 新增 AMP 播放器<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. 使用視頻索引器外掛程式實例化 Azure 媒體播放機。

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

3. 複製認知深入解析的內嵌程式碼。

您現在可以與 Azure 媒體播放器進行通信。

有關詳細資訊,請參閱 Azure[媒體播放機 + VI 見解演示](https://codepen.io/videoindexer/pen/rYONrO)。

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>嵌入視訊索引器認知見解小元件,並使用其他影片播放器

如果使用 Azure 媒體播放器以外的影片播放器,則必須手動操作視頻播放器以實現通信。

1. 插入影片播放器。

    例如,標準 HTML5 播放器:

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

有關詳細資訊,請參閱 Azure[媒體播放機 + VI 見解演示](https://codepen.io/videoindexer/pen/YEyPLd)。

## <a name="adding-subtitles"></a>新增字幕

如果使用自己的[Azure 媒體播放器](https://aka.ms/azuremediaplayer)嵌入影片索引器見解,則`GetVttUrl`可以使用 方法 獲取隱藏字幕(字幕)。 您還可以從影片索引器 AMP`getSubtitlesUrl`外掛程式 呼叫 JavaScript 方法(如前面所示)。

## <a name="customizing-embeddable-widgets"></a>自訂可內嵌的小工具

### <a name="cognitive-insights-widget"></a>認知深入解析小工具

您可以選擇所需的見解類型。 此選項,請將它們指定到您取得的嵌入代碼(從 API 或 Web 應用程式)的以下網址`&widgets=<list of wanted widgets>`參數的值: 。

可能的值`people`是: `animatedCharacters` `keywords` `labels` `sentiments`、 `emotions` `topics` `keyframes` `transcript`、 `ocr` `speakers` `scenes`、 `namedEntities`、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、 、

例如,如果要嵌入僅包含人和關鍵字見解的小部件,iframe 嵌入 URL 如下所示:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

iframe 視窗的標題也可透過將 `&title=<YourTitle>` 提供給 iframe URL 來自訂。 (它自定義`<title>`HTML 值)。
   
例如，若您想要為您的 iframe 視窗指定標題 "MyInsights"，URL w看起來會像這樣：

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

請注意，只有您需要在新視窗中開啟深入解析時，才適用此選項。

### <a name="player-widget"></a>播放器小工具

在內嵌影片索引子播放器時，您可以藉由指定 iframe 的大小來選擇播放器的大小。

例如：

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

預設情況下,視頻索引機播放器已基於視頻文本自動生成隱藏字幕。 使用上傳視頻時選擇的源語言從視頻中提取腳本。

如果要使用其他語言進行嵌入,可以添加到`&captions=<Language Code>`嵌入播放機網址。 如果默認情況下希望顯示字幕,則可以傳遞&顯示Captions_true。

內嵌 URL 將顯示如下︰

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>自動播放

默認情況下,播放機將開始播放視頻。 您可以選擇不透過傳遞到`&autoplay=false`前面的嵌入 URL。

## <a name="code-samples"></a>程式碼範例

請參考包含影片索引器 API 與小工具範例[的程式碼範例](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets):

| 檔案/資料夾                       | 描述                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | 在自定義 Azure 媒體播放器中載入影片索引器視訊。                        |
| `azure-media-player-vi-insights`  | 使用自訂 Azure 媒體播放器嵌入 VI 見解。                             |
| `control-vi-embedded-player`      | 嵌入 VI 播放機並從外部控制它。                                    |
| `custom-index-location`           | 從自訂外部位置嵌入 VI 見解(可以是客戶 Blob)。     |
| `embed-both-insights`             | VI 洞察的基本用法包括玩家和見解。                            |
| `embed-insights-with-AMP`         | 使用自訂 Azure 媒體播放器嵌入 VI 見解小部件。                      |
| `customize-the-widgets`           | 嵌入六個小部件與自定義選項。                                     |
| `embed-both-widgets`              | 嵌入 VI 播放器和見解,並在他們之間進行溝通。                      |
| `url-generator`                   | 根據使用者指定的選項生成小部件自定義嵌入 URL。             |
| `html5-player`                    | 使用預設 HTML5 影片播放器嵌入 VI 見解。                           |

## <a name="next-steps"></a>後續步驟

有關如何檢視和編輯影片索引器見解的資訊,請參閱[檢視並編輯影片索引器 。](video-indexer-view-edit.md)

此外,請檢視[影像索引器碼筆](https://codepen.io/videoindexer/pen/eGxebZ)。
