---
title: Azure Kinect DK 座標系統
description: 與 Azure 深色感應器相關聯的 Azure Kinect DK 座標系統描述
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、azure、感應器、sdk、深度相機、tof、原則、效能、失效
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276388"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK 座標系統

在本文中，我們將說明用於2D 和3D 座標系統的慣例。  有個別的座標系統與每個感應器的裝置相關聯，以及允許在其間轉換點的 [校正功能](use-calibration-functions.md) 。 [轉換](use-image-transformation.md)函式會轉換座標系統之間的整個影像。  

## <a name="2d-coordinate-systems"></a>2D 座標系統

 深度和彩色攝影機都與獨立2D 座標系統相關聯。 [X，y] 座標以圖元為單位，其中 *x* 範圍從0到寬度-1， *y* 範圍從0到高度-1。 寬度和高度取決於所選模式，也就是用來操作深度和彩色攝影機的模式。 圖元座標 `[0,0]` 對應于影像的左上角圖元。 圖元座標可以是代表子圖元座標的分數。

2D 座標系統是以0為中心，也就是，子圖元座標 `[0.0, 0.0]` 代表圖元的中間和 `[0.5,0.5]` 右下角，如下所示。

   ![2D 座標系統](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 座標系統

每個相機、加速計和陀螺儀都會與獨立的3D 座標空間系統產生關聯。

3D 座標系統中的點會表示為度量 [X，Y，Z]-座標 triplet，單位為毫米。

### <a name="depth-and-color-camera"></a>深度和彩色相機

原點 `[0,0,0]` 位於相機的焦點。 座標系統是導向的方向，因此正 X 軸指向右邊、正 Y 軸指向下點，而正 Z 軸會向前指向。

深度相機會將色彩攝影機向下傾斜6度，如下所示。 

深度相機會使用兩個 illuminators。 用於窄的 (NFOV) 模式的 illuminator 會與深度攝影機大小寫對齊，因此 illuminator 不會傾斜。 寬視野 (WFOV) 模式中所使用的 illuminator，會在相對於深度相機的向下傾斜額外的1.3 度。

![3D 座標慣例](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>陀螺儀和加速計

陀螺儀的原點與 `[0,0,0]` 深度相機的原點相同。 加速計的來源與其實體位置一致。 加速計和陀螺儀座標系統都會右手右手。 座標系統的正 X 軸反向指向左方、正 Y 軸指向左方，且正 Z 軸會指向下點，如下所示。

![IMU 座標系統](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>後續步驟

[深入瞭解 Azure Kinect 感應器 SDK](about-sensor-sdk.md)