---
title: 新增開放地理空間協會（OGC）地圖圖層 |Microsoft Azure 對應
description: 瞭解如何在地圖上覆迭 OGC 地圖圖層，以及如何在 OgcMapLayer 類別中使用不同的選項。
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: c59376344d2df7e9e9c76e630a4462b26343d187
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371008"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>從開放地理空間協會（OGC）新增地圖圖層

`atlas.layer.OgcMapLayer` 類別可以重迭地圖上的 Web 對應服務（WMS）影像和 Web 對應磚服務（WMTS）影像。 WMS 是 OGC 所開發的標準通訊協定，可透過網際網路提供 georeferenced 對應影像。 影像 georeferencing 是將影像與地理位置產生關聯的程式。 WMTS 也是 OGC 所開發的標準通訊協定。 其設計目的是要提供預先轉譯和 georeferenced 的地圖底圖。

下列各節概述 `OgcMapLayer` 類別所支援的 Web 對應服務功能。

**Web 對應服務（WMS）**

- 支援的版本： `1.0.0`、`1.1.0`、`1.1.1`和 `1.3.0`
- 服務必須支援 `EPSG:3857` 投射系統，否則服務必須能夠處理 reprojections。
- GetFeatureInfo 需要服務支援 `EPSG:4326` 或處理 reprojections。 
- 支援的作業：

    | | |
    | :-- | :-- |
    | GetCapabilities | 使用支援的功能來抓取服務的相關中繼資料 |
    | GetMap | 抓取指定區域的地圖影像 |
    | GetFeatureInfo | 抓取 `feature_info`，其中包含功能的相關基礎資料 |

**Web 對應磚服務（WMTS）**

- 支援的版本： `1.0.0`
- 磚必須是方形，因此 `TileWidth == TileHeight`。
- 支援 CRS： `EPSG:3857` 或 `GoogleMapsCompatible` 
- TileMatrix 識別碼必須是對應至地圖上縮放層級的整數值。 在 azure 地圖上，縮放層級是介於 `"0"` 和 `"22"`之間的值。 因此，支援 `"0"`，但不支援 `"00"`。
- 支援的作業：

    | | |
    | :-- | :-- |
    | GetCapabilities | 抓取支援的作業和功能 |
    | GetTile | 抓取特定磚的影像 |

## <a name="overlay-an-ogc-map-layer"></a>重迭 OGC 地圖圖層

`url` 可以是服務的基底 URL，或是包含取得服務功能之查詢的完整 URL。 根據所提供的詳細資料，工作流程用戶端可能會嘗試數種標準 URL 格式，以判斷如何一開始存取服務。

下列程式碼示範如何在地圖上重迭 OGC 地圖圖層。

<br/>

<iframe height='700' scrolling='no' title='OGC 對應圖層範例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），請參閱 Pen <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 地圖圖層範例</a>。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 地圖圖層選項

下列範例示範不同的 OGC 地圖圖層選項。 您可以按一下右上角的 [程式碼畫筆] 按鈕來編輯程式碼畫筆。

<br/>

<iframe height='700' scrolling='no' title='OGC 地圖圖層選項' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>），請參閱 Pen <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 地圖圖層選項</a>。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web 地圖服務瀏覽器

下列工具會將「Web 地圖服務」（WMS）和「Web 地圖磚服務」（WMTS）中的影像重迭為「圖層」。 您可以選取要在地圖上呈現服務中的哪些圖層。 您也可以查看這些圖層的相關圖例。

<br/>

<iframe height='700' scrolling='no' title='OGC Web 地圖服務瀏覽器' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>請參閱<a href='https://codepen.io'>CodePen</a>上的 Azure 地圖服務（<a href='https://codepen.io/azuremaps'>@azuremaps</a>）的 Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web 地圖服務瀏覽器</a>。
</iframe>

您也可以指定要使用 proxy 服務的對應設定。 Proxy 服務可讓您載入未啟用 CORs 的網域上所裝載的資源。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

請參閱下列文章，其中包含您可以新增至對應的程式碼範例：

> [!div class="nextstepaction"]
> [連接到工作流程服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心作業](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資料](spatial-io-supported-data-format-details.md)
