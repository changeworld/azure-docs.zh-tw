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
ms.date: 12/15/2020
ms.author: memildin
ms.openlocfilehash: 3f319c35631f8c85cab4613df0f1c14f98356caa
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563342"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>在 Azure 資訊安全中心中管理及回應安全性警示

> [!TIP]
> 此頁面上的資訊是指 [安全性警示] 頁面頂端的橫幅提供的新 (預覽) 警示體驗。 
>
> :::image type="content" source="media/security-center-managing-and-responding-alerts/preview-alerts-experience-banner.png" alt-text="連結至新的預覽警示體驗的橫幅":::

本主題說明如何查看和處理安全性中心的警示，以及保護您的資源。

觸發安全性警示的 Advanced 偵測僅適用于 Azure Defender。 有免費試用版可用。 若要升級，請參閱 [啟用 Azure Defender](security-center-pricing.md#enable-azure-defender)。

## <a name="what-are-security-alerts"></a>什麼是安全性警示：
資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。

若要瞭解不同類型的警示，請參閱 [安全性警示-參考指南](alerts-reference.md)。

如需有關安全性中心如何產生警示的總覽，請參閱 [Azure 資訊安全中心如何偵測和回應威脅](security-center-alerts-overview.md)。


## <a name="manage-your-security-alerts"></a>管理安全性警示

1. 在 [安全性中心的總覽] 頁面中，選取頁面頂端的 [ **安全性警示** ] 磚，或側邊欄的連結。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="從 Azure 資訊安全中心的總覽頁面取得安全性警示頁面":::

    [安全性警示] 頁面隨即開啟。

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Azure 資訊安全中心的安全性警示清單":::

1. 若要篩選警示清單，請選取任何相關的篩選準則。 您可以選擇性地使用 [ **加入篩選** ] 選項來新增進一步的篩選。

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="將篩選準則新增至警示視圖" lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    清單會根據您選取的篩選選項進行更新。 篩選可能很有説明。 例如，您可能想確認在過去 24 小時發生的安全性警示，因為您正在調查系統中可能的入侵行動。


## <a name="respond-to-security-alerts"></a>回應安全性警示

1. 從 [ **安全性警示** ] 清單中選取警示。 側邊窗格隨即開啟，並顯示警示和所有受影響資源的描述。 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="安全性警示的迷你詳細資料檢視":::

    > [!TIP]
    > 開啟此側邊窗格後，您可以使用鍵盤上的向上和向下箭號，快速查看警示清單。

1. 如需詳細資訊，請選取 [ **查看完整詳細資料**]。

    [安全性警示] 頁面的左窗格會顯示有關安全性警示的高階資訊： [標題]、[嚴重性]、[狀態]、[啟用時間]、[可疑活動的描述] 和 [受影響的資源]。 受影響的資源與資源相關的 Azure 標記也是。 您可以使用這些在調查警示時推斷資源的組織內容。

    右窗格包含 [ **警示詳細資料** ] 索引標籤，其中包含警示的進一步詳細資料，以協助您調查問題： IP 位址、檔案、處理常式等等。
     
    ![關於安全性警示的建議事項](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    此外，在右窗格中的 [ **採取動作** ] 索引標籤。使用此索引標籤可針對安全性警示採取進一步的動作。 動作，例如：
    - *減輕威脅* -為此安全性警示提供手動補救步驟
    - *預防未來的攻擊* -提供安全性建議，以協助減少受攻擊面、提高安全性狀態，進而防止未來的攻擊
    - *觸發程式自動回應* -提供可觸發邏輯應用程式以回應此安全性警示的選項
    - *隱藏類似警示* -提供選項，以在警示與您的組織不相關時隱藏未來具有類似特性的警示

    ![[採取動作] 索引標籤](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>請參閱

在本檔中，您已瞭解如何查看安全性警示。 請參閱下列頁面以取得相關材質：

- [設定警示隱藏規則](alerts-suppression-rules.md)
- [自動回應安全性中心觸發程式](workflow-automation.md)
- [安全性警示 - 參考指南](alerts-reference.md)
