---
title: Web SDK 支援的瀏覽器 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Microsoft Azure Maps Web SDK 支援的瀏覽器，以及如何檢查瀏覽器是否為支援的瀏覽器。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988782"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支援的瀏覽器

Azure 地圖服務 Web SDK 提供名為[isSupported](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)的 helper 函式。 此函式會偵測 web 瀏覽器是否有支援載入和呈現地圖控制項所需的最小 WebGL 功能集。 以下是如何使用函數的範例：

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

- Microsoft Edge （目前版本和舊版）
- Google Chrome （目前版本和舊版）
- Mozilla Firefox （目前版本和舊版）
- Apple Safari （Mac OS X）（目前版本和舊版）

另請參閱本文稍後的[目標舊版瀏覽器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>行動

Azure 地圖服務 Web SDK 支援下列行動瀏覽器：

- Android
  - Android 6.0 和更新版本上的最新 Chrome 版本
  - Android 6.0 和更新版本上的 Chrome Web 操作
- iOS
  - IOS 上目前和舊版主要版本的 Mobile Safari
  - 目前和先前的 iOS 主要版本上的 UIWebView 和 WKWebView
  - 適用于 iOS 的最新 Chrome 版本

> [!TIP]
> 如果您要使用 Web 視圖控制項在行動應用程式中內嵌對應，您可能會想要使用[Azure 地圖服務 WEB SDK 的 npm 套件](https://www.npmjs.com/package/azure-maps-control)，而不是參考 Azure 內容傳遞網路上所裝載的 SDK 版本。 這種方法可縮短載入時間，因為 SDK 已在使用者的裝置上，而不需要在執行時間下載。

## <a name="nodejs"></a>Node.js

Node.js 中也支援下列 Web SDK 模組：

- 服務模組（[檔](how-to-use-services-module.md) | [npm 模組](https://www.npmjs.com/package/azure-maps-rest)）

## <a name="Target-Legacy-Browsers"></a>以舊版瀏覽器為目標

您可能想要以不支援 WebGL 或只有有限支援的舊版瀏覽器為目標。 在這種情況下，建議您將 Azure 地圖服務服務與開放原始碼地圖控制項（例如[Leaflet](https://leafletjs.com/)）搭配使用。 以下是範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地圖服務 + Leaflet" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地圖服務 + Leaflet</a> 。
</iframe>


## <a name="next-steps"></a>後續步驟

深入瞭解 Azure 地圖服務 Web SDK：

> [!div class="nextstepaction"]
> [地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)
