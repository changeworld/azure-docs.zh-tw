---
title: 開始使用 Android 地圖控制項 | Microsoft Azure Maps
description: 熟悉 Azure 地圖服務 Android SDK。 瞭解如何在 Android Studio 中建立專案、安裝 SDK，以及建立互動式地圖。
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
ms.openlocfilehash: a7533e079ca13f8ac891fa96f11f740a21c1a3dc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680384"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>開始使用 Azure 地圖服務 Android SDK

Azure 地圖服務 Android SDK 是適用於 Android 的向量地圖庫。 本文會引導您完成安裝 Azure 地圖服務 Android SDK 和載入地圖的程序。

## <a name="prerequisites"></a>Prerequisites

請務必完成 [快速入門：建立 Android 應用程式](quick-android-map.md) 檔中的步驟。

## <a name="localizing-the-map"></a>將地圖當地語系化

Azure 地圖服務 Android SDK 提供三種不同的方式供您設定地圖的語言和區域檢視。 下列程式碼示範如何將語言設定為法文 ( "fr-fr" ) ，並將區域視圖設定為 "Auto"。

第一個選項是全域使用靜態 `setLanguage` 和 `setView` 方法，將語言和檢視區域資訊傳入 `AzureMaps` 類別中。 這會在應用程式中載入的所有 Azure 地圖服務控制項間設定預設語言和區域檢視。

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

第二個選項是將語言和檢視資訊傳入地圖控制項 XML 中。

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

第三個選項是使用地圖 `setStyle` 方法，以程式設計方式設定地圖的語言和區域檢視。 此方法可隨時執行，以變更地圖的語言和區域檢視。

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

以下範例是將語言設定為 "fr-fr" 且區域視圖設定為 "Auto" 的 Azure 地圖服務。

![Azure 地圖服務，以法文顯示標籤的地圖影像](media/how-to-use-android-map-control-library/android-localization.png)

如需支援的語言和區域檢視的完整清單，請參閱[這裡](supported-languages.md)。

## <a name="navigating-the-map"></a>瀏覽地圖

有數種不同方式可用來對地圖進行縮放、移動瀏覽、旋轉及調整傾斜度。 以下將詳細說明瀏覽地圖的各種不同方式。

**縮放地圖**

* 以兩支手指接觸地圖，捏合即可縮小，張開手指即可放大。
* 在地圖上點兩下可放大一個等級。
* 以兩個手指點一下，將地圖放大一層。
* 點兩下；在點第二下時將手指放在地圖上並向上拖曳可放大，向下拖曳則可縮小。

**移動瀏覽地圖**

* 觸控地圖，然後往任何方向拖曳。

**旋轉地圖**

* 以兩支手指觸控地圖並旋轉。

**調整地圖傾斜度**

* 以兩支手指觸控地圖，然後將其一起向上或向下拖曳。

## <a name="azure-government-cloud-support"></a>Azure Government 雲端支援

Azure 地圖服務 Android SDK 支援 Azure Government 雲端。 您可以從相同的 Maven 存放庫存取 Azure 地圖服務 Android SDK。 您必須完成下列工作，才能連接到 Azure Government cloud 版本的 Azure 地圖服務平臺。

在指定 Azure 地圖服務 authentication 詳細資料的相同位置，新增下列程式程式碼，告訴地圖使用 Azure 地圖服務政府雲端網域。

```java
AzureMaps.setDomain("atlas.azure.us");
```

驗證對應和服務時，請務必使用 Azure Government 雲端平臺 Azure 地圖服務的驗證詳細資料。

## <a name="next-steps"></a>後續步驟

了解如何在地圖上新增重疊資料：

> [!div class="nextstepaction"]
> [管理 Azure 地圖服務中的驗證](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [變更 Android 地圖中的地圖樣式](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [新增符號圖層](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [新增線條圖層](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [新增多邊形圖層](how-to-add-shapes-to-android-map.md)
