---
title: Azure 媒體播放機 API 方法
description: Azure 媒體播放機 API 可讓您透過 JavaScript 與影片互動，不論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他支援的播放技術來播放影片。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "81727264"
---
# <a name="api"></a>API #

Azure 媒體播放機 API 可讓您透過 JavaScript 與影片互動，不論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他支援的播放技術來播放影片。

## <a name="referencing-the-player"></a>參照播放機 ##

若要使用 API 函式，您需要有 player 物件的存取權。 幸運的是，很容易就能取得。 您只需要確定您的影片標記具有識別碼。 範例內嵌程式碼的識別碼為 `vid1` 。 如果您在單一頁面上有多個影片，請確定每個 video 標記都有唯一的識別碼。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果播放程式尚未透過資料安裝屬性或其他方法進行初始化，這也會初始化播放程式。

## <a name="wait-until-the-player-is-ready"></a>等到玩家準備就緒 ##

設定影片和 API 所 Azure 媒體播放機需的時間，會根據所使用的播放技術而有所不同。 HTML5 的載入速度通常會比 Flash 或 Silverlight 快很多。 基於這個理由，應該使用播放程式的「就緒」功能來觸發任何需要播放程式 API 的程式碼。

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

或者

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API 方法 ##

現在您可以存取就緒的播放程式，您可以控制影片、取得值或回應影片事件。 Azure 媒體播放機 API 函數名稱會嘗試遵循[HTML5 媒體 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要差異在於 getter/setter 函式會用於影片屬性。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>註冊事件 ##
事件應該在第一次初始化播放程式之後直接註冊，以確保所有事件都會適當地回報給應用程式，而且應該在「就緒」事件以外完成。

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