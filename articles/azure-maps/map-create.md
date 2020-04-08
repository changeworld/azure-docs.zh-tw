---
title: 使用 Azure 地圖建立地圖 |微軟 Azure 地圖
description: 在本文中,您將瞭解如何使用 Microsoft Azure 地圖 Web SDK 在網頁上呈現地圖。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802326"
---
# <a name="create-a-map"></a>建立地圖

本文將說明建立地圖及以動畫顯示地圖的方法。  

## <a name="loading-a-map"></a>載入地圖

要載入地圖,請創建[Map 類](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)的新實例。 初始化地圖時,傳遞 DIV 元素 ID 以呈現地圖,並傳遞一組載入地圖時要使用的選項。 如果未在`atlas`命名空間上指定預設身份驗證資訊,則載入地圖時需要在地圖選項中指定此資訊。 地圖以異步方式載入多個資源以進行性能載入。 因此,在創建地圖實例後,將或`ready``load`事件附加到地圖,然後將與映射交互的任何其他代碼添加到事件處理程式。 一`ready`旦地圖載入了足夠的資源以程式設計方式與之交互,事件就會觸發。 初始`load`地圖檢視完全載入後,事件將觸發。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="基本地圖負載" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 () 查看筆<a href='https://codepen.io/azuremaps/pen/rXdBXx/'>基本地圖載入</a>。
</iframe>

> [!TIP]
> 您可以在同一頁上載入多個地圖。 同一頁上的多個地圖可能使用相同的或不同的身份驗證和語言設置。

## <a name="show-a-single-copy-of-the-world"></a>顯示世界的單一複本

當地圖在寬螢幕上縮小時,世界的多個副本將水平顯示。 此選項對於某些方案非常理想,但對於其他應用程式,最好看到世界的單一副本。 此行為通過將地圖`renderWorldCopies`選項設置`false`為實現。

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="成成世界複本 = 假" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上,請參閱「按<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure 映射 () 顯示的筆<a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>呈現世界副本 = false。</a>
</iframe>


## <a name="map-options"></a>地圖選項

在那裡創建地圖時,可以傳入幾種不同類型的選項來自定義地圖的函數方式,如下所示。

- [Camera 選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)和[CameraBound 選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions)用於指定地圖應顯示的區域。
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions)用於指定地圖應如何與為地圖供電的服務進行交互。
- [樣式選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)用於指定地圖應設置樣式和渲染。
- [使用者交互選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions)用於指定當使用者與地圖互動時地圖應如何到達。 

`setCamera`在使用`setServiceOptions`、`setStyle``setUserInteraction`函數載入地圖後,也可以更新這些選項。 

## <a name="controlling-the-map-camera"></a>控制地圖攝影機

有兩種方法可以使用地圖的相機設置地圖的顯示區域。 載入地圖時,可以設置攝像機選項。 或者,在地圖載入後`setCamera`,您可以隨時呼叫該選項以以程式設計方式更新地圖檢視。  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>設定觀景窗

地圖攝像機控制地圖畫布的視口中顯示的內容。 在初始化或傳遞到地圖函數中時,可以將相機選項傳遞到地圖`setCamera`選項中。

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

在以下代碼中,將創建一個[Map物件](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map),並設置中心和縮放選項。 地圖屬性(如中心級和縮放級別)是[「相機選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions)」的一部分。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraOptions 建立地圖' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>透過  建立地圖`CameraOptions` </a>，發佈者：以 Azure 位置為基礎的服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>設定觀景窗界限

邊界框可用於更新地圖攝像機。 如果邊界框是根據點數據計算的,則通常也很有用,在攝像機選項中指定圖元填充值以考慮圖示大小。 這將有助於確保點不會從地圖視口邊緣脫落。

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

在以下代碼中,通過構造[Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)`new atlas.Map()`物件。 `CameraBoundsOptions` 等地圖屬性可透過 Map 類別的 [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) 函式來定義。 邊界和邊框間距屬性會使用 `setCamera` 來設定。

<br/>

<iframe height='500' scrolling='no' title='透過 CameraBoundsOptions 建立地圖' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>透過  建立地圖`CameraBoundsOptions` </a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

### <a name="animate-map-view"></a>以動畫方式呈現地圖的檢視

設定地圖的相機選項時,也可以設定[動畫選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions)。 這些選項指定移動攝像機所應採用的動畫類型和持續時間。

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

在以下代碼中,第一個代碼塊創建一個地圖並設置輸入和縮放地圖樣式。 在第二個程式碼塊中,為動畫按鈕創建一個單擊事件處理程式。 按下此按鍵時,使用`setCamera`[「相機選項](/javascript/api/azure-maps-control/atlas.cameraoptions)」和「[動畫選項](/javascript/api/azure-maps-control/atlas.animationoptions)」的一些隨機值呼叫該函數。

<br/>

<iframe height='500' scrolling='no' title='以動畫方式呈現地圖的檢視' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/WayvbO/'>以動畫方式呈現地圖的檢視</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="try-out-the-code"></a>試用程式碼

查看代碼示例。 您可以在**JS 選項卡**中編輯 JavaScript 代碼,並在 **「結果」選項卡**上看到地圖視圖更改。您還可以在右上角按一下**CodePen 上的「編輯**」,並在 CodePen 中修改代碼。

<a id="relatedReference"></a>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [相機選項](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [動畫選項](/javascript/api/azure-maps-control/atlas.animationoptions)

請參閱程式碼範例，將功能新增至您的應用程式：

> [!div class="nextstepaction"]
> [變更地圖樣式](choose-map-style.md)

> [!div class="nextstepaction"]
> [將控制項新增至地圖](map-add-controls.md)

> [!div class="nextstepaction"]
> [程式碼範例](https://docs.microsoft.com/samples/browse/?products=azure-maps)
