---
title: Azure 媒體播放機完整安裝
description: 瞭解如何設定 Azure 媒體播放機。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "81727238"
---
# <a name="azure-media-player-full-setup"></a>Azure 媒體播放器完整安裝 #

Azure 媒體播放器很容易設定。 只需要幾分鐘的時間，就能從您的 Azure 媒體服務帳戶直接取得媒體內容的基本播放。 版本的範例目錄中也會提供[範例](https://github.com/Azure-Samples/azure-media-player-samples)。


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>步驟1：將 JavaScript 和 CSS 檔案包含在頁面的標頭中 ##

使用 Azure 媒體播放機，您可以從 CDN 託管版本存取腳本。 通常建議您將 JavaScript 放在結尾主體標籤之前`<body>` `<head>`，而不是，但 Azure 媒體播放機包含 ' HTML5 Shiv '，這必須位於較舊 IE 版本的前端，才能將影片標記視為有效的元素。

> [!NOTE]
> 如果您已經使用像是[Modernizr](http://modernizr.com/)的 HTML5 shiv，您可以在任何地方包含 Azure 媒體播放機 JavaScript。 不過，請確定您的 Modernizr 版本包含適用于影片的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> 您**不**應在生產環境中使用 `latest` 版本，因為此版本將依需求而變動。 取代`latest`為 Azure 媒體播放機的版本。 例如，以 `2.1.1` 取代 `latest`。 您可以在[這裡](azure-media-player-changelog.md)查詢 Azure 媒體播放器的版本。

> [!NOTE]
> `1.2.0`發行後，就不再需要將位置包含在回溯 techs 中（它會自動從 azuremediaplayer 的相對路徑中挑選位置），。 您可以在上述腳本後面的`<head>`中新增下列腳本，以修改 fallback techs 的位置。

> [!NOTE]
> 由於 Flash 和 Silverlight 外掛程式的本質，swf 和 xap 檔案應該裝載于不含任何敏感性資訊或資料的網域上，這會自動為您處理 Azure CDN 託管版本。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>步驟2：將 HTML5 影片標記新增至您的頁面 ##

使用 Azure 媒體播放機，您可以使用 HTML5 video 標記來內嵌影片。 Azure 媒體播放機接著會讀取標記，並讓它在所有瀏覽器中都可以使用，而不只是支援 HTML5 影片的範本。 除了基本標記以外，Azure 媒體播放機需要一些額外的部分。

1. 上`<data-setup>`的屬性會`<video>`告知 Azure 媒體播放機在頁面就緒時自動設定影片，並從屬性讀取任何（以 JSON 格式）。
1. `id`屬性：應該用於相同頁面上的每個影片，且必須是唯一的。
1. `class`屬性包含兩個類別：
    - `azuremediaplayer`套用 Azure 媒體播放機 UI 功能所需的樣式
    - `amp-default-skin`將預設的外觀套用至 HTML5 控制項
1. 包含`<source>`兩個必要的屬性
    - `src`屬性可以包含從 Azure 媒體服務新增的 **. ism/資訊清單*檔案，Azure 媒體播放機會自動新增以破折號、平滑和 HLS 的 url 給播放程式
    - `type`屬性是資料流程所需的 MIME 類型。 與 *". ism/manifest"* 相關聯的 MIME 類型為 *"application/application. vnd.ms-sstr + xml"*
1. 上的*選擇性* `<data-setup>`屬性會`<source>`告知 Azure 媒體播放機是否有任何來自 Azure 媒體服務的資料流程的唯一傳遞原則，包括但不限於加密類型（AES 或 PlayReady、Widevine 或 FairPlay）和權杖。

包含/排除屬性、設定、來源和追蹤，與 HTML5 影片完全一樣。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

根據預設，大型 [播放] 按鈕位於左上角，因此不會涵蓋海報的有趣部分。 如果您想要將大型 [播放] 按鈕置中，可以將其他`amp-big-play-centered` `class`專案新增`<video>`至您的元素。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>動態載入的 HTML 的替代設定 ###

如果您的網頁或應用程式會以動態方式載入影片標記（ajax、appendChild 等），使其在頁面載入時可能不存在，您會想要手動設定播放程式，而不是依賴資料安裝屬性。 若要這麼做，請先從標記中移除資料安裝程式屬性，以便在播放程式初始化時不會造成混淆。 接下來，在 Azure 媒體播放機 JavaScript 載入之後，以及在影片標記載入至 DOM 之後，執行下列 JavaScript 一段時間。

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

函式中`amp`的第一個引數是您影片標記的識別碼。 以您自己的取代。

第二個引數是 options 物件。 它可讓您設定其他選項，就像使用資料安裝屬性一樣。

第三個引數是「就緒」回呼。 一旦 Azure 媒體播放機初始化之後，它就會呼叫這個函式。 在準備的回呼中，' this ' 物件是指 player 實例。

您也可以將參考傳遞給專案本身，而不是使用專案識別碼。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)