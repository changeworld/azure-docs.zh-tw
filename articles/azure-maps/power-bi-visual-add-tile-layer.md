---
title: 將圖格圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps 視覺效果中 Power BI 的圖格圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 8a0d930d1c0fd30c48d97b0d1d4b94548077fbca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261679"
---
# <a name="add-a-tile-layer"></a>新增圖格圖層

圖格圖層功能和參考圖層功能一樣，可讓其他資料在地圖上重迭，以提供更多的內容。 圖格圖層可讓您將影像疊加在 Azure 地圖服務的地圖底圖磚上方。 這是重迭大型或複雜資料集（例如無人機的影像或數百萬個數據列）的絕佳方法。

> [!div class="mx-imgBorder"]
> ![顯示圖格圖層上方之反升圖層的地圖，顯示 Azure 地圖服務的目前紅外線氣象資料](media/power-bi-visual/radar-tile-layer-with-bubbles.png)

圖格圖層會在伺服器的磚中載入。 這些映射可以預先轉譯或以動態方式呈現。 預先轉譯的影像會以圖格圖層瞭解的命名慣例，與伺服器上的任何其他影像儲存起來一樣。 動態轉譯的影像會使用服務，以接近即時的方式載入映射。 圖格圖層是將地圖上的大型資料集視覺化的絕佳方式。 您不僅可以從影像產生圖格圖層，也可以將向量資料轉譯為圖格圖層。

您可以將磚服務可用的周框方塊和縮放範圍作為設定來傳遞，以限制要求磚的位置。 這同時也是視覺效果和磚服務的效能增強功能。 以下概要說明 [**圖格圖層**] 區段中可用的 [**格式**] 窗格中可用的所有設定。

| 設定        | 描述   |
|----------------|---------------|
| Url            | 指向磚服務的格式化 URL。  |
| 圖格大小      | 整數值，指定磚的寬度和高度維度。   |
| 北系結    | 可使用磚之周框方塊的北緯度。 |
| 南部系結    | 周框方塊的南緯度，其中有可用的磚。 |
| 東亞     | 周框方塊的東部經度，其中有可用的磚。  |
| 西歐     | 周框方塊的西部經度，其中有可用的磚。   |
| 透明度   | 圖格圖層的透明度。   |
| 是 TM         | 圖格地圖服務，此規格會反轉圖格圖層的 Y 座標軸。 |
| 最小縮放       | 可使用最小縮放層級磚。 |
| 最大縮放       | 最大縮放層級磚可供使用。  |
| 圖層位置 | 指定圖層相對於其他地圖圖層的位置。 |

## <a name="tile-url-formatting"></a>磚 URL 格式化

Azure 地圖服務的視覺效果支援三種不同的磚服務命名慣例：

-   **X、Y、縮放標記法** -x 是資料行，Y 是磚方格中磚的資料列位置，而縮放標記法是以縮放層級為基礎的值。
-   **Quadkey 標記法** -將 x、y 和 zoom 資訊合併成單一字串值。 這個字串值會成為單一圖格的唯一識別碼。
-   周**框**方塊-指定周框方塊座標格式的影像： `{west},{south},{east},{north}` 。 [ (WMS) 的 Web 對應服務](https://www.opengeospatial.org/standards/wms)通常會使用此格式。

磚 URL 為圖格 URL 範本的 HTTPs URL，此 url 會使用下列參數：

-   `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
-   `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
-   `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
-   `{quadkey}` -以 `quadkey` Bing Maps 圖格系統命名慣例為基礎的磚識別碼。
-   `{bbox-epsg-3857}` - `{west},{south},{east},{north}` EPSG 3857 空間參考系統中的周框方塊字串格式。

例如，以下是 Azure 地圖服務中 [氣象圖磚服務](https://docs.microsoft.com/rest/api/maps/renderv2/getmaptilepreview) 的格式化磚 URL。 請注意，這 `[subscription-key]` 是 Azure 地圖服務訂用帳戶金鑰的預留位置。

> `https://atlas.microsoft.com/map/tile?zoom={z}&x={x}&y={y}&tilesetId=microsoft.weather.radar.main&api-version=2.0&subscription-key=[subscription-key]`

如需 Azure 地圖服務並排顯示系統的詳細資訊，請參閱 [縮放層級和圖格格線](zoom-levels-and-tile-grid.md)。

## <a name="next-steps"></a>接下來的步驟

在地圖中新增更多內容：

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
