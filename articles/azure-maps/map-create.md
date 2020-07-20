---
title: 建立具有 Azure 地圖服務的對應 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在網頁上呈現地圖。
author: Philmea
ms.author: philmea
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 97eb1ebb61e5ff78ed918fded8107f5775b533c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "83124018"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="loading-a-map"></a>載入對應

若要載入對應，請建立[map 類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)的新實例。 在初始化對應時，傳遞 DIV 元素識別碼來轉譯對應，並傳遞一組要在載入對應時使用的選項。 如果命名空間未指定預設驗證資訊 `atlas` ，載入對應時，必須在對應選項中指定這項資訊。 對應會以非同步方式載入數個資源，以提高效能。 因此，建立 map 實例之後，請將 `ready` 或事件附加 `load` 至對應，然後將與對應互動的任何額外程式碼加入至事件處理常式。 `ready`當對應的載入資源足以與程式互動時，就會引發事件。 在 `load` 初始對應視圖完全載入完成後，就會引發事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地圖載入" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 CodePen 上的 Azure 地圖服務（）的畫筆<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地圖載入</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

> [!TIP]
> 您可以在相同的頁面上載入多個對應。 相同頁面上的多個對應可能會使用相同或不同的驗證和語言設定。

## <a name="show-a-single-copy-of-the-world"></a>顯示單一的世界複本

當地圖在寬螢幕上放大時，會以水準方式顯示多個世界複本。 此選項適用于某些案例，但對於其他應用程式，您可以看到一份世界的單一複本。 這個行為是藉由將 maps `renderWorldCopies` 選項設定為來執行 `false` 。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 CodePen 上 Azure 地圖服務（）的 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>


## <a name="map-options"></a>對應選項

在該處建立對應時，有數種不同類型的選項可傳入，以自訂下面所列的地圖功能。

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)和[CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions)是用來指定地圖應該顯示的區域。
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions)是用來指定地圖應如何與支援地圖的服務互動。
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)用來指定對應的樣式和呈現方式。
- [UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions)是用來指定當使用者與地圖互動時，地圖應如何觸及。 

您也可以在使用 `setCamera` 、、和函式載入對應之後，更新這些選項 `setServiceOptions` `setStyle` `setUserInteraction` 。 

## <a name="controlling-the-map-camera"></a>控制地圖攝影機

有兩種方式可以使用地圖的相機來設定地圖的顯示區域。 您可以在載入對應時設定相機選項。 或者，您可以在 `setCamera` 對應載入之後隨時呼叫選項，以程式設計方式更新地圖視圖。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

地圖攝影機可控制地圖畫布的視口中顯示的內容。 相機選項可以在初始化或傳遞至 maps 函式時，傳遞至地圖選項 `setCamera` 。

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

在下列程式碼中，會建立[地圖物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)，並設定置中和縮放選項。 地圖屬性（例如置中和縮放層級）是[CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)的一部分。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>透過  建立地圖`CameraOptions` </a>，發佈者：以 Azure 位置為基礎的服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

周框方塊可以用來更新地圖攝影機。 如果周框方塊是從點資料計算的，通常也可以在相機選項中指定圖元填補值，以考慮圖示大小。 這將有助於確保點不會落在地圖區的邊緣。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

在下列程式碼中， [Map 物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)是透過所建立 `new atlas.Map()` 。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>透過  建立地圖`CameraBoundsOptions` </a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

設定地圖的相機選項時，也可以設定[動畫選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions)。 這些選項會指定移動相機所應採取的動畫類型與持續時間。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

在下列程式碼中，第一個程式碼區塊會建立地圖，並設定 [輸入] 和 [縮放] 地圖樣式。 在第二個程式碼區塊中，會建立 [動畫] 按鈕的 click 事件處理常式。 按一下此按鈕時， `setCamera` 會使用[CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)和[AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)的一些隨機值來呼叫函數。

<br/>

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="try-out-the-code"></a>試用程式碼

查看程式碼範例。 您可以編輯 [ **JS]** 索引標籤內的 JavaScript 程式碼，並在 [**結果]** 索引標籤上查看地圖視圖的變更。您也可以在右上角按一下**CodePen 上**的 [編輯]，然後修改 CodePen 中的程式碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [變更地圖樣式](choose-map-style.md)

> [!div class="nextstepaction"]
> [將控制項新增至地圖](map-add-controls.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
