---
title: 將反升圖圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 Microsoft Azure Maps 視覺效果中使用反升圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: ff0d2c8e90995222af79d5be63ca3e9da24b3c45
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/28/2020
ms.locfileid: "92896220"
---
# <a name="add-a-bubble-layer"></a>新增泡泡圖層

反升 **圖層** 會將位置資料轉譯為地圖上的縮放圓形。

> [!div class="mx-imgBorder"]
> ![使用反升圖圖層顯示點資料的地圖](media/power-bi-visual/bubble-layer-with-legend-color.png)

一開始，所有的氣泡都有相同的填滿色彩。 如果欄位已傳遞至 [ **欄位** ] 窗格的 [圖例] 值區中，則會根據其分類來將氣泡 **標示** 為彩色。 [反升] 的輪廓預設為白色，但可以變更為新的色彩，或啟用高對比大綱選項。 **高對比大綱** 選項會動態指派外框色彩，其為填滿色彩的高對比變異。 這有助於確保不論地圖的樣式為何，都能清楚地顯示氣泡。 以下是 [反升 **圖層** ] 區段中可用的 [ **格式** ] 窗格中的主要設定。

| 設定               | 描述    |
|-----------------------|----------------|
| 大小                  | 每個氣泡的大小。 當欄位傳遞到 [ **欄位** ] 窗格的 [ **大小] 值** 區時，就會隱藏這個選項。 如需其他選項，請參閱本文後面的「反升 [尺寸縮放](#bubble-size-scaling) 」主題中所述。 |
| 填滿色彩            | 每個氣泡的色彩。 當欄位已傳遞至 [ **欄位** ] 窗格的 [ **圖例** ] 值區，而且 [ **格式** ] 窗格中顯示不同的 [ **資料色彩** ] 區段時，就會隱藏這個選項。 |
| 填滿透明度     | 每個氣泡的透明度。 |
| 高對比大綱 | 使用填滿色彩的高對比變異，讓外框色彩與填滿色彩對比，以獲得更好的協助工具。 |
| 外框色彩         | 概述反升的色彩。 啟用 **高對比大綱** 選項時，會隱藏此選項。 |
| 大綱透明  | 外框的透明度。 |
| 外框寬度         | 框線的寬度（以圖元為單位）。 |
| 模糊                  | 套用至外框的模糊量。 值為1時，不會將氣泡模糊，因此只有中心點沒有透明度。 值為0會套用任何模糊效果。 |
| 音調對齊       | 指定當地圖有音調時，如何進行反升的外觀。 <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•視口-在地圖上的相對於視口的邊緣出現 [氣泡]。 (預設值)<br/>&nbsp;&nbsp;&nbsp;&nbsp;•地圖會在地圖介面上以平坦的圖呈現。 |
| 縮放比例            | 氣泡相對於縮放層級的調整量。 縮放比例表示無縮放比例。 放大時，大數值會讓氣泡變小。 這有助於減少地圖上的雜亂，並在放大時確保點顯得更多。 值為1時，不會套用任何調整。 |
| 最小縮放              | 可使用最小縮放層級磚。 |
| 最大縮放              | 最大縮放層級磚可供使用。 |
| 圖層位置        | 指定圖層相對於其他地圖圖層的位置。 |

## <a name="bubble-size-scaling"></a>氣泡大小調整

如果欄位已傳遞至 [ **欄位** ] 窗格的 [ **大小** ] 值區，則會相對於每個資料點的量值調整氣泡。 當欄位傳遞到 **大小** 值區時，[ **格式** ] 窗格的 [反升 **圖層** ] 區段中的 [ **大小** ] 選項將會消失，因為反升會在最小值與最大值之間調整半徑。 當 **大小值** 區有指定欄位時，下列選項將會出現在 [ **格式** ] 窗格的 [反升 **圖層** ] 區段中。

| 設定             | 描述  |
|---------------------|--------------|
| 最小大小            | 調整資料時的最小反升尺寸。|
| 大小上限            | 調整資料時的最大反升尺寸。|
| 大小調整方法 | 用來決定相對反升尺寸的調整演算法。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•線性範圍的輸入資料會以線性方式對應至最小和最大大小。 (預設值)<br/>&nbsp;&nbsp;&nbsp;&nbsp;•對應至最小和最大大小之輸入資料 logarithmically 的記錄範圍。<br/>&nbsp;&nbsp;&nbsp;&nbsp;• Cubic-Bezier-指定 Cubic-Bezier 曲線的 X1、Y1、X2、Y2 值，以建立自訂的調整方法。 |

當 **大小調整方法** 設定為 [ **記錄** ] 時，將會提供下列選項。

| 設定   | 描述      |
|-----------|------------------|
| 記錄規模 | 計算氣泡大小時要套用的對數刻度。 |

當 [ **大小調整] 方法** 設定為 [ **三次方貝塞爾** ] 時，會提供下列選項來自訂縮放曲線。

| 設定 | 描述                           |
|---------|---------------------------------------|
| X1      | 三次方貝茲曲線的 X1 參數。 |
| Y1      | 三次方貝茲曲線的 X2 參數。 |
| X2      | 三次方貝茲曲線的 Y1 參數。 |
| Y2      | 三次方貝茲曲線的 Y2 參數。 |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/) 有一個便利的工具，可用於建立 Cubic-Bezier 曲線的參數。

## <a name="next-steps"></a>後續步驟

變更在地圖上顯示資料的方式：

> [!div class="nextstepaction"]
> [新增長條圖圖層](power-bi-visual-add-bar-chart-layer.md)

在地圖中新增更多內容：

> [!div class="nextstepaction"]
> [新增參考圖層](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)

自訂視覺效果：

> [!div class="nextstepaction"]
> [在 Power BI 中色彩格式化的秘訣和訣竅](/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [自訂視覺效果標題、背景和圖例](/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)