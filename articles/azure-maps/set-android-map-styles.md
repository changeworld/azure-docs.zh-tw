---
title: 使用 Azure 地圖 Android SDK 設置地圖樣式*微軟 Azure 地圖
description: 在本文中，您將瞭解 Android SDK 的 Microsoft Azure 地圖樣式相關功能。
author: philmea
ms.author: philmea
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: a4d761abf54682ed0263922d0a118debc9eccf0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334360"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>使用 Azure 地圖 Android SDK 設置地圖樣式

本文顯示了使用 Azure 地圖 Android SDK 設置地圖樣式的兩種方法。 Azure 地圖有六種不同的地圖樣式可供選擇。 有關支援的地圖樣式的詳細資訊，請參閱[Azure 地圖 中支援地圖樣式](./supported-map-styles.md)。


## <a name="prerequisites"></a>Prerequisites

要完成本文中的過程，您需要安裝[Azure 地圖 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)來載入地圖。


## <a name="set-map-style-in-the-layout"></a>在佈局中設置地圖樣式

您可以在活動類的佈局檔中設置地圖樣式。 **>activity_main.xml 編輯>佈局**，因此它看起來像下面的佈局：

```XML
<?xml version="1.0" encoding="utf-8"?>
<FrameLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    >

    <com.microsoft.azure.maps.mapcontrol.MapControl
        android:id="@+id/mapcontrol"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:mapcontrol_centerLat="47.602806"
        app:mapcontrol_centerLng="-122.329330"
        app:mapcontrol_zoom="12"
        app:mapcontrol_style="grayscale_dark"
        />

</FrameLayout>
```

上面`mapcontrol_style`的屬性將地圖樣式設置為**grayscale_dark**。 

<center>

![風格-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>在活動類中設置地圖樣式

可以在活動類中設置地圖樣式。 將以下程式碼片段複製到類`MainActivity.java`的**onCreate（）** 方法中。 此代碼將地圖樣式設置為**satellite_road_labels**。

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![風格衛星道路標籤](./media/set-android-map-styles/satellite-road-labels.png)</center>