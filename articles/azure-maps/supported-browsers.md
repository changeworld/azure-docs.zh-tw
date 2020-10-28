---
title: Web SDK 支援的瀏覽器 |Microsoft Azure 對應
description: 瞭解如何檢查 Azure 地圖服務 Web SDK 是否支援瀏覽器。 查看支援的瀏覽器清單。 瞭解如何搭配使用地圖服務與舊版瀏覽器。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 43bf70d66c42bc0ecd5e26e4cc724456bd4bf84e
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896917"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支援的瀏覽器

Azure 地圖服務 Web SDK 提供名為 [isSupported](/javascript/api/azure-maps-control/atlas#issupported-boolean-)的 helper 函數。 此函式會偵測網頁瀏覽器是否有支援載入和轉譯地圖控制項所需的最小一組 WebGL 功能。 以下是如何使用函數的範例：

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌上型

Azure 地圖服務 Web SDK 支援下列桌面瀏覽器：

- Microsoft Edge (目前版本和舊版) 
- Google Chrome (目前版本和舊版) 
- Mozilla Firefox (目前版本和舊版) 
- Apple Safari (Mac OS X)  (最新版本和舊版) 

請參閱本文稍後的將目標設為 [舊版瀏覽器](#Target-Legacy-Browsers) 。

## <a name="mobile"></a>行動

Azure 地圖服務 Web SDK 支援下列行動瀏覽器：

- Android
  - Android 6.0 和更新版本上的最新版 Chrome
  - Android 6.0 和更新版本上的 Chrome Web 視圖
- iOS
  - IOS 上目前和先前主要版本的 Mobile Safari
  - 目前和先前主要版本 iOS 的 UIWebView 和 WKWebView
  - 適用于 iOS 的 Chrome 目前版本

> [!TIP]
> 如果您要使用 web 程式控制項在行動應用程式中內嵌地圖，您可能會想要使用 [Azure 地圖服務 WEB SDK 的 npm 套件](https://www.npmjs.com/package/azure-maps-control) ，而不是參考 Azure 內容傳遞網路上裝載的 SDK 版本。 這種方法可縮短載入時間，因為 SDK 已在使用者的裝置上，而不需要在執行時間下載。

## <a name="nodejs"></a>Node.js

Node.js 中也支援下列 Web SDK 模組：

- 服務模組 ([檔](how-to-use-services-module.md)  |  [npm 模組](https://www.npmjs.com/package/azure-maps-rest)) 

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>以舊版瀏覽器為目標

您可能想要以不支援 WebGL 或只有有限支援的舊版瀏覽器為目標。 在這種情況下，我們建議您搭配使用 Azure 地圖服務服務與開放原始碼的地圖控制項（例如 [Leaflet](https://leafletjs.com/)）。 以下為範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地圖服務 + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 >codepen 上的 <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地圖服務 + Leaflet</a> ，Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>) 。
</iframe>


## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 地圖服務 Web SDK：

[地圖控制項](how-to-use-map-control.md)

[服務模組](how-to-use-services-module.md)