---
title: Azure Kinect DK 座標系統
description: 與 Azure 深色感應器相關聯的 Azure Kinect DK 座標系統描述
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，azure，感應器，sdk，深度攝影機，tof，原則，效能，失效
ms.openlocfilehash: 4bb1c3b79862b918870cff786042d9b4c66270d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276388"
---
# <a name="azure-kinect-dk-coordinate-systems"></a>Azure Kinect DK 座標系統

在本文中，我們將描述用於2D 和3D 座標系統的慣例。  有不同的座標系統與每個感應器的裝置相關聯，而[校準功能](use-calibration-functions.md)則允許在其間轉換點。 [轉換](use-image-transformation.md)函式會在座標系統之間轉換整個影像。  

## <a name="2d-coordinate-systems"></a>2D 座標系統

 深度和彩色相機都與獨立2D 座標系統相關聯。 [X，y] 座標是以圖元為單位來表示，其中*x*的範圍是從0到寬度-1，而*y*範圍是從0到 height-1。 寬度和高度取決於所選擇的模式，其中的深度和色彩攝影機會在其中運作。 圖元座標會 `[0,0]` 對應至影像的左上角圖元。 圖元座標可以是代表子圖元座標的分數。

2D 座標系統是以0為中心，也就是說，子圖元座標 `[0.0, 0.0]` 代表圖元的置中和 `[0.5,0.5]` 右下角，如下所示。

   ![2D 座標系統](./media/concepts/concepts-coordinate-systems/coordinate-systems-sdk-2d-system.png)

## <a name="3d-coordinate-systems"></a>3D 座標系統

每個攝影機、加速計和陀螺儀都與獨立的3D 座標空間系統相關聯。

3D 座標系統中的點會表示為公制 [X，Y，Z] 座標的 triplet，並以毫米為單位。

### <a name="depth-and-color-camera"></a>深度和彩色攝影機

原點 `[0,0,0]` 位於相機的焦點。 座標系統的導向方向，如下：正 X 軸指向右方、正 Y 軸向下點，以及正 Z 軸點向前。

深度相機的向下會傾斜6度，如下所示。 

深度攝影機使用兩個 illuminators。 用於縮小欄位（NFOV）模式的 illuminator 會與深度相機大小寫一致，因此 illuminator 不會傾斜。 在寬型欄位（WFOV）模式中使用的 illuminator 會相對於深度攝影機向下傾斜額外的1.3 度。

![3D 座標慣例](./media/concepts/concepts-coordinate-systems/coordinate-systems-camera-features.png)

### <a name="gyroscope-and-accelerometer"></a>陀螺儀和加速計

陀螺儀的原點與 `[0,0,0]` 深度攝影機的原點相同。 加速計的來源與實體位置一致。 加速計和陀螺儀座標系統都是右手式的。 座標系統的正 X 軸會向後反向、正 Y 軸指向左方，而正 Z 軸會向下指向，如下所示。

![IMU 座標系統](./media/concepts/concepts-coordinate-systems/coordinate-systems-gyroscope.png)

## <a name="next-steps"></a>後續步驟

[瞭解 Azure Kinect 感應器 SDK](about-sensor-sdk.md)