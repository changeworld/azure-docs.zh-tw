---
title: 在地圖上顯示路況 | Microsoft Azure 地圖服務
description: 瞭解如何將流量資料新增至地圖。 瞭解流量資料，並瞭解如何使用 Azure 地圖服務 Web SDK 將事件資料和流程資料新增至對應。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b6fcf39e05c4649503f0b7a80aadaaa16df24674
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599552"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖服務可用的路況資料類型有兩種：

- 事件資料 - 包含以點和線為基礎的資料，用於施工、道路封閉和事故等事項。
- 車流資料 - 提供有關道路路況車流的計量。 車流資料通常用來為道路上色。 這些色彩是以有多少路況正在減慢車流為基礎 (相對於速度限制或另一個計量)。 有四個值可傳遞至地圖的流量 `flow` 選項。

    |流程值 | 描述|
    | :-- | :-- |
    | `none` | 不顯示地圖上的交通資料 |
    | `relative` | 顯示相對於道路的自由流動速度的流量資料 |
    | `relative-delay` | 顯示比平均預期延遲更慢的區域 |
    | `absolute` | 顯示所有車輛在道路上的絕對速度 |

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

<iframe height='500' scrolling='no' title='在地圖上顯示路況' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 製作的<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>在地圖上顯示路況</a>Pen。
</iframe>

## <a name="traffic-overlay-options"></a>交通覆蓋選項

下列工具可供在不同的交通覆蓋設定之間切換，以查看呈現情況如何變化。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通覆蓋選項" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
請參閱 <a href='https://codepen.io'>CodePen</a> 上由 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 建立的 Pen：<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>交通覆蓋選項</a>。
</iframe>


## <a name="add-traffic-controls"></a>新增流量控制

有兩個不同的流量控制可新增至地圖。 第一個控制項會 `TrafficControl` 加入切換按鈕，可用來開啟和關閉流量。 此控制項的選項可讓您指定顯示流量時要使用的流量設定。 依預設，此控制項會顯示相對的流量和事件資料，不過，您可以變更此值，以顯示絕對流量和沒有事件（如有需要）。 第二個控制項 `TrafficLegendControl` 會將交通流程圖例新增至地圖，以協助使用者瞭解色彩代碼道路所強調的意義。 此控制項只會在地圖上顯示流量資料，而且會在其他所有時間隱藏時，才會出現在地圖上。

下列程式碼示範如何將流量控制項新增至地圖。

```JavaScript
//Att the traffic control toogle button to the top right corner of the map.
map.controls.add(new atlas.control.TrafficControl(), { position: 'top-right' });

//Att the traffic legend control to the bottom left corner of the map.
map.controls.add(new atlas.control.TrafficLegendControl(), { position: 'bottom-left' });
```

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="流量控制" src="https://codepen.io/azuremaps/embed/ZEWaeLJ?height500&theme-id=0&default-tab=js,result&embed-version=2&editable=true" frameborder='no' loading="lazy" loading="lazy" allowtransparency="true" allowfullscreen="true">
查看 >codepen 上 Azure 地圖服務 () 的畫筆<a href='https://codepen.io/azuremaps/pen/ZEWaeLJ'>流量控制</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'></a>
</iframe>


## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [TrafficOptions](/javascript/api/azure-maps-control/atlas.trafficoptions)

增強您的使用者體驗：

> [!div class="nextstepaction"]
> [地圖與滑鼠事件的互動](map-events.md)

> [!div class="nextstepaction"]
> [建置可存取的地圖](map-accessibility.md)

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)