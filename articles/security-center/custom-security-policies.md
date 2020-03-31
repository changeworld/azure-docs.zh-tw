---
title: 在 Azure 安全中心創建自訂安全性原則 |微軟文檔
description: Azure 自訂策略定義由 Azure 安全中心監視。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: memildin
ms.openlocfilehash: c709890ae6c57a001c6a0e9df4e973bd3bd24602
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258255"
---
# <a name="using-custom-security-policies"></a>使用自訂安全性原則

為了説明保護系統和環境，Azure 安全中心生成安全建議。 這些建議基於行業最佳實踐，這些最佳實踐已納入提供給所有客戶的通用預設安全性原則中。 它們也可以來自安全中心對行業和監管標準的瞭解。

使用此功能，您可以添加自己的*自訂*計畫。 如果環境沒有遵循您創建的策略，您將收到建議。 您創建的任何自訂計畫都將[與教程中描述的](security-center-compliance-dashboard.md)法規遵從性儀表板中的內置計畫一起出現。

如[Azure](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#definition-location)策略文檔中所述，當您為自訂計畫指定位置時，它必須是管理組或訂閱。 

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>向訂閱添加自訂計畫 

1. 從安全中心的側邊欄，打開**安全性原則**頁面。

1. 選擇要向其添加自訂計畫的訂閱或管理組。

    [![選擇要為其創建自訂策略的訂閱](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > 您必須在訂閱級別（或更高版本）添加自訂標準，才能在安全中心中評估和顯示這些標準。 
    >
    > 添加自訂標準時，它將*計畫*分配給該作用域。 因此，我們建議您選擇該分配所需的最寬範圍。

1. 在"安全性原則"頁中，在"自訂計畫"下，按一下"**添加自訂計畫**"。

    [![按一下 [添加自訂計畫]](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    將顯示以下頁面：

    ![創建或添加策略](media/custom-security-policies/create-or-add-custom-policy.png)

1. 在"添加自訂計畫"頁中，查看組織中已創建的自訂策略的清單。 如果您看到要分配給訂閱的訂閱，請按一下"**添加**"。 如果清單中沒有滿足您需求的計畫，請跳過此步驟。

1. 要創建新的自訂計畫，

    1. 按一下 **"創建新**"。
    1. 輸入定義的位置和名稱。
    1. 選擇要包含的策略，然後按一下"**添加**"。
    1. 輸入任何所需的參數。
    1. 按一下 [儲存]****。
    1. 在"添加自訂計畫"頁中，按一下刷新，您的新計畫將顯示為可用。
    1. 按一下"**添加**"並將其分配給訂閱。

    > [!NOTE]
    > 創建新計畫需要訂閱擁有者憑據。 有關 Azure 角色的詳細資訊，請參閱[Azure 安全中心中的許可權](security-center-permissions.md)。

    您的新計畫生效，您可以通過以下兩種方式看到影響：

    * 在安全中心側邊欄中，在策略&合規性下，選擇**法規遵從性**。 合規性儀表板將打開，以顯示您的新自訂計畫以及內置計畫。
    
    * 如果環境沒有遵循您定義的策略，您將開始收到建議。

1. 要查看策略的結果建議，請按一下側邊欄**中的建議**以打開建議頁面。 建議將顯示為"自訂"標籤，大約在一小時內可用。

    [![自訂建議](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)


## <a name="next-steps"></a>後續步驟

在本文中，您學習了如何創建自訂安全性原則。 

有關其他相關材料，請參閱以下文章： 

- [安全性原則概述](tutorial-security-policy.md)
- [內置安全性原則的清單](security-center-policy-definitions.md)