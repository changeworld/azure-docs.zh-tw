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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245300"
---
# <a name="security-recommendations-in-azure-security-center"></a>Azure 資訊安全中心的安全性建議 
本主題介紹如何查看和理解 Azure 安全中心中的建議，以説明您保護 Azure 資源。

> [!NOTE]
> 本文件將使用範例部署來介紹服務。  本文件不是一份逐步解說指南。
>

## <a name="what-are-security-recommendations"></a>有哪些安全性建議？

建議是為了保護您的資源而需要執行的操作。

安全中心定期分析 Azure 資源的安全狀態，以識別潛在的安全性漏洞。 然後，它為您提供有關如何刪除它們的建議。

每個建議都為您提供：

- 建議內容的簡短說明。
- 為了執行建議而要執行的補救步驟。 <!-- In some cases, Quick Fix remediation is available. -->
- 需要哪些資源對它們執行建議的操作。
- **安全分數影響**，這是如果您實施此建議，您的安全分數將上升的金額。

## <a name="monitor-recommendations"></a>監測建議<a name="monitor-recommendations"></a>

安全中心分析資源的安全狀態，以識別潛在的漏洞。 **"概述**"下**的建議**磁貼顯示安全中心標識的建議總數。

![安全中心概述](./media/security-center-recommendations/asc-overview.png)

1. 選擇 **"概述**"下**的建議磁貼**。 將打開 **"建議"** 清單。

      ![檢視建議](./media/security-center-recommendations/view-recommendations.png)

    您可以篩選建議。 若要篩選建議，請選取 [建議]**** 刀鋒視窗上的 [篩選]****。 即會開啟 [篩選] **** 刀鋒視窗，您可以選取想要查看的嚴重性和狀態值。

   * **建議**：建議。
   * **安全分數影響**：安全中心使用您的安全建議生成的分數，並應用高級演算法來確定每個建議有多重要。 有關詳細資訊，請參閱[安全分數計算](security-center-secure-score.md#secure-score-calculation)。
   * **資源**：列出這個建議適用的資源。
   * **狀態 BARS**： 描述該特定建議的嚴重性：
       * **高（紅色）：** 存在有意義的資源（如應用程式、VM 或網路安全性群組）的漏洞，需要注意。
       * **中等（橙色）：** 存在漏洞，需要非關鍵或額外步驟來消除它或完成一個過程。
       * **低（藍色）：** 存在一個漏洞，應該解決，但不需要立即注意。 (預設不會顯示嚴重性低的建議，但是如果您要查看它們，則可以篩選嚴重性低的建議。) 
       * **健康（綠色）：**
       * **不可用（灰色）：**

1. 要查看每個建議的詳細資訊，請按一下該建議。

    ![建議的詳細資料](./media/security-center-recommendations/recommendation-details.png)

>[!NOTE] 
> 請參閱 Azure 資源[的經典部署模型和資源管理器部署模型](../azure-classic-rm.md)。
 
## <a name="next-steps"></a>後續步驟

在本文件中，已向您介紹「資訊安全中心」的安全性建議。 要瞭解如何修復建議：

* [修正建議](security-center-remediate-recommendations.md)– 瞭解如何為 Azure 訂閱和資源組配置安全性原則。
