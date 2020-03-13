---
title: Azure 資訊安全中心的安全性建議
description: 本文件將逐步引導您了解「Azure 資訊安全中心」的建議如何協助您保護 Azure 資源及遵守安全性原則。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/29/2019
ms.author: memildin
ms.openlocfilehash: 408b0f020be72b8e6b10dd6c97298afda1b91360
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245300"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 資訊安全中心的安全性建議 
本主題說明如何在 Azure 資訊安全中心中查看及瞭解建議，以協助您保護 Azure 資源。

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>

## <a name="what-are-security-recommendations"></a>有哪些安全性建議？

建議是為了保護您的資源而採取的動作。

資訊安全中心定期分析 Azure 資源的安全性狀態，以找出潛在的安全性弱點。 接著，它會提供您有關如何移除它們的建議。

每個建議都會提供下列各項：

- 建議的簡短描述。
- 要執行的補救步驟，以便執行建議。 <!-- In some cases, Quick Fix remediation is available. -->
- 您需要對這些資源執行建議的動作。
- **安全分數會影響**，這是您執行此建議時，您的安全分數將會增加的數量。

## 監視建議<a name="monitor-recommendations"></a>

資訊安全中心會分析您資源的安全性狀態，以找出潛在的弱點。 [**總覽**] 底下的 [**建議**] 圖格會顯示資訊安全中心所識別的建議總數。

![安全性中心總覽](./media/security-center-recommendations/asc-overview.png)

1. 選取 [**總覽**] 底下的 [**建議] 圖**格。 [**建議**] 清單隨即開啟。

      ![檢視建議](./media/security-center-recommendations/view-recommendations.png)

    您可以篩選建議。 若要篩選建議，請選取 [建議] 刀鋒視窗上的 [篩選]。 即會開啟 [篩選] 刀鋒視窗，您可以選取想要查看的嚴重性和狀態值。

   * **建議**：建議。
   * **安全分數的影響**：資訊安全中心使用您的安全性建議所產生的分數，並套用先進的演算法來判斷每個建議的重要程度。 如需詳細資訊，請參閱[安全分數計算](security-center-secure-score.md#secure-score-calculation)。
   * **資源**：列出這個建議適用的資源。
   * **狀態列**：描述該特定建議的嚴重性：
       * **高（紅色）** ：有意義的資源（例如應用程式、VM 或網路安全性群組）有弱點存在，並且需要注意。
       * **中（橙色）** ：有弱點存在，需要非關鍵性或額外的步驟，才能將其排除或完成處理常式。
       * **低（藍色）** ：應該解決但不需要立即注意的弱點存在。 (預設不會顯示嚴重性低的建議，但是如果您要查看它們，則可以篩選嚴重性低的建議。) 
       * **狀況良好（綠色）** ：
       * **無法使用（灰色）** ：

1. 若要查看每個建議的詳細資料，請按一下 [建議]。

    ![建議的詳細資料](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> 請參閱適用于 Azure 資源的[傳統和 Resource Manager 部署模型](../azure-classic-rm.md)。
 
## <a name="next-steps"></a>後續步驟

在本文件中，已向您介紹「資訊安全中心」的安全性建議。 若要瞭解如何修復建議：

* [補救建議](security-center-remediate-recommendations.md)：瞭解如何為您的 Azure 訂用帳戶和資源群組設定安全性原則。
