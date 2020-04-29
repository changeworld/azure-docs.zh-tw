---
title: 顯示地圖上的流量 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Web SDK，在地圖上顯示流量資料。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "79534809"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖服務可用的流量資料類型有兩種：

- 事件資料-包含以點和行為基礎的資料，用於結構、後端和事故等事物。
- 流量資料-提供有關道路流量的計量。 通常，流量資料會用來為道路上色。 這些色彩是以相對於速度限制或另一個計量的流量緩慢程度為基礎。 Azure 地圖服務中的流量資料有三種不同的度量計量：
    - `relative`-相對於道路的自由流量速度。
    - `absolute`-是道路上所有車輛的絕對速度。
    - `relative-delay`-顯示比平均預期延遲慢的區域。

下列程式碼顯示如何在地圖上顯示流量資料。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

以下是上述功能的完整執行程式碼範例。

<br/>

<iframe height='500' scrolling='no' title='在地圖上顯示路況' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 製作的<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>在地圖上顯示路況</a>Pen。
</iframe>

## <a name="traffic-overlay-options"></a>交通的覆蓋選項

下列工具可讓您在不同的流量重迭設定之間切換，以查看呈現如何變更。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通的覆蓋選項" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的「畫筆流量重迭」<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增強您的使用者體驗：

> [!div class="nextstepaction"]
> [地圖與滑鼠事件的互動](map-events.md)

> [!div class="nextstepaction"]
> [建置可存取的地圖](map-accessibility.md)

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
