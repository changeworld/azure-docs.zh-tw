---
title: Azure 媒體播放機選項
description: Azure 媒體播放機內嵌程式碼只是 HTML5 video 標記，因此針對許多選項，您可以使用標準標記屬性來設定選項。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 2b7acb1ad6fbe0beb4d79ee2a833561f0829664c
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423026"
---
# <a name="options"></a>選項。 #

## <a name="setting-options"></a>設定選項 ##

Azure 媒體播放機內嵌程式碼只是 HTML5 video 標記，因此針對許多選項，您可以使用標準標記屬性來設定選項。

`<video controls autoplay ...>`

或者，您可以使用資料安裝屬性，以[JSON](http://json.org/example.html)格式提供選項。 這也是您如何設定影片標記不標準的選項。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最後，如果您未使用資料安裝程式屬性來觸發播放程式設定，您可以在 JavaScript 安裝函式的第二個引數中傳入具有播放機選項的物件。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 在設定來源之前，只會在第一次初始化時設定此函數中的選項。  如果您想要修改相同已初始化 Azure 媒體播放機專案上的選項，您必須先更新選項，然後再變更來源。 您可以使用來更新 JavaScript 中的選項 `myPlayer.options({/*updated options*/});` 。 請注意，只有變更的選項會受到影響，所有其他先前設定的選項將會保存。

## <a name="individual-options"></a>個別選項 ##

> [!NOTE]
>影片標籤屬性只能是 true 或 false （布林值），您只需要包含屬性（不帶正負號）即可開啟，或將其排除以將它關閉。 例如，若要將控制項開啟：錯誤 `<video controls="true" ...>` 的正確處 `<video controls ...>` 在於有人嘗試將這些值設定為 false，並使用 false 作為值（例如 controls = "false"），這實際上會執行相反的動作，並將值設定為 true，因為仍然包含該屬性。

### <a name="controls"></a>controls ###

Controls 選項會設定播放程式是否具有可與使用者互動的控制項。 如果沒有控制項，啟動播放影片的唯一方式是使用 [自動播放] 屬性或透過 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果 [自動播放] 為 true，影片會在頁面載入時立即開始播放（不需要使用者進行任何互動）。

> [!NOTE]
> 行動裝置（例如 Windows Phone、Apple iOS 和 Android）不支援此選項。 行動裝置會封鎖自動播放功能，以防止取用者的每月通話方案使用（通常昂貴）。 在此情況下，需要使用者觸控/按一下才能啟動影片。

`<video autoplay ...>`或`{ "autoplay": true }`

### <a name="poster"></a>海報 ###
海報屬性會設定在影片開始播放之前顯示的影像。 這通常是影片的框架或自訂標題畫面。 一旦使用者按一下 [播放]，影像就會消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

Width 屬性會設定影片的顯示寬度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>身高 ###

Height 屬性會設定影片的顯示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>外掛程式 ###

外掛程式 JSON 會決定要載入該 AMP 實例的外掛程式，可讓您設定外掛程式可能擁有的任何選項。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

如需外掛程式開發和使用方式的詳細資訊，請參閱[撰寫外掛程式](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他選項 ###

您可以 `<video>` 使用 `data-setup` 接受 JSON 的參數，在標記上設定其他選項。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

這會明確設定為 false。 將設定為 false，可讓 Azure 媒體播放機面板在不同的平臺上呈現相同的外觀。  此外，相對於名稱，仍然會啟用觸控。

### <a name="fluid"></a>分離器 ###

藉由將此選項設定為 true video 元素，將會填滿父系容器的寬度，而且高度會調整為符合具有標準16:9 外觀比例的影片。

`<video ... data-setup='{"fluid": true}'>`

`fluid`選項會覆寫明確 `width` 和 `height` 設定。 此選項僅適用于 Azure 媒體播放機版本 `2.0.0` 和更新版本。

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed`選項控制使用者可用的 playbackSpeed 控制項和播放速度設定集。 `playbackSpeed`取得物件。 若要在控制列上啟用 [播放速度控制]，必須將物件的屬性 `enabled` 設定為 [true]。 在標記中啟用播放速度的範例：

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


設定的其他屬性 `playbackSpeed` 是由[PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)物件所提供。

在 JavaScript 中設定播放速度選項的範例：

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

此選項僅適用于 Azure 媒體播放機版本2.0.0 和更新版本。

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

此 `staleDataTimeLimitInSec` 選項是一種優化，可讓您設定要在 mediaSource 緩衝區中保留多少秒的過時資料。 此選項預設為停用狀態。

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

將 [已啟用] 設定為 [true]，可讓您在即時串流和即時封存中顯示即時 CEA 字幕。 標籤屬性不是必要的，如果不包含，播放機將會切換回預設標籤。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此選項僅適用于 Azure 媒體播放機2.1.1 版和更新版本。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
