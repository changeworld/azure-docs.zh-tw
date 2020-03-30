---
title: 支援的地圖樣式 |微軟 Azure 地圖
description: 在本文中，您將瞭解 Microsoft Azure 地圖支援的不同地圖呈現樣式。
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334029"
---
# <a name="azure-maps-supported-map-styles"></a>Azure 地圖服務支援的地圖樣式
「Azure 地圖服務」支援數個不同的內建地圖樣式，如下所述。

## <a name="road"></a>路段
**路段**圖是標準的地圖，其中顯示道路、自然和人工景觀，以及這些景觀的標籤。

![路線圖樣式](./media/supported-map-styles/road.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖磁貼](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* 安卓地圖控制項

## <a name="blank-and-blank_accessible"></a>空白和blank_accessible

**空白**和**blank_accessible**地圖樣式提供了一個空白畫布，用於在其中視覺化資料。 **blank_accessible**樣式將繼續提供帶有地圖位置詳細資訊的螢幕閱讀器更新，即使不顯示底地圖也是如此。

> [!Note]
> 在 Web SDK 中，您可以通過設置地圖 DIV 元素的 CSS`background-color`樣式來更改地圖的背景顏色。

**適用 API：**
* Web SDK 地圖控制項

## <a name="satellite"></a>衛星 
**衛星**樣式是衛星和空拍影像的結合。

![衛星瓷磚地圖樣式](./media/supported-map-styles/satellite.png)

**適用 API：**
* [衛星底圖](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Web SDK 地圖控制項
* 安卓地圖控制項

## <a name="satellite_road_labels"></a>satellite_road_labels
此地圖樣式包含道路及覆蓋在衛星和空拍影像上的標籤。

![satellite_road_labels地圖樣式](./media/supported-map-styles/satellite-road-labels.png)

**適用 API：**
* Web SDK 地圖控制項
* 安卓地圖控制項

## <a name="grayscale_dark"></a>grayscale_dark
**深灰階**是路段圖樣式的深色版本。

![gray_scale地圖樣式](./media/supported-map-styles/grayscale-dark.png)

**適用 API：**
* [地圖影像](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [地圖磁貼](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項 
* 安卓地圖控制項


## <a name="grayscale_light"></a>grayscale_light
**灰度光**是路線圖樣式的淺色版本。

![灰度光貼圖樣式](./media/supported-map-styles/grayscale-light.png)

**適用 API：**
* Web SDK 地圖控制項
* 安卓地圖控制項


## <a name="night"></a>夜間
**夜間**是路段圖樣式的深色版本，具有彩色的道路和符號。

![夜地圖樣式](./media/supported-map-styles/night.png)

**適用 API：**
* Web SDK 地圖控制項
* 安卓地圖控制項

## <a name="road_shaded_relief"></a>road_shaded_relief
**路段陰影起伏圖**是以地球等高線完成的「Azure 地圖服務」的主要樣式。

![底子浮雕地圖樣式](./media/supported-map-styles/shaded-relief.png)

**適用 API：**
* [地圖磁貼](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Web SDK 地圖控制項
* 安卓地圖控制項

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark**是一種暗地圖樣式，與其他樣式相比對比度更高。

![高對比深色地圖樣式](./media/supported-map-styles/high-contrast-dark.png)

**適用 API：**
* Web SDK 地圖控制項

## <a name="next-steps"></a>後續步驟

瞭解如何在 Azure 地圖中設置地圖樣式：

> [!div class="nextstepaction"]
> [選擇地圖樣式](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
