---
title: 處理 Android 地圖中的地圖事件 |Microsoft Azure 對應
description: 瞭解使用者與地圖互動時所引發的事件。 查看所有支援的地圖事件清單。 請參閱如何使用 Azure 地圖服務 Android SDK 來處理事件。
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681467"
---
# <a name="interact-with-the-map-android-sdk"></a> (Android SDK 的地圖互動) 

本文說明如何使用 maps 事件管理員。

## <a name="interact-with-the-map"></a>與地圖互動

對應會透過其屬性來管理所有事件 `events` 。 下表列出所有支援的地圖事件。

| 事件                  | 事件處理常式格式 | 描述 |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>在地圖進入「閒置」狀態之前最後一個轉譯的畫面之後引發：<ul><li>沒有任何觀景窗正在轉換。</li><li>所有目前要求的圖格都已載入。</li><li>所有淡出或轉換動畫都已完成。</li></ul></p> |
| `OnCameraMove`         | `()`                 | 在因使用者互動或方法的結果，而從某個檢視動態轉換到另一個檢視期間重複引發。 |
| `OnCameraMoveCanceled` | `()`                 | 在對相機的移動要求取消時引發。 |
| `OnCameraMoveStarted`  | `(int reason)`       | 在地圖因使用者互動或方法的結果，而開始從某個檢視轉換到另一個檢視之前引發。 `reason`事件接聽程式的引數會傳回整數值，以提供如何起始相機移動的詳細資料。 以下是可能的原因清單：<ul><li>1：手勢</li><li>2：開發人員動畫</li><li>3： API 動畫</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | 在地圖上的相同位置按下並放開地圖時引發。 |
| `OnFeatureClick`       | `(List<Feature>)`    | 在功能的相同時間點按下並放開地圖時引發。  |
| `OnLongClick`          | `(double lat, double lon)` | 在按下地圖時引發，並保留一段時間，然後在地圖上的相同位置放開。 |
| `OnLongFeatureClick `  | `(List<Feature>)`    | 在按下地圖時引發，並保留一段時間，然後在功能的相同時間點釋放。 |
| `OnReady`              | `(AzureMap map)`     | 在一開始載入地圖時或應用程式方向變更時引發，而且會載入所需的最小對應資源，且對應可供以程式設計方式進行互動。 |

下列程式碼示範如何將 `OnClick` 、 `OnFeatureClick` 和 `OnCameraMove` 事件加入至對應。

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

如需詳細資訊，請參閱 [流覽對應](how-to-use-android-map-control-library.md#navigating-the-map) 檔，瞭解如何與地圖和觸發程式事件互動。

## <a name="scope-feature-events-to-layer"></a>將功能事件範圍設為圖層

將 `OnFeatureClick` 或事件新增 `OnLongFeatureClick` 至對應時，可以將圖層識別碼作為第二個參數傳入。 傳入圖層識別碼時，事件只會在該圖層發生事件時引發。 符號、反升、線條和多邊形圖層支援範圍為各層的事件。

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>後續步驟

請參閱下列文章中的完整程式碼範例：

> [!div class="nextstepaction"]
> [瀏覽地圖](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增泡泡圖層](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)
