---
title: 在 Azure 資訊安全中心中使用法規合規性儀表板
description: 瞭解如何在安全性中心的法規合規性儀表板中新增和移除法規標準
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/15/2020
ms.author: memildin
ms.openlocfilehash: 5e32aa8491e18a66a3e8b90f065e6b785d1e3bd9
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/16/2020
ms.locfileid: "92106258"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>如何在法規合規性儀表板中自訂一組標準

Azure 資訊安全中心會持續比較您的資源設定與業界標準、法規和效能評定的需求。 **法規合規性儀表板**會根據您符合特定合規性控制和需求的程度，提供合規性狀態的見解。


## <a name="overview-of-compliance-packages"></a>合規性套件概觀

在資訊安全中心，業界標準、法規標準和效能評定會以「相容性套件」來表示。  每個套件都是在 Azure 原則中定義的計畫。 若要查看在儀表板中對應為評量的相容性資料，請在 [安全性原則] 頁面中，將合規性套件新增至您的管理群組或訂用帳戶。 (若要深入了解 Azure 原則和計畫，請參閱[使用安全性原則](tutorial-security-policy.md)。)

當您將標準或效能評定上架至您選取的範圍時，其會將計畫指派至該範圍，而標準會出現在您的法規合規性儀表板中，並將所有相關聯的相容性資料對應為評量。 您也可以下載摘要報告，以取得任何已上架的標準。

Microsoft 也會追蹤法規標準本身，並隨著時間自動改善某些套件中的涵蓋範圍。 當 Microsoft 發行計畫的新內容時 (對應至標準中更多控制項的新原則)，其他內容會自動顯示在您的儀表板中。

> [!TIP]
> 可隨著 Microsoft 發行新內容而改進的一種標準就是 [Azure CIS 1.1.0 (新版)] (更正式地說，[CIS Microsoft Azure Foundations Benchmark 1.1.0 版](https://www.cisecurity.org/benchmark/azure/))。 您必須將此標準新增至儀表板中 "Azure CIS 1.1.0" (代表每個資訊安全中心環境中預設設定的 Azure CIS) 的旁邊。 該套件依賴一組靜態規則。 較新的套件包含更多原則，而且會隨著時間自動更新。 如下所述，更新為新的動態套件。


## <a name="available-packages"></a>可用的套件

您可以新增 NIST SP 800-53 R4、SWIFT CSP CSCF-v2020、UK Official 和 UK NHS、加拿大聯邦 PBMM 和 Azure CIS 1.1.0 (新版) 等標準，這是更完整的 Azure CIS 1.1.0 表述。 

此外，您可以新增 **Azure 安全性效能評定**，這是 Microsoft 針對以通用合規性架構為基礎的安全性和合規性最佳做法所撰寫的 Azure 特定指導方針。 ([深入了解 Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/introduction)。)

當儀表板可供使用時，將會支援其他標準。 


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>將法規標準新增至儀表板

下列步驟說明如何使用其中一個支援的法規標準來新增套件，以監視您的合規性。

> [!NOTE]
> 只有身為擁有者或原則參與者的使用者具有新增合規性標準所需的權限。 

1. 從資訊安全中心的資訊看板中，選取 [法規合規性] 以開啟法規合規性儀表板。 您可在此查看目前已指派給目前所選訂用帳戶的合規性標準。   

1. 從頁面頂端，選取 [管理相容性原則]。 [原則管理] 頁面隨即出現。

1. 選取您要管理法規合規性狀態的訂用帳戶或管理群組。 

    > [!TIP]
    > 我們建議選取適用標準的最高範圍，以便彙總及追蹤所有巢狀資源的合規性資料。 

1. 若要新增與您組織相關的標準，請按一下 [新增更多標準]。 

1. 從 [新增法規合規性標準] 頁面，您可以搜尋套件中是否有任何可用的標準。 其中一些可用的標準：

    - **Azure 安全性效能評定**
    - **NIST SP 800-53 R4**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 和 UK NHS**
    - **加拿大 PBMM**
    
    ![將法規套件新增至 Azure 資訊安全中心的法規合規性儀表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 選取 [ **新增** ]，然後輸入特定方案的所有必要詳細資料，例如範圍、參數和補救。

1. 從資訊安全中心的資訊看板中，再次選取 [法規合規性] 以返回法規合規性儀表板。
    * 您的新標準會出現在您的業界和法規標準清單中。 
    * 如果您新增了 [Azure CIS 1.1.0 (新版)]，Azure CIS 1.1.0 合規性的原始「靜態」檢視也會隨之保留。 未來可能會自動移除。

    > [!NOTE]
    > 新增的標準可能需要幾個小時的時間，才會出現在合規性儀表板中。

    [![顯示舊版和新版 Azure CIS 的法規合規性儀表板](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>從儀表板移除標準

如果任何一項提供的法規標準與您的組織無關，則直接從 UI 中移除它們是簡單的流程。 這可讓您進一步自訂法規合規性儀表板，並僅專注于適用于您的標準。

若要移除標準：

1. 從 [安全性中心] 功能表中，選取 [ **安全性原則**]。

1. 選取您要從中移除標準的相關訂用帳戶。

    > [!NOTE]
    > 您可以從訂用帳戶中移除標準，但不能從管理群組中移除。 

    [安全性原則] 頁面隨即開啟。 針對選取的訂用帳戶，它會顯示預設原則、產業和法規標準，以及您已建立的任何自訂行動。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="在 Azure 資訊安全中心中從您的法規合規性儀表板移除法規標準":::

1. 針對您想要移除的標準，請選取 [ **停**用]。 確認視窗隨即出現。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="在 Azure 資訊安全中心中從您的法規合規性儀表板移除法規標準":::

1. 選取 [是]。 將會移除標準。 


## <a name="next-steps"></a>後續步驟

在本文中，您已了解如何**新增相容性套件**，以監視您是否符合其他標準。 

如需其他相關資料，請參閱下列文章： 

- [Azure 安全性效能評定](https://docs.microsoft.com/azure/security/benchmarks/introduction)
- [資訊安全中心合規性儀表板](security-center-compliance-dashboard.md)
- [使用安全性原則](tutorial-security-policy.md)