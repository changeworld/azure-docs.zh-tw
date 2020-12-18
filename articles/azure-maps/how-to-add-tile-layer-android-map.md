---
title: 將圖格圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將圖格圖層新增至地圖。 請參閱使用 Azure 地圖服務 Android SDK 將天氣雷達圖重迭新增至地圖的範例。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8ea6f44c47c5cd4d223b053640f65827f46db482
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679313"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>將圖格圖層新增至地圖 (Android SDK) 

本文說明如何使用 Azure 地圖服務 Android SDK，在地圖上呈現圖格圖層。 圖格圖層可讓您在 Azure 地圖服務的地圖底圖上覆蓋影像。 您可以在[縮放層級和圖格格線](zoom-levels-and-tile-grid.md)文件中找到有關 Azure 地圖服務圖格顯示系統的詳細資訊。

圖格圖層會在伺服器的磚中載入。 您可以使用圖格圖層瞭解的命名慣例，在伺服器上預先轉譯和儲存這些映射，就像任何其他影像一樣。 或者，您可以使用動態服務轉譯這些映射，以近乎即時的方式產生影像。 Azure 地圖服務的 >tilelayer 類別支援三種不同的磚服務命名慣例：

* X、Y、縮放標記法 - 以縮放層級為基礎，在圖格格線中的圖格上，x 是資料行位置，而 y 是資料列位置。
* Quadkey 標記法 - 將 x、y、縮放資訊結合成單一字串值，以作為圖格的唯一識別碼。
* 周框方塊-周框方塊座標可以用來指定格式的影像 `{west},{south},{east},{north}` ，這通常是 [Web 對應服務 (WMS) ](https://www.opengeospatial.org/standards/wms)使用的格式。

> [!TIP]
> TileLayer 是視覺化地圖上大型資料集的好方法。 不只可以從影像產生圖格圖層，向量資料也可轉譯為圖格圖層。 藉由將向量資料轉譯為圖格圖層，地圖控制項只需要載入圖格，其檔案大小可能會比它們所代表的向量資料小很多。 許多人因為需要轉譯地圖上數百萬個資料列，而選擇使用這項技術。

傳遞至圖格圖層的圖格 URL 必須是 TileJSON 資源的 http/https URL，或是使用下列參數的圖格 URL 範本： 

* `{x}` - 圖格的 X 位置。 也需要 `{y}` 和 `{z}`。
* `{y}` - 圖格的 Y 位置。 也需要 `{x}` 和 `{z}`。
* `{z}` 圖格的縮放層級。 也需要 `{x}` 和 `{y}`。
* `{quadkey}` -圖格 quadkey 識別碼，以 Bing Maps 圖格系統的命名慣例為基礎。
* `{bbox-epsg-3857}` - 使用 `{west},{south},{east},{north}` 格式的週框方塊字串，位在 EPSG 3857 空間參考系統中。
* `{subdomain}` -子域值的預留位置（如果已指定子域值）。

## <a name="prerequisites"></a>必要條件

若要完成本文中的程式，您必須安裝 [Azure 地圖服務 Android SDK](how-to-use-android-map-control-library.md) 以載入地圖。

## <a name="add-a-tile-layer-to-the-map"></a>將圖格圖層新增至地圖

這個範例示範如何建立指向一組圖格的圖格圖層。 這個範例會使用「x，y，zoom」的圖格系統。 此圖格圖層的來源為 [OpenSeaMap 專案](https://openseamap.org/index.php)，其中包含了更大量的海裡。 通常在觀看磚圖層時，最好能夠清楚地查看地圖上城市的標籤。 您可以將圖格圖層插入地圖標籤圖層下方，來達成此行為。

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

下列螢幕擷取畫面顯示上面的程式碼，在具有深色灰階樣式的地圖上顯示每個海裡資訊的圖格圖層。

![顯示磚圖層的 Android 地圖](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以深入瞭解設定地圖樣式的方式

> [!div class="nextstepaction"]
> [變更地圖樣式](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [新增熱度圖](map-add-heat-map-layer-android.md)
