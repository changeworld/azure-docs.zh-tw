---
title: 空間分析區域和行位置
titleSuffix: Azure Cognitive Services
description: 瞭解如何使用空間分析來設定區域和線條
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: aahi
ms.openlocfilehash: 7e2a64c14d7d7a1d20b64b746969aca1e60ab218
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "90934439"
---
# <a name="zone-and-line-placement-guide"></a>區域和行放置指南

本文提供指導方針，說明如何定義空間分析作業的區域和線條，以精確分析空間中的人員移動。 這適用于所有作業。 

區域和線條是使用 JSON SPACEANALYSIS_CONFIG 參數來定義。 如需詳細資訊，請參閱 [空間分析作業](spatial-analysis-operations.md) 文章。

## <a name="guidelines-for-drawing-zones"></a>繪製區域的指導方針

請記住，每個空間都不同;您必須根據自己的需求來更新位置或大小。

如果您想要查看相機視圖的特定區段，請建立您最大的區域，以涵蓋您感興趣的特定地面區域，但不包含您不感興趣的其他區域。 這會增加所收集資料的精確度，並防止您不想要追蹤之區域的誤報。放入多邊形的邊角時請務必小心，並確定它們不在您想要追蹤的區域之外。  

### <a name="example-of-a-well-shaped-zone"></a>妥善塑造的區域範例

區域應該夠大，足以容納每個邊緣的三人，並專注于感興趣的區域。 空間分析會識別其英尺放置於區域中的人員，因此在2D 影像上繪製區域時，請將該區域想像為地面上的地毯。

![正確的區域](./media/spatial-analysis/zone-good-example.png)

### <a name="examples-of-zones-that-arent-well-shaped"></a>未妥善塑造的區域範例

下列範例顯示不正確的區域。 在這些範例中，感興趣的區域是 *它的遊戲時間* 顯示前的空間。

**區域不在地面上。**

![成形不良的區域](./media/spatial-analysis/zone-not-on-floor.png) 

**區域太小。**

![區域太小](./media/spatial-analysis/zone-too-small.png)

**區域無法完全捕獲顯示周圍的區域。**

![區域無法完整地捕捉端點端點的周圍區域](./media/spatial-analysis/zone-bad-capture.png)

**區域太接近相機影像的邊緣，也不會捕捉右邊的顯示。**

![區域太接近相機影像的邊緣，且不會捕捉右邊顯示](./media/spatial-analysis/zone-edge.png)

**區域已部分封鎖，所以無法完全看見人員和樓層。**

![區域已部分封鎖，所以無法完全看見人員](./media/spatial-analysis/zone-partially-blocked.png)

### <a name="example-of-a-well-shaped-line"></a>成形線的範例

該行的長度應該足以容納整個進入。 空間分析將會找出距離線線的使用者，因此當2D 影像上繪製線條時，會假設您要將它們繪製成不同的平面。 

可能的話，請將該行的寬度延長到實際的進入範圍之外。 如果這不會導致額外的交點 (如下圖所示，當線條是針對牆) 然後加以擴充。

![適當的線條](./media/spatial-analysis/zone-line-good-example.png)

### <a name="examples-of-lines-that-arent-well-shaped"></a>未妥善成形的線條範例

下列範例顯示定義不佳的行。

**行不涵蓋地面的整個進入方式。**

![行不涵蓋地面的整個進入方式](./media/spatial-analysis/zone-line-bad-coverage.png)

**行太高，且未涵蓋整個門。**

![行太高，且未涵蓋整個大門](./media/spatial-analysis/zone-line-too-high.png)

## <a name="next-steps"></a>後續步驟

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [設定空間分析作業](./spatial-analysis-operations.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [攝影機放置指南](spatial-analysis-camera-placement.md)
