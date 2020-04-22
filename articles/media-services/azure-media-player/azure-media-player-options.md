---
title: Azure 媒體播放器選項
description: Azure 媒體播放器嵌入代碼只是一個 HTML5 視訊標記,因此對於許多選項,您可以使用標準標記屬性來設置選項。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727160"
---
# <a name="options"></a>選項 #

## <a name="setting-options"></a>設定選項 ##

Azure 媒體播放器嵌入代碼只是一個 HTML5 視訊標記,因此對於許多選項,您可以使用標準標記屬性來設置選項。

`<video controls autoplay ...>`

或者,您可以使用數據設置屬性以[JSON](http://json.org/example.html)格式提供選項。 這也是如何設置不標準到視頻標記的選項。

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

最後,如果您不使用數據設置屬性來觸發播放機設置,則可以將具有播放器選項的物件作為 JAVAScript 設定函數中的第二個參數傳遞。

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> 建構函數中的選項僅在設置源之前在第一個初始化上設置。  如果要修改同一初始化 Azure 媒體播放器元素上的選項,則必須在更改源之前更新這些選項。 您可以使用 更新 JavaScript`myPlayer.options({/*updated options*/});`中的選項 。 請注意,只有更改的選項才會受到影響,所有其他以前設置的選項將保持不變。

## <a name="individual-options"></a>單個選項 ##

> [!NOTE]
>視頻標記屬性只能是真或假(布林),您只需包括屬性(無等於符號)來打開它,或排除它來將其關閉。 例如,打開控制項:錯誤`<video controls="true" ...>``<video controls ...>`正確 遇到的最大問題是,使用 false 作為值(例如控制項="false")將這些值設定為 false,該值實際上執行相反操作並將值設定為 true,因為屬性仍包含在內。

### <a name="controls"></a>controls ###

控制項選項設定播放器是否具有使用者可以與之互動的控制項。 如果沒有控制項,啟動影片播放的唯一方法是使用自動播放屬性或透過 API。

`<video controls ...>` 或 `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

如果自動播放為 true,則視訊將在載入頁面後立即開始播放(無需用戶進行任何互動)。

> [!NOTE]
> Windows 手機、Apple iOS 和 Android 等行動裝置不支援此選項。 移動設備阻止自動播放功能,以防止消費者每月數據計劃過度使用(通常成本高昂)。 在這種情況下,需要使用者觸摸/單擊才能啟動視頻。

`<video autoplay ...>`或`{ "autoplay": true }`

### <a name="poster"></a>海報 ###
海報屬性設置在視頻開始播放之前顯示的圖像。 這通常是視頻或自定義標題螢幕的框架。 使用者一點擊播放,圖像就會消失。

`<video poster="myPoster.jpg" ...>` 或 `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

寬度屬性設置視頻的顯示寬度。

`<video width="640" ...>` 或 `{ "width": 640 }`

### <a name="height"></a>height ###

高度屬性設置視頻的顯示高度。

`<video height="480" ...>` 或 `{ "height": 480 }`

### <a name="plugins"></a>外掛程式 ###

外掛程式 JSON 確定哪些外掛程式載入 AMP 實體,允許您設定外掛程式可能具有的任何選項。

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

有關外掛程式開發和使用的詳細資訊,請參閱[編寫外掛程式](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>其他選項 ###

可以使用採用 JSON`<video>``data-setup`的 參數在標記上設置其他選項。
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>本機控制ForTouch ####

這顯式設置為 false。 通過將設置為 false,它將允許跨平台呈現 Azure 媒體播放器外觀。  此外,與名稱相反,仍可啟用觸摸。

### <a name="fluid"></a>流體 ###

通過將此選項設置為 true 視頻元素,將採用父容器的完整寬度,並且高度將進行調整,以適應具有標準 16:9 縱橫比的視頻。

`<video ... data-setup='{"fluid": true}'>`

`fluid`選項覆蓋顯式`width`和`height`設置。 此選項僅在 Azure 媒體播放`2.0.0`器版本和更高版本中可用。

### <a name="playbackspeed"></a>播放速度 ###

`playbackSpeed`選項控制播放速度控制和一組可供使用者使用的播放速度設置。 `playbackSpeed`獲取物件。 為了在控制列上啟用播放速度控制,需要將物件的`enabled`屬性設置為 true。 在標記中開啟播放速度的範例:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


`playbackSpeed`設置的其他屬性由[PlaybackSpeedOptions](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)物件提供。

在 JavaScript 中設定播放速度選項的範例:

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

此選項僅在 Azure 媒體播放器版本 2.0.0 及更高版本中可用。

### <a name="staledatatimelimitinsec"></a>陳舊的資料時間限值 ###

該`staleDataTimeLimitInSec`選項是一個優化,允許您設定要在 mediaSource 緩衝區中保留多少秒的過時數據。 此選項預設為停用狀態。

### <a name="cea708captionssettings"></a>cea708 標題設定 ###

啟用為 true 的設定允許您在即時串流和即時存檔中顯示即時 CEA 字幕。 標籤屬性不是必需的,如果未包含,播放機將回退到預設標籤。

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

此選項僅在 Azure 媒體播放器版本 2.1.1 及更高版本中可用。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)