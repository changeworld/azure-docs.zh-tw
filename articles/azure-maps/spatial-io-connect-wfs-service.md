---
title: 連接到 Web 功能服務（工作流程）服務 |Microsoft Azure 對應
description: 瞭解如何連接到工作流程服務，然後使用 Azure 地圖服務 web SDK 和空間 IO 模組來查詢工作流程服務。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 18ac583837c7cb8b2dabbfa6f7d7210c8afe3fcb
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/06/2020
ms.locfileid: "78402764"
---
# <a name="connect-to-a-wfs-service"></a>連接到工作流程服務

Web 功能服務（工作流程）是一種 web 服務，用來查詢具有標準化 API （已由開放地理空間協會（OGC）定義）的空間資料。 空間 IO 模組中的 `WfsClient` 類別，可讓開發人員連接至工作流程服務，並從服務查詢資料。

`WfsClient` 類別支援下列功能：

- 支援的版本： `1.0.0`、`1.1.0`和 `2.0.0`
- 支援的篩選運算子：二進位比較、邏輯、數學、值和 `bbox`。
- 只會使用 `HTTP GET` 來提出要求。
- 支援的作業：

    | | |
    | :-- | :-- |
    | GetCapabilities | 產生具有有效工作流程作業和參數的元資料檔案 |
    | GetFeature | 從資料來源傳回功能的選取專案 |
    | DescribeFeatureType | 傳回支援的功能類型 |

## <a name="using-the-wfs-client"></a>使用工作流程用戶端

空間 IO 模組中的 `atlas.io.ogc.WfsClient` 類別，可讓您輕鬆地查詢工作流程服務，並將回應轉換成 GeoJSON 物件。 然後，這個 GeoJSON 物件就可以用於其他對應用途。

下列程式碼會查詢工作流程服務，並將傳回的功能呈現在地圖上。

<br/>

<iframe height='700' scrolling='no' title='簡單的工作流程範例' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>Simple 工作流程範例</a>。
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

<iframe height='500' scrolling='no' title= '工作流程篩選範例' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen<a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>工作流程篩選器範例</a>。
</iframe>

## <a name="wfs-service-explorer"></a>工作流程 service explorer

下列程式碼會使用工作流程用戶端來探索工作流程服務。 選取服務內的屬性類型圖層，並查看相關聯的圖例。

<br/>

<iframe height='700' style='width: 100%;' scrolling='no' title= '工作流程 service explorer' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen<a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>工作流程服務瀏覽器</a>。
</iframe>

您也可以使用 proxy 服務，載入未啟用 CORs 的網域上所裝載的資源。 您會先定義一個變數來保存 proxy 服務 url，並為工作流程用戶端設定 `proxyService` 選項。 若要為使用者呈現 proxy 服務選項，請將使用者輸入新增至 UI。 當您按一下輸入時，載入服務 url。 下列程式碼片段示範如何使用 proxy 服務。

```JavaScript

//A variable to hold the URL of the proxy service
var proxyServiceUrl = window.location.origin + 'CorsEnabledProxyService.ashx?url=';

//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: (document.getElementById('useProxyService').checked) ? proxyServiceUrl : null
});

function proxyOptionChanged() {
    if (currentServiceUrl) {
        loadClient(currentServiceUrl);
    }
}

```

下列 HTML 程式碼片段對應于上述的 JavaScript 程式碼：

```html
<!-- use the proxy service -->
<input id="useProxyService" type="checkbox" onclick="proxyOptionChanged()"/>
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
