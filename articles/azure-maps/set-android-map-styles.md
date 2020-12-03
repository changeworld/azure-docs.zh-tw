---
title: 使用 Azure 地圖服務 Android SDK 設定地圖樣式
description: 瞭解設定地圖樣式的兩種方式。 瞭解如何使用版面配置檔案或活動類別中的 Microsoft Azure Maps Android SDK 來調整樣式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/18/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8c7689fb87575ac6e150f793b43f35e8bf6adc83
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532464"
---
# <a name="set-map-style-using-azure-maps-android-sdk"></a>使用 Azure 地圖服務 Android SDK 設定地圖樣式

本文說明如何使用 Azure 地圖服務 Android SDK 設定地圖樣式。 Azure 地圖服務有六種不同的地圖樣式可供選擇。 如需支援的地圖樣式的詳細資訊，請參閱 [Azure 地圖服務中支援的地圖樣式](./supported-map-styles.md)。

## <a name="prerequisites"></a>Prerequisites

1. [建立 Azure 地圖服務帳戶](quick-demo-map-app.md#create-an-azure-maps-account)
2. [取得主要訂用帳戶金鑰](quick-demo-map-app.md#get-the-primary-key-for-your-account)，也稱為主要金鑰或訂用帳戶金鑰。
3. 下載並安裝 [Azure 地圖服務 Android SDK](./how-to-use-android-map-control-library.md)。


## <a name="set-map-style-in-the-layout"></a>在版面配置中設定地圖樣式

您可以在活動類別的版面配置檔案中設定地圖樣式。 編輯 `res > layout > activity_main.xml` ，如下所示：

```XML
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

`mapcontrol_style`上述屬性會將地圖樣式設為 **grayscale_dark**。

:::image type="content" source="./media/set-android-map-styles/grayscale-dark.png" border="true" alt-text="Azure 地圖服務，將樣式顯示為 grayscale_dark 的地圖影像":::

## <a name="set-map-style-in-the-mainactivity-class"></a>在 >mainactivity 類別中設定地圖樣式

地圖樣式也可以在 >mainactivity 類別中設定。 開啟檔案 `java > com.example.myapplication > MainActivity.java` ，並將下列程式碼片段複製到 **>oncreate ( # B1** 方法中。 這段程式碼會將地圖樣式設定為 **satellite_road_labels**。

>[!WARNING]
>Android Studio 可能尚未匯入必要的類別。  因此，程式碼將會有一些無法解析的參考。 若要匯入所需的類別，只要將滑鼠停留在每個無法解析的參考上，然後按 `Alt + Enter` (選項 + 在 Mac) 上返回。

```Java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(47.64, -122.33), zoom(14));

    //Set the style of the map.
    map.setStyle((style(SATELLITE_ROAD_LABELS)));
       
});
```

:::image type="content" source="./media/set-android-map-styles/satellite-road-labels.png" border="true" alt-text="Azure 地圖服務，將樣式顯示為 satellite_road_labels 的地圖影像":::