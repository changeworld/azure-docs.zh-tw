---
title: 將參考圖層新增至 Azure 地圖服務 Power BI 視覺效果 |Microsoft Azure 對應
description: 在本文中，您將瞭解如何在 Power BI 的 Microsoft Azure Maps 視覺效果中使用參考圖層。
author: rbrundritt
ms.author: richbrun
ms.date: 06/26/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: eb6c9fed42f263ca6cfaa1ea975d31cb8f4a75cf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "86261682"
---
# <a name="add-a-reference-layer"></a>新增參考圖層

參考圖層功能可將次要空間資料集上傳至視覺效果，並在地圖上重迭以提供加法內容。 此資料集是由 Power BI 所裝載，而且[GeoJSON file](https://wikipedia.org/wiki/GeoJSON)必須是副檔名為 `.json` 或副檔名的 GeoJSON 檔 `.geojson` 。

若要將 **GeoJSON** 檔案加入為參考圖層，請移至 [ **格式** ] 窗格，展開 [ **參考圖層** ] 區段，然後按 [ **+ 新增本機** 檔案] 按鈕。

將 GeoJSON 檔案加入至參考圖層之後，檔案的名稱將會顯示為 [ **+ 新增本機** 檔案] 按鈕，其旁邊有 **X** 。 按 [ **X** ] 按鈕，從視覺效果中移除資料，並從 Power BI 中刪除 GeoJSON 檔案。

下列地圖顯示 [針對科羅拉多的2016人口普查區域，以](https://github.com/Azure-Samples/AzureMapsCodeSamples/tree/master/AzureMapsCodeSamples/Common/data/geojson)人口色彩顯示。

> [!div class="mx-imgBorder"]
> ![顯示2016人口普查區域的地圖，以人口作為參考圖層著色](media/power-bi-visual/reference-layer-CO-census-tract.png)

以下是 [**參考圖層**] 區段中可用的 [**格式**] 窗格中的所有設定。

| 設定              | 說明   |
|----------------------|---------------|
| 參考圖層資料 | 要上傳至視覺效果的資料 GeoJSON 檔，做為地圖內的其他圖層。 [ **+ 新增本機** 檔案] 按鈕會開啟 [檔案] 對話方塊，供使用者選取具有或副檔名的 GeoJSON `.json` 檔 `.geojson` 。 |

> [!NOTE]
> 在此 Azure 地圖服務視覺效果的預覽中，參考圖層只會將前5000圖形功能載入地圖。 未來的更新將會增加此限制。

## <a name="styling-data-in-a-reference-layer"></a>設定參考圖層中資料的樣式

您可以將屬性新增至 GeoJSON 檔案內的每個功能，以自訂其在地圖上的樣式。 這項功能會使用 Azure 地圖服務 Web SDK 中的簡單資料層功能。 如需詳細資訊，請參閱此檔中有關 [支援的樣式屬性](spatial-io-add-simple-data-layer.md#default-supported-style-properties)。 Azure 地圖服務的視覺效果中不支援自訂圖示影像作為安全性預防措施。

以下是 GeoJSON 點功能的範例，可將其顯示的色彩設定為紅色。

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
## <a name="next-steps"></a>接下來的步驟

在地圖中新增更多內容：

> [!div class="nextstepaction"]
> [新增圖格圖層](power-bi-visual-add-tile-layer.md)

> [!div class="nextstepaction"]
> [顯示即時流量](power-bi-visual-show-real-time-traffic.md)
