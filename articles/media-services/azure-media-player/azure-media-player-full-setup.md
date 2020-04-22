---
title: Azure 媒體播放器完整設定
description: 瞭解如何設置 Azure 媒體播放器。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727238"
---
# <a name="azure-media-player-full-setup"></a>Azure 媒體播放器完整設定 #

Azure 媒體播放器很容易設定。 只需幾分鐘,就能直接從 Azure 媒體服務帳戶獲得媒體內容的基本播放。 [範例](https://github.com/Azure-Samples/azure-media-player-samples)也提供在版本的範例目錄中。


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>第一步:將 JavaScript 與 CSS 檔包含在頁面頭 ##

使用 Azure 媒體播放器,可以從 CDN 託管版本訪問腳本。 現在通常建議將 JavaScript 放在最終正文`<body>`標記`<head>`之前,而不是放在中,但 Azure 媒體播放器包含一個「HTML5 Shiv」,它需要在舊 IE 版本中處於頭腦中,才能將視訊標記視為有效元素。

> [!NOTE]
> 如果您已經在使用像[Modernizr](http://modernizr.com/)這樣的 HTML5 shiv,則可以在任何地方包括 Azure 媒體播放器 JavaScript。 但是,請確保您的 Modernizr 版本包含影片的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> 不應在**NOT**`latest`生產中使用版本,因為可能會按需更改。 替換為`latest`Azure 媒體播放器的版本。 例如，以 `2.1.1` 取代 `latest`。 可以[在此處](azure-media-player-changelog.md)查詢 Azure 媒體播放器版本。

> [!NOTE]
> 自`1.2.0`發佈以來,不再需要將位置包含到回退技術(它會自動從 azuremediaplayer.min.js 檔的相對路徑中選取位置)。 您可以通過在上述腳`<head>`本 之後添加以下腳本來修改回退技術的位置。

> [!NOTE]
> 由於 Flash 和 Silverlight 外掛程式的性質,swf 和 xap 檔應託管在沒有任何敏感資訊或資料的域上 - Azure CDN 託管版本會自動為您保管。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>第二步:向頁面加入 HTML5 視訊標記 ##

使用 Azure 媒體播放器,可以使用 HTML5 視訊標記嵌入影片。 然後,Azure 媒體播放器將讀取該標記,使其在所有瀏覽器中工作,而不僅僅是支援 HTML5 視頻的瀏覽器。 除了基本標記之外,Azure 媒體播放機還需要一些額外的部件。

1. 上`<data-setup>`的屬性`<video>`告訴 Azure 媒體播放器在頁面準備就緒時自動設置影片,並從屬性中讀取任何(以 JSON 格式)。
1. 屬性`id`:應在同一頁面上的每個視頻使用和唯一。
1. 該`class`屬性包含兩個類別:
    - `azuremediaplayer`套用 Azure 媒體播放器 UI 功能所需的樣式
    - `amp-default-skin`將預設外觀應用於 HTML5 控制項
1. 包括`<source>`兩個必需的屬性
    - `src`屬性可以包含 Azure 媒體服務中的 **.ism/清單*檔,Azure 媒體播放器會自動向播放機添加 DASH、SMOOTH 和 HLS 的 URL
    - `type`屬性是流的所需 MIME 類型。 與 *".ism/清單"* 關聯的 MIME 類型是 *"應用程式/vnd.ms-sstr_xml"*
1. 如果*optional*`<data-setup>`Azure`<source>`媒體 服務中的流有任何唯一的傳遞策略,包括但不限於加密類型(AES 或 PlayReady、Widevine 或 FairPlay)和權杖,則上的可選屬性將告訴 Azure 媒體播放器。

完全按照 HTML5 影片的格式包括/ 排除屬性、設定、源和追蹤。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

默認情況下,大型播放按鈕位於左上角,因此不會遮蓋海報的有趣部分。 如果您希望將大型播放按鈕居中,則可以向`amp-big-play-centered``class``<video>`元素添加其他內容。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>動態載入 HTML 的替代設定 ###

如果您的網頁或應用程式動態載入視訊標記(ajax、附加兒童等),以便在頁面載入時可能不存在,則需要手動設置播放機,而不是依賴資料設定屬性。 為此,請先從標記中刪除數據設置屬性,這樣在初始化播放機時不會出現混淆。 接下來,在 Azure 媒體播放器 JavaScript 載入後,並在視訊標記載入 DOM 之後,執行以下 JAVAScript。

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

函數中`amp`的第一個參數是視頻標記的 ID。 用你自己的替換它。

第二個參數是選項物件。 它允許您設置其他選項,如使用數據設置屬性。

第三個參數是"就緒"回調。 Azure 媒體播放器初始化後,它將調用此函數。 在就緒回調中,"此"物件引用播放機實例。

也可以傳遞對元素本身的引用,而不是使用元素 ID。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)