---
title: 使用 Azure 地圖服務 Android SDK 來設定地圖樣式 |Microsoft Azure 對應
description: 瞭解設定地圖樣式的兩種方式。 請參閱如何使用設定檔案或活動類別中的 Azure 地圖服務 Android SDK 來調整樣式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4e37ae82b46ce06162d0a67d74af54cddaf3f6eb
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/10/2020
ms.locfileid: "88030940"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 設定地圖樣式

本文說明使用 Azure 地圖服務 Android SDK 設定地圖樣式的兩種方式。 Azure 地圖服務有六種不同的地圖樣式可供選擇。 如需有關支援的地圖樣式的詳細資訊，請參閱[Azure 地圖服務中支援的地圖樣式](./supported-map-styles.md)。


## <a name="prerequisites"></a>必要條件

若要完成本文中的程式，您必須安裝[Azure 地圖服務 Android SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-android-map-control-library)以載入對應。


## <a name="set-map-style-in-the-layout"></a>設定版面配置中的地圖樣式

您可以在活動類別的版面配置檔案中設定地圖樣式。 編輯**res > 配置 > activity_main.xml**，使其看起來如下所示：

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

`mapcontrol_style`上述屬性會將地圖樣式設定為**grayscale_dark**。 

<center>

![樣式-grayscale_dark](./media/set-android-map-styles/grayscale-dark.png)</center>

## <a name="set-map-style-in-the-activity-class"></a>設定 activity 類別中的地圖樣式

地圖樣式可以在 activity 類別中設定。 將下列程式碼片段複製到您類別的**onCreate ( # B1**方法中 `MainActivity.java` 。 這段程式碼會將地圖樣式設定為**satellite_road_labels**。

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