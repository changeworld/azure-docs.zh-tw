---
title: 在 Azure 地圖服務 Android SDK 中顯示功能資訊 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何使用 Microsoft Azure Maps Android SDK，在地圖上顯示功能資訊。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "75911682"
---
# <a name="display-feature-information"></a>顯示功能資訊

空間資料通常是使用點、線條和多邊形來表示。 此資料通常會有相關聯的中繼資料資訊。 例如，某個點可能代表商店的位置，而該餐廳的中繼資料可能是其名稱、位址和其所提供的食物類型。 您可以使用，將這個中繼資料新增為這些功能`JsonObject`的屬性。 下列程式碼會使用`title`屬性（其值為 "Hello World！"）來建立簡單點功能。

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

當使用者與地圖上的功能互動時，可以使用事件來回應這些動作。 常見的案例是針對使用者互動之功能的中繼資料屬性顯示訊息。 `OnFeatureClick`事件是主要事件，用來偵測使用者何時按地圖上的功能。 另外還有一個`OnLongFeatureClick`事件。 將`OnFeatureClick`事件加入至對應時，您可以藉由傳入圖層的識別碼來限制它的範圍，以將它限制為單一層。 如果未傳入任何圖層識別碼，不論其所在的層級為何，在地圖上點擊任何功能都會引發此事件。 下列程式碼會建立符號圖層來轉譯地圖上的點資料，然後加入`OnFeatureClick`事件並將其限制為此符號圖層。

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>顯示快顯通知訊息

快顯訊息是向使用者顯示資訊的最簡單方式之一，並可在所有版本的 Android 中使用。 它不支援任何類型的使用者輸入，而且只會在短時間內顯示。 如果您想要快速地讓使用者知道他們所用的專案，快顯訊息可能是個不錯的選項。 下列程式碼顯示快顯訊息如何與`OnFeatureClick`事件搭配使用。

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![所指的功能的動畫，以及要顯示的快顯訊息](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

除了快顯訊息以外，還有許多其他方法可以呈現功能的中繼資料屬性，例如：

- [Snakbar widget](https://developer.android.com/training/snackbar/showing.html) -Snackbars 提供有關作業的輕量意見反應。 它們會在行動裝置的底部顯示簡短的訊息，並在較大的裝置上顯示左下方。 Snackbars 會出現在螢幕上的所有其他專案上方，一次只能顯示一個專案。
- [對話方塊-對話](https://developer.android.com/guide/topics/ui/dialogs)是一個小視窗，會提示使用者做出決定，或輸入其他資訊。 對話方塊不會填滿畫面，而且通常會用於需要使用者採取動作才能繼續的強制回應事件。
- 將[片段](https://developer.android.com/guide/components/fragments)新增至目前的活動。
- 流覽至另一個 [活動] 或 [視圖]。

## <a name="next-steps"></a>後續步驟

若要將更多資料新增至您的對應：

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [將圖形新增至 Android 地圖](how-to-add-shapes-to-android-map.md)
