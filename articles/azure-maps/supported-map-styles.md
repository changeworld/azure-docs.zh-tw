---
title: 支援的內建 Azure 地圖服務地圖樣式
description: 瞭解 Azure 地圖服務支援的內建地圖樣式，例如道路、blank_accessible、衛星、satellite_road_labels、road_shaded_relief 和夜間。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4a50a9d6d4a485f7d8e63adb9ae5032f49edc261
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/25/2020
ms.locfileid: "91310351"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure 地圖服務支援的內建地圖樣式

「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段

**道路**地圖是顯示道路的標準地圖。 它也會顯示自然和人工的功能，以及這些功能的標籤。

![道路地圖樣式](./media/supported-map-styles/road.png)

**適用 API：**

* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="blank-and-blank_accessible"></a>空白和 blank_accessible

**空白**和**blank_accessible**的地圖樣式會提供空白畫布來將資料視覺化。 **Blank_accessible**樣式將繼續提供具有地圖位置詳細資料的螢幕讀取器更新，即使未顯示基底地圖也一樣。

> [!Note]
> 在 Web SDK 中，您可以藉由設定地圖 DIV 元素的 CSS 樣式來變更地圖的背景色彩 `background-color` 。

**適用 API：**

* Web SDK 地圖控制項

## <a name="satellite"></a>satellite

**衛星**樣式是衛星和空拍影像的結合。

![附屬圖格地圖樣式](./media/supported-map-styles/satellite.png)

**適用 API：**

* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="satellite_road_labels"></a>satellite_road_labels

此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels 地圖樣式](./media/supported-map-styles/satellite-road-labels.png)

**適用 API：**

* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="grayscale_dark"></a>grayscale_dark

**深灰階**是路段圖樣式的深色版本。

![gray_scale 地圖樣式](./media/supported-map-styles/grayscale-dark.png)

**適用 API：**

* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="grayscale_light"></a>grayscale_light

**灰階燈** 是道路地圖樣式的輕量版本。

![灰階淺色地圖樣式](./media/supported-map-styles/grayscale-light.png)

**適用 API：**
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="night"></a>夜間

**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜晚地圖樣式](./media/supported-map-styles/night.png)

**適用 API：**

* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="road_shaded_relief"></a>road_shaded_relief

**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏地圖樣式](./media/supported-map-styles/shaded-relief.png)

**適用 API：**

* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** 是深色的地圖樣式，相較于其他樣式的對比度更高。

![高對比深色地圖樣式](./media/supported-map-styles/high-contrast-dark.png)

**適用 API：**

* Web SDK 地圖控制項
* Power BI 視覺效果

## <a name="next-steps"></a>後續步驟

瞭解如何在 Azure 地圖服務中設定地圖樣式：

[選擇地圖樣式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)