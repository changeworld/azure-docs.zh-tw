---
title: 建立具有 Azure 地圖服務的對應 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在網頁上呈現地圖。
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988578"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="loading-a-map"></a>載入對應

若要載入對應，請建立[map 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)的新實例。 在初始化對應時，傳遞 DIV 元素識別碼來轉譯對應，並傳遞一組要在載入對應時使用的選項。 如果未在 `atlas` 命名空間上指定預設驗證資訊，載入對應時，就必須在對應選項中指定這項資訊。 對應會以非同步方式載入數個資源，以提高效能。 因此，建立 map 實例之後，請將 `ready` 或 `load` 事件附加至對應，然後將與對應互動的任何額外程式碼加入至事件處理常式。 當對應的資源載入足以與程式互動時，`ready` 事件就會引發。 在初始對應視圖完全載入完成後，就會引發 `load` 事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地圖載入" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的畫筆<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地圖載入</a>。
</iframe>

> [!TIP]
> 您可以在相同的頁面上載入多個對應。 相同頁面上的多個對應可能會使用相同或不同的驗證和語言設定。

## <a name="show-a-single-copy-of-the-world"></a>顯示單一的世界複本

當地圖在寬螢幕上放大時，會以水準方式顯示多個世界複本。 此選項適用于某些案例，但對於其他應用程式，您可以看到一份世界的單一複本。 這種行為的執行方式是將 map `renderWorldCopies` 選項設定為 `false`。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> 。
</iframe>

## <a name="controlling-the-map-camera"></a>控制地圖攝影機

有兩種方式可以使用地圖的相機來設定地圖的顯示區域。 您可以在載入對應時設定相機選項。 或者，您可以在對應載入之後，隨時呼叫 `setCamera` 選項，以程式設計方式更新地圖視圖。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

在下列程式碼中，會建立[地圖物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)，並設定置中和縮放選項。 地圖屬性（例如置中和縮放層級）是[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)的一部分。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 位置服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>） `CameraOptions` 的畫筆<a href='https://codepen.io/azuremaps/pen/qxKBMN/'>建立地圖</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

在下列程式碼中， [Map 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)是透過 `new atlas.Map()`來建立。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），透過 `CameraBoundsOptions` 的畫筆<a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>建立地圖</a>。
</iframe>

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

在下列程式碼中，第一個程式碼區塊會建立地圖，並設定 [輸入] 和 [縮放] 地圖樣式。 在第二個程式碼區塊中，會建立 [動畫] 按鈕的 click 事件處理常式。 按一下此按鈕時，會使用[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)和[AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)的一些隨機值來呼叫 `setCamera` 函式。

<br/>

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="try-out-the-code"></a>試用程式碼

查看程式碼範例。 您可以編輯 [ **JS]** 索引標籤內的 JavaScript 程式碼，並在 [**結果]** 索引標籤上查看地圖視圖的變更。您也可以在右上角按一下**CodePen 上**的 [編輯]，然後修改 CodePen 中的程式碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [變更地圖樣式](choose-map-style.md)

> [!div class="nextstepaction"]
> [將控制項新增至對應](map-add-controls.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
