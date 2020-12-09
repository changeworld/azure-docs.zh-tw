---
title: 行動服務 (預覽) Microsoft Azure 對應中的資料結構
description: 瞭解如何將資料組織成 Azure 地圖服務行動服務 (預覽) 的 metro 區域。 查看哪些欄位儲存有關公用運輸停止和行的資訊。
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8ffedc18ba331733723a6293756b60b733cc32cf
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/09/2020
ms.locfileid: "96904715"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Azure 地圖服務行動服務中的資料結構 (預覽)  

> [!IMPORTANT]
> Azure 地圖服務行動服務目前處於公開預覽狀態。
> 此預覽版本是在沒有服務等級協定的情況下提供，不建議用於生產工作負載。 可能不支援特定功能，或可能已經限制功能。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。



本文介紹 [Azure 地圖服務行動服務](/rest/api/maps/mobility)中 Metro 區域的概念。 我們會討論這項服務在查詢公用傳輸停止和行時所傳回的一些常見欄位。 建議您先閱讀本文，再使用行動服務 Api 進行開發。

## <a name="metro-area"></a>Metro 區域

行動服務 (預覽) 資料會依支援的 metro 區域分組。 Metro 區域不會跟隨城市界限。 Metro 區域可以包含多個城市、密集填入的城市，以及周圍城市。 事實上，國家/地區可以是一個 metro 區域。 

`metroID`是 metro 區域的識別碼，可用來呼叫「[取得 Metro 區域資訊 API](/rest/api/maps/mobility/getmetroareainfopreview)」。 使用 Azure 地圖服務「取得 Metro」 API 來要求運輸類型、運輸機關、作用中的警示，以及所選 Metro 的其他詳細資料。 您也可以要求支援的 metro 區域和 metroIDs。 Metro 區域識別碼可能會變更。

**metroID：** 522   **Name：** 西雅圖-Tacoma-Bellevue

![西雅圖-metro-區域](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>停止識別碼

傳輸停止可透過兩種類型的識別碼來參考： [一般傳輸摘要規格 (GFTS) ](http://gtfs.org/) 識別碼和 AZURE 地圖服務停止識別碼。 GFTS 識別碼稱為「stopKey」，Azure 地圖服務的「停止」識別碼稱為「stopID」。 當經常參考傳輸停止時，建議您使用 Azure 地圖服務的停止識別碼。 只要實體停止存在，stopID 會更穩定且可能保持不變。 GTFS 停止識別碼會更頻繁地更新。 例如，您可以根據 GTFS 提供者要求或在新的 GTFS 版本發行時，更新 GTFS stop ID。 雖然實體停止沒有變更，但 GTFS 停止識別碼可能會變更。

若要開始，您可以使用「 [取得附近傳輸」 API](/rest/api/maps/mobility/getnearbytransitpreview)來要求附近的傳輸停止。

## <a name="line-groups-and-lines"></a>行群組和線條

行動服務 (預覽) 使用平行資料模型來處理線條和線條群組。 此模型可用來更妥善處理繼承自 [GTFS](http://gtfs.org/) 路由與行程資料的變更。


### <a name="line-groups"></a>行群組

「線條群組」是一個實體，可將所有以邏輯方式組成相同群組的行群組在一起。 通常，線條群組會包含兩行，一個從點 A 到 B，另一個從點 B 傳回至。這兩行都屬於相同的公用傳輸機構，且具有相同的行號。 不過，在某些情況下，行群組可能會有兩行以上的行，或只包含一行。


### <a name="lines"></a>線條

如上所述，每個線條群組都是由一組線組成。 每個線條群組都是由兩行所組成，每一行都會描述一個方向。  不過，在某些情況下，會有更多行來撰寫線條群組。 例如，有一條線有時候會繞道到特定的鄰近部分，有時候則不會。 在這兩種情況下，它會在相同的行號下運作。 此外，行群組也可以由一行組成。 具有單一方向的圓形線條是具有一行的 ling 群組。

若要開始，您可以使用「 [取得傳輸行 API](/rest/api/maps/mobility/gettransitlineinfopreview)」來要求行群組。


## <a name="next-steps"></a>後續步驟

瞭解如何使用行動服務 (預覽) 來要求傳輸資料：

> [!div class="nextstepaction"]
> [如何要求傳輸資料](how-to-request-transit-data.md)

瞭解如何使用行動服務 (預覽版) 來要求即時資料：

> [!div class="nextstepaction"]
> [如何要求即時資料](how-to-request-real-time-data.md)

探索 Azure 地圖服務行動服務 (預覽) API 檔

> [!div class="nextstepaction"]
> [行動服務 API 檔](/rest/api/maps/mobility)