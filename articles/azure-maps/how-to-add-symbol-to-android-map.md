---
title: 將符號圖層新增至 Android 地圖 |Microsoft Azure 對應
description: 瞭解如何將標記新增至地圖。 請參閱使用 Azure 地圖服務 Android SDK 來新增符號圖層，其中包含來自資料來源之以點為基礎之資料的符號圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 040fcde35707074ffaf102ed6c224b2f47a084bb
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679351"
---
# <a name="add-a-symbol-layer-android-sdk"></a>將符號圖層新增 (Android SDK) 

本文說明如何使用 Azure 地圖服務 Android SDK，將資料來源中的點資料呈現為地圖上的符號圖層。 符號圖層會將點轉譯為地圖上的影像和文字。

> [!TIP]
> 根據預設，符號圖層會轉譯資料來源中所有幾何圖形的座標。 若要限制圖層，使其只轉譯點幾何特徵，請將 `filter` 圖層的選項設定為 `eq(geometryType(), "Point")` 。 如果您也想要包含 MultiPoint 功能，請將 `filter` 圖層的選項設定為 `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` 。

## <a name="prerequisites"></a>必要條件

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。 本文中的程式碼區塊可以插入 maps `onReady` 事件處理常式中。

## <a name="add-a-symbol-layer"></a>新增符號圖層

您必須採取幾個步驟，才能將符號圖層新增至地圖。 首先，建立資料來源，並將它加入至對應。 建立符號圖層。 然後，將資料來源傳入符號圖層，以從資料來源取得資料。 最後，將資料新增至資料來源，以便有一些要轉譯的內容。

下列程式碼示範載入對應之後應新增的內容。 此範例會使用符號圖層，在地圖上呈現單一點。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point and add it to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source);

//Add the layer to the map.
map.layers.add(layer);
```

有三種不同類型的 point 資料可新增至地圖：

- GeoJSON 點幾何-此物件只包含點的座標，而不包含任何其他專案的座標。 `Point.fromLngLat`靜態方法可以用來輕鬆建立這些物件。
- GeoJSON MultiPoint geometry-這個物件包含多個點的座標，其他則沒有。 將點陣列傳遞至 `MultiPoint` 類別，以建立這些物件。
- GeoJSON 功能-此物件包含任何 GeoJSON 幾何，以及包含與幾何相關聯之中繼資料的一組屬性。

如需詳細資訊，請參閱 [建立資料來源](create-data-source-android-sdk.md) 文檔以建立資料並將其新增至地圖。

下列程式碼範例會建立 GeoJSON 點幾何，並將其傳遞至 GeoJSON 功能，並將 `title` 值新增至其屬性。 在 `title` 地圖上的符號圖示上方，屬性會顯示為文字。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(0, 0));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source, 
    //Get the title property of the feature and display it on the map.
    textField(get("title"))
);

//Add the layer to the map.
map.layers.add(layer);
```

下列螢幕擷取畫面顯示上述程式碼如何使用具有符號圖層的圖示和文字標籤來轉譯點特徵。

![使用以符號圖層呈現的點（地圖），顯示點特徵的圖示和文字標籤](media/how-to-add-symbol-to-android-map/android-map-pin.png)

> [!TIP]
> 根據預設，符號圖層會隱藏重迭的符號來將符號轉譯優化。 當您放大時，隱藏的符號就會變成可見。 若要停用這項功能並隨時轉譯所有符號，請將 `iconAllowOverlap` 和 `textAllowOverlap` 選項設定為 `true` 。

## <a name="add-a-custom-icon-to-a-symbol-layer"></a>將自訂圖示新增至符號圖層

使用 WebGL 來呈現符號圖層。 所有這類資源 (例如圖示影像) 必須載入 WebGL 內容中。 此範例說明如何將自訂圖示新增至地圖資源。 然後，這個圖示會用來在地圖上以自訂符號呈現點資料。 符號圖層的 `textField` 屬性需要指定運算式。 在此情況下，我們想要呈現溫度屬性。 因為溫度是數位，所以必須轉換成字串。 此外，我們還想要將 "° F" 附加至該檔案。 運算式可以用來進行這種串連; `concat(Expression.toString(get("temperature")), literal("°F"))`.

```java
//Load a custom icon image into the image sprite of the map.
map.images.add("my-custom-icon", R.drawable.showers);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-73.985708, 40.75773));

//Add a property to the feature.
feature.addNumberProperty("temperature", 64);

//Add the feature to the data source.
source.add(feature);

//Create a symbol layer to render icons and/or text at points on the map.
SymbolLayer layer = new SymbolLayer(source,
    iconImage("my-custom-icon"),
    iconSize(0.5f),

    //Get the title property of the feature and display it on the map.
    textField(concat(Expression.toString(get("temperature")), literal("°F"))),
    textOffset(new Float[]{0f, -1.5f})
);
```

在此範例中，下圖已載入至應用程式的可繪製資料夾。

| ![Rain 大氣的氣象圖示影像](media/how-to-add-symbol-to-android-map/showers.png)|
|:-----------------------------------------------------------------------:|
| showers.png                                                  |

下列螢幕擷取畫面顯示上述程式碼如何使用自訂圖示和具有符號圖層的格式化文字標籤來轉譯點特徵。

![使用符號圖層呈現的地圖與點特徵的自訂圖示和格式化文字標籤](media/how-to-add-symbol-to-android-map/android-custom-symbol-layer.png)

> [!TIP]
> 當您只想要轉譯具有符號圖層的文字時，您可以將 `iconImage` 圖示選項的屬性設定為，以隱藏圖示 `"none"` 。

## <a name="next-steps"></a>後續步驟

請參閱下列文章，以取得更多可新增至地圖的程式碼範例：

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [使用資料驅動樣式運算式](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [顯示功能資訊](display-feature-information-android.md)
