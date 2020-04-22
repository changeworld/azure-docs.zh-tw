---
title: Azure 媒體播放器 API 方法
description: Azure 媒體播放器 API 允許您透過 JavaScript 與影片進行互動,無論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他受支援的播放技術播放影片。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727264"
---
# <a name="api"></a>API #

Azure 媒體播放器 API 允許您透過 JavaScript 與影片進行互動,無論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他受支援的播放技術播放影片。

## <a name="referencing-the-player"></a>參考播放器 ##

要使用 API 函數,您需要存取播放器物件。 幸運的是,這是很容易得到。 您只需確保您的影片標籤具有 ID。 範例嵌入代碼的識別碼`vid1`。 如果一個頁面上有多個視頻,請確保每個視頻標記都具有唯一的 ID。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果玩家尚未通過數據設置屬性或其他方法初始化,這也將初始化該播放機。

## <a name="wait-until-the-player-is-ready"></a>等待玩家準備就緒 ##

設置視頻和 API 所需的時間因正在使用的播放技術而異。 HTML5 的載入速度通常比快閃記憶體或銀光快得多。 因此,玩家的「就緒」功能應用於觸發任何需要玩家 API 的代碼。

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OR

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 方法 ##

現在,您可以訪問現成的播放機,您可以控制視頻、獲取值或回應視頻事件。 Azure 媒體播放器 API 函式名稱試著遵循[HTML5 媒體 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要區別是 getter/setter 函數用於視頻屬性。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>註冊活動 ##
事件應在首次初始化播放器后直接註冊,以確保所有事件都適當地報告給應用程式,並且應在就緒事件之外完成。

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>後續步驟 ##

<!---Some context for the following links goes here--->
- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)