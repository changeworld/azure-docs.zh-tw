---
title: 使用 Azure 地圖服務建立地圖 |Microsoft Azure 對應
description: 瞭解如何使用 Azure 地圖服務 Web SDK 將地圖新增至網頁。 瞭解動畫、樣式、相機、服務和使用者互動的選項。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 833b6413cc5dfde1129075a286e5fe93a06e159f
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890916"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="loading-a-map"></a>載入對應

若要載入對應，請建立 [map 類別](/javascript/api/azure-maps-control/atlas.map)的新實例。 初始化對應時，傳遞 DIV 元素識別碼以轉譯地圖，並傳遞一組選項以在載入地圖時使用。 如果未在命名空間上指定預設驗證資訊，則在 `atlas` 載入對應時，必須在對應選項中指定這項資訊。 地圖會以非同步方式載入數個資源以獲得效能。 如此一來，在建立對應實例之後，請將 `ready` 或 `load` 事件附加至對應，然後將與對應互動的任何其他程式碼加入至事件處理常式。 當 `ready` 對應的資源載入足以以程式設計方式進行互動時，就會引發此事件。 `load`初始地圖視圖完全載入完成之後，就會引發此事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地圖載入" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆 <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地圖載入</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

> [!TIP]
> 您可以在相同頁面上載入多個對應。 相同頁面上的多個對應可能會使用相同或不同的驗證和語言設定。

## <a name="show-a-single-copy-of-the-world"></a>顯示一份世界

當地圖在寬螢幕上放大時，會以水準方式顯示多個世界複本。 此選項在某些案例中很有用，但是對於其他應用程式來說，最好是看到一份世界的複本。 此行為是藉由將 maps `renderWorldCopies` 選項設定為來實現 `false` 。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
請參閱 >codepen 上的 Azure 地圖服務 () ，查看 Pen <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>


## <a name="map-options"></a>對應選項

在那裡建立對應時，有數種不同類型的選項可供傳入，以自訂對應的運作方式，如下所示。

- [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 和 [CameraBoundOptions](/javascript/api/azure-maps-control/atlas.cameraboundsoptions) 是用來指定地圖應該顯示的區域。
- [ServiceOptions](/javascript/api/azure-maps-control/atlas.serviceoptions) 可用來指定地圖應該如何與支援地圖的服務互動。
- [StyleOptions](/javascript/api/azure-maps-control/atlas.styleoptions) 用來指定應將地圖設為樣式和呈現。
- [UserInteractionOptions](/javascript/api/azure-maps-control/atlas.userinteractionoptions) 可用來指定當使用者與地圖互動時，地圖應該如何到達。 

使用 `setCamera` 、 `setServiceOptions` 、 `setStyle` 和函數載入對應之後，也可以更新這些選項 `setUserInteraction` 。 

## <a name="controlling-the-map-camera"></a>控制地圖攝影機

有兩種方式可以使用地圖的相機來設定地圖的顯示區域。 您可以在載入地圖時設定相機選項。 或者，您可以在 `setCamera` 地圖載入之後隨時呼叫選項，以程式設計方式更新地圖視圖。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

地圖攝影機可控制地圖畫布的區中顯示的內容。 當您將相機選項初始化或傳遞至 maps 函式時，可以將相機選項傳遞至地圖選項中 `setCamera` 。

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

在下列程式碼中，會建立 [地圖物件](/javascript/api/azure-maps-control/atlas.map) ，並設定置中和縮放選項。 地圖屬性（例如置中和縮放層級）是 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions)的一部分。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>透過  建立地圖`CameraOptions` </a>，發佈者：以 Azure 位置為基礎的服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

您可以使用周框方塊來更新地圖攝影機。 如果已從點資料計算周框方塊，則也會在攝影機選項中指定圖元填補值，以考慮圖示大小。 這有助於確保點不會落在地圖區的邊緣。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

在下列程式碼中，會透過來建立 [地圖物件](/javascript/api/azure-maps-control/atlas.map) `new atlas.Map()` 。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](/javascript/api/azure-maps-control/atlas.map) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>透過  建立地圖`CameraBoundsOptions` </a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

設定地圖的相機選項時，也可以設定 [動畫選項](/javascript/api/azure-maps-control/atlas.animationoptions) 。 這些選項會指定移動相機所應採取的動畫類型和持續時間。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

在下列程式碼中，第一個程式碼區塊會建立地圖並設定輸入和縮放地圖樣式。 在第二個程式碼區塊中，會建立 [動畫] 按鈕的 click 事件處理常式。 當您按一下此按鈕時， `setCamera` 就會使用 [CameraOptions](/javascript/api/azure-maps-control/atlas.cameraoptions) 和 [AnimationOptions](/javascript/api/azure-maps-control/atlas.animationoptions)的一些隨機值來呼叫函數。

<br/>

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="request-transforms"></a>要求轉換

有時候，能夠修改地圖控制項所提出的 HTTP 要求會很有用。 例如：

- 將額外標頭新增至磚要求。 這通常是針對受密碼保護的服務所完成。
- 修改 Url 以透過 proxy 服務執行要求。

對應的 [服務選項](/javascript/api/azure-maps-control/atlas.serviceoptions) 有 `transformRequest` ，可以用來修改對應之前的所有要求。 `transformRequest`選項是採用兩個參數的函式、字串 URL，以及表示要求用途的資源類型字串。 此函數必須傳回 [RequestParameters](/javascript/api/azure-maps-control/atlas.requestparameters) 結果。

```JavaScript
transformRequest: (url: string, resourceType: string) => RequestParameters
```

下列範例示範如何使用此方法，藉 `https://example.com` 由新增使用者名稱和密碼做為要求的標頭，來修改所有大小的要求。

```JavaScript
var map = new atlas.Map('myMap', {
    transformRequest: function (url, resourceType) {
        //Check to see if the request is to the specified endpoint.
        if (url.indexOf('https://examples.com') > -1) {
            //Add custom headers to the request.
            return {
                url: url,
                header: {
                    username: 'myUsername',
                    password: 'myPassword'
                }
            };
        }

        //Return the URL unchanged by default.
        return { url: url };
    },

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

## <a name="try-out-the-code"></a>試用程式碼

查看程式碼範例。 您可以在 [ **JS]** 索引標籤內編輯 JavaScript 程式碼，並在 [ **結果]** 索引標籤上查看地圖視圖的變更。您也可以按一下 [>codepen] 右上角的 [ **編輯** ]，然後修改 >codepen 中的程式碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](/javascript/api/azure-maps-control/atlas.map)

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
> [程式碼範例](/samples/browse/?products=azure-maps)