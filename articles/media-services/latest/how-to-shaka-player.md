---
title: 如何搭配 Azure 媒體服務使用 Shaka 播放機
description: 本文說明如何搭配使用 Shaka 播放機與 Azure 媒體服務
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/23/2020
ms.author: inhenkel
ms.custom: devx-track-javascript
ms.openlocfilehash: 5882b79232e858bbc8ad7e0da94ad4b04f5165ca
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/29/2020
ms.locfileid: "87422975"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>如何搭配 Azure 媒體服務使用 Shaka 播放機

## <a name="overview"></a>概觀

Shaka Player 是適用于彈性媒體的開放原始碼 JavaScript 程式庫。 它會在瀏覽器中播放適應性媒體格式（例如破折號和 HLS），而不需要使用外掛程式或 Flash。 相反地，Shaka Player 會使用開放式 web 標準媒體來源延伸模組和加密媒體延伸模組。

我們建議使用[Mux.js](https://github.com/videojs/mux.js/) ，但若不這麼做，Shaka player 會支援 HLS CMAF 格式，但無法 HLS TS。

您可以在[Shaka player 檔](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html)中找到其官方檔。

## <a name="sample-code"></a>範例程式碼
本文的範例程式碼位於[Azure-範例/媒體-服務-3rdparty-播放程式範例](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)。

## <a name="implementing-the-player"></a>執行 player

如果您需要執行自己的播放程式實例，請遵循下列指示：

1. 建立 `index.html` 要裝載播放程式的檔案。 新增下列幾行程式碼（如果適用，您可以取代較新的版本）：

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. 使用下列程式碼新增 JavaScript 檔案：

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. 將取代 `manifestUrl` 為您資產的串流定位器中的 HLS 或破折號 URL，可以在 Azure 入口網站的串流定位器頁面上找到。
    ![串流定位器 Url](media/how-to-shaka-player/streaming-urls.png)

1. 執行伺服器（例如使用 `npm http-server` ），而您的播放程式應該正在運作 .。。

## <a name="set-up-captions"></a>設定標題

### <a name="set-up-vod-captions"></a>設定 VOD 標題

執行下列幾行程式碼，並將取代 `captionUrl` 為您的 vtt 目錄（vtt 檔案必須位於相同的主控制項，以避免 CORS 錯誤）、 `lang` 語言的兩個字母代碼，以及 `type` `caption` 或 `subtitle` ：

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>設定即時資料流標題

啟用即時資料流中的標題時，會將下列程式程式碼設定為：

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>設定權杖驗證

執行下列幾行程式碼，並 `token` 以您的 token 字串取代：

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>設定 AES-128 加密

Shaka Player 目前不支援 AES-128 加密。

GitHub[問題](https://github.com/google/shaka-player/issues/850)的連結，以遵循這項功能的狀態。

## <a name="set-up-drm-protection"></a>設定 DRM 保護

Shaka Player 會使用加密的媒體延伸模組（EME），這需要安全的 URL 才能使用。 因此，若要測試任何受 DRM 保護的內容，則必須使用 HTTPs。 如果網站使用 HTTPs，則資訊清單和每個區段也都必須使用 HTTPs。 這是因為混合內容的需求。

對其授權伺服器的 URL 進行 Shaka 管理的喜好設定順序：

1. ClearKey config 用於進行偵錯工具，應該覆寫所有其他專案。 （應用程式仍可指定 ClearKey 授權伺服器）。
2. 應用程式設定的伺服器（如果有的話）應該覆寫資訊清單中的任何專案。
3. 只有在未指定任何其他內容時，才會使用資訊清單提供的授權伺服器。

若要指定 Widevine 或 PlayReady 的授權伺服器 URL，我們可以使用下列程式碼：

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

所有 FairPlay 內容都需要設定伺服器憑證。 它是在 Player 設定中設定：

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

如需詳細資訊，請參閱[Shaka PLAYER DRM 保護檔](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html)。

## <a name="next-steps"></a>後續步驟

* [使用 Azure 媒體播放機](../azure-media-player/azure-media-player-overview.md)
* [快速入門：加密內容](encrypt-content-quickstart.md)
