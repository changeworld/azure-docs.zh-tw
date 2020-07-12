---
title: 將參考圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 [Microsoft Azure 地圖] 視覺效果中使用參考層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/11/2020
ms.locfileid: "86261682"
---
# <a name="add-a-reference-layer"></a>新增參考圖層

[參考圖層] 功能可讓次要空間資料集上傳至視覺效果，並在地圖上重迭以提供加入內容。 此資料集是由 Power BI 所主控，而且必須是副檔名為或的[GeoJSON](https://wikipedia.org/wiki/GeoJSON)檔案 `.json` `.geojson` 。

若要將**GeoJSON**檔案加入為參考圖層，請移至 [**格式**] 窗格，展開 [**參考圖層**] 區段，然後按 [ **+ 新增本機**檔案] 按鈕。

將 GeoJSON 檔案新增至參考圖層之後，檔案的名稱會出現在 [ **+ 新增本機**檔案] 按鈕的旁邊，其中包含**X** 。 按下 [ **X** ] 按鈕，從視覺效果中移除資料，並從 Power BI 刪除 GeoJSON 檔案。

下列地圖會顯示[科羅拉多的2016人口普查 tracts](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)，並以人口為色彩。

> [!div class="mx-imgBorder"]
> ![顯示科羅拉多的2016人口普查 tracts 的地圖，以人口作為參考圖層](media/power-bi-visual/reference-layer-CO-census-tract.png)

下列是 [**格式**] 窗格中的所有設定，可在 [**參考圖層**] 區段中取得。

| 設定              | 描述   |
|----------------------|---------------|
| 參考圖層資料 | 要上傳至視覺效果的資料 GeoJSON 檔，做為地圖內的其他圖層。 [ **+ 新增本機**檔案] 按鈕會開啟 [檔案] 對話方塊，使用者可以用來選取具有或副檔名的 GeoJSON 檔案 `.json` `.geojson` 。 |

> [!NOTE]
> 在此 Azure 地圖服務視覺效果的預覽中，參考圖層只會將前5000個圖形功能載入至地圖。 未來的更新將會增加此限制。

## <a name="styling-data-in-a-reference-layer"></a>在參考圖層中設定資料的樣式

屬性可以新增至 GeoJSON 檔案中的每個功能，以自訂其在地圖上的樣式。 這項功能會使用 Azure 地圖服務 Web SDK 中的簡單資料層功能。 如需詳細資訊，請參閱此檔中[支援的樣式屬性](spatial-io-add-simple-data-layer.md#default-supported-style-properties)。 Azure 地圖服務視覺效果中不支援自訂圖示影像做為安全性預防措施。

以下是 GeoJSON 點功能的範例，其會將其顯示的色彩設為紅色。

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": {
        "color": "red"
    }
}
```
## <a name="next-steps"></a>後續步驟

將更多內容新增至地圖：

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
