---
title: 使用 Video.js 播放機搭配 Azure 媒體服務
description: 本文說明如何搭配使用 HTML video 物件和 JavaScript 與 Azure 媒體服務
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: a0b357705de04ed4c2be3223f9dd07f61f75d970
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954575"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>如何搭配 Azure 媒體服務使用 Video.js 播放機

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>概觀

Video.js 是專為 HTML5 世界打造的 web 影片播放程式。 它會在瀏覽器中播放自動調整媒體格式 (例如破折號和 HLS) ，而不需要使用外掛程式或 Flash。 相反地，Video.js 會使用開放式 web 標準 MediaSource 延伸模組和加密的媒體延伸模組。 此外，它也支援在桌上型電腦和行動裝置上播放影片。

您可以在中找到它的官方檔 [https://docs.videojs.com/](https://docs.videojs.com/) 。

## <a name="sample-code"></a>範例程式碼
本文的範例程式碼可在 Azure 中取得 [-範例/媒體服務-協力廠商-播放機-範例](https://github.com/Azure-Samples/media-services-3rdparty-player-samples)。

## <a name="implement-the-player"></a>執行播放機

1. 建立 `index.html` 您將在其中裝載播放程式的檔案。 新增下列幾行程式碼 (您可以在適用的) 中取代較新的版本：

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. 新增 `index.js` 具有下列程式碼的檔案：

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. `manifestUrl`以 HLS 或虛線 URL 取代您資產的串流定位器，您可以在 Azure 入口網站的 [串流定位器] 頁面找到這些 URL。
    ![串流定位器 Url](media/how-to-shaka-player/streaming-urls.png)

4. 取代 `protocolType` 為下列選項：

    - 適用于 HLS 通訊協定的 "application/mpegURL"
    - 適用于虛線通訊協定的 "application/破折號 + xml"

### <a name="set-up-captions"></a>設定標題

執行 `addRemoteTextTrack` 方法，並取代：

- `subtitleKind` 使用 `"captions"` 、、 `"subtitles"` `"descriptions"` 或 `"metadata"`  
- `caption` 使用 vtt 檔案路徑時 (vtt 檔必須位於相同的主機中，以避免 CORS 錯誤) 
- `subtitleLang` 以適用于語言的 BCP 47 程式碼為例，例如 `"eng"` 英文或 `"es"` 西班牙文
- `subtitleLabel` 使用您想要的標題顯示名稱

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>設定權杖驗證

權杖必須在要求標頭的授權欄位中設定。 為了避免發生 CORS 問題，此權杖必須只在其 URL 中的這些要求中設定 `'keydeliver'` 。 下列程式程式碼應執行工作：

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

然後，上述函數必須附加至 `videojs.Hls.xhr.beforeRequest` 事件。

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>設定 AES-128 加密

Video.js 支援不含任何其他設定的 AES-128 加密。 

> [!NOTE] 
> 目前無法播放加密與 HLS/虛線 CMAF 內容的 [問題](https://github.com/videojs/video.js/issues/6717) 。

### <a name="set-up-drm-protection"></a>設定 DRM 保護

為了支援 DRM 保護，您必須新增 [videojs-contrib-eme](https://github.com/videojs/videojs-contrib-eme) 官方延伸模組。 CDN 版本也可以運作。

1. 在上述檔案中 `index.js` ，您必須在 `videoJS.eme();` 新增影片的來源 *之前* 加入，以初始化 EME 延伸模組：

   ```javascript
    videoJS.eme();
   ```

2. 現在您可以定義 DRM 服務的 Url，以及對應授權的 Url，如下所示：

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>取得授權 URL

為了取得授權 URL，您可以：

- 請參閱您的 DRM 提供者設定
- 或者，如果您使用範例，請參閱 `output.json` 執行 VODs 的 *setup-vod.ps1* PowerShell 腳本時所產生的檔，或即時資料流的 *start-live.ps1* 腳本。 您也可以在此檔案中找到小孩。

#### <a name="using-tokenized-drm"></a>使用 token 化 DRM

為了支援 token 化 DRM 保護，您必須將下列這一行新增至播放程式的 `src` 屬性：

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>下一步

- [使用 Azure 媒體播放機](../azure-media-player/azure-media-player-overview.md)  
- [快速入門：加密內容](encrypt-content-quickstart.md)
