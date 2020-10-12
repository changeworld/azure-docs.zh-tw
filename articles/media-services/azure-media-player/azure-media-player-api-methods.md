---
title: Azure 媒體播放機 API 方法
description: 無論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他支援的播放技術來播放影片，Azure 媒體播放機 API 都可讓您透過 JavaScript 與影片互動。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "81727264"
---
# <a name="api"></a>API #

無論瀏覽器是透過 HTML5 影片、Flash、Silverlight 或任何其他支援的播放技術來播放影片，Azure 媒體播放機 API 都可讓您透過 JavaScript 與影片互動。

## <a name="referencing-the-player"></a>參考播放 ##

若要使用 API 函式，您需要存取 player 物件。 幸運的是，您很容易就能取得。 您只需要確定您的影片標記具有識別碼。 內嵌程式碼範例的識別碼為 `vid1` 。 如果您在單一頁面上有多部影片，請確定每個影片標記都有唯一的識別碼。

`var myPlayer = amp('vid1');`

> [!NOTE]
> 如果播放程式尚未透過資料安裝屬性或其他方法初始化，這也會初始化播放程式。

## <a name="wait-until-the-player-is-ready"></a>等候播放程式就緒 ##

Azure 媒體播放機設定影片和 API 所需的時間，會根據所使用的播放技術而有所不同。 HTML5 通常會比 Flash 或 Silverlight 更快載入。 基於這個理由，播放程式的 ' ready ' 函式應該用來觸發任何需要播放機 API 的程式碼。

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

現在您已可存取就緒的播放程式，您可以控制影片、取得值或回應影片事件。 Azure 媒體播放機 API 函式名稱會嘗試遵循 [HTML5 媒體 API](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html)。 主要差異在於 getter/setter 函式是用於影片屬性。

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>註冊事件 ##
您應該在第一次初始化播放程式之後直接註冊事件，以確保所有事件都已適當地回報給應用程式，並應在就緒事件之外進行。

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