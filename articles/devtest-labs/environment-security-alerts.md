---
title: Azure DevTest Labs 環境的安全性警示
description: 本文說明如何在 DevTest Labs 中查看環境的安全性警示，並採取適當的動作。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308685"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs 環境的安全性警示
您以實驗室使用者的身份，現在可以查看實驗室環境 Azure 資訊安全中心的警示。 資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。 [深入瞭解 Azure 資訊安全中心中的安全性警示](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>必要條件
目前，您只能在部署至實驗室的平臺即服務 (PaaS) 環境中，查看安全性警示。 若要測試或使用這項功能，請將 [環境部署到您的實驗室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看環境的安全性警示

1. 在實驗室的首頁上，選取左側功能表上的 [ **安全性警示** ]。 您應該會看到 [高]、[中] 和 [低])  (的安全性警示數目。 深入瞭解 [警示的分類方式](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)。

    ![安全性警示-總覽](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 以滑鼠右鍵按一下最後一個資料行中 ( ... ) 的三個點，然後選取 [ **視圖安全性警示**]。 

    ![顯示 [安全性警示] 頁面的螢幕擷取畫面，其中已選取 [查看安全性警示]。](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 您會看到有關警示和 advisor 建議的更多詳細資料。 深入瞭解 [如何在 Azure 資訊安全中心中管理及回應安全性警示](../security-center/security-center-managing-and-responding-alerts.md)。

    ![檢視安全性警訊](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>後續步驟
若要深入瞭解環境，請參閱下列文章：

- [使用 Azure Resource Manager 範本建立多個 vm 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定並使用公用環境](devtest-lab-configure-use-public-environments.md)
