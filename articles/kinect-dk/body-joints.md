---
title: Azure Kinect 主體追蹤接點
description: 瞭解 Azure Kinect DK 中的內文框架、接點、聯合座標和聯合階層。
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect、移植、主體、追蹤、聯合、階層、骨骼、連接
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "85276513"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 主體追蹤接點

Azure Kinect 內文追蹤可同時追蹤多個人為主體。 每個主體都包含框架和運動學基本架構之間的時態性相互關聯識別碼。 您可以使用來取得每個框架中偵測到的主體數目 `k4abt_frame_get_num_bodies()` 。

## <a name="joints"></a>關節

聯合位置和方向是相對於參考全球深度感應器框架的估計值。 以毫米指定位置。 方向以正規化四元數表示。

## <a name="joint-coordinates"></a>聯合座標

每個聯合的位置和方向會形成自己的聯合座標系統。 所有聯合座標系統都是相對於深度相機3D 座標系統的絕對座標系統。

> [!NOTE]
> 聯合座標是以軸為方向。 軸方向廣泛用於商業虛擬人偶、遊戲引擎和轉譯軟體。 使用軸方向可簡化鏡像的移動，例如，將這兩個臂提高20度。

![聯合座標](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>聯合階層

基本架構包含32接點，其中聯合階層從主體中央流向 extremities。 每個 (骨骼的連接) 都會連結與子系聯合的父項。 此圖說明與人為身體相關的聯合位置和連接。

![聯合階層](./media/concepts/joint-hierarchy.png)

下表列舉標準的聯合連接。

|索引 |聯合名稱     | 父聯合   |
|------|---------------|----------------|
| 0    |骨盆         | -              |
| 1    |SPINE_NAVAL    | 骨盆         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |脖子           | SPINE_CHEST    |
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
| 18   |HIP_LEFT       | 骨盆         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | 骨盆         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | 脖子           |
| 27   |鼻子           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>後續步驟

[全身追蹤索引對應](body-index-map.md)
