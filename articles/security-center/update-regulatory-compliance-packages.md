---
title: 如何在 Azure 安全中心法規合規性儀錶板中更新到動態法規遵從性監視 |微軟文件
description: 更新法規遵從性套件
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: fa5027ed285456247891c84e559b74a14237f553
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537775"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard"></a>更新到法規遵從性儀錶板中的動態合規性包

Azure 安全中心不斷將資源的配置與行業標準、法規和基準方面的要求進行比較。 **法規遵從性儀錶板**基於您滿足特定合規性控制和要求的實現方式,提供對合規性狀況的見解。

可以跟蹤合規性狀態的一個標準是[Azure CIS 1.1.0(](https://www.cisecurity.org/benchmark/azure/)更正式地說,"CIS 微軟 Azure 基礎基準版本 1.1.0")。 

最初出現在合規性儀錶板中的 Azure CIS 表示依賴於安全中心隨附的靜態規則集。

借助**動態合規性包**功能,Security Center 會隨著時間的推移自動提高行業標準的覆蓋範圍。 合規性包本質上是 Azure 策略中定義的計劃。 它們可以分配給您選擇的作用域(訂閱、管理組等)。 要查看儀錶板中映射為評估的合規性數據,請從安全策略中向管理組或訂閱添加合規性包。 添加合規性包可有效地將法規遵從性計劃分配給您選擇的範圍。 通過這種方式,您可以在儀錶板中跟蹤新發佈的法規計劃作為合規性標準。 當 Microsoft 發表計畫的新內容(映射到標準中更多控制件的新策略)時,其他內容會自動顯示在儀表板中。

Azure CIS 基準的動態合規性包**Azure CIS 1.1.0(新)** 在原始*靜態*版本上改進::

* 包括更多政策
* 新增新覆寫範圍時自動更新 

更新到新的動態包,如下所述。

## <a name="adding-a-dynamic-compliance-package"></a>新增動態合規性包

以下步驟說明如何添加動態包,以監視是否符合 Azure CIS 基準 v1.1.0。   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>更新到 Azure CIS 1.1.0(新)動態合規性包 

1. 打開**安全策略**頁面。 此頁顯示管理組、訂閱、工作區和管理組結構的數量。

1. 選擇要為其管理法規遵從性狀態的訂閱或管理組。 我們建議選擇標準適用的最高範圍,以便聚合和跟蹤所有嵌套資源的合規性數據。 

1. 在「行業&法規標準部分中,您將看到 Azure CIS 1.1.0 可以針對新內容進行更新。 按下 **「立即更新**」。 

1. 可選,按下「**添加更多標準**」以打開「**添加法規遵從性標準」** 頁。 在那裡,您可以手動搜索 Azure **CIS 1.1.0 (新)** 和動態包,查找其他合規性標準,如**NIST SP 800-53 R4、SWIFT** **CSP CSCF-v2020、UKO****和英國 NHS**以及**加拿大 PBMM**。
    
    > [!TIP]
    > 只有擁有者或策略參與者的使用者才具有添加合規性標準的必要許可權。 

    ![將法規包添加到 Azure 安全中心的法規遵從性儀表板](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. 從安全中心的側邊欄中,選擇**法規遵從性**以打開法規遵從性儀錶板。 
    * Azure CIS 1.1.0 (New) 現在顯示在行業&監管標準清單中。 
    * Azure CIS 1.1.0 合規性的原始*靜態*視圖也將保留在它旁邊。 將來可能會自動刪除它。

    > [!NOTE]
    > 新添加的標準可能需要幾個小時才能顯示在合規性儀錶板中。


    [![顯示新舊 Azure CIS 的法規遵從性儀表板](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>後續步驟

在本文中，您已了解：

* 如何將法規遵從儀表板中顯示的**標準升級到**新的*動態*套件
* 如何**添加合規性包**來監視您對其他標準的遵守情況。 

有關其他相關材料,請參閱以下文章: 

- [安全中心法規合規性儀錶板](security-center-compliance-dashboard.md)
- [使用安全性原則](tutorial-security-policy.md)
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)了解如何使用 Azure 資訊安全中心的建議來協助您保護 Azure 資源。