---
title: 在 Android 地圖中顯示功能資訊 |Microsoft Azure 對應
description: 瞭解如何在使用者與地圖功能互動時顯示資訊。 使用 Azure 地圖服務 Android SDK 來顯示快顯訊息和其他類型的訊息。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 37b5ab1c144ed81d995da40b87edeaccdcad7253
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679983"
---
# <a name="display-feature-information"></a>顯示功能資訊

空間資料通常是使用點、線條和多邊形來表示。 這種資料通常會有相關聯的中繼資料資訊。 例如，某個點可能代表餐廳的地點，而該餐廳的中繼資料可能是其所提供的名稱、位址及食物類型。 此中繼資料可以新增為 GeoJSON 的屬性 `Feature` 。 下列程式碼會建立一個具有 `title` 值為 "Hello World！" 之屬性的簡單點功能。

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64));

//Add a property to the feature.
feature.addStringProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
source.add(feature);
```

如需建立資料和將資料加入至地圖的方式，請參閱「 [建立資料來源](create-data-source-android-sdk.md) 」檔。

當使用者與地圖上的功能互動時，事件可以用來回應這些動作。 常見的案例是顯示由使用者互動之功能的中繼資料屬性所組成的訊息。 `OnFeatureClick`事件是用來偵測使用者在地圖上點擊功能時所使用的主要事件。 另外還有一個 `OnLongFeatureClick` 事件。 將事件新增 `OnFeatureClick` 至地圖時，可以藉由傳入圖層的識別碼加以限制，限制為單一層。 如果未傳入任何圖層識別碼，只要在地圖上按下任何功能，就會引發此事件。 下列程式碼會建立符號圖層，以在地圖上呈現點資料，然後加入 `OnFeatureClick` 事件，並將其限制為此符號圖層。

```java
//Create a symbol and add it to the map.
SymbolLayer layer = new SymbolLayer(source);
map.layers.add(layer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Do something with the message.
}, layer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>顯示快顯訊息

快顯訊息是向使用者顯示資訊最簡單的方式之一，在所有版本的 Android 中都有提供。 它不支援任何類型的使用者輸入，而且只會在短時間內顯示。 如果您想要快速讓使用者知道他們所說的內容，則快顯訊息可能是不錯的選擇。 下列程式碼說明如何搭配事件使用快顯訊息 `OnFeatureClick` 。

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, layer.getId());    //Limit this event to the symbol layer.
```

![正在點擊的功能和顯示的快顯訊息的動畫](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)

除了快顯訊息之外，還有許多其他方式可呈現功能的中繼資料屬性，例如：

- [Snackbar 小工具](https://developer.android.com/training/snackbar/showing.html)  -  `Snackbars`提供有關操作的輕量意見反應。 他們會在行動裝置上的畫面底部顯示簡短的訊息，並在較大的裝置上向下移動。 `Snackbars` 會顯示在螢幕上的所有其他元素上方，一次只能顯示一個專案。
- [對話方塊-對話方塊](https://developer.android.com/guide/topics/ui/dialogs) 是一個小視窗，會提示使用者進行決策或輸入其他資訊。 對話方塊不會填滿畫面，通常用於需要使用者採取動作才能繼續的強制回應事件。
- 將 [片段](https://developer.android.com/guide/components/fragments) 新增至目前的活動。
- 流覽至其他活動或視圖。

## <a name="next-steps"></a>後續步驟

若要將更多資料新增至對應：

> [!div class="nextstepaction"]
> [回應對應事件](android-map-events.md)

> [!div class="nextstepaction"]
> [建立資料來源](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)
