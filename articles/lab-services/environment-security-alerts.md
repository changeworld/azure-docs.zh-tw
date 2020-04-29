---
title: Azure DevTest Labs 中環境的安全性警示
description: 本文說明如何在 DevTest Labs 中查看環境的安全性警示，並採取適當的動作。
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 04/28/2020
ms.locfileid: "77588700"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest Labs 中環境的安全性警示
身為實驗室使用者，您現在可以看到實驗室環境的 Azure 資訊安全中心警示。 資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。 [深入瞭解 Azure 資訊安全中心中的安全性警示](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>先決條件
目前，您只可以針對部署在實驗室中的平臺即服務（PaaS）環境，查看安全性警示。 若要測試或使用這項功能，請將[環境部署至您的實驗室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看環境的安全性警示

1. 在您實驗室的首頁上，選取左側功能表上的 [**安全性警示**]。 您應該會看到安全性警示的數目（[高]、[中] 和 [低]）。 深入瞭解[警示的分類方式](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)。

    ![安全性警示-總覽](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 以滑鼠右鍵按一下最後一個資料行中的三個點（...），然後選取 [**查看安全性警示**]。 

    ![檢視安全性警示](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 您會看到有關警示和 advisor 建議的更多詳細資料。 深入瞭解[如何管理及回應 Azure 資訊安全中心中的安全性警示](../security-center/security-center-managing-and-responding-alerts.md)。

    ![檢視安全性警示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>後續步驟
若要深入瞭解環境，請參閱下列文章：

- [使用 Azure Resource Manager 範本來建立多個 vm 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定並使用公用環境](devtest-lab-configure-use-public-environments.md)
