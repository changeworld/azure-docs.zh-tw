---
title: 在地圖上顯示路況 | Microsoft Azure 地圖服務
description: 在本文中，您會了解如何使用 Microsoft Azure 地圖服務 Web SDK，在地圖上顯示路況資料。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-javascript
ms.openlocfilehash: 5bb088c4650cfdfec59383a7bcf8f340952e5d55
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87273009"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖服務可用的路況資料類型有兩種：

- 事件資料 - 包含以點和線為基礎的資料，用於施工、道路封閉和事故等事項。
- 車流資料 - 提供有關道路路況車流的計量。 車流資料通常用來為道路上色。 這些色彩是以有多少路況正在減慢車流為基礎 (相對於速度限制或另一個計量)。 Azure 地圖服務中的路況車流資料有三種不同度量計量：
    - `relative` - 相對於道路的自由車流速度。
    - `absolute` - 是道路上所有車輛的絕對速度。
    - `relative-delay` - 顯示比平均預期延遲還慢的區域。

下列程式碼顯示如何在地圖上顯示路況資料。

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

## <a name="traffic-overlay-options"></a>交通覆蓋選項

下列工具可供在不同的交通覆蓋設定之間切換，以查看呈現情況如何變化。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通覆蓋選項" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>交通覆蓋選項</a>。
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
