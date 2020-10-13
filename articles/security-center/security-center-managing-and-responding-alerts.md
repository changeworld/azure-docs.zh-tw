---
title: 在 Azure 資訊安全中心管理安全性警示 | Microsoft Docs
description: 本文件可協助您使用「Azure 資訊安全中心」功能來管理及回應安全性警示。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2020
ms.author: memildin
ms.openlocfilehash: e17c7d6618d67d66c89875696c5c529af3a85ea9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91440562"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>在 Azure 資訊安全中心管理和回應安全性警示

本主題說明如何查看和處理您為了保護資源所收到的警示。 

* 若要瞭解不同類型的警示，請參閱 [安全性警示類型](alerts-reference.md)。
* 如需有關安全性中心如何產生警示的總覽，請參閱 [Azure 資訊安全中心如何偵測和回應威脅](security-center-alerts-overview.md)。

> [!NOTE]
> 若要啟用先進的偵測，請啟用 Azure Defender。 有免費試用版可用。 若要升級，請選取 [安全性原則](tutorial-security-policy.md)中的 [定價層]。 若要深入了解，請參閱 [Azure 資訊安全中心價格](security-center-pricing.md)。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。

> [!NOTE]
> 如需資訊安全中心偵測功能運作方式的詳細資訊，請參閱 [Azure 資訊安全中心如何偵測和回應威脅](security-center-alerts-overview.md#detect-threats)。

## <a name="manage-your-security-alerts"></a>管理安全性警示

1. 從 [安全性中心] 儀表板，查看 [  **威脅防護** ] 磚以查看警示。

    ![資訊安全中心的 [安全性警示] 圖格](./media/security-center-managing-and-responding-alerts/security-center-dashboard-alert.png)

1. 若要查看警示的更多詳細資料，請按一下圖格。

   ![資訊安全中心內的安全性警示](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. 若要篩選顯示的警示，請按一下 [ **篩選**]，然後從開啟的 [ **篩選器** ] 分頁中，選取您要套用的篩選選項。 清單會根據選取的篩選準則進行更新。 篩選可能很有説明。 例如，您可能想確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵行動。

    ![篩選資訊安全中心的警示](./media/security-center-managing-and-responding-alerts/security-center-filter-alerts.png)

## <a name="respond-to-security-alerts"></a>回應安全性警示

1. 在 [ **安全性警示** ] 清單中，按一下安全性警示。 其中會顯示相關的資源，以及補救攻擊所需採取的步驟。

    ![回應安全性警示](./media/security-center-managing-and-responding-alerts/security-center-alert.png)

1. 查看資訊之後，請按一下受攻擊的資源。

    [安全性警示] 頁面的左窗格會顯示有關安全性警示的高階資訊： [標題]、[嚴重性]、[狀態]、[啟用時間]、[可疑活動的描述] 和 [受影響的資源]。 受影響的資源與資源相關的 Azure 標記也是。 您可以使用這些在調查警示時推斷資源的組織內容。

    右窗格包含 [ **警示詳細資料** ] 索引標籤，其中包含警示的進一步詳細資料，以協助您調查問題： IP 位址、檔案、處理常式等等。
     
    ![關於安全性警示的建議事項](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    此外，在右窗格中的 [ **採取動作** ] 索引標籤。使用此索引標籤可針對安全性警示採取進一步的動作。 動作，例如：
    - *減輕威脅* -為此安全性警示提供手動補救步驟
    - *預防未來的攻擊* -提供安全性建議，以協助減少受攻擊面、提高安全性狀態，進而防止未來的攻擊
    - *觸發程式自動回應* -提供可觸發邏輯應用程式以回應此安全性警示的選項
    - *隱藏類似警示* -提供選項，以在警示與您的組織不相關時隱藏未來具有類似特性的警示

    ![[採取動作] 索引標籤](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>另請參閱

在本檔中，您已瞭解如何查看安全性警示。 請參閱下列頁面以取得相關材質：

- [設定警示隱藏規則](alerts-suppression-rules.md)
- [使用工作流程自動化將警示和建議的回應自動化](workflow-automation.md)
