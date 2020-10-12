---
title: 將橫條圖圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 Microsoft Azure Maps 視覺效果中使用橫條圖圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 6a686f6915e332893f619e66944591999063a07c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288163"
---
# <a name="add-a-bar-chart-layer"></a>新增長條圖圖層

**橫條圖圖層**可讓您將位置資料視覺化為地圖上的3d 橫條圖或圓柱圖，以將資料用於下一個維度。 類似于反升圖層，橫條圖稍後可以使用色彩和相對高度，以相同的時間輕鬆地將兩個計量視覺化。 為了讓橫條具有高度，量值必須新增至 [**欄位**] 窗格的 [**大小**] 值區。 如果未提供量值，則沒有高度的橫條會顯示為平面方塊或圓形（視 **橫條圖形狀** 選項而定）。

> [!div class="mx-imgBorder"]
> ![使用橫條圖圖層來顯示點資料的地圖](media/power-bi-visual/bar-chart-layer-styled.png)

使用者可以傾斜和旋轉地圖，以從不同的觀點來查看您的資料。 您可以使用下列其中一種方法，將地圖傾斜或音調。

-   在 [**格式**] 窗格的 [**地圖] 設定**中，開啟 [**導覽控制項**] 選項。 這會新增按鈕來傾斜地圖。
-   按滑鼠右鍵，然後將滑鼠向上或向下拖曳。
-   使用觸控式螢幕，以兩支手指觸控地圖，然後將其一起向上或向下拖曳。
-   以地圖為焦點時，按住 **Shift** 鍵，然後按 **向上** 鍵或 **向下** 鍵。

您可以使用下列其中一種方法來旋轉地圖。

-   在 [**格式**] 窗格的 [**地圖] 設定**中，開啟 [**導覽控制項**] 選項。 這會加入按鈕來旋轉地圖。
-   按下滑鼠右鍵，然後將滑鼠向左或向右拖曳。
-   使用觸控式螢幕，以兩支手指觸控地圖並旋轉。
-   以地圖為焦點時，按住 **Shift** 鍵，然後按 **向左鍵** 或 **向右箭** 號鍵。

以下是 [**橫條圖圖層**] 區段中可用的 [**格式**] 窗格中的所有設定。

| 設定              | 說明      |
|----------------------|------------------|
| 橫條圖形            | 3D 橫條圖的形狀。<br/><br/>&nbsp;&nbsp;&nbsp;&nbsp;• Box –以矩形方塊呈現的橫條。<br/>&nbsp;&nbsp;&nbsp;&nbsp;•圓柱圖會轉譯為圓柱。 |
| 高度               | 每個橫條的高度。 如果欄位已傳遞至 [**欄位**] 窗格的 [**大小**] 值區中，則會相對於此高度值來調整橫條圖。 |
| 縮放時縮放高度 | 指定橫條的高度是否應該相對於縮放層級進行縮放。 |
| 寬度                | 每個橫條的寬度。  |
| 縮放時調整寬度  | 指定橫條圖的寬度是否應該相對於縮放層級進行縮放。  |
| 填滿色彩           | 每個橫條的色彩。 當欄位已傳遞至 [**欄位**] 窗格的 [**圖例**] 值區，而且 [**格式**] 窗格中顯示不同的 [**資料色彩**] 區段時，就會隱藏這個選項。 |
| 透明度         | 每個橫條的透明度。 |
| 最小縮放             | 可使用最小縮放層級磚。 |
| 最大縮放             | 最大縮放層級磚可供使用。 |
| 圖層位置       | 指定圖層相對於其他地圖圖層的位置。 |

> [!NOTE]
> 如果橫條的寬度值較小，而且 **縮放比例選項的尺規寬度** 已停用，則在放大大量時可能會消失，因為轉譯的寬度會小於大小的圖元。 但是，當縮放 **比例** 選項啟用時，會在縮放層級變更時執行額外的計算，這可能會影響大型資料集的效能。

## <a name="next-steps"></a>接下來的步驟

在地圖中新增更多內容：

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
