---
title: 空間分析攝影機放置
titleSuffix: Azure Cognitive Services
description: 瞭解如何設定相機以搭配空間分析使用
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: aahi
ms.openlocfilehash: 00bca2d0c7e267bef8cd4a917463c5e7db159792
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933919"
---
# <a name="camera-placement-guide"></a>攝影機放置指南

本文提供空間分析 (公開預覽) 的相機放置建議。 其中包含一般指導方針，以及對所有包含之作業的高度、角度和相機對點距離的特定建議。 

> [!NOTE]
> 本指南是針對 Axis M3045-V 攝影機所設計。 此攝影機將使用解析度1920x1080、106度水準欄位、59度垂直視野和固定的 2.8 mm 焦距長度。 下列準則適用于所有攝影機，但需要調整相機高度和相機對點距離的特定指導方針，才能與其他攝影機搭配使用。 

## <a name="general-guidelines"></a>一般指導方針

將攝影機定位於空間分析時，請考慮下列一般指導方針：

* **光源高度。** 將相機放在燈光裝置下，讓裝置不會封鎖相機。
* **障礙。** 若要避免阻礙相機的觀看，請注意障礙物，例如兩極、告示板、擱置、牆壁和現有的 LP 攝影機。
* **環境背光。** 室外背光會影響相機影像品質。 若要避免發生嚴重的背光狀況，請避免將攝影機導向至對外的視窗和玻璃門。
* **當地隱私權規則和規定。** 當地法規可能會限制攝影機的可捕獲。 請先確定您已瞭解本機規則和規定，再放置相機。
* **建立結構。** HVAC、撒水器和現有的接線可能會限制攝影機的硬掛接。
* **纜線管理。** 確定您可以從規劃的攝影機裝入位置將乙太網路纜線路由傳送至 Power Over 網際網路 (PoE) 交換器。

## <a name="height-focal-point-distance-and-angle"></a>高度、焦點距離和角度

當您決定如何安裝相機以進行空間分析時，必須考慮三件事：
- 相機高度
- 攝影機與焦點距離
- 相對於樓層平面的相機角度

也請務必瞭解大部分的人在可能的情況下， (person 的方向，) 相對於相機的觀點。 此方向對於系統效能而言很重要。

![某位人員在方向上的影像](./media/spatial-analysis/person-walking-direction.png)

下圖顯示 person 行走方向的提高許可權。

![提高許可權與計畫視圖](./media/spatial-analysis/person-walking-direction-diagram.png)

## <a name="camera-height"></a>相機高度

一般而言，攝影機應從地面掛上12-14 英尺。 當您在此範圍內規劃相機掛接時，請考慮障礙物 (例如：擱置、懸掛燈、下垂告示板，以及顯示可能影響相機視圖的) ，然後視需要調整高度。

## <a name="camera-to-focal-point-distance"></a>攝影機與焦點距離

_攝影機與焦距距離_ 是從焦點開始的線性距離， (或將感興趣的點中心焦點) 至相機。

![攝影機與焦點距離](./media/spatial-analysis/camera-focal-point.png)

這個距離會以 floor 平面來測量。

![如何從樓層測量攝影機到焦點距離](./media/spatial-analysis/camera-focal-point-floor-plane.png)

上述內容看起來像這樣：

![從上方算起的如何測量攝影機與焦點距離](./media/spatial-analysis/camera-focal-point-above.png)

您可以使用下表，根據特定的裝載高度，判斷相機與焦點之間的距離。 這些距離適用于最佳位置。 請注意，資料表會提供 12 '-14 ' 建議的指導方針，因為某些上限可能會限制高度。

| 相機高度 | 攝影機與點距離 (最小/最大)  |  
| ------------- | ---------------------------------------- |  
| 八角            | 10 '-13 '                                  |  
| 10           | 7 '-13 '                                   |  
| 全年           | 10 '-17 '                                  |  
| 日           | 11 '-18 '                                  |  
| 16           | 12 '-22 '                                  |  
| 名           | 15 '-30 '                                  |  

下圖從最接近且最遠的鏡頭到焦點，模擬相機視圖。

| 最近                                      | 遠                                      |  
| -------------------------------------------- | --------------------------------------------- |  
| ![最接近的攝影機與焦距距離](./media/spatial-analysis/focal-point-closest.png) | ![最遠的攝影機與焦距距離](./media/spatial-analysis/focal-point-farthest.png) |  

## <a name="camera-angle-mounting-ranges"></a>攝影機角度裝入範圍

本節說明可接受的相機角度裝載範圍。 這些裝載範圍會顯示最佳放置的可接受範圍。

### <a name="line-configuration"></a>行配置

下表顯示針對 **cognitiveservices spatialanalysis personcrossingline** 作業所設定之攝影機的建議。 

| 相機高度 | 攝影機與焦點距離 | 最佳相機裝入角度 (最小/最大)  |  
| ------------- | ------------------------------ | ------------------------------------------ |  
| 八角            | 形                             | +/-40 °                                     |  
| 10           | 10                            | +/-30 °                                     |  
| 全年           | .13                            | +/-20 °                                     |  
| 16           | 達                            | +/-10 °                                     |  
| 名           | 日                            | +/-10 °                                     |  

下圖會使用最左邊的 ( ) ，以及最右邊的 (+) 裝入角度建議來模擬相機視圖，以使用 **cognitiveservices。 spatialanalysis-personcrossingline** 以門的方式進行進入計數。

| 最左邊的觀點                | 最右邊的觀點                |  
| ---------------------------- | ----------------------------- |  
| ![最左邊的攝影機角度](./media/spatial-analysis/camera-angle-left.png) | ![最右邊的攝影機角度](./media/spatial-analysis/camera-angle-right.png) |  

下圖顯示來自鳥瞰圖的相機位置和裝入角度。

![鳥瞰圖](./media/spatial-analysis/camera-angle-top.png)

### <a name="zone-configuration"></a>區域設定

我們建議您將攝影機放在上方10英尺以上，以確保涵蓋範圍夠大。 

當區域位於像是牆或貨架的障礙旁時，會在可接受的120度角範圍內，從目標的指定距離掛接相機，如下圖所示。

![可接受的攝影機角度](./media/spatial-analysis/camera-angle-acceptable.png)

下圖提供在貨架旁邊之區域的左和右攝影機視圖模擬。

| 左方視圖        | 右視圖        |  
| ---------------- | ----------------- |  
| ![左方視圖](./media/spatial-analysis/end-cap-left.png) | ![右視圖](./media/spatial-analysis/end-cap-right.png) |  

#### <a name="queues"></a>佇列

**Cognitiveservices spatialanalysis-personcount**、 **cognitiveservices、spatialanalysis-persondistance**和**cognitiveservices. spatialanalysis-personcrossingpolygon 的**技能可能用來監視佇列。 為了達到最佳佇列資料品質，建議將 retractable 皮帶阻礙降至最低，以將佇列中的人員遮蔽降至最低，並確保佇列位置在一段時間內是一致的。

![Retractable 皮帶佇列](./media/spatial-analysis/retractable-belt-queue.png)

這種類型的屏障優先于佇列的不透明阻礙，以充分發揮系統中的深入解析精確度。

佇列有兩種類型：線性和鋸齒-紋。

下圖顯示線性佇列的建議：

![線性佇列建議](./media/spatial-analysis/camera-angle-linear-queue.png)

下圖提供線性佇列的左和右攝影機視圖模擬。 請注意，您可以在佇列的另一端掛接相機。

| 左方視圖                          | 右視圖                          |  
| ---------------------------------- | ----------------------------------- |  
| ![線性佇列的左邊角](./media/spatial-analysis/camera-angle-linear-left.png) | ![線性佇列的右角](./media/spatial-analysis/camera-angle-linear-right.png) |  

針對鋸齒紋佇列，最好避免將相機直接放在佇列線方向，如下圖所示。 請注意，圖中的四個範例攝影機位置都提供理想的視圖，每個方向都有 +/-15 度的可接受偏差。

下圖會模擬在鋸齒-紋佇列的理想位置中放置的相機的視圖。

| 視圖1        | 視圖2        |  
| ------------- | ------------- |  
| ![視圖1](./media/spatial-analysis/camera-angle-ideal-location-right.png) | ![視圖2](./media/spatial-analysis/camera-angle-ideal-location-left.png) |  

| 視圖3 |  視圖4 |  
| ---- | ----  |
| ![視圖3](./media/spatial-analysis/camera-angle-ideal-location-back.png) |  ![視圖4](./media/spatial-analysis/camera-angle-ideal-location-back-left.png) | 

##### <a name="organic-queues"></a>有機佇列

隨機佇列行表單茁壯。 如果佇列不是形成超過2-3 人，以及區域定義內的線條表單，就可以接受此佇列樣式。 如果佇列長度通常超過2-3 人，我們建議使用 retractable 皮帶關卡來協助引導佇列方向，並確保區域定義中的行形式。

## <a name="next-steps"></a>下一步

* [部署人員計數 web 應用程式](spatial-analysis-web-app.md)
* [設定空間分析作業](./spatial-analysis-operations.md)
* [記錄和疑難排解](spatial-analysis-logging.md)
* [區域和行放置指南](spatial-analysis-zone-line-placement.md)
