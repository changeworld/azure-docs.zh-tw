---
title: Azure 媒體播放器完整安裝
description: 瞭解如何設定 Azure 媒體播放機。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b6b239f44311424db2e80c59e2aba639ae3c0000
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797455"
---
# <a name="azure-media-player-full-setup"></a>Azure 媒體播放器完整安裝 #

Azure 媒體播放器很容易設定。 只需要幾分鐘的時間，就能直接從您的 Azure 媒體服務帳戶取得媒體內容的基本播放。 版本的範例目錄中也會提供[範例](https://github.com/Azure-Samples/azure-media-player-samples)。

## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>步驟1：將 JavaScript 和 CSS 檔案包含在頁面的開頭 ##

您可以使用 Azure 媒體播放機來存取 CDN 裝載版本的腳本。 通常建議您在結尾主體標記之前放入 JavaScript `<body>` ，而不是在 `<head>` ，但 Azure Meia 播放套裝程式含 ' HTML5 Shiv '，其必須位於舊版 IE 版本的開頭，才能將影片標記視為有效元素。

> [!NOTE]
> 如果您已經在使用 HTML5 shiv （例如 [Modernizr](https://modernizr.com/) ），您可以在任何位置包含 Azure 媒體播放機 JavaScript。 不過，請確定您的 Modernizr 版本包含適用于影片的 shiv。

### <a name="cdn-version"></a>CDN 版本 ###

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> 您 **不** 應在生產環境中使用 `latest` 版本，因為此版本將依需求而變動。 取代為 `latest` Azure 媒體播放機的版本。 例如，以 `2.1.1` 取代 `latest`。 您可以在[這裡](azure-media-player-changelog.md)查詢 Azure 媒體播放器的版本。

> [!NOTE]
> 從 `1.2.0` 版本開始，不再需要將位置包含在回溯 techs 中 (它會自動從 azuremediaplayer.min.js 檔) 的相對路徑中挑選位置。 您可以在上述腳本之後，在中新增下列腳本，以修改 fallback techs 的位置 `<head>` 。

> [!NOTE]
> 由於 Flash 和 Silverlight 外掛程式的本質，在沒有任何敏感性資訊或資料的網域上裝載 swf 和 xap 檔案，這會自動為您處理 Azure CDN 裝載版本。

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>步驟2：將 HTML5 影片標記新增至您的頁面 ##

使用 Azure 媒體播放機時，您可以使用 HTML5 影片標記來內嵌影片。 Azure 媒體播放機接著會讀取標記，並使其在所有瀏覽器中運作，而不只是支援 HTML5 影片的瀏覽器。 除了基本標記之外，Azure 媒體播放機需要一些額外的部分。

1. `<data-setup>`上的屬性 `<video>` 會告知 Azure 媒體播放機在頁面就緒時自動設定影片，並從屬性讀取任何 JSON 格式的 () 。
1. `id`屬性：應該針對相同頁面上的每個影片使用和唯一。
1. `class`屬性包含兩個類別：
    - `azuremediaplayer` 套用 Azure 媒體播放機 UI 功能所需的樣式
    - `amp-default-skin` 將預設面板套用至 HTML5 控制項
1. `<source>`包含兩個必要屬性
    - `src` 屬性可以包含來自 Azure 媒體服務的 **. ism/資訊清單* 檔案，Azure 媒體播放機會自動新增以虛線、流暢且 HLS 至播放程式的 url
    - `type` attribute 是資料流程的必要 MIME 類型。 與 *". ism/manifest"* 相關聯的 MIME 類型為 *"application/vnd.openxmlformats-officedocument.spreadsheetml.sheet. vnd.ms-sstr + xml"*
1. 上的 *選擇性* `<data-setup>` 屬性 `<source>` 會告知 Azure 媒體播放機如果 Azure 媒體服務的資料流程有任何唯一的傳遞原則，包括但不限於加密類型 (AES 或 PlayReady、Widevine 或 FairPlay) 和權杖。

包含/排除屬性、設定、來源，以及與 HTML5 影片完全相同的追蹤。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

依預設，[大型播放] 按鈕位於左上角，因此不會涵蓋海報的有趣部分。 如果您想要置中大型 [播放] 按鈕，您可以將其他專案新增 `amp-big-play-centered` `class` 至您的專案 `<video>` 。

### <a name="alternative-setup-for-dynamically-loaded-html"></a>動態載入 HTML 的替代設定 ###

如果您的網頁或應用程式會以動態方式載入影片標籤 (ajax、appendChild 等 ) ，如此一來，當頁面載入時，就可能不會存在它，您會想要手動設定播放程式，而不是依賴資料安裝屬性。 若要這樣做，請先從標記中移除資料安裝屬性，如此一來，當播放程式初始化時，就不會有混淆。 接下來，在 Azure 媒體播放機 JavaScript 載入之後，以及在將影片標記載入至 DOM 之後，執行下列 JavaScript。

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

函數中的第一個引數 `amp` 是您的影片標記識別項。 將它取代為您自己的。

第二個引數是 options 物件。 它可讓您設定其他選項，就像您可以使用資料安裝屬性一樣。

第三個引數是「就緒」的回呼。 一旦 Azure 媒體播放機初始化之後，它就會呼叫這個函式。 在就緒的回呼中，' this ' 物件指的是 player 實例。

您也可以傳遞元素本身的參考，而不是使用專案識別碼。

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)