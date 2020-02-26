---
title: 如何在您的 Azure 資訊安全中心法規合規性儀表板中更新為動態法規合規性監視 |Microsoft Docs
description: 更新您的法規合規性套件（預覽）
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
ms.openlocfilehash: cfa39799e44cee0a2d36efccd454ccf8ca15157f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603236"
---
# <a name="update-to-dynamic-compliance-packages-in-your-regulatory-compliance-dashboard-preview"></a>在您的法規合規性儀表板（預覽）中更新為動態合規性套件

Azure 資訊安全中心持續比較您的資源設定與業界標準、法規和基準測試的需求。 [**法規合規性] 儀表板**可讓您根據符合特定合規性控制和需求的方式，深入瞭解您的合規性狀況。

您可以追蹤合規性狀態的一個標準是[AZURE CIS 1.1.0](https://www.cisecurity.org/benchmark/azure/) （更正式地說，也就是「CIS Microsoft Azure 基礎基準測試版本1.1.0」）。 

一開始出現在合規性儀表板中的 Azure CI，會依賴一組包含在資訊安全中心中的靜態規則。

有了**動態的相容性套件（預覽）** 功能，資訊安全中心會在一段時間後自動改善其產業標準的涵蓋範圍。 合規性套件基本上是 Azure 原則中定義的計畫。 您可以將它們指派給您選取的範圍（訂用帳戶、管理群組等等）。 若要查看在儀表板中對應為評量的相容性資料，請從安全性原則中，將合規性套件新增至您的管理群組或訂用帳戶。 新增合規性套件可有效地將法規合規性方案指派給您選取的範圍。 如此一來，您可以在儀表板中追蹤新發佈的法規計畫，做為合規性標準。 當 Microsoft 發行計畫的新內容時（對應至標準中更多控制項的新原則），其他內容會自動顯示在您的儀表板中。

Azure CIS 基準測試的動態合規性套件（ **AZURE cis 1.1.0 （新的））** 透過下列方式改善原始*靜態*版本：

* 包含更多原則
* 新增新涵蓋範圍時自動更新 

更新至新的動態封裝，如下所述。

## <a name="adding-a-dynamic-compliance-package"></a>新增動態合規性套件

下列步驟說明如何使用 Azure CIS 基準1.1.0 來新增動態封裝，以監視您的合規性。   

### <a name="update-to-the-azure-cis-110-new-dynamic-compliance-package"></a>Azure CIS 1.1.0 （新）動態合規性套件的更新 

1. 開啟 [**安全性原則**] 頁面。 此頁面會顯示管理群組、訂用帳戶、工作區和您的管理群組結構的數目。

1. 選取您要管理其合規性狀態的訂用帳戶或管理群組。 我們建議您選取適用標準的最高範圍，以便為所有的嵌套資源匯總和追蹤合規性資料。 

1. 在業界 & [法規標準（預覽）] 區段中，您會看到 Azure CIS 1.1.0 可以針對新的內容進行更新。 按一下 [**立即更新**]。 

1. （選擇性）按一下 [**新增更多標準**] 以開啟 [**新增法規合規性標準**] 頁面。 在這裡，您可以針對其他合規性標準（例如**NIST SP 800-53 R4**、 **SWIFT CSP CSCF-v2020**、 **UKO 和 UK NHS**和**加拿大 PBMM**）手動搜尋**Azure CIS 1.1.0 （新的）** 和動態封裝。
    
    ![將法規套件新增至 Azure 資訊安全中心的法規合規性儀表板](./media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-additional-standards.png)


1. 從資訊安全中心的提要欄位中，選取 [**法規遵循**] 以開啟 [法規合規性儀表板]。 
    * Azure CIS 1.1.0 （新的）現在會出現在您的業界 & 法規標準清單中。 
    * Azure CI 1.1.0 合規性的原始*靜態*視圖也會隨之保留。 未來可能會自動移除。

    > [!NOTE]
    > 可能需要幾個小時的時間，新加入的標準才會出現在合規性儀表板中。


    [顯示舊版和新 Azure CI 的 ![法規合規性儀表板](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png)](media/update-regulatory-compliance-packages/security-center-dynamic-regulatory-compliance-cis-old-and-new.png#lightbox)


## <a name="next-steps"></a>後續步驟

在本文中，您已了解：

* 如何將您的法規合規性儀表板中顯示**的標準升級**至新的*動態*套件
* 如何**新增合規性套件**，以使用其他標準來監視合規性。 

如需其他相關內容，請參閱下列文章： 

- [安全性中心法規合規性儀表板](security-center-compliance-dashboard.md)
- [使用安全性原則](tutorial-security-policy.md)
- [管理 Azure 資訊安全中心的安全性建議](security-center-recommendations.md)了解如何使用 Azure 資訊安全中心的建議來協助您保護 Azure 資源。