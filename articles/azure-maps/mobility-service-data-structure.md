---
title: Azure 地圖中的移動服務資料結構*微軟 Azure 地圖
description: 在本文中，您將瞭解通過 Microsoft Azure 地圖移動服務返回的常見欄位和資料結構。
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334451"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure 地圖移動服務中的資料結構

本文介紹了[Azure 地圖移動服務](https://aka.ms/AzureMapsMobilityService)中的地鐵區域概念。 我們將討論在查詢此服務以查詢公共交通網站和線路時返回的一些常見欄位。 我們建議您在使用移動服務 API 開發之前閱讀本文。

## <a name="metro-area"></a>地鐵區

移動服務資料按支援的大都市區分組。 地鐵區域不遵循城市邊界。 大都市區可以包含多個城市、人口稠密的城市和周邊城市。 事實上，國家/地區可以是一個大都市區。 

是`metroID`一個大都市區的ID，可以用來調用[獲取地鐵區域資訊API。](https://aka.ms/AzureMapsMobilityMetroAreaInfo) 使用 Azure 地圖的"獲取地鐵"API 請求所選地鐵的公交類型、中轉機構、活動警報和其他詳細資訊。 您也可以請求支援的大都市區和地鐵。 地鐵區區號的年號可能會有所更改。

**地鐵ID：** 522**名稱：** 西雅圖-塔科馬-貝爾維尤

![西雅圖-地鐵區](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止指示

中轉站可以由兩種類型的 ID（[一般傳輸饋送規範 （GFTS）](https://gtfs.org/) ID 和 Azure 映射停止 ID）來引用。 GFTS ID 稱為停止金鑰，Azure 映射停止 ID 稱為 stopID。 當經常提到中轉站時，鼓勵您使用 Azure 映射停止 ID。 只要存在物理停止，停止 ID 就越穩定，並且可能保持不變。 GTFS 停止 ID 更新頻率更大。 例如，GTFS 停止 ID 可以根據 GTFS 提供程式請求或發佈新的 GTFS 版本時更新。 雖然物理停止沒有變化，但 GTFS 停止 ID 可能會更改。

要開始，您可以使用[獲取附近的中轉 API](https://aka.ms/AzureMapsMobilityNearbyTransit)請求附近的中轉站。

## <a name="line-groups-and-lines"></a>線組和行

移動服務對行和行組使用並行資料模型。 此模型用於更好地處理從[GTFS](https://gtfs.org/)路由和行程資料繼承的更改。


### <a name="line-groups"></a>行組

行組是一個實體，它將所有邏輯上屬於同一組一部分的行組合在一起。 通常，線組包含兩條線，一條從 A 點到 B 點，另一條從點 B 返回 A。兩條線路將屬於同一公共交通機構，並且具有相同的線路號。 但是，在某些情況下，行組中可能有兩行以上或其中只有一行。


### <a name="lines"></a>線條

如上所述，每個行組由一組行組成。 每行組由兩行組成，每行描述一個方向。  但是，在某些情況下，更多的行組成一個行組。 例如，有一條線有時繞道穿過某個社區，有時沒有。 在這兩種情況下，它在同一行號下運行。 此外，線組可以由單行組成。 具有單個方向的圓線是具有一條線的 ling 組。

首先，您可以使用["獲取傳輸線 API"](https://aka.ms/AzureMapsMobilityTransitLine)請求行組。


## <a name="next-steps"></a>後續步驟

瞭解如何使用移動服務請求傳輸資料：

> [!div class="nextstepaction"]
> [如何請求傳輸資料](how-to-request-transit-data.md)

瞭解如何使用移動服務請求即時資料：

> [!div class="nextstepaction"]
> [如何請求即時資料](how-to-request-real-time-data.md)

流覽 Azure 地圖移動服務 API 文檔

> [!div class="nextstepaction"]
> [移動服務 API 文檔](https://aka.ms/AzureMapsMobilityService)
