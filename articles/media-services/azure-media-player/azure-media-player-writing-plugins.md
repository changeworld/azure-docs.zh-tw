---
title: 撰寫 Azure 媒體播放機的外掛程式
description: 瞭解如何使用 JavaScript 撰寫 Azure 媒體播放機的外掛程式
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: c1a64bff8b81735d9c4c9a14d2c1e12bd0bfe57e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296156"
---
# <a name="writing-plugins-for-azure-media-player"></a>撰寫 Azure 媒體播放機的外掛程式 #

外掛程式是撰寫成擴充或增強播放程式的 JavaScript。 您可以撰寫外掛程式來變更 Azure 媒體播放機的外觀、其功能，甚至讓它與其他服務進行介面。 您可以用兩個簡單的步驟來執行此動作：

## <a name="step-1"></a>步驟 1 ##

在函數中撰寫您的 JavaScript，如下所示：

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

您可以直接在 `<script>` 標記或外部 JavaScript 檔案中的 HTML 網頁撰寫您的程式碼。 如果您這樣做，請務必將 JavaScript 檔案包含在 `<head>` 您的 HTML 網頁中的 AMP 腳本 *之後* 。

範例：

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>步驟 2 ##
以下列兩種方式之一初始化具有 JavaScript 的外掛程式：

方法 1：

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

方法 2：

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

外掛程式選項不是必要的選項，包括只允許開發人員使用您的外掛程式來設定其行為，而不需要變更原始程式碼。

如需有關建立外掛程式的靈感和更多範例，請參閱我們的資源 [庫](azure-media-player-plugin-gallery.md)

>[!NOTE]
> 外掛程式程式碼會在檢視器的播放機體驗存留期內動態變更 DOM 中的專案，而不會對播放程式的原始程式碼進行永久變更。 這就是了解您瀏覽器開發人員工具的方便之處。 例如，如果您想要變更播放程式中專案的外觀，您可以依其類別名稱找到其 HTML 專案，然後從該處新增或變更屬性。 以下是[變更 HTML 屬性](http://www.w3schools.com/js/js_htmldom_html.asp)的絕佳資源。

### <a name="integrated-plugins"></a>整合式外掛程式 ###

 目前有兩個外掛程式內建至 AMP：[時間提示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)[和快速鍵](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)。 這些外掛程式最初開發為播放程式的模組化外掛程式，但現在已包含在播放程式原始程式碼中。

### <a name="plugin-gallery"></a>外掛程式資源庫 ###

[外掛程式資源庫](https://aka.ms/ampplugins)有數個外掛程式，該社區已針對時間表標記、縮放、分析等功能提供功能。 頁面會提供外掛程式的存取權，以及如何設定的指示，以及示範如何進行設定的示範。 如果您建立了您認為應該包含在資源庫中的酷炫外掛程式，歡迎您提交，讓我們可以將它簽出。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
