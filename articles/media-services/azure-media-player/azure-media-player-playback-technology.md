---
title: Azure 媒體播放器播放技術
description: 深入了解用來播放影片或音訊的播放技術。
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9772f89f78f21cfb0f2f9baa7c6b9915fbacf37
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91275688"
---
# <a name="playback-technology-tech"></a>播放技術 (「技術」) #

播放技術指的是用來播放影片或音訊的特定瀏覽器或外掛程式技術。

- **azureHtml5JS**：將 MSE 和 EME 標準與 video 元素搭配使用，以便透過無外掛程式的方式播放 DASH 內容，並支援來自 Azure 媒體服務的 AES-128 位元信封加密內容或 DRM 一般加密內容 (透過瀏覽器所支援的 PlayReady 和 Widevine)
- **flashSS**：利用 Flash播放器技術來播放 Smooth 內容，並支援來自 Azure 媒體服務的 AES-128 位元信封解密 - 需要 Flash 11.4 版或更高版本
- **html5FairPlayHLS**：透過 HLS 與 video 元素，利用 Safari 以瀏覽器為基礎的特有播放技術。 必須有此技術才能從 Azure 媒體服務播放 FairPlay 保護的內容，且此技術已於 10/19/16 新增至 techOrder
- **silverlightSS**：利用 silverlight 技術來播放 Smooth 內容，並支援來自 Azure 媒體服務的 PlayReady 保護內容。
- **html5**：利用以瀏覽器為基礎的播放技術與 video 元素。  在 Apple iOS 或 Android 裝置上，此技術可讓您播放 HLS 串流，且基本支援 AES-128 位元信封加密或 DRM 內容 (透過瀏覽器所支援的 FairPlay)。

## <a name="tech-order"></a>技術順序 ##

為了確保您的資產可在各種不同的裝置上播放，建議您使用下列技術順序，而且如果符合下列條件，這也會是預設順序：`techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]`，您可以直接在 `<video>` 上設定，或在選項中以程式設計方式進行設定：

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

或

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>相容性矩陣 ##

根據 Azure 媒體服務的串流內容建議技術順序，預期會有下列相容性播放矩陣

| 瀏覽器        | OS                                                       | 預期技術 (Clear)  | 預期技術 (AES)  | 預期技術 (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| EdgeIE 11      | Windows 10、Windows 8.1、Windows Phone 101               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| IE 11IE 9-101  | Windows 7、Windows Vista<sup>1</sup>                     | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| IE 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | 不支援                |
| Edge           | Xbox One<sup>1</sup> (2015 年 11 月更新)                   | azureHtml5JS           | azureHtml5JS         | 不支援                |
| Chrome 37+     | Windows 10、Windows 8.1、macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47+    | Windows 10、Windows 8.1、macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10、Windows 8.1、macOS X Yosemite+<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | silverlightSS (PlayReady)    |
| Firefox 35-41  | Windows 10、Windows 8.1                                  | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Safari         | iOS 6+                                                   | html5                  | html5 (無權杖)3    | 不支援                |
| Safari 8+      | OS X Yosemite+                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | flashSS                | flashSS              | silverlightSS (PlayReady)    |
| Chrome 37+     | Android 4.4.4+<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37+     | Android 4.02                                             | html5                  | html5 (無權杖)<sup>3</sup>    | 不支援                |
| Firefox 42+    | Android 5.0+<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | 不支援                |
| IE 8           | Windows                                                  | 不支援          | 不支援        | 不支援                |

<sup>1</sup> 設定不受支援或未經過測試；列為完成參考。

<sup>2</sup> 要在 Android 裝置上成功播放，有賴裝置功能、圖形支援、轉碼器轉譯、OS 支援等各個層面的結合。 由於 Android 是一個開放原始碼平台，可讓電話製造商變更 Google 所提供的 Vanilla Android OS，因此會造成 Android 空間有一些破裂，某些裝置可能會因為缺少功能而不受支援。 此外，有些 Android 裝置並未支援所有轉碼器。  

<sup>3</sup> 在未支援權杖的情況下，可使用 Proxy 來新增這項功能。 若要深入了解此解決方案，請參閱此[部落格](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/)。

> [!NOTE]
> 如果所選的預期技術需要安裝外掛程式 (例如 Flash)，但使用者的機器上卻未安裝，AMP 會繼續查看技術清單中下一項技術的功能，並搭配來源類型和保護資訊。 例如，如果嘗試在 OS X Yosemite 上的 Safari 8 中檢視未受保護的隨選串流，而且 Flash 和 Silverlight 皆未安裝，AMP 便會選取原生 Html5 技術來進行播放。<br/><br/>瀏覽器技術日新月異，因此可能會影響此矩陣。

## <a name="next-steps"></a>後續步驟 ##

- [Azure 媒體播放器快速入門](azure-media-player-quickstart.md)