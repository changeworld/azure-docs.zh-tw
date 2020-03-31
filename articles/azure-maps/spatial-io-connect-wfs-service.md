---
title: 連接到 Web 功能服務 （WFS） 服務 |微軟 Azure 地圖
description: 瞭解如何連接到 WFS 服務，然後使用 Azure 地圖 Web SDK 和空間 IO 模組查詢 WFS 服務。
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8b511395eb61e8845aaa11e5ca7a490dc461424d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334208"
---
# <a name="connect-to-a-wfs-service"></a>連接到 WFS 服務

Web 要素服務 （WFS） 是一種用於查詢具有由開放地理空間聯盟 （OGC） 定義的標準化 API 的空間資料的 Web 服務。 空間`WfsClient`IO 模組中的類允許開發人員連接到 WFS 服務並從該服務查詢資料。

`WfsClient`類支援以下功能：

- 支援的版本： `1.0.0` `1.1.0`、 和`2.0.0`
- 支援的篩選器運算子：二進位比較、邏輯、數學、值和`bbox`。
- 僅使用`HTTP GET`請求。
- 支援的操作：

    | | |
    | :-- | :-- |
    | 獲取功能 | 生成具有有效 WFS 操作和參數的中繼資料文檔 |
    | 獲取功能 | 從資料來源返回一系列要素 |
    | 描述功能類型 | 返回受支援的要素類型 |

## <a name="using-the-wfs-client"></a>使用 WFS 用戶端

空間`atlas.io.ogc.WfsClient`IO 模組中的類便於查詢 WFS 服務並將回應轉換為 GeoJSON 物件。 然後，此 GeoJSON 物件可用於其他映射目的。

以下代碼查詢 WFS 服務並呈現地圖上返回的要素。

<br/>

<iframe height='700' scrolling='no' title='簡單 WFS 示例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>簡單 WFS 示例</a>。
</iframe>

## <a name="supported-filters"></a>支援的篩選器

WFS 標準的規範使用 OGC 濾波器。 假定被調用的服務也支援這些篩選器，則 WFS 用戶端支援以下篩選器。 自訂篩選器字串可以傳遞到類中`CustomFilter`。

**邏輯運算子**

- `And`
- `Or`
- `Not`

**價值運算子**

- `GmlObjectId`
- `ResourceId`

**數學運算子**

- `Add`
- `Sub`
- `Mul`
- `Div`

**比較運算子**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

以下代碼演示了對 WFS 用戶端使用不同的篩選器。

<br/>

<iframe height='500' scrolling='no' title= 'WFS 篩選器示例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS 篩選器示例</a>。
</iframe>

## <a name="wfs-service-explorer"></a>WFS 服務資源管理器

以下代碼使用 WFS 用戶端來探索 WFS 服務。 選擇服務中的屬性類型圖層，並查看關聯的圖例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= 'WFS 服務資源管理器' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通過 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 查看 Pen <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS 服務資源管理器</a>。
</iframe>

要訪問託管在未啟用 CORS 的終結點上的 WFS 服務，可以將啟用 CORS 的`proxyService`代理服務傳遞到 WFS 用戶端的選項中，如下所示。 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [WfsClient](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [Wfs服務選項](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [利用核心業務](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)
