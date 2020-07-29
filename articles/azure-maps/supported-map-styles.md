---
title: 支援的內建 Azure 地圖服務地圖樣式
description: 在本文中，您將瞭解 Microsoft Azure 對應所支援的各種內建地圖樣式。
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b9641fe647eebae020ee67ee19a9cc131a487a7c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286890"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure 地圖服務支援的內建地圖樣式

「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段

「**道路**圖」是一種標準地圖，可顯示道路。 它也會顯示自然和人工的功能，以及這些功能的標籤。

![道路圖樣式](./media/supported-map-styles/road.png)

**適用 API：**

* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="blank-and-blank_accessible"></a>空白和 blank_accessible

**空白**和**blank_accessible**地圖樣式提供空白畫布來視覺化資料。 即使未顯示基底地圖， **blank_accessible**樣式仍會繼續提供具有地圖位置詳細資料的螢幕閱讀程式更新。

> [!Note]
> 在 Web SDK 中，您可以藉由設定地圖 DIV 元素的 CSS 樣式來變更地圖的背景色彩 `background-color` 。

**適用 API：**

* Web SDK 地圖控制項

## <a name="satellite"></a>satellite

**衛星**樣式是衛星和空拍影像的結合。

![附屬磚地圖樣式](./media/supported-map-styles/satellite.png)

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

「**灰階光線**」是道路地圖樣式的輕量版本。

![灰階 light 地圖樣式](./media/supported-map-styles/grayscale-light.png)

**適用 API：**
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="night"></a>夜間

**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜間地圖樣式](./media/supported-map-styles/night.png)

**適用 API：**

* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="road_shaded_relief"></a>road_shaded_relief

**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![陰影起伏圖樣式](./media/supported-map-styles/shaded-relief.png)

**適用 API：**

* [地圖底圖](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* Android 地圖控制項
* Power BI 視覺效果

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark**是深色地圖樣式，其對比高於其他樣式。

![高對比深色地圖樣式](./media/supported-map-styles/high-contrast-dark.png)

**適用 API：**

* Web SDK 地圖控制項
* Power BI 視覺效果

## <a name="next-steps"></a>後續步驟

瞭解如何在 Azure 地圖服務中設定地圖樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
