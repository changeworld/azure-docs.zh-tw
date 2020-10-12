---
title: 瞭解 Azure 地圖服務 Power BI 視覺效果中的圖層 |Microsoft Azure 對應
description: 在本文中，您將瞭解適用于 Power BI Microsoft Azure Maps 視覺效果中的不同層級。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: a5c7296a0e7b7f6ea33c1f4a669675efd90f9e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261652"
---
# <a name="understanding-layers-in-the-azure-maps-power-bi-visual"></a>瞭解 Azure 地圖服務 Power BI 視覺效果中的圖層

Azure 地圖服務視覺效果中有兩種可用的層級。 第一個型別著重于轉譯資料，而這些資料會傳入視覺效果的 [ **欄位** ] 窗格，並且包含下列各層，讓我們來呼叫這些資料轉譯圖層。

:::row:::
    :::column span="":::
        **泡泡圖層**

        將點轉譯為地圖上的縮放圓形。

        ![地圖上的反升圖圖層](media/power-bi-visual/bubble-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **橫條圖圖層**

        將點轉譯為地圖上的3D 橫條。
        
        ![地圖上的橫條圖圖層](media/power-bi-visual/bar-chart-layer-thumb.png)
    :::column-end:::
:::row-end:::

第二種類型的圖層會將額外的外部資料源連接到地圖，以提供更多內容並包含下列各層。

:::row:::
    :::column span="":::
        **參考圖層**

        在地圖頂端覆迭上傳的 GeoJSON 檔案。

        ![地圖上的參考圖層](media/power-bi-visual/reference-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **圖格圖層**

        將自訂磚圖層重迭在地圖上。
        
        ![地圖上的圖格圖層](media/power-bi-visual/tile-layer-thumb.png)
    :::column-end:::
    :::column span="":::
        **流量層**

        在地圖上覆蓋即時交通資訊。
        
        ![地圖上的交通層](media/power-bi-visual/traffic-layer-thumb.png)
    :::column-end:::
:::row-end:::

所有資料轉譯層以及 **磚圖層**都有最小和最大縮放層級的選項，可用來指定應顯示這些圖層的縮放層級範圍。 這可讓您在一個縮放層級上使用一種轉譯層，並在另一個縮放層級轉換成另一個轉譯層。

這些圖層也有一個相對於地圖中其他圖層的放置選項。 使用多個資料轉譯圖層時，它們新增至地圖的順序會決定它們具有相同 **圖層位置** 值時的相對分層順序。

## <a name="general-layer-settings"></a>一般圖層設定

[ **格式** ] 窗格的 [一般層] 區段是一般設定，適用于連接到 [ **欄位** ] 窗格中 Power BI 資料集的圖層 ([反升圖圖層]、[橫條圖]) 。

| 設定     | 說明   |
|-------------|---------------|
| 未選取的透明度 | 選取一或多個圖形時，未選取之圖形的透明度。  |
| 顯示零              | 指定大小值為零的點是否應使用最小半徑顯示在地圖上。 |
| 顯示否定          | 指定是否應繪製負值大小值的絕對值。   |
| 最小資料值          | 要據以調整之輸入資料的最小值。 適用于裁剪極端值。  |
| 最大資料值          | 要調整之輸入資料的最大值。 適用于裁剪極端值。  |

## <a name="next-steps"></a>接下來的步驟

變更在地圖上顯示資料的方式：

> [!div class="nextstepaction"]
> [新增泡泡圖層](power-bi-visual-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [新增長條圖圖層](power-bi-visual-add-bar-chart-layer.md)

在地圖中新增更多內容：

> [!div class="nextstepaction"]
> [新增參考圖層](power-bi-visual-add-reference-layer.md)

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
