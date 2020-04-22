---
title: 為 Azure 媒體播放器編寫外掛程式
description: 瞭解如何使用 JavaScript 使用 Azure 媒體播放器編寫外掛程式
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727108"
---
# <a name="writing-plugins-for-azure-media-player"></a>為 Azure 媒體播放器編寫外掛程式 #

外掛程式是為擴展或增強播放器而編寫的 JavaScript。 您可以編寫更改 Azure 媒體播放器外觀、功能甚至將其與其他服務介面的外掛程式。 您可以透過兩個簡單的步驟執行此操作:

## <a name="step-1"></a>步驟 1 ##

將 JavaScript 寫入這樣的函數中:

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

可以直接在標記中的`<script>`HTML 頁或外部 JavaScript 檔中編寫代碼。 如果執行後者,請確保在 AMP 文稿*之後的*HTML 頁中`<head>`包含 JavaScript 檔。

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
使用 JavaScript 以以下兩種方式之一初始化外掛程式:

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

外掛程式選項不是必需的,包括它們只是允許使用外掛程式的開發人員配置其行為,而無需更改原始程式碼。

有關創建外掛程式的靈感和更多範例,請查看我們的[畫廊](azure-media-player-plugin-gallery.md)

>[!NOTE]
> 外掛程式代碼在檢視器的播放器體驗的生命週期內動態更改 DOM 中的專案,它永遠不會對播放機的原始程式碼進行永久性更改。 在這裡,瞭解瀏覽器的開發人員工具將派上用場。 例如,如果要更改播放器中元素的外觀,可以通過其類名稱查找其 HTML 元素,然後從那裡添加或更改屬性。 以下是[更改 HTML 屬性](http://www.w3schools.com/js/js_htmldom_html.asp)的一個很好的資源。

### <a name="integrated-plugins"></a>整合外掛程式 ###

 目前有兩個外掛程式烘焙到AMP:[時間提示](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html)和[熱鍵](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html)。 這些外掛程式最初被開發為玩家的模組化外掛程式,但現在被包含在玩家原始程式碼中。

### <a name="plugin-gallery"></a>外掛程式庫 ###

[外掛程式庫](http//:aka.ms/ampplugins)有幾個外掛程式,社區已經為諸如時間線標記、縮放、分析等功能做出了貢獻。 該頁面提供對外掛程式的訪問和有關如何設置外掛程式的說明,以及顯示外掛程式操作的演示。 如果您創建了一個很酷的外掛程式,你認為應該包含在我們的畫廊,請隨時提交它,以便我們可以檢查出來。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)