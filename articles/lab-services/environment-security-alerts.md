---
title: Azure 開發人員測試實驗室中環境的安全警報
description: 本文介紹如何在 DevTest 實驗室中查看環境的安全警報並採取適當的操作。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588700"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure 開發人員測試實驗室中環境的安全警報
作為實驗室使用者，您現在可以查看實驗室環境的 Azure 安全中心警報。 資訊安全中心會自動收集、分析及整合您 Azure 資源、網路和已連線的合作夥伴解決方案 (例如防火牆和端點保護解決方案) 的記錄檔資料，來偵測真正的威脅並減少誤判情形。 「資訊安全中心」會顯示優先安全性警示清單，以及需要您快速調查問題的資訊，和如何修復攻擊行為的建議。 [瞭解有關 Azure 安全中心中安全警報的更多詳細資訊](../security-center//security-center-alerts-overview.md)。  


## <a name="prerequisites"></a>Prerequisites
目前，您只能查看部署在實驗室中的平臺作為服務 （PaaS） 環境的安全警報。 要測試或使用此功能，請將[環境部署到實驗室](devtest-lab-create-environment-from-arm.md)。 

## <a name="view-security-alerts-for-an-environment"></a>查看環境的安全警報

1. 在實驗室的主頁上，選擇左側功能表上**的安全警報**。 您應該看到安全警報的數量（高、中和低）。 詳細瞭解[警報的分類方式](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)。

    ![安全警報 - 概述](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 按右鍵最後一列中的三個點 （...），然後選擇 **"查看安全警報**"。 

    ![檢視安全性警示](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 您將看到有關警報和顧問建議的更多詳細資訊。 詳細瞭解 Azure[安全中心中的安全和回應](../security-center/security-center-managing-and-responding-alerts.md)。

    ![檢視安全性警示](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>後續步驟
要瞭解有關環境的更多詳細資訊，請參閱以下文章：

- [使用 Azure 資源管理器範本創建多 vm 環境和 PaaS 資源](devtest-lab-create-environment-from-arm.md)
- [設定並使用公用環境](devtest-lab-configure-use-public-environments.md)
