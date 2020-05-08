---
title: 如何在您的 Azure 資訊安全中心法規合規性儀表板中更新為動態法規合規性監視 |Microsoft Docs
description: 更新您的法規合規性套件
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
ms.openlocfilehash: 7a017f9485ec847c0a2c045e4c511b6a68db0cb2
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889935"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>在您的法規合規性儀表板中自訂一組標準

Azure 資訊安全中心持續比較您的資源設定與業界標準、法規和基準測試的需求。 [**法規合規性] 儀表板**可讓您根據符合特定合規性控制和需求的方式，深入瞭解您的合規性狀況。


## <a name="overview-of-compliance-packages"></a>合規性套件總覽

業界標準、法規標準和基準測試會在資訊安全中心中表示為*合規性套件*。  每個套件都是在 Azure 原則中定義的計畫。 若要查看在儀表板中對應為評量的相容性資料，請在 [**安全性原則**] 頁面中，將合規性套件新增至您的管理群組或訂用帳戶。 （深入瞭解[使用安全性原則](tutorial-security-policy.md)的 Azure 原則和計畫）。

當您對選取的範圍上架標準或基準測試時，標準會出現在您的「法規合規性儀表板」中，並將所有相關聯的相容性資料對應為評量。 您也可以下載任何已上架之標準的摘要報告。

Microsoft 也會追蹤法規標準本身，並在一段時間後自動改善部分套件中的涵蓋範圍。 當 Microsoft 發行計畫的新內容時（對應至標準中更多控制項的新原則），其他內容會自動顯示在您的儀表板中。

> [!TIP]
> 一種標準，可在 Microsoft 發行新內容時改善一段時間， **AZURE CIS 1.1.0 （新）** （更正式地說， [Ci Microsoft Azure 基礎基準測試版本 1.1.0](https://www.cisecurity.org/benchmark/azure/)）。 您必須將此新增至儀表板，以及「Azure CIS 1.1.0」，這是每個資訊安全中心環境中預設設定的 Azure CI 標記法。 該套件依賴一組靜態的規則。 較新的套件包含更多原則，而且會隨著時間自動更新。 更新至新的動態封裝，如下所述。


## <a name="available-packages"></a>可用的套件

您可以新增如 NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、英國官方和英國 NHS、加拿大聯邦 PBMM 和 Azure CIS 1.1.0 （新）等標準，這是更完整的 Azure CI 1.1.0 標記法。 

此外，您也可以根據常見的合規性架構，新增**Azure 安全性基準測試**，這是 Microsoft 撰寫的 azure 特定指導方針，適用于安全性和合規性最佳做法。 （[深入瞭解 Azure 安全性基準測試](https://docs.microsoft.com/azure/security/benchmarks/introduction)）。

當儀表板可供使用時，將會支援其他標準。 


## <a name="adding-a-regulatory-standard-to-your-dashboard"></a>將法規標準新增至您的儀表板

下列步驟說明如何使用其中一個支援的法規標準來新增封裝，以監視您的合規性。

> [!NOTE]
> 只有身為擁有者或原則參與者的使用者具有新增合規性標準的必要許可權。 

1. 從資訊安全中心的提要欄位中，選取 [**法規遵循**] 以開啟 [法規合規性儀表板]。 在這裡，您可以看到目前已指派給目前所選訂用帳戶的合規性標準。   

1. 從頁面頂端，選取 [**管理相容性原則**]。 [原則管理] 頁面隨即出現。

1. 選取您要管理其合規性狀態的訂用帳戶或管理群組。 

    > [!TIP]
    > 我們建議您選取適用標準的最高範圍，以便為所有的嵌套資源匯總和追蹤合規性資料。 

1. 若要新增與您組織相關的標準，請按一下 [**新增更多標準**]。 

1. 從 [**新增法規合規性標準**] 頁面中，您可以搜尋任何可用標準的套件。 其中一些可用的標準是：

    - **Azure 安全性效能評定**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 和 UK NHS**
    - **加拿大 PBMM**
    
    ![將法規套件新增至 Azure 資訊安全中心的法規合規性儀表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 從資訊安全中心的提要欄位中，再次選取 [**法規合規性**] 以返回 [法規合規性] 儀表板。
    * 您的新標準會出現在您的業界 & 法規標準清單中。 
    * 如果您已新增**AZURE cis 1.1.0 （新的）**，您 azure ci 1.1.0 合規性的原始*靜態*視圖也會與它一起保留。 未來可能會自動移除。

    > [!NOTE]
    > 可能需要幾個小時的時間，新加入的標準才會出現在合規性儀表板中。

    [![顯示舊版和新 Azure CI 的法規合規性儀表板](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)

## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何**新增合規性套件**，以使用其他標準來監視合規性。 

如需其他相關內容，請參閱下列文章： 

- [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [安全性中心法規合規性儀表板](security-center-compliance-dashboard.md)
- [使用安全性原則](tutorial-security-policy.md)