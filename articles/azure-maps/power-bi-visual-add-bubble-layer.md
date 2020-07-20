---
title: 將反升圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 [Microsoft Azure 地圖] 視覺效果中使用反升圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 4443b0f479079a4722a5d62fea40afcb4a58632d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261678"
---
# <a name="add-a-bubble-layer"></a>新增泡泡圖層

反升**圖層**會將位置資料轉譯為地圖上的縮放圓形。

> [!div class="mx-imgBorder"]
> ![使用反升層來顯示點資料的地圖](media/power-bi-visual/bubble-layer-with-legend-color.png)

一開始，所有的氣泡都具有相同的填滿色彩。 如果將欄位傳遞至 [**欄位**] 窗格的 [**圖例**] 值區，則會根據其分類來著色氣泡。 [氣泡] 的輪廓是 [白色]，但可以變更為新的色彩，或啟用 [高對比] 大綱選項。 [**高對比外框**] 選項會動態指派外框色彩，這是填滿色彩的高對比變體。 這有助於確保氣泡清楚可見，不論地圖的樣式為何。 下列是 [**格式**] 窗格中的主要設定，可以在 [**氣泡圖層**] 區段中找到。

| 設定               | 描述    |
|-----------------------|----------------|
| 大小                  | 每個氣泡的大小。 當欄位傳遞至 [**欄位**] 窗格的 [**大小] 值**區時，此選項會隱藏。 如需其他選項，請參閱本文正下方的「反升[大小調整](#bubble-size-scaling)」主題中所述。 |
| 填滿色彩            | 每個氣泡的色彩。 當欄位傳遞至 [**欄位**] 窗格的 [**圖例**] 值區，而且 [**格式**] 窗格中出現個別的 [**資料色彩**] 區段時，這個選項就會隱藏。 |
| 填滿透明度     | 每個氣泡的透明度。 |
| 高對比外框 | 使用填滿色彩的高對比變體，讓大綱色彩與填滿色彩相比較，以取得更佳的協助工具。 |
| 外框色彩         | 概述氣泡的色彩。 啟用 [**高對比外框**] 選項時，會隱藏此選項。 |
| 外框透明度  | 外框的透明度。 |
| 外框寬度         | 外框的寬度（以圖元為單位）。 |
| 模糊                  | 套用至外框的模糊數量。 值為1會模糊氣泡，因此只有中心點沒有透明度。 值為0會套用任何模糊效果。 |
| 音調對齊       | 指定當地圖為音調時，氣泡的外觀。 <br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• [視口-氣泡] 會在相對於 [視口] 的地圖上顯示在其邊緣上。 (預設值)<br/>&nbsp;&nbsp;&nbsp;&nbsp;•地圖-氣泡會在地圖的表面上以平面方式呈現。 |
| 縮放比例            | 氣泡相對於縮放層級的縮放量。 其中一個縮放比例表示沒有縮放比例。 當放大時，大數值會使氣泡變小。 當縮小時，這有助於減少地圖上的雜亂，同時可確保點在放大時有更多的功能。 值為1時，不會套用任何調整。 |
| 最小縮放              | 最小縮放層級磚可供使用。 |
| 縮放上限              | 最大縮放層級磚可供使用。 |
| 圖層位置        | 指定圖層相對於其他地圖圖層的位置。 |

## <a name="bubble-size-scaling"></a>反升大小調整

如果將欄位傳遞至 [**欄位**] 窗格的 [**大小**] 值區，則會將氣泡調整為相對於每個資料點的量值。 當欄位傳入 [**大小**] 值區時，[**格式**] 窗格的 [**氣泡圖層**] 區段中的 [**大小**] 選項將會消失，因為氣泡會將其半徑調整為最小和最大值。 當**大小值**區具有指定的欄位時，下列選項會出現在 [**格式**] 窗格的 [**氣泡圖層**] 區段中。

| 設定             | 描述  |
|---------------------|--------------|
| 大小下限            | 調整資料時的最小氣泡大小。|
| 大小上限            | 調整資料時的最大氣泡大小。|
| 大小調整方法 | 用來決定相對反升大小的調整演算法。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;•線性範圍的輸入資料，以線性方式對應到最小和最大大小。 (預設值)<br/>&nbsp;&nbsp;&nbsp;&nbsp;•輸入資料 logarithmically 的記錄範圍會對應到最小和最大大小。<br/>&nbsp;&nbsp;&nbsp;&nbsp;•三次方貝塞爾-指定以三次方貝茲曲線的 X1、Y1、X2、Y2 值，以建立自訂的縮放方法。 |

當**大小調整方法**設定為 [**記錄**] 時，將會提供下列選項。

| 設定   | 描述      |
|-----------|------------------|
| 對數刻度 | 計算氣泡大小時要套用的對數刻度。 |

當 [**大小調整] 方法**設定為 [**三次方貝塞爾**] 時，將會使用下列選項來自訂縮放曲線。

| 設定 | 描述                           |
|---------|---------------------------------------|
| X1      | 三次方貝茲曲線的 X1 參數。 |
| Y1      | 三次方貝茲曲線的 X2 參數。 |
| X2      | 三次方貝茲曲線的 Y1 參數。 |
| Y2      | 三次方貝茲曲線的 Y2 參數。 |

> [!TIP]
> [https://cubic-bezier.com/](https://cubic-bezier.com/)具有便利的工具，可用於建立三次方貝茲曲線的參數。

## <a name="next-steps"></a>後續步驟

變更您的資料在地圖上的顯示方式：

> [!div class="nextstepaction"]
> [新增橫條圖圖層](power-bi-visual-add-bar-chart-layer.md)

將更多內容新增至地圖：

> [!div class="nextstepaction"]
> [新增參考圖層](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)

自訂視覺效果：

> [!div class="nextstepaction"]
> [在 Power BI 中色彩格式化的秘訣和訣竅](https://docs.microsoft.com/power-bi/visuals/service-tips-and-tricks-for-color-formatting)

> [!div class="nextstepaction"]
> [自訂視覺效果標題、背景和圖例](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-customize-title-background-and-legend)