---
title: 將快顯視窗添加到地圖上的點 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Web SDK 將快顯視窗添加到某個點。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055958"
---
# <a name="add-a-popup-to-the-map"></a>在地圖上新增快顯

本文說明如何在地圖上的某個點新增快顯。

## <a name="understand-the-code"></a>了解程式碼

以下代碼使用符號圖層向地圖添加具有`name`和`description`屬性的點要素。 將創建[彈出類](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)的實例，但不會顯示。 滑鼠事件將添加到符號圖層中，以觸發快顯視窗的打開和關閉。 當標記`position`符號被懸停時，快顯視窗的屬性會隨著標記的位置進行更新，並且`content`該選項將更新為一些 HTML，該 HTML 將要懸停`name`的`description`點要素和屬性進行包裝。 然後，快顯視窗將利用其`open`功能顯示在地圖上。

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='使用 Azure 地圖服務新增快顯' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>使用 Azure 地圖服務新增快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重複使用具有多個點的快顯

在某些情況下，最好的方法是創建一個快顯視窗並重用它。 例如，您可能具有大量點，並且希望一次只顯示一個快顯視窗。 通過重用快顯視窗，應用程式創建的 DOM 元素的數量大大減少，從而提供更好的性能。 以下示例創建三點要素。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='重複使用具有多個釘選的快顯' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重複使用具有多個釘選的快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customizing-a-popup"></a>自訂快顯視窗

預設情況下，快顯視窗的背景為白色，底部有指標箭頭，右上角有一個關閉按鈕。 以下示例使用快顯視窗`fillColor`的選項將背景顏色更改為黑色。 通過將`CloseButton`選項設置為 false，將刪除關閉按鈕。 快顯視窗的 HTML 內容使用從快顯視窗邊緣填充的 10 圖元。 文本是白色的，所以它顯示在黑色背景上。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自訂快顯視窗" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上，請參閱 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>地圖 （） 自訂的筆<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自訂快顯視窗</a>。
</iframe>

## <a name="add-popup-templates-to-the-map"></a>將彈出範本添加到地圖

彈出式範本便於為快顯視窗創建資料驅動的佈局。 以下各節演示了使用各種彈出式範本使用功能屬性生成格式化內容。

> [!NOTE]
> 預設情況下，使用彈出式範本呈現的所有內容都將在 iframe 內部作為安全功能進行沙箱處理。 但是，存在限制：
>
> - 禁用所有腳本、表單、指標鎖定和頂部導航功能。 按一下時，允許在新選項卡中打開連結。 
> - 不支援 iframe 上`srcdoc`參數的舊瀏覽器將僅限於呈現少量內容。
> 
> 如果您信任載入到快顯視窗中的資料，並且可能希望載入到快顯視窗中的這些腳本能夠訪問您的應用程式，則可以通過將快顯視窗範本`sandboxContent`選項設置為 false 來禁用此功能。 

### <a name="string-template"></a>字串範本

字串範本使用要素屬性的值替換預留位置。 不必為要素的屬性分配 String 類型的值。 例如，`value1`保存整數。 然後，這些值將傳遞給 的內容`popupTemplate`屬性。 

該`numberFormat`選項指定要顯示的數位的格式。 如果未指定`numberFormat`， 則代碼將使用彈出範本日期格式。 該`numberFormat`選項使用["數位.到本地字串"](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)功能設置數位格式。 要設置大數位的格式，請考慮`numberFormat`將 選項與[NumberFormat.format.格式](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)中的函數一起使用。 例如，下面的程式碼片段用於`maximumFractionDigits`將分數位數限制為兩個。

> [!Note]
> 字串範本只有一種方式可以呈現圖像。 首先，字串範本需要有一個圖像標記。 傳遞給圖像標記的值應為圖像的 URL。 然後，字串範本需要在 中`isImage``HyperLinkFormatOptions`設置為 true。 該`isImage`選項指定超連結用於圖像，超連結將載入到圖像標記中。 按一下超連結時，圖像將打開。

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>屬性資訊範本

屬性資訊範本顯示該功能的可用屬性。 該`label`選項指定要向使用者顯示的文本。 如果未`label`指定，則將顯示超連結。 而且，如果超連結是圖像，將顯示分配給"alt"標記的值。 指定`dateFormat`日期的格式，如果未指定日期格式，則日期將呈現為字串。 該`hyperlinkFormat`選項呈現可按一下的連結，同樣，`email`該選項可用於呈現可按一下的電子郵件地址。

在屬性資訊範本向最終使用者顯示內容之前，它會遞迴地檢查這些屬性確實已為該功能定義。 它還忽略顯示樣式和標題屬性。 例如，`color`它不會顯示`size`、、`anchor``strokeOpacity`和`visibility`。 因此，一旦屬性路徑檢查在後端完成，屬性資訊範本將以表格式顯示內容。

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>多個內容範本

功能還可以使用字串範本和屬性資訊範本的組合顯示內容。 在這種情況下，字串範本在白色背景上呈現預留位置值。  而且，屬性資訊範本在表格中呈現全寬度圖像。 此示例中的屬性與我們在前面的示例中解釋的屬性類似。

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>沒有已定義範本的點

當快顯視窗範本未定義為字串範本、屬性資訊範本或兩者的組合時，它使用預設設置。 當`title`和`description`是唯一分配的屬性時，彈出範本將顯示一個白色背景，右上角的關閉按鈕。 在中小型螢幕上，它顯示了底部的箭頭。 預設設置顯示在 表內，用於 和 以外的`title`所有屬性。 `description` 即使回到預設設置，快顯視窗範本仍可以通過程式設計方式進行操作。 例如，使用者可以關閉超連結檢測，預設設置仍應用於其他屬性。

按一下 CodePen 中地圖上的點。 地圖上有以下每個彈出範本的點：字串範本、屬性資訊範本和多個內容範本。 還有三點可以顯示範本如何使用預設設置呈現。

<br/>

<iframe height='500' scrolling='no' title='彈出範本' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/dyovrzL/'>快顯視窗範本</a>。
</iframe>

## <a name="reuse-popup-template"></a>重複使用快顯視窗範本

與重複使用快顯視窗類似，您可以重複使用快顯視窗範本。 當您只想一次顯示多個點的彈出範本時，此方法非常有用。 通過重用快顯視窗範本，應用程式創建的 DOM 元素的數量將減少，從而提高應用程式的性能。 以下示例對三個點使用相同的快顯視窗範本。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='重用彈出範本' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的筆<a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>重用彈出範本</a>。
</iframe>

## <a name="popup-events"></a>彈出事件

快顯視窗可以打開、關閉和拖動。 彈出類提供事件來説明開發人員對這些事件做出反應。 以下示例突出顯示當使用者打開、關閉或拖動快顯視窗時觸發的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="彈出事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看筆<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>快顯視窗事件</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [彈出範本](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

請參閱下列推薦文章，了解完整程式碼範例：

> [!div class="nextstepaction"]
> [添加符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)
