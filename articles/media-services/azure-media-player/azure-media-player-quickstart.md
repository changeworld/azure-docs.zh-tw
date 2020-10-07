---
title: Azure 媒體播放器快速入門
description: 了解用來設定 Azure 媒體播放器的基本步驟。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/05/2020
ms.locfileid: "81727475"
---
# <a name="azure-media-player-quickstart"></a>Azure 媒體播放器快速入門
Azure 媒體播放器很容易設定。 只需要一些時間從 Azure 媒體服務帳戶取得基本的媒體內容播放。 本節將說明基本步驟而不深入討論。 以下幾節提供關於安裝和設定 Azure 媒體播放器的特定資訊。  請將下列內容新增至文件的 `<head>`：

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> 您**不**應在生產環境中使用 `latest` 版本，因為此版本將依需求而變動。 請將 `latest` 版本取代為某個 Azure 媒體播放器版本；例如，將 `latest` 版本取代為 `1.0.0` 版。 您可以在[這裡](azure-media-player-changelog.md)查詢 Azure 媒體播放器的版本。

## <a name="use-the-video-element"></a>使用 video 元素

接下來，只要像平常一樣使用 `<video>` 元素即可，但會有一個包含任何選項的額外 `data-setup` 屬性。 這些選項可以在有效的 JSON 物件中包含任何 Azure 媒體服務選項。

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

如果您不想要使用自動安裝，則可以省略 `data-setup` 屬性，並以手動方式初始化 video 元素。

```html
    var myPlayer = amp('vid1', { /* Options */
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
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)