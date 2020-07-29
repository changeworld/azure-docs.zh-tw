---
title: 連接到 Web 功能服務（工作流程）服務 |Microsoft Azure 對應
description: 瞭解如何連接到工作流程服務，然後使用 Azure 地圖服務 web SDK 和空間 IO 模組來查詢工作流程服務。
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-javascript
ms.openlocfilehash: 890bf60ac794740716817094a60f2b4cb4ba5835
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87282784"
---
# <a name="connect-to-a-wfs-service"></a>連接到 WFS 服務

Web 功能服務（工作流程）是一種 web 服務，可用於查詢具有標準 API （由開放地理空間協會（OGC）所定義）的空間資料。 `WfsClient`空間 IO 模組中的類別可讓開發人員連接至工作流程服務，並查詢服務中的資料。

類別支援下列功能 `WfsClient` ：

- 支援的版本： `1.0.0` 、 `1.1.0` 和`2.0.0`
- 支援的篩選運算子：二進位比較、邏輯、數學、值和 `bbox` 。
- 要求只會使用進行 `HTTP GET` 。
- 支援的作業：

    | 作業 | 描述 |
    | :-- | :-- |
    | GetCapabilities | 產生具有有效工作流程作業和參數的元資料檔案 |
    | GetFeature | 從資料來源傳回功能的選取專案 |
    | DescribeFeatureType | 傳回支援的功能類型 |

## <a name="using-the-wfs-client"></a>使用工作流程用戶端

`atlas.io.ogc.WfsClient`空間 IO 模組中的類別可讓您輕鬆地查詢工作流程服務，並將回應轉換成 GeoJSON 物件。 然後，這個 GeoJSON 物件就可以用於其他對應用途。

下列程式碼會查詢工作流程服務，並將傳回的功能呈現在地圖上。

<br/>

<iframe height='700' scrolling='no' title='簡單的工作流程範例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 CodePen 上 Azure 地圖服務（）的 Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>SIMPLE 工作流程範例</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> <a href='https://codepen.io'> </a>。
</iframe>

## <a name="supported-filters"></a>支援的篩選器

工作流程標準的規格會使用 OGC 篩選器。 工作流程用戶端支援下列篩選準則，假設所呼叫的服務也支援這些篩選器。 自訂篩選字串可以傳遞至 `CustomFilter` 類別。

**邏輯運算子**

- `And`
- `Or`
- `Not`

**值運算子**

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

下列程式碼示範如何搭配工作流程用戶端使用不同的篩選準則。

<br/>

<iframe height='500' scrolling='no' title= '工作流程篩選範例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 CodePen 上 Azure 地圖服務（）的 Pen<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>工作流程篩選器範例</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

## <a name="wfs-service-explorer"></a>工作流程 service explorer

下列程式碼會使用工作流程用戶端來探索工作流程服務。 選取服務內的屬性類型圖層，並查看相關聯的圖例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= '工作流程 service explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱 CodePen 上 Azure 地圖服務（）的 Pen<a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>工作流程服務瀏覽器</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> 。 <a href='https://codepen.io'>CodePen</a>
</iframe>

若要存取裝載于未啟用 CORS 之端點上的工作流程服務，可以將已啟用 CORS 的 proxy 服務傳遞至 `proxyService` 工作流程用戶端的選項，如下所示。 

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
> [WfsServiceOptions](https://docs.microsoft.com/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)
