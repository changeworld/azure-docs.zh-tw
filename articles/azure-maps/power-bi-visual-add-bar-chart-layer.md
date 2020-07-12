---
title: 將橫條圖圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 [Microsoft Azure 地圖] 視覺效果中使用橫條圖圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 34d2d96e28f90249ad25788f6994dac63f83b1f6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261683"
---
# <a name="add-a-bar-chart-layer"></a>新增橫條圖圖層

**橫條圖圖層**可讓您將位置資料的視覺效果作為地圖上的3d 橫條圖或圓柱，以將資料帶到下一個維度。 類似于反升圖層，在稍後，橫條圖可以輕鬆地使用色彩和相對高度，同時將兩個度量視覺化。 為了讓橫條具有高度，必須將量值新增至 [**欄位**] 窗格的 [**大小**] bucket。 如果未提供量值，則視 [**橫條圖] 圖形**選項而定，沒有高度做為一般正方形或圓形的橫條。

> [!div class="mx-imgBorder"]
> ![使用橫條圖圖層顯示點資料的地圖](media/power-bi-visual/bar-chart-layer-styled.png)

使用者可以傾斜和旋轉地圖，以從不同的角度來查看您的資料。 您可以使用下列其中一種方法，將地圖傾斜或音調。

-   在 [**格式**] 窗格的 [**對應設定**] 中，開啟 [**導覽控制項**] 選項。 這會新增一個按鈕來傾斜地圖。
-   按下滑鼠右鍵，然後向上或向下拖曳滑鼠。
-   使用觸控式螢幕，以兩支手指觸控地圖，然後將其一起向上或向下拖曳。
-   以地圖為焦點時，按住**Shift**鍵，然後按**向上**或**向下**鍵。

您可以使用下列其中一種方法來旋轉對應。

-   在 [**格式**] 窗格的 [**對應設定**] 中，開啟 [**導覽控制項**] 選項。 這會新增一個按鈕來旋轉地圖。
-   按下滑鼠右鍵，並向左或向右拖曳滑鼠。
-   使用觸控式螢幕，以兩支手指觸控地圖並旋轉。
-   以地圖為焦點時，按住**Shift**鍵，然後按**向左鍵**或**向右鍵**。

以下是 [**橫條圖圖層**] 區段中可用的 [**格式**] 窗格中的所有設定。

| 設定              | 描述      |
|----------------------|------------------|
| 橫條圖圖形            | 3D 橫條的形狀。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box –以矩形方塊呈現的橫條。<br/>&nbsp;&nbsp;&nbsp;&nbsp;•圓柱–橫條圖呈現為圓柱。 |
| 高度               | 每個橫條的高度。 如果將欄位傳遞至 [**欄位**] 窗格的 [**大小**] 值區，則會相對於此高度值來調整橫條圖。 |
| 縮放高度 | 指定橫條的高度是否應該相對於縮放層級縮放。 |
| 寬度                | 每個橫條的寬度。  |
| 尺規上的縮放寬度  | 指定橫條的寬度是否應該相對於縮放層級縮放。  |
| 填滿色彩           | 每個橫條的色彩。 當欄位傳遞至 [**欄位**] 窗格的 [**圖例**] 值區，而且 [**格式**] 窗格中出現個別的 [**資料色彩**] 區段時，這個選項就會隱藏。 |
| 透明度         | 每個橫條的透明度。 |
| 最小縮放             | 最小縮放層級磚可供使用。 |
| 縮放上限             | 最大縮放層級磚可供使用。 |
| 圖層位置       | 指定圖層相對於其他地圖圖層的位置。 |

> [!NOTE]
> 如果橫條有小的寬度值，且 [縮放**比例**] 選項已停用，當縮小大量時，可能會消失，因為其呈現寬度會小於圖元大小。 不過，當 [縮放**比例寬度**] 選項為 [已啟用] 時，會在縮放層級變更時執行其他計算，這可能會影響大型資料集的效能。

## <a name="next-steps"></a>後續步驟

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