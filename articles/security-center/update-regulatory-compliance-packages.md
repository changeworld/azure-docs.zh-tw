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
ms.date: 01/24/2021
ms.author: memildin
ms.openlocfilehash: 6fb2e5c0193bc4e66f8fb4215732a69c43731146
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756621"
---
# <a name="customizing-the-set-of-standards-in-your-regulatory-compliance-dashboard"></a>如何在法規合規性儀表板中自訂一組標準

Azure 資訊安全中心會持續比較您的資源設定與業界標準、法規和效能評定的需求。 **法規合規性儀表板** 會根據您符合特定合規性需求的方式，提供您合規性狀態的見解。


## <a name="how-are-regulatory-compliance-standards-represented-in-security-center"></a>資訊安全中心如何標記法規合規性標準？

業界標準、法規標準和基準測試都會以安全性中心的法規合規性儀表板表示。 每個標準都是 Azure 原則中定義的計畫。

若要在您的儀表板中查看對應為評量的合規性資料，請從 [ **安全性原則** ] 頁面中，將合規性標準新增至管理群組或訂用帳戶。 若要深入瞭解 Azure 原則和方案，請參閱 [使用安全性原則](tutorial-security-policy.md)。

當您將標準或基準測試指派給選取的範圍時，標準會顯示在您的法規合規性儀表板中，並將所有相關聯的合規性資料對應為評量。 您也可以下載任何已指派標準的摘要報告。

Microsoft 會自行追蹤法規標準，並在一段時間內自動改善部分套件的涵蓋範圍。 當 Microsoft 發行方案的新內容時，它會自動出現在您的儀表板中，作為與標準中的控制項對應的新原則。


## <a name="what-regulatory-compliance-standards-are-available-in-security-center"></a>資訊安全中心有哪些法規合規性標準？

依預設，每個訂用帳戶都已指派 **Azure 安全性基準測試** 。 這是 Microsoft 撰寫的 Azure 專屬指導方針，適用于以通用合規性架構為基礎的安全性和合規性最佳作法。 [深入了解 Azure 安全性效能評定](../security/benchmarks/introduction.md)。

您也可以新增如下的標準：

- NIST SP 800-53 R4
- SWIFT CSP CSCF-v2020
- UK 官方和 UK NHS
- 加拿大聯邦 PBMM
- Azure CIS 1.1.0

當儀表板變成可用時，系統會將標準新增至儀表板。


## <a name="add-a-regulatory-standard-to-your-dashboard"></a>將法規標準新增至儀表板

下列步驟說明如何使用其中一個支援的法規標準來新增套件，以監視您的合規性。

> [!NOTE]
> 若要將標準新增至儀表板，訂用帳戶必須啟用 Azure Defender。 此外，只有身為擁有者或原則參與者的使用者具有新增合規性標準的必要許可權。 

1. 從資訊安全中心的資訊看板中，選取 [法規合規性] 以開啟法規合規性儀表板。 您可在此查看目前已指派給目前所選訂用帳戶的合規性標準。   

1. 從頁面頂端，選取 [管理相容性原則]。 [原則管理] 頁面隨即出現。

1. 選取您要管理法規合規性狀態的訂用帳戶或管理群組。 

    > [!TIP]
    > 我們建議選取適用標準的最高範圍，以便彙總及追蹤所有巢狀資源的合規性資料。 

1. 若要新增與您組織相關的標準，請按一下 [新增更多標準]。 

1. 您可以從 [ **新增法規合規性標準** ] 頁面搜尋任何可用的標準，包括：

    - **NIST SP 800-53 R4**
    - **NIST SP 800 171 R2**
    - **SWIFT CSP CSCF-v2020**
    - **UKO 和 UK NHS**
    - **加拿大 PBMM**
    
    ![將法規標準新增至 Azure 資訊安全中心的法規合規性儀表板](./media/update-regulatory-compliance-packages/dynamic-regulatory-compliance-additional-standards.png)

1. 選取 [ **新增** ]，然後輸入特定方案的所有必要詳細資料，例如範圍、參數和補救。

1. 從資訊安全中心的資訊看板中，再次選取 [法規合規性] 以返回法規合規性儀表板。

    您的新標準會出現在您的業界和法規標準清單中。 

    > [!NOTE]
    > 新增的標準可能需要幾個小時的時間，才會出現在合規性儀表板中。

    [![顯示舊版和新版 Azure CIS 的法規合規性儀表板](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark-small.png)](media/update-regulatory-compliance-packages/regulatory-compliance-dashboard-with-benchmark.png#lightbox)


## <a name="removing-a-standard-from-your-dashboard"></a>從儀表板移除標準

如果任何一項提供的法規標準與您的組織無關，這是從 UI 中移除它們的簡單流程。 這可讓您進一步自訂法規合規性儀表板，並僅專注于適用于您的標準。

若要移除標準：

1. 從 [安全性中心] 功能表中，選取 [ **安全性原則**]。

1. 選取您要從中移除標準的相關訂用帳戶。

    > [!NOTE]
    > 您可以從訂用帳戶中移除標準，但不能從管理群組中移除。 

    [安全性原則] 頁面隨即開啟。 針對選取的訂用帳戶，它會顯示預設原則、產業和法規標準，以及您已建立的任何自訂行動。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard.png" alt-text="在 Azure 資訊安全中心中從您的法規合規性儀表板移除法規標準":::

1. 針對您想要移除的標準，請選取 [ **停** 用]。 確認視窗隨即出現。

    :::image type="content" source="./media/update-regulatory-compliance-packages/remove-standard-confirm.png" alt-text="確認您真的想要移除所選的法規標準":::

1. 選取 [是]  。 將會移除標準。 


## <a name="next-steps"></a>後續步驟

在本文中，您已瞭解如何 **新增合規性標準** ，以根據法規和產業標準來監視您的合規性。

如需相關材質，請參閱下列頁面：

- [Azure 安全性效能評定](../security/benchmarks/introduction.md)
- [資訊安全中心合規性儀表板](security-center-compliance-dashboard.md)
- [使用安全性原則](tutorial-security-policy.md)