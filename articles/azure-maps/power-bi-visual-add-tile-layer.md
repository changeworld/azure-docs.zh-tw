---
title: 將圖格圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 [Microsoft Azure 地圖] 視覺效果中使用磚圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261679"
---
# <a name="add-a-tile-layer"></a>新增圖格圖層

圖格圖層功能（如參考圖層功能）可讓其他資料在地圖上重迭，以提供更多內容。 圖格圖層可讓您將影像疊加在 Azure 地圖服務的地圖底圖磚上。 這是用來覆迭大型或複雜資料集（例如來自無人機的影像或數百萬個數據列）的絕佳方式。

> [!div class="mx-imgBorder"]
> ![在圖格圖層上方顯示反升圖層的地圖，其中顯示來自 Azure 地圖服務的目前紅外線天氣資料](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

磚圖層會從伺服器載入磚。 這些映射可以是預先轉譯或動態呈現。 預先呈現的影像會以磚圖層所瞭解的命名慣例，與伺服器上的任何其他影像一樣儲存。 動態呈現的影像會使用服務來載入接近即時的影像。 圖格圖層是將地圖上的大型資料集視覺化的絕佳方式。 圖格圖層不僅可以從影像產生，向量資料也可以呈現為磚圖層。

圖格服務可供使用的周框方塊和縮放範圍可以做為設定來傳遞，以限制要求磚的位置。 這同時也是視覺效果和磚服務的效能增強功能。 以下概要說明 [**圖格圖層**] 區段中提供的 [**格式**] 窗格中可用的所有設定。

| 設定        | 描述   |
|----------------|---------------|
| Url            | 指向磚服務的格式化 URL。  |
| 圖格大小      | 整數值，指定磚的寬度和高度維度。   |
| 北系結    | 周框方塊的北部緯度，其中有磚可用。 |
| 南部系結    | 周框方塊中有磚的南部緯度。 |
| 東系結     | 周框方塊中可使用磚的東部經度。  |
| 西歐     | 有磚的周框方塊的西方經度。   |
| 透明度   | 圖格圖層的透明度。   |
| 是 TM         | 磚地圖服務，這是一個反轉磚圖層 Y 座標軸的規格。 |
| 最小縮放       | 最小縮放層級磚可供使用。 |
| 縮放上限       | 最大縮放層級磚可供使用。  |
| 圖層位置 | 指定圖層相對於其他地圖圖層的位置。 |

## <a name="tile-url-formatting"></a>磚 URL 格式設定

Azure 地圖服務視覺效果支援三種不同的磚服務命名慣例：

-   **X、Y、縮放標記法**-X 是資料行，Y 是磚方格中磚的資料列位置，而 zoom 標記法是以縮放層級為基礎的值。
-   **Quadkey 標記法**-將 x、y 和 zoom 資訊結合成單一字串值。 這個字串值會變成單一磚的唯一識別碼。
-   周**框**方塊-以周框方塊座標格式指定影像： `{west},{south},{east},{north}` 。 [Web 對應服務 (WMS) ](https://www.opengeospatial.org/standards/wms)通常會使用此格式。

磚 URL 會使用下列參數，將 HTTPs URL 指向磚 URL 範本：

-   `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
-   `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
-   `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
-   `{quadkey}`- `quadkey` 根據 Bing Maps 磚系統命名慣例的磚識別碼。
-   `{bbox-epsg-3857}`- `{west},{south},{east},{north}` EPSG 3857 空間參考系統中具有格式的周框方塊字串。

例如，下列是 Azure 地圖服務中[氣象雷達圖磚](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview)的格式化磚 URL。 請注意， `[subscription-key]` 是您 Azure 地圖服務訂用帳戶金鑰的預留位置。

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

如需 Azure 地圖服務並排顯示系統的詳細資訊，請參閱 [縮放層級和磚方格](zoom-levels-and-tile-grid.md)。

## <a name="next-steps"></a>後續步驟

將更多內容新增至地圖：

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
