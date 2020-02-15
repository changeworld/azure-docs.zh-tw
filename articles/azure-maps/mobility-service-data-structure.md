---
title: Azure 地圖服務中的行動服務資料結構 |Microsoft Azure 對應
description: 在本文中，您將瞭解透過 Microsoft Azure Maps 行動服務所傳回的通用欄位和資料結構。
author: farah-alyasari
ms.author: v-faalya
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 685810a6efa46c8eb3ad6cee0c2424299f0347d8
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209608"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure 地圖服務行動服務中的資料結構

本文介紹[Azure 地圖服務行動服務](https://aka.ms/AzureMapsMobilityService)中 Metro 區域的概念。 我們會討論當查詢此服務的公用傳輸停止和行時，所傳回的一些常見欄位。 我們建議您先閱讀這篇文章，再使用行動服務 Api 進行開發。

## <a name="metro-area"></a>Metro 區域

行動服務資料會依支援的 metro 區域分組。 Metro 區域不會遵循城市界限。 Metro 區域可以包含多個城市、密集填入的城市和周圍城市。 事實上，一個國家/地區可以是一個 metro 面積。 

`metroID` 是 metro 區域的識別碼，可以用來呼叫「[取得 Metro 區域資訊」 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)。 使用 Azure 地圖服務的「取得 Metro」 API 來要求運輸類型、運輸機關、作用中警示，以及所選 Metro 的其他詳細資料。 您也可以要求支援的 metro 區域和 metroIDs。 Metro 區域識別碼可能會變更。

**metroID：** 522**名稱：** 西雅圖-Tacoma-Bellevue

![西雅圖-metro-區域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止識別碼

傳輸停止可以透過兩種類型的識別碼來參考：[一般傳輸摘要規格（GFTS）](https://gtfs.org/)識別碼和 AZURE 地圖服務停止識別碼。 GFTS 識別碼稱為 stopKey，而 Azure 地圖服務停止識別碼稱為 stopID。 當經常參照傳輸停止時，建議您使用 Azure 地圖服務停止識別碼。 stopID 更穩定，而且只要實體停止存在，就有可能保持相同。 GTFS 停止識別碼會更頻繁地更新。 例如，您可以根據 GTFS 提供者要求，或在新的 GTFS 版本發行時，更新 GTFS 停止識別碼。 雖然實體停止不會變更，但 GTFS 停止識別碼可能會變更。

若要開始，您可以使用[取得附近的傳輸 API](https://aka.ms/AzureMapsMobilityNearbyTransit)，要求附近的傳輸停止。

## <a name="line-groups-and-lines"></a>行群組和線條

行動服務會針對線條和線條群組使用平行處理資料模型。 此模型可用來更有效地處理繼承自[GTFS](https://gtfs.org/)路由和行程資料的變更。


### <a name="line-groups"></a>行群組

「線條群組」是一個實體，它會將邏輯上屬於相同群組的所有線條群組在一起。 通常，「行」群組包含兩行，一條從點 A 到 B，另一條從點 B 返回。這兩行都屬於相同的公用傳輸代理程式，且具有相同的行號。 不過，在某些情況下，線條群組可能會有兩行以上，或其中只有一行。


### <a name="lines"></a>線條

如上所述，每個線條群組都是由一組行所組成。 每個線條群組都是由兩行組成，而每一行描述一個方向。  不過，在某些情況下，會有更多行組成行群組。 例如，有時會繞道到特定的鄰近地區，有時則不會。 在這兩種情況下，它會在相同的行號底下運作。 行群組也可以由一行組成。 具有單一方向的圓形線條是具有一行的 ling 群組。

若要開始，您可以使用「[取得傳輸線 API](https://aka.ms/AzureMapsMobilityTransitLine)」來要求「行群組」。


## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

瞭解如何使用行動服務來要求即時資料：

> [!div class="nextstepaction"]
> [如何要求即時資料](how-to-request-real-time-data.md)

探索 Azure 地圖服務行動服務 API 檔

> [!div class="nextstepaction"]
> [行動服務 API 檔](https://aka.ms/AzureMapsMobilityService)
