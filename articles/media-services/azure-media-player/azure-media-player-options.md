---
title: Azure 媒體播放機選項
description: Azure 媒體播放機內嵌程式碼只是一個 HTML5 影片標記，因此您可以使用標準標籤屬性來設定選項。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91296190"
---
# <a name="options"></a>選項。 #

## <a name="setting-options"></a>設定選項 ##

Azure 媒體播放機內嵌程式碼只是一個 HTML5 影片標記，因此您可以使用標準標籤屬性來設定選項。

`<video controls autoplay ...>`

或者，您可以使用資料安裝屬性來提供 [JSON](http://json.org/example.html) 格式的選項。 這也是您如何設定影片標籤不標準的選項。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最後，如果您不使用資料安裝屬性來觸發播放程式設定，您可以在 JavaScript 安裝函式中，以播放機選項傳入物件做為第二個引數。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 在設定來源之前，只會在第一次初始化時設定函數中的選項。  如果您想要修改相同初始化 Azure 媒體播放機專案上的選項，您必須先更新選項，然後再變更來源。 您可以使用來更新 JavaScript 中的選項 `myPlayer.options({/*updated options*/});` 。 請注意，只有變更的選項才會受到影響，所有其他先前設定的選項都會保存下來。

## <a name="individual-options"></a>個別選項 ##

> [!NOTE]
>影片標籤屬性只能是 true 或 false (布林值) ，您只需包含屬性 (沒有任何等號) 即可開啟，或將它排除以關閉。 例如，若要開啟控制項：錯 `<video controls="true" ...>` 了， `<video controls ...>` 人們遇到的最大問題，就是嘗試將這些值設定為 false，使用 false 做為值 (例如控制項 = "false" ) 它實際上會執行相反的動作，並且將值設為 true，因為仍包含該屬性。

### <a name="controls"></a>controls ###

[控制項] 選項會設定玩家是否有使用者可互動的控制項。 如果沒有控制項，開始播放影片的唯一方法就是使用 [自動播放] 屬性或透過 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果 [自動播放] 為 true，則當頁面載入時，影片就會立即開始播放 (而不會與使用者) 互動。

> [!NOTE]
> Windows Phone、Apple iOS 和 Android 等行動裝置不支援此選項。 行動裝置會封鎖自動播放功能，以避免過度使用取用者的每月資料方案 (通常耗用昂貴的) 。 在此情況下，需要使用者觸控/按一下才能啟動影片。

`<video autoplay ...>`或 `{ "autoplay": true }`

### <a name="poster"></a>海報 ###
海報屬性會設定在影片開始播放之前顯示的影像。 這通常是影片或自訂標題畫面的畫面格。 一旦使用者按下 [播放]，映射就會消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

Width 屬性會設定影片的顯示寬度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>身高 ###

Height 屬性會設定影片的顯示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>外掛程式 ###

外掛程式 JSON 會決定使用該實例載入的外掛程式，可讓您設定外掛程式可能具有的任何選項。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

如需外掛程式開發和使用方式的詳細資訊，請參閱 [撰寫外掛程式](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他選項 ###

您可以 `<video>` 使用 `data-setup` 採用 JSON 的參數，在標記上設定其他選項。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

這會明確設定為 false。 藉由將設定為 false，可讓 Azure 媒體播放機的面板在各平臺之間呈現相同的外觀。  此外，與名稱相反，仍會啟用觸控。

### <a name="fluid"></a>流體 ###

藉由將此選項設定為 true video 元素，將會填滿父容器的寬度，並將高度調整為符合具有標準16:9 外觀比例的影片。

`<video ... data-setup='{"fluid": true}'>`

`fluid` 選項會覆寫明確 `width` 和 `height` 設定。 此選項僅適用于 Azure 媒體播放機版 `2.0.0` 和更新版本。

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` 選項會控制使用者可用的 playbackSpeed 控制項和播放速度設定。 `playbackSpeed` 取得物件。 若要在控制列上啟用 [播放速度] 控制項，必須將 `enabled` 物件的屬性設為 true。 在標記中啟用播放速度的範例：

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


設定的其他屬性 `playbackSpeed` 是由 [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) 物件提供。

以 JavaScript 設定播放速度選項的範例：

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

此選項僅適用于 Azure 媒體播放機2.0.0 版和更新版本。

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

此 `staleDataTimeLimitInSec` 選項是一項優化，可讓您設定您想要保留在 mediaSource 緩衝區中的過時資料的秒數。 此選項預設為停用狀態。

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

將 [啟用] 設定為 true，可讓您在即時串流和即時封存中顯示即時 CEA 字幕。 標籤屬性不是必要的，如果未包含，播放程式將會回復為預設標籤。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此選項僅適用于 Azure 媒體播放機版2.1.1 和更新版本。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)
