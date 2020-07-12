---
title: 瞭解 Azure 地圖服務 Power BI 視覺效果中的圖層 |Microsoft Azure 對應
description: 在本文中，您將瞭解適用于 Power BI 的 [Microsoft Azure 地圖] 視覺效果中的不同層級。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261652"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>瞭解 Azure 地圖服務 Power BI 視覺效果中的圖層

Azure 地圖服務視覺效果中有兩種可用的層級。 第一種類型著重于轉譯傳遞至視覺效果 [**欄位**] 窗格的資料，並包含下列層級，讓我們呼叫這些資料呈現層。

:::row:::
    :::column span="":::
        **反升圖層**

        將點呈現為地圖上的縮放圓形。

        ![地圖上的氣泡圖層](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **橫條圖圖層**

        將點轉譯為地圖上的3D 橫條圖。
        
        ![地圖上的橫條圖圖層](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

第二種層級會連接額外的外部資料源來進行對應，以提供更多內容，並由下列各層組成。

:::row:::
    :::column span="":::
        **參考圖層**

        將上傳的 GeoJSON 檔案重迭到地圖上。

        ![地圖上的參考圖層](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **圖格圖層**

        將地圖上的自訂磚圖層重迭。
        
        ![地圖上的圖格圖層](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **流量層**

        對應地圖上的即時流量資訊。
        
        ![地圖上的流量層](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

所有資料轉譯層和**磚圖層**都有最小和最大縮放層級的選項，可用來指定縮放層級範圍，這些圖層應顯示在。 這可讓您在一個縮放層級上使用一種轉譯層，並在另一個縮放層級轉換至另一個呈現層。

這些圖層也可以選擇相對於地圖中的其他圖層來定位。 使用多個資料轉譯層時，它們新增至對應的順序會決定它們的相對分層順序（當它們具有相同的**圖層位置**值時）。

## <a name="general-layer-settings"></a>一般圖層設定

[**格式**] 窗格的 [一般圖層] 區段是常用的設定，適用于連接到 [**欄位**] 窗格中的 Power BI 資料集的圖層， ([反升圖層]、[橫條圖]) 。

| 設定     | 描述   |
|-------------|---------------|
| 未選取的透明度 | 選取一個或多個圖形時，未選取之圖形的透明度。  |
| 顯示零              | 指定大小值為零的點是否應使用最小半徑顯示在地圖上。 |
| 顯示否定          | 指定是否應該繪製負值大小值的絕對值。   |
| 最小資料值          | 要針對其進行調整的輸入資料的最小值。 適用于裁剪極端值。  |
| 最大資料值          | 要針對其進行調整的輸入資料的最大值。 適用于裁剪極端值。  |

## <a name="next-steps"></a>後續步驟

變更您的資料在地圖上的顯示方式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增橫條圖圖層](power-bi-visual-add-bar-chart-layer.md)

將更多內容新增至地圖：

> [!div class="nextstepaction"]
> [新增參考圖層](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
