---
title: 在地圖上顯示流量 |微軟 Azure 地圖
description: 在本文中，您將學習如何使用 Microsoft Azure 地圖 Web SDK 在地圖上顯示流量資料。
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 9bd5ae462013924a46a3da8400719e83abae3424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534809"
---
# <a name="show-traffic-on-the-map"></a>在地圖上顯示路況

Azure 地圖中有兩種類型的流量資料可用：

- 事故資料 - 由建築、道路封閉和事故等基於點和線的資料組成。
- 流量資料 - 提供有關道路上交通流量的指標。 通常，交通流量資料用於對道路進行著色。 顏色基於流量減慢流量的速度、相對於速度限制或其他指標。 Azure 地圖中的流量資料有三種不同的度量指標：
    - `relative`- 相對於道路的自由流動速度。
    - `absolute`- 是道路上所有車輛的絕對速度。
    - `relative-delay`- 顯示低於平均預期延遲的區域。

以下代碼演示如何在地圖上顯示流量資料。

```javascript
//Show traffic on the map using the traffic options.
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

下面是上述功能的完整運行代碼示例。

<br/>

<iframe height='500' scrolling='no' title='在地圖上顯示路況' src='//codepen.io/azuremaps/embed/WMLRPw/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在 <a href='https://codepen.io'>CodePen</a> 上查看 Azure 地圖服務 (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) 製作的<a href='https://codepen.io/azuremaps/pen/WMLRPw/'>在地圖上顯示路況</a>Pen。
</iframe>

## <a name="traffic-overlay-options"></a>交通的覆蓋選項

以下工具允許您在不同的流量疊加設置之間切換，以查看呈現如何更改。 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="交通的覆蓋選項" src="//codepen.io/azuremaps/embed/RwbPqRY/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/RwbPqRY/'>流量疊加選項</a>。
</iframe>

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [地圖](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [流量選項](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.trafficoptions)

增強您的使用者體驗：

> [!div class="nextstepaction"]
> [地圖與滑鼠事件的互動](map-events.md)

> [!div class="nextstepaction"]
> [建置可存取的地圖](map-accessibility.md)

> [!div class="nextstepaction"]
> [程式碼範例頁面](https://aka.ms/AzureMapsSamples) \(英文\)
