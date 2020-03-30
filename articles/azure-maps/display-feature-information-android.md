---
title: 在 Azure 地圖 Android SDK 中顯示要素資訊 |微軟 Azure 地圖
description: 在本文中，您將瞭解如何使用 Microsoft Azure 地圖 Android SDK 在地圖上顯示要素資訊。
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911682"
---
# <a name="display-feature-information"></a>顯示功能資訊

空間資料通常使用點、線和麵表示。 此資料通常具有與其關聯的中繼資料資訊。 例如，點可能表示商店的位置，有關該餐廳的中繼資料可能是其名稱、位址和所供應的食物類型。 此中繼資料可以使用 添加為這些功能的屬性`JsonObject`。 以下代碼創建一個簡單的點要素，`title`其屬性的值為"你好世界！

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

當使用者與地圖上的要素交互時，可以使用事件對這些操作做出反應。 常見方案是顯示由使用者與之交互的功能的中繼資料屬性組成的消息。 該`OnFeatureClick`事件是用於檢測使用者何時在地圖上點擊要素的主要事件。 還有一個`OnLongFeatureClick`事件。 將`OnFeatureClick`事件添加到地圖時，可以通過傳入圖層的 ID 來將其限制為單個圖層。 如果未傳入圖層 ID，則點擊地圖上的任何要素（無論該要素位於哪個圖層中）都將觸發此事件。 以下代碼創建一個符號圖層以渲染地圖上的點資料，然後添加事件`OnFeatureClick`並將其限制為此符號圖層。

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

## <a name="display-a-toast-message"></a>顯示 Toast 消息

Toast 消息是向使用者顯示資訊的最簡單方法之一，可在 Android 的所有版本中使用。 它不支援任何類型的使用者輸入，並且只顯示一小段時間。 如果要快速讓使用者知道他們點擊的內容，Toast 消息可能是一個不錯的選擇。 以下代碼顯示 Toast 消息如何與`OnFeatureClick`事件一起使用。

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

![正在點擊的功能的動畫和顯示 Toast 消息](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

除了 Toast 消息之外，還有許多其他方法來顯示要素的中繼資料屬性，例如：

- [Snakbar 小部件](https://developer.android.com/training/snackbar/showing.html)- 小吃杆提供有關操作的羽量級回饋。 它們在移動顯示幕上螢幕底部顯示一條簡短消息，在較大的設備上顯示左下角。 小吃條顯示在螢幕上所有其他元素之上，一次只能顯示一個。
- [對話方塊](https://developer.android.com/guide/topics/ui/dialogs)- 對話方塊是一個小視窗，提示使用者做出決定或輸入其他資訊。 對話方塊不會填充螢幕，通常用於要求使用者在繼續操作之前執行操作的模式事件。
- 向當前活動添加[片段](https://developer.android.com/guide/components/fragments)。
- 導航到其他活動或視圖。

## <a name="next-steps"></a>後續步驟

要向地圖添加更多資料，請進行：

> [!div class="nextstepaction"]
> [添加符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [將形狀添加到 Android 地圖](how-to-add-shapes-to-android-map.md)
