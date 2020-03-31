---
title: 支援 Web SDK 的瀏覽器 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 地圖 Web SDK 受支援的瀏覽器以及如何檢查瀏覽器是否受支援的瀏覽器。
author: rbrundritt
ms.author: richbrun
ms.date: 03/25/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: e81b15b974469d319384a67b08512130b7876a30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988782"
---
# <a name="web-sdk-supported-browsers"></a>Web SDK 支援的瀏覽器

Azure 地圖 Web SDK 提供了一個稱為[atlas.is 支援的](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas?view=azure-iot-typescript-latest#issupported-boolean-)説明器函數。 此功能檢測 Web 瀏覽器是否具有支援載入和呈現地圖控制項所需的最小 WebGL 功能集。 下面是如何使用 函數的示例：

```JavaScript
if (!atlas.isSupported()) {
    alert('Your browser is not supported by Azure Maps');
} else if (!atlas.isSupported(true)) {
    alert('Your browser is supported by Azure Maps, but may have major performance caveats.');
} else {
    // Your browser is supported. Add your map code here.
}
```

## <a name="desktop"></a>桌面

Azure 映射 Web SDK 支援以下桌面瀏覽器：

- 微軟邊緣（當前版本和以前的版本）
- 谷歌Chrome（當前版本和以前的版本）
- Mozilla 火狐（當前和以前的版本）
- 蘋果野生動物園（Mac OS X）（當前和以前的版本）

請參閱本文後面的[目標舊版瀏覽器](#Target-Legacy-Browsers)。

## <a name="mobile"></a>行動訊息

Azure 地圖 Web SDK 支援以下移動瀏覽器：

- Android
  - Android 6.0 及更高版本的 Chrome
  - 在 Android 6.0 及更高版本上查看 Chrome 網路視圖
- iOS
  - iOS 當前和以前的主要版本的移動 Safari
  - UIWebView 和 WKWebView 在當前和以前的 iOS 主要版本上
  - 適用于 iOS 的當前版本的 Chrome

> [!TIP]
> 如果使用 WebView 控制項將地圖嵌入移動應用程式中，則可能更喜歡使用 Azure 地圖 Web SDK[的 npm 包](https://www.npmjs.com/package/azure-maps-control)，而不是引用 Azure 內容交付網路上託管的 SDK 版本。 此方法減少了載入時間，因為 SDK 已在使用者的設備上，並且不需要在運行時下載。

## <a name="nodejs"></a>Node.js

Node.js 中還支援以下 Web SDK 模組：

- 服務模組 （[文檔](how-to-use-services-module.md) | [npm 模組](https://www.npmjs.com/package/azure-maps-rest)）

## <a name="target-legacy-browsers"></a><a name="Target-Legacy-Browsers"></a>目標舊版瀏覽器

您可能希望將目標對準不支援 WebGL 或僅對 WebGL 支援有限的較舊的瀏覽器。 在這種情況下，我們建議您將 Azure 地圖服務與開源地圖控制項（如["傳單"）](https://leafletjs.com/)一起使用。 以下是範例：

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Azure 地圖 + 傳單" src="//codepen.io/azuremaps/embed/GeLgyx/?height=500&theme-id=0&default-tab=html,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 顯示的筆<a href='https://codepen.io/azuremaps/pen/GeLgyx/'>Azure 地圖和傳單</a>。
</iframe>


## <a name="next-steps"></a>後續步驟

瞭解有關 Azure 地圖 Web SDK 的更多內容：

> [!div class="nextstepaction"]
> [地圖控制項](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [服務模組](how-to-use-services-module.md)
