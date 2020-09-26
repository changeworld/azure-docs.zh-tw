---
title: 將快顯視窗新增至地圖上的某個點 |Microsoft Azure 對應
description: 瞭解 Azure 地圖服務中的快顯視窗、快顯範本和快顯事件。 瞭解如何將快顯視窗新增至地圖上的某個點，以及如何重複使用和自訂快顯視窗。
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: a67fad5d0980730eb73f4219f12eaf3a7b9ec7dc
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310470"
---
# <a name="add-a-popup-to-the-map"></a>在地圖上新增快顯

本文說明如何在地圖上的某個點新增快顯。

## <a name="understand-the-code"></a>了解程式碼

下列程式碼會使用符號圖層，將具有和屬性的點特徵新增 `name` `description` 至地圖。 隨即建立 [快顯視窗類別](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) 的實例，但不會顯示。 滑鼠事件會加入至符號圖層，以觸發開啟和關閉快顯視窗。 當標記符號停留時，快顯視窗的 `position` 屬性會以標記的位置進行更新，而 `content` 選項會以一些 HTML 來更新，以包裝要停留的  `name` `description` 點功能和屬性。 然後，會使用其函式在地圖上顯示快顯視窗 `open` 。

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

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='使用 Azure 地圖服務新增快顯' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看畫筆 <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>使用 Azure 地圖服務新增快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>重複使用具有多個點的快顯

在某些情況下，最好的方法是建立一個快顯視窗，然後重複使用它。 例如，您可能有大量的點，而且只想要一次顯示一個快顯視窗。 藉由重複使用快顯視窗，會大幅減少應用程式所建立的 DOM 元素數目，進而提供更好的效能。 下列範例會建立3個點的功能。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='重複使用具有多個釘選的快顯' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>查看 Pen <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>重複使用具有多個釘選的快顯</a>，發佈者：Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>)，發佈位置：<a href='https://codepen.io'>CodePen</a>。
</iframe>

## <a name="customizing-a-popup"></a>自訂快顯視窗

根據預設，快顯視窗會有白色背景、底部的指標箭號，以及右上角的 [關閉] 按鈕。 下列範例會使用快顯視窗的選項，將背景色彩變更為黑色 `fillColor` 。 將選項設定為 false，即可移除 [關閉] 按鈕 `CloseButton` 。 快顯視窗的 HTML 內容會從快顯的邊緣使用填補10圖元。 文字是以白色顯示，因此會在黑色背景上美觀顯示。  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="自訂快顯視窗" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆<a href='https://codepen.io/azuremaps/pen/ymKgdg/'>自訂快顯視窗</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="add-popup-templates-to-the-map"></a>將快顯視窗範本新增至地圖

快顯範本可讓您輕鬆地建立快顯視窗的資料驅動版面配置。 下列各節示範如何使用各種彈出範本，以使用功能屬性來產生格式化的內容。

> [!NOTE]
> 根據預設，轉譯的所有內容都會使用快顯視窗範本，在 iframe 內以安全性功能的形式進行沙箱化。 不過，有一些限制：
>
> - 已停用所有腳本、表單、指標鎖定和頂端導覽功能。 當您按一下時，可以在新的索引標籤中開啟連結。 
> - 不支援 iframe 上參數的舊版瀏覽器 `srcdoc` 將受限於轉譯少量的內容。
> 
> 如果您信任正在載入至快顯視窗中的資料，而且可能想要載入到快顯視窗的這些腳本能夠存取您的應用程式，您可以將快顯範本選項設定為 false，以停用此 `sandboxContent` 選項。 

### <a name="string-template"></a>字串範本

字串範本會以功能屬性的值取代預留位置。 不需要將字串類型的值指派給功能的屬性。 例如， `value1` 保留一個整數。 這些值接著會傳遞至的 content 屬性 `popupTemplate` 。 

`numberFormat`選項會指定要顯示之數位的格式。 如果 `numberFormat` 未指定，則程式碼會使用快顯範本的日期格式。 `numberFormat`選項會使用[toLocaleString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString)函數來格式化數位。 若要格式化大量數位，請考慮使用 `numberFormat` [>cultureinfo.numberformat 格式](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format)的選項。 例如，下列程式碼片段會使用 `maximumFractionDigits` 將小數位數限制為二。

> [!Note]
> 字串範本只能用一種方式來呈現影像。 首先，字串範本必須有影像標記。 傳遞至影像標記的值應該是影像的 URL。 然後，字串範本 `isImage` 在中必須設定為 true `HyperLinkFormatOptions` 。 `isImage`選項會指定影像的超連結，而且會將超連結載入至影像標記。 按一下超連結時，影像就會開啟。

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

### <a name="propertyinfo-template"></a>PropertyInfo 範本

PropertyInfo 範本會顯示功能的可用屬性。 `label`選項會指定要對使用者顯示的文字。 如果 `label` 未指定，則會顯示超連結。 而且，如果超連結是影像，則會顯示指派給 "alt" 標記的值。 `dateFormat`指定日期的格式，如果未指定日期格式，則會將日期轉譯為字串。 `hyperlinkFormat`選項會轉譯可點按的連結，同樣地，此 `email` 選項可以用來呈現可按的電子郵件地址。

在 PropertyInfo 範本將屬性顯示給終端使用者之前，它會以遞迴方式檢查屬性是否確實已針對該功能定義。 它也會忽略顯示樣式和標題屬性。 例如，它不會顯示 `color` 、、、 `size` `anchor` `strokeOpacity` 和 `visibility` 。 因此，在後端中完成屬性路徑檢查後，PropertyInfo 範本會以表格格式顯示內容。

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

某項功能也可能會使用字串範本和 PropertyInfo 範本的組合來顯示內容。 在此情況下，字串範本會將預留位置值轉譯成白色背景。  而且，PropertyInfo 範本會在表格內轉譯完整寬度的影像。 此範例中的屬性類似于先前範例中所述的屬性。

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

當 Popup 範本未定義為字串範本、PropertyInfo 範本或兩者的組合時，就會使用預設設定。 當 `title` 和 `description` 是唯一指派的屬性時，快顯視窗範本會顯示白色背景，也就是右上角的 [關閉] 按鈕。 而且，在小型和中型螢幕上，它會在底部顯示箭號。 預設設定會顯示在資料表中，除了和以外的所有屬性 `title` `description` 。 即使回到預設設定，也可以透過程式設計的方式操作快顯範本。 例如，使用者可以關閉超連結偵測，預設設定仍會套用至其他屬性。

在 >codepen 中，按一下地圖上的點。 下列每個快顯範本的對應上都有一個點：字串範本、PropertyInfo 範本和多個內容範本。 另外還有三個重點可顯示範本如何使用預設設定來呈現。

<br/>

<iframe height='500' scrolling='no' title='PopupTemplates' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 >codepen 上的 Azure 地圖服務 () 的畫筆<a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="reuse-popup-template"></a>重複使用快顯視窗範本

類似于重複使用快顯視窗，您可以重複使用快顯範本。 當您只想要針對多個點一次顯示一個快顯範本時，此方法很有用。 藉由重複使用快顯範本，應用程式所建立的 DOM 元素數目會減少，進而改善您的應用程式效能。 下列範例會針對三個點使用相同的快顯視窗範本。 如果您對任何一個特徵按一下，便會顯示快顯，內有該點特徵的內容。

<br/>

<iframe height='500' scrolling='no' title='ReusePopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 >codepen 上的 Azure 地圖服務 () 的畫筆<a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>ReusePopupTemplate</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="popup-events"></a>快顯視窗事件

您可以開啟、關閉和拖曳快顯視窗。 Popup 類別會提供事件，以協助開發人員回應這些事件。 下列範例會反白顯示當使用者開啟、關閉或拖曳快顯視窗時引發的事件。 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="快顯視窗事件" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
在 >codepen 上 Azure 地圖服務 () ，查看畫筆<a href='https://codepen.io/azuremaps/pen/BXrpvB/'>彈出事件</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [PopupTemplate](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

請參閱下列推薦文章，了解完整程式碼範例：

> [!div class="nextstepaction"]
> [新增符號圖層](./map-add-pin.md)

> [!div class="nextstepaction"]
> [新增 HTML 標記](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [新增線條圖層](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](map-add-shape.md)
