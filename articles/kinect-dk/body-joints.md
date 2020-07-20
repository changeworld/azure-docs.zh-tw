---
title: Azure Kinect 主體追蹤接點
description: 瞭解 Azure Kinect DK 中的主體框架、接點、接點座標和聯合階層。
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect，移植，主體，追蹤，聯合，階層，骨骼，連接
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 07/02/2020
ms.locfileid: "85276513"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 主體追蹤接點

Azure Kinect 內文追蹤可以同時追蹤多個人為主體。 每個主體都包含框架與運動基本架構之間的時態性相互關聯識別碼。 您可以使用來取得每個畫面格中偵測到的主體數目 `k4abt_frame_get_num_bodies()` 。

## <a name="joints"></a>聯接

聯合位置和方向是相對於參考之全域深度感應器框架的估計值。 位置是以毫米指定。 方向以正規化四元數表示。

## <a name="joint-coordinates"></a>聯合座標

每一個聯合形式的位置和方向都是它自己的聯合座標系統。 所有的聯合座標系統都是相對於深度相機3D 座標系統的絕對座標系統。

> [!NOTE]
> 接點座標是以軸為方向。 軸方向廣泛搭配商用虛擬人偶、遊戲引擎和轉譯軟體使用。 使用軸方向可簡化鏡像移動，例如以20度引發兩個臂。

![聯合座標](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>聯合階層

基本架構包含32接點，其中的聯合階層會從主體中心流向 extremities。 每個連接（骨骼）都會連結父項與子系的聯合。 此圖說明相對於人類主體的聯合位置和連接。

![聯合階層](./media/concepts/joint-hierarchy.png)

下表列舉標準的聯合連接。

|索引 |聯合名稱     | 父聯合   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |間           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | 間           |
| 27   |鼻           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>後續步驟

[全身追蹤索引對應](body-index-map.md)
