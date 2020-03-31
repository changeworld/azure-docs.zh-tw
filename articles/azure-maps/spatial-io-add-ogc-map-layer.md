---
title: 添加開放地理空間聯盟 （OGC） 地圖圖層 |微軟 Azure 地圖
description: 瞭解如何在地圖上疊加 OGC 地圖圖層，以及如何在 OgcMapLayer 類中使用不同的選項。
author: philmea
ms.author: philmea
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b753ecfc07cfb3806838f8a05dbe33ef0bb92730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334289"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>從開放地理空間聯盟 （OGC） 添加地圖圖層

該`atlas.layer.OgcMapLayer`類可以在地圖上疊加 Web 地圖服務 （WMS） 影像和 Web 地圖切片服務 （WMTS） 影像。 WMS 是 OGC 為在互聯網上提供地理參考地圖圖像而開發的標準協定。 圖像地理參照是將圖像與地理位置相關聯的過程。 WMTS 也是 OGC 開發的標準協定。 它專為提供預渲染和地理參考地圖切片而設計。

以下各節概述了`OgcMapLayer`類支援的 Web 地圖服務功能。

**網路地圖服務**

- 支援的版本： `1.0.0` `1.1.0`、 `1.1.1`、 和`1.3.0`
- 服務必須支援`EPSG:3857`投影系統，或處理重新投影。
- GetFeatureInfo 需要該服務來支援`EPSG:4326`或處理重新投影。 
- 支援的操作：

    | | |
    | :-- | :-- |
    | 獲取功能 | 使用受支援的功能檢索有關服務的中繼資料 |
    | 獲取映射 | 檢索指定區域的地圖圖像 |
    | 獲取功能資訊 | 檢索`feature_info`，其中包含有關該功能的基礎資料 |

**Web 地圖磁貼服務 （WMTS）**

- 支援的版本：`1.0.0`
- 磁貼必須為正方形，`TileWidth == TileHeight`因此 。
- 支援 CRS：`EPSG:3857`或`GoogleMapsCompatible` 
- TileMatrix 識別碼必須是對應于地圖上縮放級別的整數值。 在 Azure 地圖上，縮放級別是 和`"0"``"22"`之間的值。 因此，`"0"`受支援，但`"00"`不受支援。
- 支援的操作：

    | | |
    | :-- | :-- |
    | 獲取功能 | 檢索支援的操作和功能 |
    | GetTile | 檢索特定磁貼的影像 |

## <a name="overlay-an-ogc-map-layer"></a>覆蓋 OGC 地圖圖層

`url`可以是服務的基本 URL，也可以是具有獲取服務功能的查詢的完整 URL。 根據提供的詳細資訊，WFS 用戶端可能會嘗試幾種標準 URL 格式來確定最初如何訪問服務。

以下代碼演示如何在地圖上疊加 OGC 地圖圖層。

<br/>

<iframe height='700' scrolling='no' title='OGC 地圖圖層示例' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，請參閱按 Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>映射 （） 的筆<a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 地圖圖層示例</a>。
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 地圖圖層選項

下面的示例演示了不同的 OGC 地圖圖層選項。 您可以按一下右上角的代碼筆按鈕來編輯代碼筆。

<br/>

<iframe height='700' scrolling='no' title='OGC 地圖圖層選項' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上按 Azure 地圖<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看筆<a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 地圖圖層選項</a>。
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC Web 地圖服務資源管理器

以下工具將 Web 地圖服務 （WMS） 和 Web 地圖磁貼服務 （WMTS） 中的影像疊加為圖層。 您可以選擇服務中的哪些圖層在地圖上呈現。 您還可以查看這些圖層的關聯圖例。

<br/>

<iframe height='750' style='width: 100%;' scrolling='no' title='OGC Web 地圖服務資源管理器' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>在<a href='https://codepen.io'>CodePen</a>上，通過 Azure 映射<a href='https://codepen.io/azuremaps'>@azuremaps</a>（） 查看 Pen <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC Web 地圖服務資源管理器</a>。
</iframe>

您還可以指定地圖設置以使用代理服務。 代理服務允許您載入託管在未啟用 CORS 的域中的資源。

## <a name="next-steps"></a>後續步驟

深入了解本文使用的類別和方法：

> [!div class="nextstepaction"]
> [OgcMapLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayer選項](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

請參閱以下文章，其中包含可以添加到地圖的代碼示例：

> [!div class="nextstepaction"]
> [連接到 WFS 服務](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [利用核心業務](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [支援的資料格式詳細資訊](spatial-io-supported-data-format-details.md)
