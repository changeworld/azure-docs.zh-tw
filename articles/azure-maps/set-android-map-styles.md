---
title: 使用 Azure 地圖服務 Android SDK 來設定地圖樣式 |Microsoft Azure 對應
description: 在本文中，您將瞭解 Android SDK 的 Microsoft Azure Maps 樣式相關功能。
author: farah-alyasari
ms.author: v-faalya
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 96564a89a2b64203eef913b0d8300f0dafa332c5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209574"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 設定地圖樣式

本文說明使用 Azure 地圖服務 Android SDK 設定地圖樣式的兩種方式。 Azure 地圖服務有六種不同的地圖樣式可供選擇。 如需有關支援的地圖樣式的詳細資訊，請參閱[Azure 地圖服務中支援的地圖樣式](./supported-map-styles.md)。


## <a name="prerequisites"></a>Prerequisites

若要完成本文中的程式，您必須安裝[Azure 地圖服務 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)以載入對應。


## <a name="set-map-style-in-the-layout"></a>設定版面配置中的地圖樣式

您可以在活動類別的版面配置檔案中設定地圖樣式。 編輯**res > layout > activity_main .xml**，使其看起來如下所示：

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

上述 `mapcontrol_style` 屬性會將地圖樣式設為**grayscale_dark**。 

<center>

![樣式-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>設定 activity 類別中的地圖樣式

地圖樣式可以在 activity 類別中設定。 將下列程式碼片段複製到 `MainActivity.java` 類別的**onCreate （）** 方法中。 這段程式碼會將地圖樣式設定為**satellite_road_labels**。

```Java
mapControl.onReady(map -> {
    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE));
});
```

<center>

![樣式-衛星道路-標籤](./media/set-android-map-styles/satellite-road-labels.png)</center>