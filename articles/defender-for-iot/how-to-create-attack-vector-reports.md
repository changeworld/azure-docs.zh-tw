---
title: 建立攻擊向量報表
description: 攻擊向量報表提供可攻擊之裝置的弱點鏈的圖形標記法。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e22bf80cd9dbcd80a0de84fd5d044354b1fc4e2d
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811622"
---
# <a name="attack-vector-reporting"></a>攻擊向量報告

## <a name="about-attack-vector-reports"></a>關於攻擊向量報表

攻擊向量報表提供可攻擊之裝置的弱點鏈的圖形標記法。 這些弱點可讓攻擊者存取重要的網路裝置。 攻擊向量模擬器會即時計算攻擊媒介，並分析特定目標的所有攻擊媒介。

使用攻擊向量可讓您評估風險降低活動在攻擊順序中的影響。 例如，您可以判斷系統升級中斷攻擊鏈的路徑，或是否保留替代的攻擊路徑。 此資訊可協助您設定補救和緩和活動的優先順序。

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="在控制中心內查看您的警示。":::

> [!NOTE]
> 系統管理員和安全性分析師可以執行本節所述的程式。

## <a name="create-an-attack-vector-report"></a>建立攻擊向量報表

若要建立攻擊向量模擬：

1. 選取側邊功能表上的 :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="加號":::，以新增模擬。

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="攻擊向量模擬。":::

2. 輸入模擬屬性：

   - **名稱**：模擬名稱。

   - **最大向量**：單一模擬中向量的最大數目。

   - **在裝置對應中顯示：以** 裝置地圖上的篩選顯示攻擊向量。

   - **所有來源裝置**：攻擊向量都會將所有裝置視為攻擊來源。

   - **攻擊來源**：攻擊向量只會將指定的裝置視為攻擊來源。

   - **所有目標裝置**：攻擊向量都會將所有裝置視為攻擊目標。

   - **攻擊目標**：攻擊向量只會將指定的裝置視為攻擊目標。

   - **排除裝置**：系統會將指定的裝置排除在攻擊向量模擬之外。

   - **排除子網**：指定的子網將會排除在攻擊向量模擬之外。

3. 選取 [ **新增模擬**]。 模擬將會新增至模擬清單。

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="新增模擬。":::

4. :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false":::如果您想要編輯模擬，請選取此選擇。

   :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false":::如果您想要刪除模擬，請選取此選擇。

   :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false":::如果您想要將模擬標示為我的最愛，請選取此項。

5. 攻擊向量的清單隨即出現，其中包含 100) 、攻擊來源裝置和攻擊目標裝置 (的向量分數。 針對攻擊媒介的圖形描述選取特定的攻擊。

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="攻擊媒介。":::

## <a name="see-also"></a>另請參閱

[攻擊向量報告](how-to-create-attack-vector-reports.md)


